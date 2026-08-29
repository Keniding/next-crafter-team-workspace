# Spec: reactions (new capability)

## Requirement: typed, toggleable reactions per user

`packages/reactions` MUST store one reaction row per `(repositoryId, userId, kind)`, where `kind` is `rip | tried_too | should_exist`. A user MUST be able to toggle each kind independently (react with more than one kind on the same repository).

Public API:

```ts
reactions.toggle({ repositoryId, userId, kind: "rip" | "tried_too" | "should_exist" })
  → { kind, count, active: boolean }

reactions.listForRepository({ repositoryId })
  → Array<{ kind: "rip" | "tried_too" | "should_exist", count: number }>
```

### Scenario: first reaction

- GIVEN a signed-in user has not reacted to a repository with `kind: "rip"`
- WHEN they call `toggle({ ..., kind: "rip" })`
- THEN a reaction row MUST be created
- AND the repository's `reactionCount` (baseline, total across kinds) MUST increment by 1

### Scenario: un-reacting

- GIVEN a user already reacted with `kind: "rip"`
- WHEN they call `toggle` again with the same kind
- THEN the row MUST be removed
- AND `reactionCount` MUST decrement by 1

### Scenario: anonymous visitors cannot react

- GIVEN no authenticated session (`packages/auth` has no current user)
- WHEN `toggle` is called
- THEN it MUST be rejected
- AND `listForRepository` MUST still work for anonymous visitors on public repositories, per `specs/convex-and-mobile.md`

## Forbidden imports

`apps/**`. MAY import `packages/repository` and `packages/user` as types only (a `RepositoryId` and `UserId`) — MUST NOT import their internal persistence logic.
