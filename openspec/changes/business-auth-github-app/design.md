# Design: Clerk business auth + GitHub App connector

## Modules (supersedes the table in `workspace-module-contracts/specs/module-contracts.md`)

| Name | Folder | Status |
| --- | --- | --- |
| convex | `convex/` | unchanged folder, new routes/actions |
| mobile | `apps/mobile/` | unchanged folder, mounts auth wrapper |
| domain | `packages/domain/` | unchanged |
| **auth** | `packages/auth/` | **new** |
| github | `packages/github/` | revised: OAuth token → GitHub App installation |
| user | `packages/user/` | revised: identity now sourced via `packages/auth` |
| repository | `packages/repository/` | unchanged |

## Dependency direction

```text
apps/mobile → convex
apps/mobile → packages/auth        (session wrapper + Clerk provider mount only)
convex      → packages/auth        (requireUser bridge, Clerk webhook handling)
convex      → packages/github      (installation tokens, webhook handling)
convex      → packages/repository → packages/user
convex      → packages/domain
packages/auth → packages/user      (resolves a Clerk session to a UserId)
```

`packages/github` and `packages/repository` MUST NOT import `packages/auth` directly — they receive an already-resolved `userId` as an argument from the calling Convex function, exactly like the prior `packages/user` boundary rule. Only `convex/` composes `packages/auth` with the other packages.

## Identity flow (Clerk → Convex → domain `UserId`)

```text
[Expo App]
   └── <ClerkProvider tokenCache=expo-secure-store>
         └── <ConvexProviderWithClerk client useAuth={useAuth}>
               └── app screens call useBusinessAuth() — never Clerk hooks directly

[Clerk Dashboard] — JWT Template "convex" exposes Issuer URL

[Convex] convex/auth.config.ts → providers: [{ domain: CLERK_JWT_ISSUER_DOMAIN, applicationID: "convex" }]
[Convex] httpAction /clerk/webhook (user.created|updated|deleted) → upserts `users` row, keyed by clerkId
[Convex] requireUser(ctx) → Doc<"users">, used by every other module's mutations/actions
```

Convex validates the Clerk-issued JWT; it never issues or stores a password or session token itself. `packages/user`'s `UserId` is the Convex `users` document id, not the Clerk id — other modules (`repository`, `github`) keep referencing `Id<"users">` exactly as before, so `repository.md`'s ownership/lineage requirements are untouched.

**Sign-in method:** Google is enabled and set as the default Social Connection in the Clerk Dashboard; `openSignIn()` surfaces it as the primary option. Clerk's own Google OAuth credentials cover this by default in development — a production Google Cloud OAuth client (`GOOGLE_CLIENT_ID`/`GOOGLE_CLIENT_SECRET` registered in the Clerk Dashboard, not in Convex env) is only needed to remove Clerk's dev-mode branding/limits before launch.

## GitHub connection flow (GitHub App, replaces the OAuth-token model)

```text
[Expo] githubConnector.connect()
   → WebBrowser.openAuthSessionAsync(installUrl, expo-linking redirect)
   → github.com/apps/<slug>/installations/new  (user picks "All" or specific repos)
   → GitHub redirects to Convex httpAction /github/setup (installation_id, setup_action, state)
   → Convex validates installation_id against the signed-in userId, persists installation + repos,
     redirects to the app's deep link (tuapp://github/callback?status=...)
   → Expo wrapper resolves the pending promise with a typed GithubConnectResult
[GitHub] → httpAction /github/webhook (installation / installation_repositories events)
   → keeps `github_installations` / `github_repositories` in sync without the user reopening the app
```

Installation tokens are short-lived (~1h), generated from the GitHub App private key inside a Convex action, cached server-side, and never sent to `apps/mobile`. This replaces `listCandidates(userAccessToken)` from the superseded spec — the public API other modules call is unchanged in shape (agnostic query/mutation), only the underlying token model changes.

## Entities added or changed

| Entity | Module | Notes |
| --- | --- | --- |
| `users` (Convex table) | `packages/auth` + `packages/user` | keyed by `clerkId`; other modules reference `Id<"users">` only |
| `github_installations` | `packages/github` | replaces the implicit "one OAuth token per user" model |
| `github_repositories` | `packages/github` | installation-scoped repo metadata; feeds `packages/domain` candidates exactly as `listCandidates` did |
| `repository` | `packages/repository` | **unchanged** — still built from a `DomainCandidate`, still owns the two clocks (`statusUpdatedAt`, `githubSyncedAt`) |

## Clocks

Unchanged from `specs/repository.md` in `workspace-module-contracts`: `statusUpdatedAt` and `githubSyncedAt` remain distinct fields, set by the same rules.
