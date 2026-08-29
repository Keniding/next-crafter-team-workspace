# Contract: `convex`

Only backend. Persistence, authz, Clerk JWT validation, GitHub App actions.

## MVP

Persist users, GitHub candidates, and autopsies. No other connectors, no other identity provider.

## In scope

- Tables for users/ideas/candidates/ownership/reactions/notifications
- Public visit vs owner-only private, plus an owner-scoped "my repositories" listing (reminders view)
- `auth.config.ts` wired to Clerk's JWT issuer; `httpAction /clerk/webhook`
- `httpAction /github/setup` and `httpAction /github/webhook` for the GitHub App installation flow
- File-storage upload URL (`generateUploadUrl` action) for `packages/manual-entry` evidence — Convex owns the actual bytes; `packages/manual-entry` only ever handles the resulting `storageId`
- Composing `packages/repository`'s revive flow with `packages/notifications`' `notifyOwner`
- Actions that call `packages/auth` and `packages/github` with server secrets

## Out of scope

- YouTube, Luma, Zernio, or any other live connector
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
