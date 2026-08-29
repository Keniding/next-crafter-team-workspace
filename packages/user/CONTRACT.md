# Contract: `packages/user`

User identity for the cemetery. Domain types only — session/auth is `packages/auth`'s job, not this module's.

## In scope

- Types: `UserId` (= Convex `users` document id), `githubLogin`, `displayName`
- Owner of a `repository` (who buried it)
- Actor on lineage (bury / revive)
- `UserId` MUST come from `packages/auth` (Clerk-backed); this package MUST NOT implement login itself

## Out of scope

- Clerk SDK/session/webhook handling directly (that is `packages/auth`)
- A second identity bridge of any kind — see `openspec/changes/business-auth-github-app/specs/auth.md`
- Reactions, autopsies, GitHub API

## Public API

TypeScript types only. Persistence lives in `convex/`.

## Forbidden imports

`apps/**`, `packages/github`, Clerk SDK types. MAY be imported by `packages/domain` and `convex`. `packages/auth` produces the `UserId` this module types — `packages/user` does not import `packages/auth` back.
