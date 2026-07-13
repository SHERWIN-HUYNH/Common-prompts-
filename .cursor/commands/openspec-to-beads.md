---
name: /openspec-to-beads
id: openspec-to-beads
category: OpenSpec
description: Use this command after an OpenSpec change is approved (user said Go!).
---

## What to do

1. Ask the user (if not provided) which OpenSpec change ID to use.
2. Read:
   - `openspec/changes/<id>/proposal.md`
   - `openspec/changes/<id>/tasks.md`
   - any `openspec/changes/<id>/specs/**/spec.md`
3. Based on these files:
   - Create a Beads epic: `bd create --title="Implement <feature-name>" --type=epic --priority=1 --description="Epic for <id> OpenSpec. See openspec/changes/<id>/."`
   - For each phase in `tasks.md` (`##` headings), create a child task with mapped checkbox range in description.
   - Link child tasks to epic (parent-child dependency per `bd` conventions).
   - Add phase dependencies: `bd dep add <child-id> <depends-on-id>` (infra → backend → API → tests).
4. Write `openspec/changes/<id>/beads-map.md` with a table: Beads ID | Section | tasks.md range.
5. Run `bd ready` and summarize ready tasks.
6. Print epic ID, task IDs, dependency graph, and path to `beads-map.md`.

## Sync rule (mandatory during implementation)

- **Beads = source of truth** for progress.
- On `bd close <id>`: update matching `- [ ]` → `- [x]` in `openspec/changes/<id>/tasks.md` for all checkboxes in that phase (see `beads-map.md`).
- Do not tick tasks.md without closing the corresponding Beads task (or document why tasks are 1:many).
- Before `bd close`, verify all non-optional checkboxes in that phase are `[x]` (optional items like "4.6 (Optional)" may stay open).

## After this command

Tell the user: run `/opsx:apply <id>` to implement from `bd ready`.
