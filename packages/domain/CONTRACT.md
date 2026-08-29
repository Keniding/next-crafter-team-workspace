# Contract: `packages/domain`

Shared types. Only module other packages may import for cemetery fields.

## MVP

GitHub drafts only. Other origins are reserved enums, not implemented.

## In scope

- `origin`: MVP MUST use `github`. Reserved: `youtube | instagram | tiktok | linkedin | luma | manual`
- `bucket`: MVP MUST use `unpublished_draft`. Reserved: `saved_unconsumed`
- `visibility`: `public | private`
- `contentType`: `repo | pr | other` in MVP. Reserved: `post | video | event`
- Candidate fields vs human fields: connectors fill candidates; humans write `whyItDied`, `whatYouLearned`

## Out of scope

- UI, Convex functions, HTTP, SDKs, secrets

## Public API

TypeScript types only. No I/O.

## Forbidden imports

`apps/**`, `convex/**`, `packages/github`.
