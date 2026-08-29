# SDD Project Context — next-crafter-team-workspace

Detected: 2026-08-29
Artifact store: openspec
HEAD: `682fc9e` on `main`
Official remote: https://github.com/Next-Crafter-Team/next-crafter-team-workspace.git

## What this repository is

Team collaboration workspace. It is **not** a running application.

Verified absences:

- No `convex/` directory
- No frontend, no `src/`, no `tsconfig`
- No tests or test runner configuration
- README is a title only (`# next-crafter-team-workspace`)

Do **not** invent an app that does not exist. Do **not** implement product code during init. Do **not** copy architecture from personal repos. Product source for later SDD phases is a Notion paste.

## Stack (declared, not implemented)

| Area | Detected | Evidence |
| --- | --- | --- |
| Backend | Convex chosen, not scaffolded | `package.json` dependency `convex` `^1.45.0`; `AGENTS.md` / `CLAUDE.md` Convex AI stubs |
| Frontend | None | No UI framework dependency, no app directory |
| Language | JavaScript package manifest only | `package.json`; no TypeScript config yet |
| Runtime install | `node_modules` not present | Working tree |

`package.json` still lists repository URL `git+https://github.com/Keniding/next-crafter-team-workspace.git`. Git `origin` is the official Next-Crafter-Team remote above.

## Current contents

- Convex agent skill copies under `.agents/skills/` (source of truth), mirrored to `.claude`, `.continue`, `.junie`, `.trae`
- `skills-lock.json` (Convex skills from `get-convex/agent-skills`)
- `.github/workflows/sync-skills.yml` (rsync `.agents/skills` into agent folders on `main`)
- `.agents/expo-skills-research.md` is research notes only; Expo is **not** a declared project stack
- `.atl/skill-registry.md` exists (local, gitignored)

## Conventions

- Technical SDD artifacts MUST be English
- Convex AI guidance in `AGENTS.md` / `CLAUDE.md` points at `convex/_generated/ai/guidelines.md`, which does not exist until Convex files are generated
- `.gitignore`: `node_modules/`, `convex/_generated/`, `.env.local`, `.atl/`
- Skill sync: `.agents/skills/` is workspace source of truth

## Testing Capabilities

**Strict TDD Mode**: disabled
**Detected**: 2026-08-29

### Test Runner

- Command: _(none)_
- Framework: none
- `npm test` is `echo "Error: no test specified" && exit 1` — placeholder, not a runner

### Test Layers

| Layer | Available | Tool |
| --- | --- | --- |
| Unit | no | — |
| Integration | no | — |
| E2E | no | — |

### Coverage

- Available: no
- Command: —

### Quality Tools

| Tool | Available | Command |
| --- | --- | --- |
| Linter | no | — |
| Type checker | no | — |
| Formatter | no | — |

Strict TDD is **false** because no real test runner exists. Enable it only after a runner is added.

## Persistence

- Mode: `openspec`
- Bootstrap: `openspec/config.yaml`, `openspec/specs/`, `openspec/changes/archive/`
- Engram was unreachable during init (`http://127.0.0.1:7437`); OpenSpec files are the source of truth

## Session defaults (from init)

- execution_mode: interactive
- delivery_strategy: ask-on-risk
- review_budget_lines: 400

## Next SDD step

`/sdd-explore` or `/sdd-new` after the Notion product paste is available. Do not scaffold Convex or a frontend until a change defines that work.
