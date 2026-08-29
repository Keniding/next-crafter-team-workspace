# Spec: github connector

_Baseline. Last updated by `business-auth-github-app` (archived). Uses a GitHub App (installation model), not plain OAuth._

## Requirement: GitHub is the only connector, via a GitHub App

`packages/github` MUST list unpublished GitHub work as candidates, using a GitHub App installation, not a raw user OAuth token. It MUST NOT write autopsies. It MUST NOT persist a long-lived user access token anywhere, including Convex tables.

Public API:

```ts
github.getConnectionStatus({ userId })
  → { connected: boolean, installationId?: string, accountLogin?: string, scopeType: "all" | "selected" }

github.listRepositories({ userId })
  → Array<{ id, fullName, private, defaultBranch, lastPushAt, isFork, isArchived }>

github.startConnection({ userId, state })
  → { installUrl: string }

github.disconnect({ userId })
  → { ok: boolean }
```

`listRepositories` output MUST map into `packages/domain` candidates with `origin: github` and `bucket: unpublished_draft`.

### Scenario: import

- GIVEN an active installation for a `userId`
- WHEN `listRepositories` succeeds
- THEN each item MUST include enough data to create a `repository` (`githubFullName`, title/name, `defaultBranch`)
- AND `whyItDied` MUST NOT be set by this module

### Scenario: token location

- GIVEN `packages/github`
- WHEN it is called
- THEN the caller MUST be a Convex action or `httpAction`
- AND `apps/mobile` MUST NOT hold any GitHub token, including an installation token
- AND the GitHub App private key MUST live only in Convex environment variables

### Scenario: installation ownership is verified, not trusted

- GIVEN a `setup` redirect arrives at `httpAction /github/setup` with an `installation_id`
- WHEN Convex processes it
- THEN Convex MUST verify that installation belongs to the signed-in `userId` (via `packages/auth`'s `requireUser(ctx)`) before persisting it
- AND MUST NOT trust `installation_id` from the URL alone

### Scenario: revocation from GitHub

- GIVEN a user uninstalls the GitHub App directly on github.com
- WHEN the `installation.deleted` webhook reaches `httpAction /github/webhook`
- THEN Convex MUST mark that installation `revoked`
- AND MUST NOT wait for the user to reopen the app to reflect that state

### Scenario: sync without status change

- GIVEN a buried repository whose installation is still active
- WHEN the `installation_repositories` webhook or a manual refresh updates GitHub metadata
- THEN `repository.githubSyncedAt` MUST change per `specs/repository.md`
- AND `repository.statusUpdatedAt` MUST stay unchanged
