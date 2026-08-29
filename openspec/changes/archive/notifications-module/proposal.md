# Proposal: notify the original owner on claim/revive

## Why

`Publica.html`'s revive CTA copy is explicit: *"El creador original recibe una notificación y queda registrado como el punto de partida."* No spec currently covers this — it's a real, small module.

## In scope

- New module `packages/notifications`: in-app notification records, triggered by the revive/claim flow

## Out of scope

- Push notifications, email, SMS — reserved for later, not this change
- Any notification kind beyond `repository_claimed` in this change

## Success

When a repository is revived, its previous owner gets an in-app notification recording who revived it.
