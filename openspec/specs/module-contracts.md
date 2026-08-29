# Spec: module layout

_Baseline. Last updated by `business-auth-github-app` (archived)._

## Requirement: named MVP modules

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

YouTube, Luma, Zernio, TikTok, Instagram, and LinkedIn MUST NOT be added without a dedicated change. No identity provider other than Clerk (via `packages/auth`) MUST be added — see `specs/auth.md`.

### Scenario: teammate opens a module

- GIVEN a clone at baseline
- WHEN they open one of the module folders
- THEN they MUST find `CONTRACT.md` covering purpose, in/out of scope, public API, auth, failure modes, and forbidden imports

## Requirement: dependency direction

`apps/mobile` MUST depend only on Convex client APIs and `packages/auth`'s Expo wrapper (never Clerk hooks directly). `convex` MAY import `packages/auth`, `packages/github`, `packages/domain`, `packages/user`, and `packages/repository`. Connector and identity packages (`packages/github`, `packages/auth`) MUST NOT import `apps/**`.

### Scenario: forbidden import

- GIVEN an agent implementing `apps/mobile`
- WHEN a feature seems to need a GitHub token or a Clerk hook
- THEN the agent MUST NOT import `packages/github` or Clerk SDK types
- AND MUST call a Convex query/mutation, or `useBusinessAuth()`, instead

## Requirement: one spec file, one owner — collaborative edits MUST NOT collide in git

Modules are implemented by different people in parallel. The folder-per-module layout above already isolates code (`packages/<name>/`, `apps/mobile/`, `convex/`) so two people editing different modules never touch the same file. This requirement extends that isolation to specs and process, so an active OpenSpec change never becomes a shared bottleneck file that many people edit at once.

- A person working on module `X` MUST only edit `packages/X/CONTRACT.md` (or `convex/CONTRACT.md` / `apps/mobile/CONTRACT.md` for those two) and that module's own spec delta file (`specs/X.md`) inside their own change folder.
- A spec or behavior change to a single module MUST live in its own `openspec/changes/<module>-<short-slug>/` folder, scoped to that module's files only (its `proposal.md`, `tasks.md`, and `specs/X.md`/`specs/flows.md` entries it touches). It MUST NOT be bundled into another module's change folder.
- Cross-cutting files — the module table/dependency diagram in this spec, and any change's shared `flows.md` describing multiple modules — MUST be edited through their own small, fast-merged change, not as a side effect of one module's feature work. Whoever adds a new module or a new cross-module flow owns that specific change and keeps it short-lived.
- `openspec/specs/` (this folder) is the merged baseline. Once a module's change is done, archive it into `openspec/changes/archive/<change-id>/` and fold its accepted deltas into the matching file here, so the next person branches from a stable baseline instead of a growing shared change.

### Scenario: two modules in flight at once

- GIVEN person A opens `openspec/changes/github-app-repo-picker/` for `packages/github`
- AND person B opens `openspec/changes/business-auth-mfa/` for `packages/auth`
- WHEN both push to `main` around the same time
- THEN their diffs MUST NOT touch any of the same files
- AND neither branch requires a rebase against the other to merge cleanly

### Scenario: a change needs a second module to change too

- GIVEN implementing `packages/repository`'s revive flow also requires a small edit to `specs/flows.md` (shared)
- WHEN the repository-module owner opens their change
- THEN they MUST keep the `specs/flows.md` edit minimal and merge it quickly
- AND MUST NOT hold it open while doing unrelated repository work in the same change, to avoid blocking other modules that also touch `flows.md`

### Scenario: forbidden cross-module edit

- GIVEN an agent or teammate is implementing `packages/github`
- WHEN they are tempted to also edit `packages/auth/CONTRACT.md` or `specs/auth.md` to "make it consistent"
- THEN they MUST NOT — they open a note/issue for the `auth` owner instead, or a separate small change if it is genuinely their call to make
