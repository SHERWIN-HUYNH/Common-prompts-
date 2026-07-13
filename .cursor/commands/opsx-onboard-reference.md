# OpenSpec command reference



Quick lookup for `/opsx:*` commands. Used by onboarding and graceful-exit flows.



## Golden path (OpenSpec → Beads → Code)



```

/opsx:propose <name>     → create artifacts

review artifacts         → user says "Go!"

/openspec-to-beads <name> → Beads epic + phased tasks + beads-map.md

/opsx:apply <name>       → implement from bd ready

/opsx:verify <name>      → verify before archive

/opsx:archive <name>     → archive change

```



## Core workflow



| Command | What it does |

|---------|--------------|

| `/opsx:propose` | Create a change and generate all artifacts |

| `/openspec-to-beads` | After **Go!** — convert change to Beads epic + tasks |

| `/opsx:apply` | Implement from `bd ready` (sync tasks.md on `bd close`) |

| `/opsx:archive` | Archive a completed change |



## Additional commands



| Command | What it does |

|---------|--------------|

| `/opsx:explore` | Think through problems before/during work |

| `/opsx:new` | Start a new change, step through artifacts one at a time |

| `/opsx:continue` | Continue working on an existing change |

| `/opsx:ff` | Fast-forward: create all artifacts at once |

| `/opsx:verify` | Verify implementation matches artifacts |

| `/opsx:sync` | Sync delta specs to main specs |



## Quick reference (with name argument)



| Command | What it does |

|---------|--------------|

| `/opsx:propose <name>` | Create a change and generate all artifacts |

| `/openspec-to-beads <name>` | Create Beads epic + tasks (after **Go!**) |

| `/opsx:explore` | Think through problems (no code changes) |

| `/opsx:apply <name>` | Implement from Beads |

| `/opsx:archive <name>` | Archive when done |

| `/opsx:new <name>` | Start a new change, step by step |

| `/opsx:continue <name>` | Continue an existing change |

| `/opsx:ff <name>` | Fast-forward: all artifacts at once |

| `/opsx:verify <name>` | Verify implementation |

