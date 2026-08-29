# Spec: user (MODIFIED — supersedes `workspace-module-contracts/specs/user.md`)

## MODIFIED Requirement: user is identity, not a login product

`packages/user` MUST define types for the cemetery actor. `UserId` MUST be the Convex `users` document id produced by `packages/auth` from a Clerk session — this package still MUST NOT implement OAuth UI, store GitHub tokens, or store Clerk secrets itself.

A user MUST include `UserId`, `githubLogin` (optional until a GitHub App installation is connected), and `displayName`.

### Scenario: bury attribution (unchanged)

- GIVEN an authenticated identity resolved via `packages/auth`
- WHEN that person buries a repository
- THEN the repository `ownerUserId` MUST equal that user's `UserId`
- AND lineage MUST record that user as the burier

### REMOVED Scenario: no second auth

The scenario "no second auth" from `workspace-module-contracts/specs/user.md`, which rejected Clerk outright, is removed. See `specs/auth.md`'s "single approved second auth" requirement in this change for its replacement.

### Scenario: identity source is exactly one bridge

- GIVEN `packages/user`
- WHEN an agent adds a second identity bridge (a module other than `packages/auth` that resolves a `UserId`)
- THEN that change MUST be rejected as out of scope
- AND `packages/auth` MUST remain the only source of `UserId`
