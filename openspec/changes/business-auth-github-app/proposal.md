# Proposal: Clerk business auth + GitHub App connector

## Why

Product decision (owner, 2026-08-29): **Clerk is the business authentication provider, final.** GitHub connectivity uses a **GitHub App** (installation model, like Vercel/Netlify), not a raw OAuth user token, so a user can pick specific repos and the backend never holds a long-lived user token.

This directly conflicts with two requirements already approved in `openspec/changes/workspace-module-contracts`:

- `specs/user.md` — *"no second auth"* scenario explicitly rejects Clerk.
- `specs/github.md` — connector is a plain `listCandidates(userAccessToken)` OAuth call, not an installation-based GitHub App.

Both conflicting requirements were never archived to `openspec/specs/` (the baseline is still empty), but their `CONTRACT.md` files are already on `main` and five people build against them. This change formally supersedes those two requirements instead of silently editing approved specs.

## Supersedes

| Prior requirement | File | Disposition |
| --- | --- | --- |
| "user is identity, not a login product" → *no second auth* scenario | `openspec/changes/workspace-module-contracts/specs/user.md` | **REJECTED.** Clerk is now the sanctioned identity/session provider, bridged through the new `packages/auth` module only. |
| "GitHub is the only connector" → `listCandidates(userAccessToken)` | `openspec/changes/workspace-module-contracts/specs/github.md` | **REPLACED.** Connector becomes installation-based (GitHub App): `startConnection`, `getConnectionStatus`, `listRepositories`, `disconnect`. |

The rejected scenario stays in `workspace-module-contracts` for history. It MUST NOT be re-applied while this change is active — two identity systems (Convex-raw + Clerk) MUST NOT coexist at runtime.

## In scope

- New module `packages/auth`: Clerk ↔ Convex identity bridge (`requireUser(ctx)`, `auth.getCurrentUser`) and an Expo session wrapper (`useBusinessAuth()`). Sole module allowed to import Clerk SDK/webhook types.
- `packages/github` revised to the GitHub App installation model: setup redirect through a Convex `httpAction`, installation webhook, short-lived installation tokens, agnostic query/mutation API (`getConnectionStatus`, `listRepositories`, `startConnection`, `disconnect`).
- `packages/user` revised: stays domain-types-only, but its `UserId` now resolves through `packages/auth`, not a raw Convex identity claim.
- `module-contracts` spec updated: add `packages/auth`, update the dependency diagram.
- Cross-module flow spec (`flows.md`) covering sign up, connect GitHub, import/resurrect a repo, disconnect, session revocation, account deletion.
- `CONTRACT.md` updates for `packages/auth` (new), `packages/github`, `packages/user`, `apps/mobile`, `convex`.

## Out of scope

- Roles/permissions, Clerk Billing.
- Any connector besides GitHub (YouTube, Luma, Zernio, TikTok, Instagram, LinkedIn) — unchanged from `workspace-module-contracts`.
- Scaffolding a real Convex/Expo runtime, real Clerk app, or real GitHub App registration — still documentation only, per `openspec/project-context.md`.

## Rollback

`packages/auth` is additive and import-isolated (only it touches Clerk). If Clerk is dropped later, revert this change's specs and `CONTRACT.md` edits; no other module references Clerk directly, so the blast radius is contained to `packages/auth` + the two `httpAction` routes it owns.

## Success

A teammate can implement business auth (Clerk) and GitHub connectivity (GitHub App) using only the agnostic contracts (`useBusinessAuth()`, `requireUser(ctx)`, `githubConnector`, `github.*` Convex functions) — without importing Clerk or Octokit/GitHub SDK types outside `packages/auth` / `packages/github` / the two `convex` `httpAction`s that own them.
