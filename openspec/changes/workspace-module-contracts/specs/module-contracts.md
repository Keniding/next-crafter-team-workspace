# Spec: module layout

## Requirement: named MVP modules

The MVP MUST use these folders, each with `CONTRACT.md`:

| Name | Folder |
| --- | --- |
| convex | `convex/` |
| mobile | `apps/mobile/` |
| domain | `packages/domain/` |
| github | `packages/github/` |
| user | `packages/user/` |
| repository | `packages/repository/` |

YouTube, Luma, Zernio, TikTok, Instagram, and LinkedIn MUST NOT be added in this change.

### Scenario: teammate opens a module

- GIVEN a clone with this change
- WHEN they open one of the six folders
- THEN they MUST find `CONTRACT.md` covering purpose, in/out of scope, public API, auth, failure modes, and forbidden imports

## Requirement: dependency direction

`apps/mobile` MUST depend only on Convex client APIs. `convex` MAY import `packages/github`, `packages/domain`, `packages/user`, and `packages/repository`. Connector packages MUST NOT import `apps/**`.

### Scenario: forbidden import

- GIVEN an agent implementing `apps/mobile`
- WHEN a feature seems to need GitHub tokens
- THEN the agent MUST NOT import `packages/github`
- AND MUST call a Convex query/mutation instead
