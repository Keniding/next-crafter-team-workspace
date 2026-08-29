# Contract: `packages/repository`

Cemetery entity for one GitHub repo. Source of the idea-detail screen.

## In scope

- Identity: `RepositoryId`, `ownerUserId`, `githubFullName` (`login/repo`)
- Cemetery status: `buried | haunted | revived | undead`
- Visibility: `public | private`
- Autopsy fields: `title`, `whyItDied`, `whatYouLearned`, optional artifacts
- **State clocks (required):**
  - `statusUpdatedAt` — last time cemetery status changed (bury / haunt / revive)
  - `githubSyncedAt` — last time GitHub candidate state was pulled
- Counters: `reactionCount`, `revivalCount`
- Lineage entries reference `packages/user`

## Out of scope

- Calling GitHub (that is `packages/github`)
- Expo rendering (that is `apps/mobile`)
- Other networks

## Public API

Types plus a mapping from a GitHub candidate → `Repository` with `githubSyncedAt = now` and `statusUpdatedAt` set only when status actually changes.

## Failure modes

Sync MUST update `githubSyncedAt` even if status does not change. Status changes MUST bump `statusUpdatedAt`. Agents MUST NOT reuse one timestamp for both.

## Forbidden imports

`apps/**`. MAY import `packages/user` and `packages/domain`. MUST NOT import `packages/github` (convex actions compose them).
