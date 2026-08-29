# Spec: repository (MODIFIED — supersedes the baseline `openspec/specs/repository.md`)

## MODIFIED Requirement: identity and status

A repository MUST have:

- `RepositoryId`
- `ownerUserId` (from `packages/user`)
- `origin` and `contentType` (from `packages/domain`)
- `githubFullName` (`login/repo`) — REQUIRED when `origin: github`, MUST be absent otherwise
- `status`: `buried | haunted | revived | undead`
- `visibility`: `public | private`
- `title`, `whyItDied`, `whatYouLearned`
- `stack` (free-text tag, e.g. `Python`, `TypeScript`) — optional
- `reactionCount` (denormalized total across all reaction kinds; source of truth is `packages/reactions`)
- `revivalCount`
- `artifacts: Artifact[]` and `lineage: LineageEntry[]` (shapes defined in `packages/domain`)

### Scenario: public autopsy hides source code (unchanged)

- GIVEN a repository with `visibility: public` and buried status
- WHEN a visitor opens the detail screen
- THEN they MUST see autopsy fields
- AND they MUST NOT receive repository file contents until a revive/claim flow grants them

### Scenario: manually buried entry has no GitHub identity

- GIVEN a repository created from `packages/manual-entry` (`origin: manual`)
- WHEN it is persisted
- THEN `githubFullName` MUST be absent and `githubSyncedAt` MUST stay `null`
- AND every other field (status, visibility, autopsy, artifacts, lineage, reactions) MUST behave identically to a `github`-origin repository

## Requirement: two clocks (unchanged)

| Field | Updates when |
| --- | --- |
| `statusUpdatedAt` | cemetery status changes (bury / haunt / revive) |
| `githubSyncedAt` | GitHub candidate data is pulled — `origin: github` only |

### Scenario: sync without status change (unchanged)

- GIVEN a buried repository with `origin: github`
- WHEN GitHub sync runs and status stays `buried`
- THEN `githubSyncedAt` MUST change
- AND `statusUpdatedAt` MUST stay unchanged

### Scenario: revive (unchanged)

- GIVEN a buried repository, any origin
- WHEN a user revives it
- THEN `status` MUST become `revived` or `undead` if `revivalCount` was already greater than zero
- AND `statusUpdatedAt` MUST change
- AND `revivalCount` MUST increment
- AND `lineage` MUST append a `LineageEntry` for the reviver from `packages/user`
- AND `packages/notifications` MUST be told to notify the prior owner (see `notifications-module`)

## Requirement: owner can always find what they buried

A signed-in user MUST be able to list every repository they own, including `visibility: private` ones, regardless of `status`. This is the "Mis recordatorios" surface.

### Scenario: reminders view

- GIVEN a signed-in user with 2 public and 3 private repositories they own
- WHEN they open their own listing (not the public browse/detail queries)
- THEN Convex MUST return all 5
- AND a different, non-owner user requesting the same listing for that `ownerUserId` MUST only see the public ones

## Requirement: detail screen fields (extended)

The visual contract is `apps/mobile/mocks/repository-detail.html`. The screen MUST be able to show: breadcrumb/category, status pill, origin pill (GitHub or manual), title, `githubFullName` when present, `stack` when present, buried age from `statusUpdatedAt`, reaction counts by kind, revival count, why it died, what was learned, the `artifacts` list (each rendered per its `kind`), `lineage` (who/what/when per entry), reaction buttons, revive CTA.

### Scenario: empty human fields (unchanged)

- GIVEN a GitHub candidate just imported, or a manual entry just drafted
- WHEN detail is shown before the owner writes the autopsy
- THEN `whyItDied` and `whatYouLearned` MUST be empty
- AND the UI MUST NOT invent autopsy text
