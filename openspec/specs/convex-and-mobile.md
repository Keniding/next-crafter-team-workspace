# Spec: convex and mobile

_Baseline, carried over unchanged from `workspace-module-contracts` (archived)._

## Requirement: Convex persists users and repositories

`convex/` MUST be the only persistence. It MUST store `user` and `repository` documents, enforce owner checks on writes, and expose queries that never leak GitHub tokens or private autopsies to anonymous visitors.

### Scenario: private repository

- GIVEN `visibility: private`
- WHEN an anonymous visitor queries it
- THEN Convex MUST NOT return autopsy body or tokens

### Scenario: public visit

- GIVEN `visibility: public`
- WHEN an anonymous visitor queries detail
- THEN Convex MAY return autopsy fields listed in the repository spec
- AND MUST NOT return source code

## Requirement: mobile uses Convex only

`apps/mobile` MUST render repository detail using Convex data. The HTML mock is a visual reference only.

### Scenario: revive CTA

- GIVEN a signed-in user on repository detail
- WHEN they activate revive
- THEN the app MUST call a Convex mutation
- AND MUST NOT call GitHub from the device
