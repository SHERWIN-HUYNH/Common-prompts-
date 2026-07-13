---
name: openspec-onboard
description: Guided onboarding for OpenSpec - walk through a complete workflow cycle with narration and real codebase work.
license: MIT
compatibility: Requires openspec CLI.
metadata:
  author: openspec
  version: "1.0"
  generatedBy: "1.3.1"
---

Guide the user through their first complete OpenSpec workflow cycle. This is a teaching experience—you'll do real work in their codebase while explaining each step.

---

## Preflight

Before starting, check if the OpenSpec CLI is installed:

```bash
# Unix/macOS
openspec --version 2>&1 || echo "CLI_NOT_INSTALLED"
# Windows (PowerShell)
# if (Get-Command openspec -ErrorAction SilentlyContinue) { openspec --version } else { echo "CLI_NOT_INSTALLED" }
```

**If CLI not installed:**
> OpenSpec CLI is not installed. Install it first, then come back to `/opsx:onboard`.

Stop here if not installed.

---

## Phase 1: Welcome

Display:

```
## Welcome to OpenSpec!

I'll walk you through a complete change cycle—from idea to implementation—using a real task in your codebase. Along the way, you'll learn the workflow by doing it.

**What we'll do:**
1. Pick a small, real task in your codebase
2. Explore the problem briefly
3. Create a change (the container for our work)
4. Build the artifacts: proposal → specs → design → tasks
5. Implement the tasks
6. Archive the completed change

**Time:** ~15-20 minutes

Let's start by finding something to work on.
```

---

## Phase 2: Task Selection

### Codebase Analysis

Scan the codebase for small improvement opportunities. Look for:

1. **TODO/FIXME comments** - Search for `TODO`, `FIXME`, `HACK`, `XXX` in code files
2. **Missing error handling** - `catch` blocks that swallow errors, risky operations without try-catch
3. **Functions without tests** - Cross-reference `src/` with test directories
4. **Type issues** - `any` types in TypeScript files (`: any`, `as any`)
5. **Debug artifacts** - `console.log`, `console.debug`, `debugger` statements in non-debug code
6. **Missing validation** - User input handlers without validation

Also check recent git activity:
```bash
# Unix/macOS
git log --oneline -10 2>/dev/null || echo "No git history"
# Windows (PowerShell)
# git log --oneline -10 2>$null; if ($LASTEXITCODE -ne 0) { echo "No git history" }
```

### Present Suggestions

From your analysis, present 3-4 specific suggestions:

```
## Task Suggestions

Based on scanning your codebase, here are some good starter tasks:

**1. [Most promising task]**
   Location: `src/path/to/file.ts:42`
   Scope: ~1-2 files, ~20-30 lines
   Why it's good: [brief reason]

**2. [Second task]**
   Location: `src/another/file.ts`
   Scope: ~1 file, ~15 lines
   Why it's good: [brief reason]

**3. [Third task]**
   Location: [location]
   Scope: [estimate]
   Why it's good: [brief reason]

**4. Something else?**
   Tell me what you'd like to work on.

Which task interests you? (Pick a number or describe your own)
```

**If nothing found:** Fall back to asking what the user wants to build:
> I didn't find obvious quick wins in your codebase. What's something small you've been meaning to add or fix?

### Scope Guardrail

If the user picks or describes something too large (major feature, multi-day work):

```
That's a valuable task, but it's probably larger than ideal for your first OpenSpec run-through.

For learning the workflow, smaller is better—it lets you see the full cycle without getting stuck in implementation details.

**Options:**
1. **Slice it smaller** - What's the smallest useful piece of [their task]? Maybe just [specific slice]?
2. **Pick something else** - One of the other suggestions, or a different small task?
3. **Do it anyway** - If you really want to tackle this, we can. Just know it'll take longer.

What would you prefer?
```

Let the user override if they insist—this is a soft guardrail.

---

## Phase 3: Explore Demo

Once a task is selected, briefly demonstrate explore mode:

```
Before we create a change, let me quickly show you **explore mode**—it's how you think through problems before committing to a direction.
```

Spend 1-2 minutes investigating the relevant code:
- Read the file(s) involved
- Draw a quick ASCII diagram if it helps
- Note any considerations

```
## Quick Exploration

[Your brief analysis—what you found, any considerations]

┌─────────────────────────────────────────┐
│   [Optional: ASCII diagram if helpful]  │
└─────────────────────────────────────────┘

Explore mode (`/opsx:explore`) is for this kind of thinking—investigating before implementing. You can use it anytime you need to think through a problem.

Now let's create a change to hold our work.
```

