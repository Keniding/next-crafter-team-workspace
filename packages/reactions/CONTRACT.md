# Contract: `packages/reactions`

Typed, per-user, toggleable reactions on a repository: `rip | tried_too | should_exist`.

## In scope

- One reaction row per `(repositoryId, userId, kind)`, toggleable independently per kind
- Keeping `repository.reactionCount` (the denormalized total) in sync on every toggle

## Out of scope

- Comments/replies
- Reaction kinds beyond the three shown in the product mock — adding one is its own future change
- Anything about who the repository owner is, GitHub, or Clerk

## Public API

```ts
reactions.toggle({ repositoryId, userId, kind: "rip" | "tried_too" | "should_exist" })
  → { kind, count, active: boolean }

reactions.listForRepository({ repositoryId })
  → Array<{ kind: "rip" | "tried_too" | "should_exist", count: number }>
```

## Auth

`toggle` MUST require a signed-in user (`packages/auth`'s `requireUser(ctx)`); anonymous callers MUST be rejected. `listForRepository` MUST work for anonymous visitors on public repositories, matching `specs/convex-and-mobile.md`.

## Failure modes

Toggling the same kind twice in a row MUST flip active/inactive, never create duplicate rows.

## Forbidden imports

`apps/**`, `packages/notifications`, `packages/github`, `packages/auth` internals. MAY reference `packages/repository` and `packages/user` types (`RepositoryId`, `UserId`) only.
