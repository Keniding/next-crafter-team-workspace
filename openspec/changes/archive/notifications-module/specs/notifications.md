# Spec: notifications (new capability)

## Requirement: in-app notification on repository claim/revive

`packages/notifications` MUST record a notification for a repository's previous owner whenever someone else revives it. Push/email delivery is out of scope — this is an in-app record only.

Public API:

```ts
notifications.notifyOwner({ recipientUserId, kind: "repository_claimed", repositoryId, actorUserId })
  → { id }

notifications.listForUser({ userId })
  → Array<{ id, kind, repositoryId, actorUserId, read: boolean, createdAt: number }>

notifications.markRead({ notificationId })
  → { ok: boolean }
```

### Scenario: revive triggers a notification

- GIVEN repository `R` owned by user `A`
- WHEN user `B` revives `R` (per `specs/repository.md`'s revive requirement)
- THEN Convex MUST call `notifications.notifyOwner({ recipientUserId: A, kind: "repository_claimed", repositoryId: R, actorUserId: B })`
- AND user `A` MUST see it in `listForUser`

### Scenario: reviving your own repository does not notify you

- GIVEN repository `R` owned by user `A`
- WHEN user `A` revives their own `R`
- THEN no notification MUST be created

### Scenario: read state is per-notification, not global

- GIVEN a user has 3 unread notifications
- WHEN they mark one as read
- THEN only that one's `read` MUST become `true`
- AND the other two MUST remain unread

## Forbidden imports

`apps/**`, `packages/github`, `packages/auth` internals. MAY reference `packages/repository` and `packages/user` types (`RepositoryId`, `UserId`) only.