**PAUSE** - Wait for user acknowledgment before proceeding.

---

## Phase 4: Create the Change

**EXPLAIN:**
```
## Creating a Change

A "change" in OpenSpec is a container for all the thinking and planning around a piece of work. It lives in `openspec/changes/<name>/` and holds your artifacts—proposal, specs, design, tasks.

Let me create one for our task.
```

**DO:** Create the change with a derived kebab-case name:
```bash
openspec new change "<derived-name>"
```

**SHOW:**
```
Created: `openspec/changes/<name>/`

The folder structure:
```
openspec/changes/<name>/
├── proposal.md    ← Why we're doing this (empty, we'll fill it)
├── design.md      ← How we'll build it (empty)
├── specs/         ← Detailed requirements (empty)
└── tasks.md       ← Implementation checklist (empty)
```

Now let's fill in the first artifact—the proposal.
```

---

## Phase 5: Proposal

**EXPLAIN:**
```
## The Proposal

The proposal captures **why** we're making this change and **what** it involves at a high level. It's the "elevator pitch" for the work.

I'll draft one based on our task.
```

**DO:** Draft the proposal content (don't save yet):

```
Here's a draft proposal:

---

## Why

[1-2 sentences explaining the problem/opportunity]

## What Changes

[Bullet points of what will be different]

## Capabilities

### New Capabilities
- `<capability-name>`: [brief description]

### Modified Capabilities
<!-- If modifying existing behavior -->

## Impact

- `src/path/to/file.ts`: [what changes]
- [other files if applicable]

---

Does this capture the intent? I can adjust before we save it.
```

**PAUSE** - Wait for user approval/feedback.

After approval, save the proposal:
```bash
openspec instructions proposal --change "<name>" --json
```
Then write the content to `openspec/changes/<name>/proposal.md`.

```
Proposal saved. This is your "why" document—you can always come back and refine it as understanding evolves.

Next up: specs.
```

---

## Phase 6: Specs

**EXPLAIN:**
```
## Specs

Specs define **what** we're building in precise, testable terms. They use a requirement/scenario format that makes expected behavior crystal clear.

For a small task like this, we might only need one spec file.
```

**DO:** Create the spec file:
```bash
# Unix/macOS
mkdir -p openspec/changes/<name>/specs/<capability-name>
# Windows (PowerShell)
# New-Item -ItemType Directory -Force -Path "openspec/changes/<name>/specs/<capability-name>"
```

Draft the spec content:

```
Here's the spec:

---

## ADDED Requirements

### Requirement: <Name>

<Description of what the system should do>

#### Scenario: <Scenario name>

- **WHEN** <trigger condition>
- **THEN** <expected outcome>
- **AND** <additional outcome if needed>

---

This format—WHEN/THEN/AND—makes requirements testable. You can literally read them as test cases.
```

Save to `openspec/changes/<name>/specs/<capability>/spec.md`.

---

## Phase 7: Design

**EXPLAIN:**
```
## Design

The design captures **how** we'll build it—technical decisions, tradeoffs, approach.

For small changes, this might be brief. That's fine—not every change needs deep design discussion.
```

**DO:** Draft design.md:

```
Here's the design:

---

## Context

[Brief context about the current state]

## Goals / Non-Goals

**Goals:**
- [What we're trying to achieve]

**Non-Goals:**
- [What's explicitly out of scope]

## Decisions

### Decision 1: [Key decision]

[Explanation of approach and rationale]

---

For a small task, this captures the key decisions without over-engineering.
```

Save to `openspec/changes/<name>/design.md`.

---

## Phase 8: Tasks

**EXPLAIN:**
```
## Tasks

Finally, we break the work into implementation tasks—checkboxes that drive the apply phase.

These should be small, clear, and in logical order.
```

**DO:** Generate tasks based on specs and design:

```
Here are the implementation tasks:

---

## 1. [Category or file]

- [ ] 1.1 [Specific task]
- [ ] 1.2 [Specific task]

## 2. Verify

- [ ] 2.1 [Verification step]

---

Each checkbox becomes a unit of work tracked via Beads during apply. Say **Go!** when ready to approve.
```

**PAUSE** - Wait for user to say **Go!** (approval gate — do not implement yet).

Save to `openspec/changes/<name>/tasks.md`.

---

## Phase 9: Beads Bridge

**EXPLAIN:**
```
## Beads Bridge

OpenSpec defines WHAT (artifacts). Beads owns HOW we execute (which task is next).

After you approve with "Go!", we convert tasks.md phases into a Beads epic + phased tasks.
```

**DO:** Run `/openspec-to-beads <name>` (or follow that skill):
- Create epic + one Beads task per `##` phase in tasks.md
- Wire dependencies between phases
- Write `openspec/changes/<name>/beads-map.md`
- Run `bd ready` and show what's unblocked

**SHOW:** Epic ID, task table, `bd ready` output.

**PAUSE** - Confirm user is ready to implement from Beads.

---

## Phase 10: Apply (Implementation)

**EXPLAIN:**
```
## Implementation

We implement from `bd ready` — not by ticking tasks.md directly.

For each Beads task: claim → implement all checkboxes in that phase → `bd close` → sync tasks.md.
```

**DO:** Run `/opsx:apply <name>` or follow the apply loop:

1. `bd ready` → pick next task → `bd update <id> --claim`
2. Announce: "Working on <Beads ID>: [title]"
3. Implement code for all checkboxes in that phase (see `beads-map.md`)
4. `bd close <id>` then sync matching `- [ ]` → `- [x]` in tasks.md
5. Repeat until no ready tasks remain

Keep narration light—don't over-explain every line of code.

After all Beads tasks closed:

```
## Implementation Complete

All phases done in Beads; tasks.md checkboxes synced.
Run `/opsx:verify <name>` then archive.
```

---

## Phase 11: Archive

**EXPLAIN:**
```
## Archiving

When a change is complete, we archive it. This moves it from `openspec/changes/` to `openspec/changes/archive/YYYY-MM-DD-<name>/`.

Archived changes become your project's decision history—you can always find them later to understand why something was built a certain way.
```

**DO:**
```bash
openspec archive "<name>"
```

**SHOW:**
```
Archived to: `openspec/changes/archive/YYYY-MM-DD-<name>/`

The change is now part of your project's history. The code is in your codebase, the decision record is preserved.
```

---

## Phase 12: Recap & Next Steps

```
## Congratulations!

You just completed a full OpenSpec + Beads cycle:

1. **Explore** - Thought through the problem
2. **New** - Created a change container
3. **Proposal** - Captured WHY
4. **Specs** - Defined WHAT in detail
5. **Design** - Decided HOW
6. **Tasks** - Broke it into steps
7. **Go!** - Approved the change
8. **Beads** - Created epic + phased tasks (`/openspec-to-beads`)
9. **Apply** - Implemented from `bd ready` (`/opsx:apply`)
10. **Archive** - Preserved the record

This same rhythm works for any size change—a small fix or a major feature.

---

## Command Reference

Read `.cursor/commands/opsx-onboard-reference.md` for the full command table when the user asks for a reference or during Phase 12 recap.

**Golden path:** `/opsx:propose` → review → **Go!** → `/openspec-to-beads` → `/opsx:apply` → `/opsx:verify` → `/opsx:archive`

**Additional:** `/opsx:explore`, `/opsx:new`, `/opsx:continue`, `/opsx:ff`

---

## What's Next?

Try `/opsx:propose` on something you actually want to build. You've got the rhythm now!
```

---

## Graceful Exit Handling

### User wants to stop mid-way

If the user says they need to stop, want to pause, or seem disengaged:

```
No problem! Your change is saved at `openspec/changes/<name>/`.

To pick up where we left off later:
- `/opsx:continue <name>` - Resume artifact creation
- After **Go!**: `/openspec-to-beads <name>` - Create Beads tasks
- `/opsx:apply <name>` - Implement from `bd ready` (requires Beads epic)

The work won't be lost. Come back whenever you're ready.
```

Exit gracefully without pressure.

### User just wants command reference

If the user says they just want to see the commands or skip the tutorial:

Read `.cursor/commands/opsx-onboard-reference.md` and show the **Quick reference** section. Then exit gracefully.

---

## Guardrails

- **Follow the EXPLAIN → DO → SHOW → PAUSE pattern** at key transitions (after explore, after proposal draft, after tasks, after archive)
- **Keep narration light** during implementation—teach without lecturing
- **Don't skip phases** even if the change is small—the goal is teaching the workflow
- **Pause for acknowledgment** at marked points, but don't over-pause
- **Handle exits gracefully**—never pressure the user to continue
- **Use real codebase tasks**—don't simulate or use fake examples
- **Adjust scope gently**—guide toward smaller tasks but respect user choice
