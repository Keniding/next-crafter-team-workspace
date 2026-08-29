# Contract: `convex`

Only backend. Persistence, authz, GitHub OAuth actions.

## MVP

Persist GitHub candidates and autopsies. No other connectors.

## In scope

- Tables for ideas/candidates/ownership
- Public visit vs owner-only private
- Actions that call `packages/github` with server secrets

## Out of scope

- YouTube, Luma, Zernio
- React Native UI
- A second database

## Auth

Convex identity. Writes MUST check owner. GitHub tokens MUST NOT leak in queries.

## Forbidden imports

Expo/React Native. Outbound GitHub HTTP only from actions.

## Skills

1. `convex/_generated/ai/guidelines.md` when present
2. `.agents/skills/convex/SKILL.md`
3. `.agents/skills/convex-expert/SKILL.md` when writing functions
