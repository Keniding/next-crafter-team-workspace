# Spec: user

## Requirement: user is identity, not a login product

`packages/user` MUST define types for the cemetery actor. Authentication MUST be Convex identity. This module MUST NOT implement OAuth or store GitHub tokens.

A user MUST include `UserId`, `githubLogin`, and `displayName`.

### Scenario: bury attribution

- GIVEN an authenticated Convex identity
- WHEN that person buries a repository
- THEN the repository `ownerUserId` MUST equal that user's `UserId`
- AND lineage MUST record that user as the burier

### Scenario: no second auth

- GIVEN `packages/user`
- WHEN an agent adds Clerk, Supabase Auth, or client-side GitHub OAuth
- THEN that change MUST be rejected as out of scope
