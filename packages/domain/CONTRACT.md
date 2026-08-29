# Contract: `packages/domain`

Shared types. Only module other packages may import for cemetery fields.

## MVP

GitHub drafts and hand-typed manual entries. Live connectors beyond GitHub are still reserved enums, not implemented.

## In scope

- `origin`: active `github | manual`. Reserved: `youtube | instagram | tiktok | linkedin | luma`
- `bucket`: MVP MUST use `unpublished_draft`. Reserved: `saved_unconsumed`
- `visibility`: `public | private`
- `contentType`: active `repo | post | video | event | other`. `pr` stays scoped to `origin: github`
- `Artifact`: `{ kind: "text" | "link" | "file"; label: string; url?: string; storageId?: Id<"_storage"> }`
- `LineageEntry`: `{ userId: Id<"users">; action: "buried" | "revived" | "noted"; note?: string; at: number }`
- Candidate fields vs human fields: connectors/`manual-entry` fill candidates; humans write `whyItDied`, `whatYouLearned`

## Out of scope

- UI, Convex functions, HTTP, SDKs, secrets
- Any live API call to youtube/instagram/tiktok/linkedin/luma — `manual` origin is hand-typed only, not a connector

## Public API

TypeScript types only. No I/O.

## Forbidden imports

`apps/**`, `convex/**`, `packages/github`.
