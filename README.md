<div align="center">

# UserThoughts.SKILL

[![Copyright](https://img.shields.io/badge/Copyright-JularDepick-0066AA)](./COPYRIGHT)
[![License](https://img.shields.io/badge/License-MIT-yellow)](./LICENSE)
[![Standard](https://img.shields.io/badge/Standard-Agent--SKILL-red)](https://agentskills.io/)

[[English]](./README.md) [[简体中文]](./README_zh-CN.md)

An [Agent Skills](https://agentskills.io/) compliant skill that enables AI agents to automatically organize user input and maintain a user idea repository (mdbase) linked to the project lifecycle.

</div>

---

## Why This Skill?

When working with AI agents on a project, users express design decisions, requirements, preferences, and constraints throughout conversations. These details are easily lost in chat history. UserThoughts.SKILL solves this by:

- **Capturing** user thoughts automatically during conversations
- **Organizing** them into a structured document library (mdbase) by dimension
- **Preserving** original user wording without simplification
- **Binding** the idea repository to the project lifecycle

---

## Installation

### Prerequisites

| Dependency | Required | Purpose |
|------------|----------|---------|
| `read` / `write` | Yes | Read/write files in `#ustht/` |
| `bash` | Yes | File operations (copy, create dirs) |
| SubAgent | No | Parallel mdbase maintenance |

### How to Install

Place the `user-thoughts/` directory in your agent's skills folder. The exact path depends on your agent:

| Agent | Skills Directory |
|-------|-----------------|
| VS Code / Copilot | `.agents/skills/user-thoughts/` |
| Claude Code | `.claude/skills/user-thoughts/` |
| OpenCode | `.opencode/skills/user-thoughts/` |
| Generic | Check your agent's documentation |

After installation, the agent discovers the skill automatically via the YAML frontmatter in `SKILL.md`.

---

## Quick Start

1. **Install** the skill (see above)
2. **Start talking** about your project — the skill activates automatically when you express project ideas
3. **Use commands** when needed:
   - `/ustht status` — check current state
   - `/ustht sortin` — organize thoughts into mdbase
   - `/ustht mdbase show` — view the idea repository

---

## How It Works

The skill operates in three stages (progressive disclosure):

1. **Discovery** — Agent reads `name` and `description` from frontmatter to decide when to activate
2. **Activation** — Agent loads `SKILL.md` body (core instructions, ~200 lines)
3. **Execution** — Agent loads `references/` files on demand for detailed specs

### Workflow

```
User speaks → Agent identifies project thought → Writes to raw/ (instant plan)
         ↓
User runs /ustht sortin → Agent processes raw/ → Appends to mdbase/ by dimension
         ↓
User runs /ustht mdbase show → Agent displays organized idea repository
```

---

## Skill Structure

```
user-thoughts/
├── SKILL.md                    # Entry point (frontmatter + core instructions)
├── references/                 # Detailed specs (loaded on demand)
│   ├── commands.md             # Command regex & natural language mapping
│   ├── sortin.md               # Maintenance algorithm & dimension management
│   ├── edge-cases.md           # Boundary scenarios & interaction examples
│   └── safety.md               # Security boundaries & data integrity
└── assets/
    └── Runtime-Template/       # Template copied to workspace on first use
        ├── define.ini
        └── mdbase/
            ├── README.ai.md
            ├── backlog.md
            └── details/...
```

---

## Commands

```
# Status & Controls
/ustht init                                # Initialize workspace (.ustht/ and templates)
/ustht status                              # Full status overview
/ustht skill [on|off]                      # View/toggle skill
/ustht instant [on|off]                    # View/toggle instant planning

# Maintenance
/ustht sortin [--dry]                      # Soft maintenance (append new thoughts), --dry preview
/ustht resort [--dry]                      # Hard maintenance (reorganize all mdbase), --dry preview

# Ignore
/ustht ignore start|end                    # Begin/end ignore interval (context only)
/ustht ignore [--last]                     # Ignore last recorded thought
/ustht ignore show                         # View ignored records
... /ustht ignore                          # Suffix mode, ignore this message

# View & Export
/ustht raw                                 # View unprocessed raw records
/ustht mdbase show [--all|--dimension]     # View mdbase index or dimension
/ustht mdbase export [--all|--dimension]   # Export mdbase to #export/
/ustht import <path>                       # Scan .md files at path, merge into mdbase
```

---

## Runtime Structure

After first use, the skill creates `.ustht/` in your working directory:

```
<working-dir>/
└── .ustht/
    ├── define.ini             # State & macro constants
    ├── raw/                   # User raw thoughts (date-sharded)
    │   └── yyyy-mm-dd.md
    ├── ignored/               # Ignored thoughts
    │   └── yyyy-mm-dd.md
    ├── export/                # Exported mdbase content
    └── mdbase/                # Organized idea repository
        ├── README.ai.md       # Index & overview
        ├── backlog.md         # Todo items
        └── details/           # Dimension-organized files
            ├── rules.md       # Project rules
            ├── plans.md       # Project plans
            ├── dev-stack.md   # Tech stack
            ├── general.md     # General (catch-all)
            ├── ui/
            │   ├── outline.md
            │   └── details.md
            └── ...            # Extensible
```

---

## License

[MIT](./LICENSE) — Copyright (c) 2026 JularDepick
