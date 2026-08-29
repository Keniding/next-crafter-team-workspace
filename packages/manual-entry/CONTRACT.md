# Contract: `packages/manual-entry`

Hand-typed burial for content that never lived in GitHub — a draft, an event, an idea that never became a repo.

## In scope

- Map a user-authored form (title, `contentType`, approximate creation date, `whyItDied`, `whatYouLearned`, optional evidence, `visibility`) into a `DomainCandidate` with `origin: manual`
- Evidence as `Artifact[]` references (`packages/domain`) — text, link, or a Convex-stored file

## Out of scope

- Any live call to YouTube, Instagram, TikTok, LinkedIn, Luma, or any other third-party API — every field is author-supplied, never fetched
- Storing the actual file bytes (that is a Convex file-storage action) — this module only ever holds the resulting `storageId`
- Editing/deleting a `repository` after creation (`packages/repository`)
- Reactions, notifications

## Public API

```ts
manualEntry.toCandidate(input: {
  title: string;
  contentType: "repo" | "post" | "video" | "event" | "other";
  createdAtLabel?: string;
  whyItDied?: string;
  whatYouLearned?: string;
  evidence?: Array<{ kind: "text" | "link" | "file"; label: string; url?: string; storageId?: Id<"_storage"> }>;
  visibility: "public" | "private";
}) → DomainCandidate
```

## Auth

Caller MUST be a signed-in user resolved via `packages/auth`'s `requireUser(ctx)`. `ownerUserId` on the resulting repository MUST equal that user.

## Failure modes

Missing evidence MUST map to an empty `artifacts` array, never an invented placeholder. Default `visibility` MUST be `private` when the form hasn't set one.

## Forbidden imports

`apps/**`, `packages/github`, `packages/auth` internals (receives an already-resolved `userId`). MAY import `packages/domain`.
