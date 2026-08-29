# Spec: module layout (MODIFIED — supersedes the baseline `openspec/specs/module-contracts.md`)

## MODIFIED Requirement: named MVP modules

The MVP MUST use these folders, each with `CONTRACT.md`:

| Name | Folder |
| --- | --- |
| convex | `convex/` |
| mobile | `apps/mobile/` |
| domain | `packages/domain/` |
| auth | `packages/auth/` |
| github | `packages/github/` |
| user | `packages/user/` |
| repository | `packages/repository/` |
| manual-entry | `packages/manual-entry/` |
| reactions | `packages/reactions/` |
| notifications | `packages/notifications/` |

YouTube, Luma, Zernio, TikTok, Instagram, and LinkedIn connectors (live API integrations) MUST NOT be added without a dedicated change — hand-typed content via `manual-entry` is not an exception to this.

## MODIFIED Requirement: dependency direction

```text
apps/mobile        → convex
apps/mobile        → packages/auth        (session wrapper + provider mount)
convex              → packages/auth
convex              → packages/github
convex              → packages/manual-entry → packages/domain
convex              → packages/reactions    → packages/repository, packages/user (types only)
convex              → packages/notifications → packages/repository, packages/user (types only)
convex              → packages/repository → packages/user
convex              → packages/domain
packages/auth       → packages/user
```

`packages/reactions` and `packages/notifications` MUST NOT import each other or `packages/github`/`packages/auth` internals — only `convex` composes all of them.

### Scenario: evidence upload stays in convex

- GIVEN `packages/manual-entry` needs to accept a file
- WHEN an agent implements the upload
- THEN the actual file bytes MUST go through a Convex file-storage action (`convex/CONTRACT.md`)
- AND `packages/manual-entry` MUST only ever handle the resulting `Artifact` reference, never raw file data
