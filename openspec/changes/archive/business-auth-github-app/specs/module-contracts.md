# Spec: module layout (MODIFIED — supersedes `workspace-module-contracts/specs/module-contracts.md`)

## MODIFIED Requirement: named MVP modules

The MVP MUST use these folders, each with `CONTRACT.md`:

| Name | Folder |
| --- | --- |
| convex | `convex/` |
| mobile | `apps/mobile/` |
| domain | `packages/domain/` |
| **auth** | **`packages/auth/`** |
| github | `packages/github/` |
| user | `packages/user/` |
| repository | `packages/repository/` |

YouTube, Luma, Zernio, TikTok, Instagram, and LinkedIn MUST NOT be added in this change. No auth provider other than Clerk (via `packages/auth`) MUST be added — see `specs/auth.md`.

### Scenario: teammate opens the auth module

- GIVEN a clone with this change
- WHEN a teammate opens `packages/auth/`
- THEN they MUST find `CONTRACT.md` covering purpose, in/out of scope, public API, auth, failure modes, and forbidden imports, same as every other module

## MODIFIED Requirement: dependency direction

`apps/mobile` MUST depend only on Convex client APIs and `packages/auth`'s Expo wrapper (for mounting the provider and reading session state — never Clerk hooks directly). `convex` MAY import `packages/auth`, `packages/github`, `packages/domain`, `packages/user`, and `packages/repository`. Connector and identity packages (`packages/github`, `packages/auth`) MUST NOT import `apps/**`.

### Scenario: forbidden import (revised)

- GIVEN an agent implementing `apps/mobile`
- WHEN a feature seems to need a GitHub token or a Clerk hook
- THEN the agent MUST NOT import `packages/github` or Clerk SDK types
- AND MUST call a Convex query/mutation, or `useBusinessAuth()`, instead
