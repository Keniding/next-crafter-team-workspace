# Contract: `packages/user`

User identity for the cemetery. Not a second auth system.

## In scope

- Types: `UserId`, `githubLogin`, `displayName`
- Owner of a `repository` (who buried it)
- Actor on lineage (bury / revive)
- Auth identity MUST come from Convex; this package MUST NOT implement login

## Out of scope

- Clerk/Supabase, OAuth UI, storing GitHub tokens
- Reactions, autopsies, GitHub API

## Public API

TypeScript types only. Persistence lives in `convex/`.

## Forbidden imports

`apps/**`, `packages/github`. MAY be imported by `packages/domain` and `convex`.
