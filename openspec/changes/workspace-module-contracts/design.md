# Design: GitHub-only modules

## Naming

Lowercase path nouns.

| Name | Folder |
| --- | --- |
| convex | `convex/` |
| mobile | `apps/mobile/` |
| domain | `packages/domain/` |
| github | `packages/github/` |
| user | `packages/user/` |
| repository | `packages/repository/` |

Visual reference: `apps/mobile/mocks/repository-detail.html`

## Dependency direction

```text
apps/mobile → convex
convex → packages/github
convex → packages/repository → packages/user
convex → packages/domain
```

## Clocks

`statusUpdatedAt` and `githubSyncedAt` are different fields. See `specs/repository.md`.
