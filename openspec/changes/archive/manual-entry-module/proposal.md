# Proposal: manual burial (non-GitHub content)

## Why

`Admin.html`'s "Subir contenido" tab lets a user bury something that never lived in GitHub: a draft, an event, an idea that never became a repo. `packages/domain` already reserved `origin: manual` and `contentType: post | video | event` for exactly this, unimplemented. This activates it — hand-typed entries only, not a live connector to any third-party network.

## In scope

- New module `packages/manual-entry`: maps a user-authored form into a `DomainCandidate` (`origin: manual`)
- Activates `origin: manual` and `contentType: post | video | event | other` in `packages/domain`, plus a shared `Artifact` type for evidence (link, file, or text)
- Evidence upload backed by Convex file storage (wired in `convex/CONTRACT.md`)

## Out of scope

- Any live integration with YouTube, Instagram, TikTok, LinkedIn, Luma — still no connector, no API calls to those services
- Editing/deleting a repository after creation (that's `packages/repository`'s concern, unchanged)
- Reactions, notifications

## Success

A user can bury a non-GitHub idea from `apps/mobile`'s upload screen, attach optional evidence, and it becomes a `repository` document indistinguishable in shape from a GitHub-origin one (per `repository-cemetery-entity`).
