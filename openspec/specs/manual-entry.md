# Spec: manual entry

_Baseline. Added by `manual-entry-module` (archived)._

## Requirement: hand-typed burial for non-GitHub content

`packages/manual-entry` MUST turn a user-authored form (title, `contentType`, an approximate creation date, `whyItDied`, `whatYouLearned`, optional evidence, `visibility`) into a `DomainCandidate` with `origin: manual`. It MUST NOT call any third-party API — every field is author-supplied.

Public API:

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

### Scenario: bury manually

- GIVEN a signed-in user (resolved via `packages/auth`) fills the upload form
- WHEN they submit with `visibility: public`
- THEN Convex MUST create a `repository` per `specs/repository.md`, with `origin: manual`, `whyItDied` as the public autopsy, and `evidence` mapped to `artifacts`
- AND `ownerUserId` MUST equal the submitting user's `UserId`

### Scenario: evidence is optional

- GIVEN the form's evidence field is left empty
- WHEN the entry is buried
- THEN `artifacts` MUST be an empty array, not a placeholder or invented item

### Scenario: default visibility is private

- GIVEN a user has not explicitly chosen visibility
- WHEN the form is first rendered
- THEN the default MUST be `private`, matching the GitHub import screen's default of "solo recordatorios" unless the user opts into public

## Requirement: evidence upload uses Convex storage, not a third-party host

### Scenario: file evidence

- GIVEN a user attaches a file (screenshot, doc) as evidence
- WHEN it uploads
- THEN it MUST go through a Convex file-storage upload URL (`convex/CONTRACT.md`), producing a `storageId`
- AND `packages/manual-entry` MUST NOT accept or store a raw file itself — it only records the resulting `Artifact`

## Forbidden imports

`apps/**`, `packages/github`, `packages/auth` internals (receives an already-resolved `userId`). MAY import `packages/domain`.
