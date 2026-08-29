# Proposal: repository becomes the general cemetery entity

## Why

Two supplied UI references (`Publica.html`, repository detail; `Admin.html`, import/upload) show real product surface not yet in `specs/repository.md`:

- A **Stack** field on the detail screen, not in the current field list.
- **Artifacts** and **Lineage** shown as structured lists (autopsy text, Figma link, hidden repo; who/what/when), while the spec only gestures at them.
- A **"Mis recordatorios"** nav item: a signed-in owner needs to see *all* their own repositories (including private ones), not just what a public visitor can see.
- `Admin.html`'s "Subir contenido" tab buries non-GitHub content (Post/Video/Evento/Otro). This directly contradicts the current requirement that "the cemetery entity is a repository (one GitHub repo), not a generic idea row" — that line is superseded here.

## Supersedes

`specs/repository.md`, requirement "identity and status": `githubFullName` was implicitly required for every repository. It becomes conditional — required only when `origin: github` — and `origin`/`contentType` (already reserved in `packages/domain`) become active fields.

## In scope

- `origin` and `contentType` fields on `repository`, sourced from `packages/domain`
- `githubFullName` optional, `githubSyncedAt` meaningful only for `origin: github`
- `stack` field
- explicit `Artifact` and `LineageEntry` shapes (defined in `packages/domain`, referenced here — see `manual-entry-module` change)
- owner-scoped listing ("my repositories", public and private) for the reminders view

## Out of scope

- The manual-entry module itself (`packages/manual-entry`) — separate change
- Reactions, notifications — separate changes
- Any live connector beyond GitHub

## Success

A repository document can represent either a GitHub-backed entry or a manually buried one, with the same lifecycle (clocks, status, lineage), and an owner can always find everything they buried regardless of visibility.
