# Spec: domain (MODIFIED — supersedes the "reserved, not implemented" status of `origin: manual`)

## MODIFIED Requirement: shared cemetery field types

`packages/domain` MUST define, as pure TypeScript types with no I/O:

- `origin`: `github | manual` are active. `youtube | instagram | tiktok | linkedin | luma` remain reserved, not implemented.
- `contentType`: `repo | post | video | event | other` are active. Connector-only types (`pr`) stay scoped to `origin: github`.
- `visibility`: `public | private` (unchanged)
- `DomainCandidate`: connector/manual-entry output before it becomes a `repository` — candidates fill machine fields, humans write `whyItDied`/`whatYouLearned` (unchanged)
- `Artifact`: `{ kind: "text" | "link" | "file"; label: string; url?: string; storageId?: Id<"_storage"> }` — `url` set for `"link"`, `storageId` set for `"file"` (a Convex-stored upload), `"text"` carries its content in `label`
- `LineageEntry`: `{ userId: Id<"users">; action: "buried" | "revived" | "noted"; note?: string; at: number }`

### Scenario: manual candidate shape

- GIVEN a user submits the "Subir contenido" form
- WHEN `packages/manual-entry` maps it to a `DomainCandidate`
- THEN `origin` MUST be `manual` and `contentType` MUST be one of `post | video | event | other` (or `repo` if they describe a repo-shaped idea that never got pushed)
- AND the candidate MUST NOT include a `githubFullName`

### Scenario: still no live connectors

- GIVEN `origin: youtube | instagram | tiktok | linkedin | luma`
- WHEN an agent implements any code that calls those services' APIs
- THEN that change MUST be rejected as out of scope
- AND `manual` MUST remain the only way non-GitHub content enters the cemetery
