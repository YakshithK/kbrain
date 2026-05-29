---
name: project-overview
description: What kbrain is, its purpose, architecture, and the three core commands
metadata:
  type: project
---

KBrain is a Claude Code plugin — a personal knowledge OS and resume builder. It turns passive note-taking into an interview-driven workflow that produces structured Obsidian notes, audience-specific resume bullets, and dated session logs.

**Why:** Most second-brain tools require the user to write notes manually. kbrain interviews the user, structures the output, and produces artifacts automatically.

**How to apply:** All work in this repo is plugin development — changes affect Yakshith's own daily workflow. Test commands against the real vault and real resume.tex before considering anything done.

## Three commands

| Command | File | Purpose |
|---|---|---|
| `/kbrain-context` | `commands/kbrain-context.md` | Session debrief → session note + project note sync + Claude memory update + resume scan |
| `/kbrain-res <project>` | `commands/kbrain-res.md` | Structured interview → project note → audience-specific resume bullets → optional LaTeX patch + PDF compile |
| `/kbrain-query <question>` | `commands/kbrain-query.md` | Full-text vault search → synthesized answer with file citations |

## Config system

Commands read `~/.claude/kbrain.local.md` for:
- `vault_path` — Obsidian vault root
- `resume_tex` — path to LaTeX resume source

Template lives at `kbrain.local.example.md`. Users copy it to `~/.claude/kbrain.local.md`.

## Vault structure expected

```
vault/
├── Projects/       — one note per project, structured frontmatter schema
├── Sessions/       — YYYY-MM-DD-slug.md
├── Me/             — Profile.md and identity notes
├── Applications/   — university application notes
├── Fellowships/    — fellowship target notes
├── Bullets/        — saved resume bullets by audience
├── Ideas.md
├── To-Do.md
└── Commitments.md
```

## Plugin manifest

`.claude-plugin/plugin.json` — name: kbrain, version 1.0.0, author Yakshith Kommineni, repo https://github.com/YakshithK/kbrain

## Install path

Users clone to `~/.claude/plugins/kbrain`. The commands auto-register as `/kbrain-context`, `/kbrain-res`, `/kbrain-query`.
