# Contract: `apps/mobile`

Expo client. Talks only to Convex.

## MVP

Show GitHub-sourced candidates and autopsies. No other networks.

## In scope

- Screens via Convex React client
- Visit without account MAY be allowed
- Writing an autopsy MUST require login
- Mounting `<ClerkProvider>` + `<ConvexProviderWithClerk>` and calling `useBusinessAuth()` for sign-in/out UI (both from `packages/auth`)
- Calling `githubConnector.connect()` (from `packages/auth`'s sibling `packages/github` wrapper) to start a GitHub App installation

## Out of scope

- GitHub/YouTube/Zernio/Luma SDKs
- Clerk hooks used directly (`useAuth`, `useUser`) — only `packages/auth`'s wrapper
- API keys, GitHub tokens, or Clerk secret keys on device
- Map vs feed product decision (still unresolved; keep UI simple)

## Forbidden imports

`packages/github` internals (only its Expo wrapper), Clerk SDK types. MUST NOT import Convex internals other than generated client APIs.

## Skills

No Expo skill on current `main`.
