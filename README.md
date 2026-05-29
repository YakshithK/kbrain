# KBrain

Personal knowledge OS + resume builder for Claude Code.

Most second-brain tools are passive — you write notes manually. KBrain interviews you, structures the output, and produces proof-of-work artifacts automatically: Obsidian notes, audience-specific resume bullets, and session logs, all from three slash commands.

## Commands

| Command | What it does |
|---|---|
| `/kbrain-res <project>` | Interviews you about a project → writes structured Obsidian note → generates rated resume bullets for 4 audiences → optionally patches your LaTeX resume and compiles PDF |
| `/kbrain-context` | Debriefs any Claude Code session → writes dated session note to vault → checks if anything is worth pushing to your resume |
| `/kbrain-query <question>` | Full-text search across your vault with synthesized answers and source citations |

## Setup

**1. Install the plugin**

```bash
git clone https://github.com/YakshithK/kbrain ~/.claude/plugins/kbrain
```

**2. Configure your paths**

```bash
cp ~/.claude/plugins/kbrain/kbrain.local.example.md ~/.claude/kbrain.local.md
```

Edit `~/.claude/kbrain.local.md`:

```yaml
---
vault_path: /path/to/your/obsidian/vault
resume_tex: ~/resume/resume.tex
---
```

WSL users: reference Windows paths as `/mnt/c/Users/<name>/...`

**3. Restart Claude Code** — the commands will be available immediately.

## Vault structure

KBrain expects (and maintains) this folder structure inside your vault:

```
vault/
├── Projects/     — one note per project, structured frontmatter
├── Sessions/     — dated session notes (YYYY-MM-DD-slug.md)
├── Me/           — identity, profile
├── Applications/ — university/program application notes
├── Fellowships/  — fellowship target notes
├── Bullets/      — saved resume bullets by audience
├── Ideas.md      — running idea list
├── To-Do.md      — task list
└── Commitments.md
```

KBrain will create session notes automatically. Project notes are created via `/kbrain-res`.

## Resume bullets

`/kbrain-res` generates bullets for 4 audiences using the XYZ formula ("Accomplished X, as measured by Y, by doing Z"):

- **cs-admission** — technical depth, architecture decisions, scale
- **fellowship** — traction, users, downloads, narrative
- **technical** — stack specifics, hard problems, system design
- **general** — broadest framing, accessible to non-technical readers

Each bullet is rated STRONG / DECENT / WEAK with specific improvement suggestions.

## Requirements

- Claude Code
- An Obsidian vault (Obsidian doesn't need to be running)
- `pdflatex` — only needed if you want `/kbrain-res` to compile your resume PDF

## License

MIT
