# Spec: repository

_Baseline, carried over unchanged from `workspace-module-contracts` (archived)._

The cemetery entity is a **repository** (one GitHub repo), not a generic "idea" row without a repo identity.

## Requirement: identity and status

A repository MUST have:

- `RepositoryId`
- `ownerUserId` (from `packages/user`)
- `githubFullName` (`login/repo`)
- `status`: `buried | haunted | revived | undead`
- `visibility`: `public | private`
- `title`, `whyItDied`, `whatYouLearned`
- `reactionCount`, `revivalCount`

### Scenario: public autopsy hides source code

- GIVEN a repository with `visibility: public` and buried status
- WHEN a visitor opens the detail screen
- THEN they MUST see autopsy fields
- AND they MUST NOT receive repository file contents until a revive/claim flow grants them

## Requirement: two clocks

A repository MUST store two timestamps and MUST NOT collapse them:

| Field | Updates when |
| --- | --- |
| `statusUpdatedAt` | cemetery status changes (bury / haunt / revive) |
| `githubSyncedAt` | GitHub candidate data is pulled |

### Scenario: sync without status change

- GIVEN a buried repository
- WHEN GitHub sync runs and status stays `buried`
- THEN `githubSyncedAt` MUST change
- AND `statusUpdatedAt` MUST stay unchanged

### Scenario: revive

- GIVEN a buried repository
- WHEN a user revives it
- THEN `status` MUST become `revived` or `undead` if `revivalCount` was already greater than zero
- AND `statusUpdatedAt` MUST change
- AND `revivalCount` MUST increment
- AND lineage MUST append the reviver from `packages/user`

## Requirement: detail screen fields

The visual contract is `apps/mobile/mocks/repository-detail.html`. Runtime UI MAY be Expo, not that HTML file. The screen MUST be able to show: breadcrumb/category, status pill, GitHub origin, title, `githubFullName`, buried age from `statusUpdatedAt`, reaction count, revival count, why it died, what was learned, artifacts, lineage, reactions, revive CTA.

### Scenario: empty human fields

- GIVEN a GitHub candidate just imported
- WHEN detail is shown before the owner writes the autopsy
- THEN `whyItDied` and `whatYouLearned` MUST be empty
- AND the UI MUST NOT invent autopsy text
