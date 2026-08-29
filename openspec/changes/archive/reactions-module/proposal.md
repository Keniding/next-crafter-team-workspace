# Proposal: typed reactions

## Why

`Publica.html`'s detail screen shows three distinct reaction kinds with independent counts ("🕯️ RIP", "Yo también lo intenté", "Esto tiene que existir"), not the single `reactionCount` number the baseline `repository.md` implies. This needs its own entity and toggle behavior.

## In scope

- New module `packages/reactions`: per-user, per-kind toggle on a repository
- Keeps `repository.reactionCount` (baseline field, unchanged shape) as a denormalized total this module maintains

## Out of scope

- Comments/replies (not in either mock)
- Reaction kinds beyond the three shown; adding a new kind is a future, separate change

## Success

A signed-in user can toggle any of the three reaction kinds on a repository; counts are accurate and visible to anonymous visitors on public repositories.
