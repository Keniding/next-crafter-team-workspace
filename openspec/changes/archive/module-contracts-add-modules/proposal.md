# Proposal: register manual-entry, reactions, notifications as modules

## Why

Three new modules were just specced (`manual-entry-module`, `reactions-module`, `notifications-module`). Per `specs/module-contracts.md`'s own "one spec file, one owner" rule, the shared module table/dependency diagram gets its own small, fast-merged change rather than being edited inside any one of those three.

## In scope

- Add `packages/manual-entry`, `packages/reactions`, `packages/notifications` to the module table
- Extend the dependency diagram
- Note the new Convex file-storage usage (evidence uploads) in `convex`'s scope

## Out of scope

Everything else — this only touches the shared layout file.
