# Tasks

- [x] 1.1 `specs/auth.md` — new capability spec for `packages/auth` (Clerk bridge)
- [x] 1.2 `specs/github.md` — MODIFIED delta: OAuth token → GitHub App installation model
- [x] 1.3 `specs/user.md` — MODIFIED delta: remove "no second auth"; allow Clerk only via `packages/auth`
- [x] 1.4 `specs/module-contracts.md` — MODIFIED delta: add `packages/auth`, update dependency diagram
- [x] 1.5 `specs/flows.md` — cross-module entities + Given/When/Then flows (sign up, connect, import/resurrect, disconnect, revoke, delete)
- [x] 1.6 `packages/auth/CONTRACT.md` (new), revise `packages/github/CONTRACT.md`, `packages/user/CONTRACT.md`, `apps/mobile/CONTRACT.md`, `convex/CONTRACT.md`
- [x] 1.7 Google set as the default Clerk sign-in method (`specs/auth.md`, `packages/auth/CONTRACT.md`)
- [ ] 2.1 Register real Clerk app + real GitHub App, scaffold Convex/Expo (not this change — code migrates from the existing Expo project into `apps/mobile/`, folder stays empty of source until then)
