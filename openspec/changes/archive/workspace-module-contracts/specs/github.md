# Spec: github connector

## Requirement: GitHub is the only connector

`packages/github` MUST list unpublished GitHub work as candidates. It MUST NOT write autopsies or persist tokens.

Public API: `listCandidates(userAccessToken) → DomainCandidate[]` with `origin: github` and `bucket: unpublished_draft`.

### Scenario: import

- GIVEN a valid GitHub token held by a Convex action
- WHEN `listCandidates` succeeds
- THEN each item MUST include enough data to create a `repository` (`githubFullName`, title/name)
- AND `whyItDied` MUST NOT be set by this module

### Scenario: token location

- GIVEN `packages/github`
- WHEN it is called
- THEN the caller MUST be a Convex action
- AND `apps/mobile` MUST NOT hold the token
