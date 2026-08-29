# Proposal: GitHub-only workspace modules

## Why

Five people implement in parallel. MVP is GitHub unpublished repos as cemetery **repositories**, owned by **users**, with explicit state clocks.

## In scope

- Modules: `convex`, `mobile`, `domain`, `github`, `user`, `repository`
- `CONTRACT.md` per module
- Specs for identity, two timestamps, public autopsy vs hidden code
- HTML mock as visual contract for repository detail

## Out of scope

- YouTube, Luma, Zernio, other networks
- Scaffolding Expo/Convex runtimes
- Live GitHub OAuth

## Success

Teammates can implement user + repository + GitHub import without inventing a second auth or mixing sync time with status time.
