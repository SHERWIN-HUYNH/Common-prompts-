---
name: openspec-to-beads
description: Convert an approved OpenSpec change into a Beads epic + child tasks with dependencies. Use after the user says "Go!" on a change.
compatibility: Requires bd CLI and an approved OpenSpec change in openspec/changes/<id>/.
---

Convert an OpenSpec change into Beads issues ready for implementation.

## Steps

1. **Identify the change ID**

   Use the change ID provided by the user (e.g., `add-conversation-history`). If not provided, run:
   ```bash
   openspec list --json
   ```
   and ask the user to select one.

2. **Read change artifacts**

   Read all of the following:
   - `openspec/changes/<id>/proposal.md` — scope and why
   - `openspec/changes/<id>/tasks.md` — implementation checklist (phases = `##` headings)
   - All `openspec/changes/<id>/specs/**/spec.md` files — behavioral contracts

3. **Create the Beads epic**

   ```bash
   bd create --title="Implement <feature-name>" --type=epic --priority=1 \
     --description="Epic for <id> OpenSpec. See openspec/changes/<id>/."
   ```

   Note the returned epic ID (e.g., `SmartCope-xxx`).

4. **Create child tasks — one per `##` phase in tasks.md**

   For each phase heading in `tasks.md`, create one Beads task:
   ```bash
   bd create --title="§N <phase title>" --type=task --priority=1 \
     --description="tasks.md §N (N.1–N.M): <summary of what the phase covers>"
   ```

   Keep descriptions concise but specific — name the key sub-tasks so the implementer knows what's in scope without re-reading tasks.md.

5. **Add dependencies**

   Wire dependencies to reflect the natural implementation order (infra → backend → API → tests):
   ```bash
   bd dep add <child-id> <depends-on-id>
   ```

   Typical order for backend features:
   - Schema/migration first (no deps)
   - History loading / refactors depend on schema
   - Core logic depends on refactors
   - Persistence depends on core logic
   - Orphan/permission layers depend on persistence
   - Endpoints depend on all above
   - Verification depends on endpoints

6. **Write beads-map.md**

   Save `openspec/changes/<id>/beads-map.md` with:

   ```markdown
   # Beads map — <id>

   | Beads ID | Section | tasks.md |
   |----------|---------|----------|
   | SmartCope-xxx | §1 Title | 1.1–1.2 |
   ```

7. **Show ready tasks**

   ```bash
   bd ready
   ```

## Sync rule (mandatory during implementation)

- **Beads = source of truth** for progress.
- On `bd close <id>`: update matching `- [ ]` → `- [x]` in `openspec/changes/<id>/tasks.md` for all checkboxes in that phase (see `beads-map.md`).
- Never tick tasks.md without closing the corresponding Beads task.
- Before `bd close`, verify all non-optional checkboxes in that phase are `[x]`.

## Output

Print:
- Epic ID
- Table: Beads ID | Section | tasks.md range
- Path to `beads-map.md`
- Dependency graph (text tree)
- Output of `bd ready`
- Reminder: run `/opsx:apply <id>` next
