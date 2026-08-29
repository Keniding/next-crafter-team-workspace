# Contract: `packages/github`

MVP connector. GitHub unpublished-work candidates only.

## In scope

- OAuth via Convex action
- Draft PRs, unmerged branches, archived repos
- Map to `packages/domain` candidates (`origin: github`, `bucket: unpublished_draft`)

## Out of scope

- YouTube, Luma, Zernio, TikTok, Instagram, LinkedIn
- Autopsy prose, Expo UI, device tokens

## Public API

`listCandidates(userAccessToken) → DomainCandidate[]`

## Auth

GitHub tokens MUST stay in Convex env/actions. This package MUST NOT persist tokens.

## Failure modes

GitHub API errors MUST be typed. MUST NOT invent autopsies.

## Forbidden imports

`apps/**`, other connectors, UI packages. MAY import `packages/domain`.

## Skills

None extra on `main`. Convex actions that call this MUST load `.agents/skills/convex/SKILL.md`.
