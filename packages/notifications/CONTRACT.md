# Contract: `packages/notifications`

In-app notification when a repository is revived/claimed by someone other than its owner.

## In scope

- One notification record per revive event, addressed to the prior owner
- Read/unread state per notification

## Out of scope

- Push notifications, email, SMS — reserved for later
- Any notification kind other than `repository_claimed` in this MVP
- Deciding *when* to notify (that's `packages/repository`'s revive flow, orchestrated by `convex`) — this module only records and serves notifications

## Public API

```ts
notifications.notifyOwner({ recipientUserId, kind: "repository_claimed", repositoryId, actorUserId })
  → { id }

notifications.listForUser({ userId })
  → Array<{ id, kind, repositoryId, actorUserId, read: boolean, createdAt: number }>

notifications.markRead({ notificationId })
  → { ok: boolean }
```

## Auth

`listForUser` and `markRead` MUST only return/affect the calling user's own notifications (resolved via `packages/auth`).

## Failure modes

A user reviving their own repository MUST NOT generate a notification to themselves. `markRead` MUST be per-notification, never bulk-affect other notifications as a side effect.

## Forbidden imports

`apps/**`, `packages/github`, `packages/reactions`, `packages/auth` internals. MAY reference `packages/repository` and `packages/user` types (`RepositoryId`, `UserId`) only.
