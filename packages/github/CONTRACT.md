# Contract: `packages/github`

MVP connector. GitHub unpublished-work candidates only. Connects via a **GitHub App** (installation model), not plain OAuth — see `openspec/changes/business-auth-github-app`.

## In scope

- GitHub App installation flow: setup redirect (`httpAction /github/setup`) → Expo deep link
- Installation webhook (`httpAction /github/webhook`): `installation`, `installation_repositories` events
- Draft PRs, unmerged branches, archived repos, from installation-scoped repos only
- Map to `packages/domain` candidates (`origin: github`, `bucket: unpublished_draft`)

## Out of scope

- YouTube, Luma, Zernio, TikTok, Instagram, LinkedIn
- Autopsy prose, Expo UI, device tokens
- Anything about Clerk/session identity — this module receives a `userId`, resolved elsewhere (`packages/auth`)

## Public API

```ts
github.getConnectionStatus({ userId }) → { connected, installationId?, accountLogin?, scopeType }
github.listRepositories({ userId }) → Array<{ id, fullName, private, defaultBranch, lastPushAt, isFork, isArchived }>
github.startConnection({ userId, state }) → { installUrl: string }
github.disconnect({ userId }) → { ok: boolean }
```

## Auth

GitHub App private key and webhook secret MUST stay in Convex env/actions. This package MUST NOT persist a long-lived user access token — only short-lived installation tokens, cached server-side and never sent to `apps/mobile`. `installation_id` from a setup redirect MUST be verified against the signed-in `userId` before being trusted.

## Failure modes

GitHub API errors MUST be typed. MUST NOT invent autopsies.

## Forbidden imports

`apps/**`, other connectors, UI packages, `packages/auth`. MAY import `packages/domain`.

## Skills

None extra on `main`. Convex actions that call this MUST load `.agents/skills/convex/SKILL.md`.
