# Contract: `apps/mobile`

Expo client. Talks only to Convex.

## MVP

Show GitHub-sourced candidates and autopsies. No other networks.

## In scope

- Screens via Convex React client
- Visit without account MAY be allowed
- Writing an autopsy MUST require login

## Out of scope

- GitHub/YouTube/Zernio/Luma SDKs
- API keys or cookies on device
- Map vs feed product decision (still unresolved; keep UI simple)

## Forbidden imports

`packages/github`. MUST NOT import Convex internals other than generated client APIs.

## Skills

No Expo skill on current `main`.
