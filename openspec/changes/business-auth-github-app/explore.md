# Exploration: business auth (Clerk) + GitHub App

## Input

Owner supplied two design docs (outside the repo, `~/Downloads/`):

- `modulo-autenticacion-negocio.md` — Clerk + Convex + Expo business auth, `useBusinessAuth()` wrapper, `users` table synced via webhook.
- `modulo-conexion-github.md` — GitHub App connector, installation model, Convex `httpAction` setup URL → Expo deep link, `githubConnector` wrapper.

Both already follow the same isolation pattern this repo's `module-contracts` spec requires: a business module talks to a stable wrapper, never to the third-party SDK directly, and the only shared key between the two is `userId`.

## Conflict found

`openspec/changes/workspace-module-contracts` (not yet archived; its `CONTRACT.md` outputs are already on `main`) explicitly forbids Clerk (`specs/user.md`, scenario "no second auth") and specs a plain-OAuth GitHub connector (`specs/github.md`), not a GitHub App.

## Resolution

Owner decision, stated directly: Clerk is final for business auth; GitHub App (not plain OAuth) for repo connection. This change (`business-auth-github-app`) supersedes the two conflicting requirements — see `proposal.md` — and adds the module/spec/contract set for the entities and flows involved: user, installation, installation repository, and the cross-module flows.

## Next

Archive this change's deltas once the team confirms, and retire the superseded scenarios from `workspace-module-contracts` history (do not delete — OpenSpec keeps prior changes for audit).
