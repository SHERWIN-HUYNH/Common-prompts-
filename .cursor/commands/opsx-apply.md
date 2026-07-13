---
name: /opsx-apply
id: opsx-apply
category: Workflow
description: Implement tasks from an OpenSpec change (Experimental)
---

Implement tasks from an OpenSpec change.

**Input**: Optionally specify a change name (e.g., `/opsx:apply add-auth`). If omitted, check if it can be inferred from conversation context. If vague or ambiguous you MUST prompt for available changes.

**Steps**

1. **Select the change**

   If a name is provided, use it. Otherwise:
   - Infer from conversation context if the user mentioned a change
   - Auto-select if only one active change exists
   - If ambiguous, run `openspec list --json` to get available changes and use the **AskUserQuestion tool** to let the user select

   Always announce: "Using change: <name>" and how to override (e.g., `/opsx:apply <other>`).

2. **Check status to understand the schema**
   ```bash
   openspec status --change "<name>" --json
   ```
   Parse the JSON to understand:
   - `schemaName`: The workflow being used (e.g., "spec-driven")
   - Which artifact contains the tasks (typically "tasks" for spec-driven, check status for others)

3. **Get apply instructions**

   ```bash
   openspec instructions apply --change "<name>" --json
   ```

   This returns:
   - `contextFiles`: artifact ID -> array of concrete file paths (varies by schema)
   - Progress (total, complete, remaining)
   - Task list with status
   - Dynamic instruction based on current state

   **Handle states:**
   - If `state: "blocked"` (missing artifacts): show message, suggest using `/opsx:continue`
   - If `state: "all_done"`: congratulate, suggest archive
   - Otherwise: proceed to implementation

4. **Read context (tiered — minimize tokens)**

   **Always read:**
   - Pending items from `tasks.md` (or only unchecked lines via grep)
   - `openspec/changes/<name>/beads-map.md` if it exists (phase ↔ Beads ID mapping)
   - `bd ready` output and `bd show <id>` for the task you are implementing

   **Read when needed (not every session):**
   - Relevant `specs/<capability>/spec.md` section for the current task only
   - `adr/` Interview soundbite + Decision for architecture questions (prefer over full `design.md`)
   - `design.md` only if task is ambiguous after Beads description + ADR

   **Do NOT re-read every apply session:**
   - Full `proposal.md` unless scope is unclear
   - Entire `design.md` if ADRs cover the decision (see `adr/README.md`)
   - All spec files when only one capability is touched

   Use `contextFiles` from CLI output to discover file paths. **Tiered reading rules take precedence over `contextFiles` when they conflict** — use `contextFiles` only to locate paths, not to override the read/skip decisions above.

5. **Show current progress**

   Display:
   - Schema being used
   - Progress: "N/M tasks complete"
   - Remaining tasks overview
   - Dynamic instruction from CLI

6. **Implement tasks (loop until done or blocked)**

   **Work from Beads, not tasks.md alone:**
   - Pick the next task from `bd ready` (claim with `bd update <id> --claim` if needed)
   - Implement the code changes
   - `bd close <id>` then sync matching checkbox in `tasks.md`: `- [ ]` → `- [x]` for all checkboxes in that phase (see `beads-map.md`)
   - Before `bd close`: verify all non-optional checkboxes in the phase are `[x]`
   - Continue to next `bd ready` task

   For each pending task:
   - Show which Beads task ID and title you are working on
   - Make minimal, focused code changes
   - Sync tasks.md after `bd close`

   **Pause if:**
   - Task is unclear → ask for clarification
   - Implementation reveals a design issue → suggest updating artifacts
   - Error or blocker encountered → report and wait for guidance
   - User interrupts

7. **On completion or pause, show status**

   Display:
   - Tasks completed this session
   - Overall progress: "N/M tasks complete"
   - If all done: suggest archive
   - If paused: explain why and wait for guidance

**Output During Implementation**

```
## Implementing: <change-name> (schema: <schema-name>)

Working on task 3/7: <task description>
[...implementation happening...]
✓ Task complete

Working on task 4/7: <task description>
[...implementation happening...]
✓ Task complete
```

**Output On Completion**

```
## Implementation Complete

**Change:** <change-name>
**Schema:** <schema-name>
**Progress:** 7/7 tasks complete ✓

### Completed This Session
- [x] Task 1
- [x] Task 2
...

All tasks complete! You can archive this change with `/opsx:archive`.
```

**Output On Pause (Issue Encountered)**

```
## Implementation Paused

**Change:** <change-name>
**Schema:** <schema-name>
**Progress:** 4/7 tasks complete

### Issue Encountered
<description of the issue>

**Options:**
1. <option 1>
2. <option 2>
3. Other approach

What would you like to do?
```

**Guardrails**
- Keep going through tasks until done or blocked
- Beads owns execution order; sync tasks.md after each `bd close`
- Use tiered context reading (step 4) — do not load full design + all specs every session
- If task is ambiguous, pause and ask before implementing
- If implementation reveals issues, pause and suggest artifact updates
- Keep code changes minimal and scoped to each task
- Update task checkbox immediately after completing each task
- Pause on errors, blockers, or unclear requirements - don't guess
- Use contextFiles from CLI output, don't assume specific file names

**Fluid Workflow Integration**

This skill supports the "actions on a change" model:

- **Can be invoked anytime**: Before all artifacts are done (if tasks exist), after partial implementation, interleaved with other actions
- **Allows artifact updates**: If implementation reveals design issues, suggest updating artifacts - not phase-locked, work fluidly
