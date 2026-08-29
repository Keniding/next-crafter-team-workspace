# Spec: business auth

_Baseline. Last updated by `business-auth-github-app` (archived)._

## Requirement: Clerk is the sole identity and session provider

`packages/auth` MUST be the only module that imports Clerk SDK types (`useAuth`, `useUser`, `ClerkProvider`, `ConvexProviderWithClerk`) on the client, and the only Convex code that verifies Clerk webhook signatures. Convex MUST validate the Clerk-issued JWT via `convex/auth.config.ts` and MUST NOT issue, store, or refresh session tokens itself.

Every other module (`packages/user`, `packages/github`, `packages/repository`, `apps/mobile` screens) MUST obtain the current user through this module's agnostic surface (`requireUser(ctx)` in Convex, `useBusinessAuth()` in Expo) and MUST NOT reference `clerkId`, Clerk hooks, or Clerk webhook payloads directly.

### Scenario: sign up creates a domain user

- GIVEN a new visitor completes Clerk sign up
- WHEN Clerk's `user.created` webhook reaches `httpAction /clerk/webhook`
- THEN Convex MUST upsert a `users` row keyed by `clerkId`
- AND that row's `_id` MUST become the `UserId` every other module uses

### Scenario: lazy sync fallback

- GIVEN a valid Clerk JWT reaches a Convex function
- WHEN no `users` row exists yet for that `clerkId` (webhook has not landed)
- THEN `requireUser(ctx)` MUST create the row lazily instead of failing
- AND MUST NOT create a duplicate row once the webhook arrives for the same `clerkId`

### Scenario: no direct Clerk access outside this module

- GIVEN any package other than `packages/auth`
- WHEN an agent imports a Clerk hook, type, or the webhook secret
- THEN that change MUST be rejected as out of scope
- AND the agent MUST use `requireUser(ctx)` / `useBusinessAuth()` instead

### Scenario: session revoked mid-app

- GIVEN a user's session was revoked from the Clerk dashboard or another device
- WHEN the app calls a Convex function with the stale JWT
- THEN Convex MUST reject the call
- AND `apps/mobile` MUST route the user back to `openSignIn()`, not retry silently

## Requirement: Google is the default sign-in method

`packages/auth` MUST configure Google as the default sign-in strategy in the Clerk Dashboard (Social Connections). `openSignIn()` MUST present Google as the first / one-tap option, not an item buried behind an email form. Email/password MAY remain enabled as a secondary method, but MUST NOT be the default path a new user is pushed toward.

### Scenario: default entry point is Google

- GIVEN a signed-out user opens the sign-in flow via `openSignIn()`
- WHEN Clerk renders the sign-in UI
- THEN Google MUST be the primary, default option presented
- AND the user MUST be able to complete sign-in/sign-up with only a Google account, no separate password step

### Scenario: Google identity still syncs to the domain user

- GIVEN a user signs in with Google for the first time
- WHEN Clerk creates the underlying identity and fires `user.created`
- THEN the `users` row MUST be created exactly as for any other Clerk sign-in method (`clerkId`, `email`, `name`, `imageUrl` from the Google profile)
- AND no module outside `packages/auth` MUST know the sign-in method was Google specifically

## Requirement: single approved second auth

### Scenario: reject a competing provider

- GIVEN `packages/auth` already provides identity
- WHEN an agent adds Supabase Auth, Firebase Auth, or a second OAuth flow outside `packages/auth`
- THEN that change MUST be rejected as out of scope
