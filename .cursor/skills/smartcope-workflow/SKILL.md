---
name: smartcope-workflow
description: Router for SmartCope OpenSpec + Beads workflow. Use when starting work, unsure which command to run, or when planning vs implementing.
---

# SmartCope workflow router

## Which command?

| User intent | Command |
|-------------|---------|
| New feature or fix (full spec) | `/opsx:propose <description>` |
| New feature (fast artifacts) | `/opsx:ff <description>` |
| Think / explore before committing | `/opsx:explore` |
| Artifacts exist, user said **Go!** | `/openspec-to-beads <change-id>` |
| Implement next task | `/opsx:apply <change-id>` or `bd ready` |
| Verify before done | `/opsx:verify <change-id>` |
| Change complete | `/opsx:archive <change-id>` |
| Continue partial artifact work | `/opsx:continue <change-id>` |
| Ad-hoc bug (no OpenSpec change) | `bd create` → `bd ready` |

## Mandatory gates

1. **Do not implement** from propose/ff output until user says **Go!**
2. **Do not `/opsx:apply`** until `/openspec-to-beads` has created the epic (unless epic already exists for that change)
3. **Do not tick `tasks.md`** without `bd close` on the matching Beads phase

## Session start checklist

1. `bd ready` — any unblocked Beads work?
2. Active change in `openspec/changes/`? Read `proposal.md` headline only
3. If Beads epic exists: read `beads-map.md` for current phase scope
4. Implement → `bd close` → sync `tasks.md`

## Deprecated

Do not use speckit (`/speckit-*`) or implement by ticking `tasks.md` alone. This project uses OpenSpec + Beads per `AGENTS.md`.
