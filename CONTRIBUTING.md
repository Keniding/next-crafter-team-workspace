# Contributing

This repo is spec-driven (OpenSpec). Read `openspec/specs/module-contracts.md` first — it defines the module layout and, since this change, the collaboration rule this file makes concrete in git terms.

## Baseline vs. changes

- `openspec/specs/` is the current, merged truth. Read it before you start.
- `openspec/changes/<change-id>/` is a proposal in flight. `openspec/changes/archive/` holds finished ones for history.
- Do not edit `openspec/specs/` directly for a real behavior change — open a change folder, get it merged, then it gets folded into the baseline.

## One module, one branch, one PR

Each module is a folder: `packages/auth`, `packages/github`, `packages/user`, `packages/repository`, `packages/domain`, `convex`, `apps/mobile`. Each has its own `CONTRACT.md`.

When you pick up work on a module:

1. Branch from `main`: `git checkout -b <module>/<short-slug>` (e.g. `github/repo-picker-ux`).
2. If it changes behavior, open `openspec/changes/<module>-<short-slug>/` with your own `proposal.md`, `tasks.md`, and only the `specs/<module>.md` (or `specs/flows.md` entries) you're actually touching. Do not copy other modules' spec files into your change folder.
3. Touch only:
   - `packages/<your-module>/**` (or `convex/**`, `apps/mobile/**` if that's your module)
   - `openspec/changes/<your-change-id>/**`
   - the matching `openspec/specs/<your-module>.md`, once your change is ready to fold into baseline
4. Rebase on `main` before you push (`git pull --rebase origin main`). Keep the branch short-lived — the longer it lives, the more likely `main` moved under it.
5. Open a PR scoped to those files. A PR that touches another module's `CONTRACT.md` or spec file without that module owner's sign-off should be split.

This is what keeps five people shipping in parallel without merge conflicts: nobody's diff overlaps anybody else's, because the folder layout and the one-change-per-module rule make the overlap impossible by construction, not by discipline alone.

## Shared / cross-cutting files

A few files aren't owned by one module: `openspec/specs/module-contracts.md` (the module table and dependency diagram) and `openspec/specs/flows.md` (cross-module scenarios). If your work genuinely needs to touch one of these:

- Keep that edit minimal and in its own commit.
- Merge it fast — don't let it sit open while you do unrelated work in the same branch, since every other in-flight module branch may also need to touch it.
- If two people need to touch the same shared file at once, coordinate directly rather than both pushing; these files are the one place git conflicts are still possible.

## Archiving a change

Once a change's tasks are done and merged to `main`:

1. Fold its `specs/*.md` deltas into the matching file under `openspec/specs/`.
2. Move the change folder into `openspec/changes/archive/`.
3. Leave it there as history — do not delete it, and do not re-open an archived change; start a new one instead.
