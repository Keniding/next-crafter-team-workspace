# Spec: cross-module flows (new)

Entities touched across `packages/auth`, `packages/github`, `packages/user`, `packages/repository`:

| Entity | Owning module | Key fields |
| --- | --- | --- |
| `users` | `packages/auth` / `packages/user` | `clerkId`, `email`, `name`, `imageUrl` |
| `github_installations` | `packages/github` | `userId`, `installationId`, `accountLogin`, `repositorySelection`, `status` |
| `github_repositories` | `packages/github` | `installationId`, `repoId`, `fullName`, `private`, `defaultBranch`, `lastPushAt` |
| `DomainCandidate` | `packages/domain` | `origin: github`, `bucket: unpublished_draft` (unchanged) |
| `repository` | `packages/repository` | `ownerUserId`, `githubFullName`, `status`, `statusUpdatedAt`, `githubSyncedAt` (unchanged) |

## Requirement: onboarding does not require GitHub

A visitor MUST be able to sign up and browse via `packages/auth` alone. Connecting GitHub MUST be a separate, later action, never a forced step of sign up.

### Scenario: sign up without connecting GitHub

- GIVEN a new user completes Clerk sign up
- WHEN they land in the app without connecting GitHub
- THEN they MUST be able to browse public repositories
- AND MUST NOT be blocked from using the app until they connect GitHub

## Requirement: connect GitHub resolves to the signed-in user

### Scenario: connect GitHub

- GIVEN a signed-in user (resolved via `packages/auth`)
- WHEN they trigger `githubConnector.connect()` from any business screen
- THEN the screen MUST NOT talk to `WebBrowser`, deep links, or GitHub directly
- AND the wrapper MUST resolve to `{ status: "success" | "cancelled" | "error" }`
- AND on success, `github_installations` MUST be linked to that same `UserId`, never a different one

## Requirement: import (resurrect) creates a repository from a candidate

### Scenario: resurrect a repo

- GIVEN `github.listRepositories` returns installation repos for a connected user
- WHEN the user picks one to bury/resurrect
- THEN Convex MUST create a `repository` document per `specs/repository.md` (owner = current `UserId`, `githubSyncedAt = now`)
- AND `whyItDied` / `whatYouLearned` MUST start empty, never invented

## Requirement: disconnecting GitHub does not delete cemetery data

### Scenario: disconnect

- GIVEN a user calls `github.disconnect`
- WHEN the installation is revoked
- THEN existing `repository` documents MUST remain (status, autopsy, lineage untouched)
- AND `repository.githubSyncedAt` MUST simply stop advancing until reconnected

## Requirement: identity loss is handled explicitly

### Scenario: Clerk account deleted

- GIVEN a user deletes their account in Clerk
- WHEN the `user.deleted` webhook reaches Convex
- THEN Convex MUST either anonymize or delete the `users` row per a decision recorded before implementation (not silently either way)
- AND `repository` documents that user owned MUST NOT be silently deleted as a side effect

### Scenario: sign-out clears session, not data

- GIVEN a signed-in user with a connected GitHub installation
- WHEN they sign out via `useBusinessAuth().signOut()`
- THEN the GitHub installation and repository data MUST persist for their next sign-in
- AND `apps/mobile` MUST NOT clear any Convex-stored data on sign-out
