# Contract: `convex`

Only backend. Persistence, authz, Clerk JWT validation, GitHub App actions.

## MVP

Persist users, GitHub candidates, and autopsies. No other connectors, no other identity provider.

## In scope

- Tables for users/ideas/candidates/ownership
- Public visit vs owner-only private
- `auth.config.ts` wired to Clerk's JWT issuer; `httpAction /clerk/webhook`
- `httpAction /github/setup` and `httpAction /github/webhook` for the GitHub App installation flow
- Actions that call `packages/auth` and `packages/github` with server secrets

## Out of scope

- YouTube, Luma, Zernio
- React Native UI
- A second database
- Any auth provider besides Clerk

## Auth

Convex validates the Clerk JWT (`packages/auth`); it MUST NOT issue its own session tokens. Writes MUST check owner via `requireUser(ctx)`. GitHub installation tokens and Clerk secrets MUST NOT leak in queries.

## Forbidden imports

Expo/React Native. Outbound GitHub HTTP only from actions.

## Skills

1. `convex/_generated/ai/guidelines.md` when present
2. `.agents/skills/convex/SKILL.md`
3. `.agents/skills/convex-expert/SKILL.md` when writing functions
