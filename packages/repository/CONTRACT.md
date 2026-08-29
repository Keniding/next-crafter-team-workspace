# Contract: `packages/repository`

Cemetery entity: GitHub-backed **or** manually buried (`packages/manual-entry`). Source of the idea-detail screen.

## In scope

- Identity: `RepositoryId`, `ownerUserId`, `origin`/`contentType` (from `packages/domain`), `githubFullName` (`login/repo`) — required only when `origin: github`
- Cemetery status: `buried | haunted | revived | undead`
- Visibility: `public | private`
- Autopsy fields: `title`, `whyItDied`, `whatYouLearned`, `stack` (optional), `artifacts: Artifact[]`, `lineage: LineageEntry[]` (shapes in `packages/domain`)
- **State clocks (required):**
  - `statusUpdatedAt` — last time cemetery status changed (bury / haunt / revive)
  - `githubSyncedAt` — last time GitHub candidate state was pulled; stays `null` for `origin: manual`
- Counters: `reactionCount` (denormalized total, `packages/reactions` is the source of truth), `revivalCount`
- Lineage entries reference `packages/user`
- Owner-scoped listing: a signed-in owner MUST be able to fetch all their own repositories, public and private (the "Mis recordatorios" screen)

## Out of scope

- Calling GitHub (that is `packages/github`)
- Hand-typed / non-GitHub candidate mapping (that is `packages/manual-entry`)
- Reaction storage, notification storage (that is `packages/reactions`, `packages/notifications` — this module only keeps the denormalized `reactionCount`)
- Expo rendering (that is `apps/mobile`)
- Other networks

## Public API

Types plus a mapping from a `DomainCandidate` (GitHub or manual origin) → `Repository`, with `githubSyncedAt = now` only for `origin: github`, and `statusUpdatedAt` set only when status actually changes.

## Failure modes

Sync MUST update `githubSyncedAt` even if status does not change, for `origin: github` only. Status changes MUST bump `statusUpdatedAt`. Agents MUST NOT reuse one timestamp for both. A revive MUST trigger `packages/notifications` to notify the prior owner.

## Forbidden imports

`apps/**`. MAY import `packages/user` and `packages/domain`. MUST NOT import `packages/github`, `packages/manual-entry`, `packages/reactions`, or `packages/notifications` (convex composes them all).
