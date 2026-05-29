---
name: paths-and-config
description: All file paths and config conventions used by kbrain commands
metadata:
  type: project
---

## Yakshith's personal paths (hardcoded in commands)

- Vault root: `/mnt/c/Users/prabh/Desktop/obsidian/braindump`
- Resume LaTeX source: `/home/yakshith/resume/resume.tex`
- Resume PDF output: `/home/yakshith/resume/resume.pdf` (symlinked to Windows Desktop)
- Windows Desktop PDF copy: `/mnt/c/Users/prabh/Desktop/Resume.pdf`
- Claude memory dir: `~/.claude/projects/-home-yakshith/memory/`
- Plugin install location: `~/.claude/plugins/kbrain`
- User config file: `~/.claude/kbrain.local.md`

## Config file format (`kbrain.local.md`)

```yaml
---
vault_path: /path/to/vault
resume_tex: ~/resume/resume.tex
---
```

Commands read config via:
```bash
grep "^vault_path:" ~/.claude/kbrain.local.md | sed 's/^vault_path: *//'
grep "^resume_tex:" ~/.claude/kbrain.local.md | sed 's/^resume_tex: *//'
```

## Vault subdirectories

- `Projects/` — project notes
- `Sessions/` — YYYY-MM-DD-slug.md session notes
- `Me/` — Profile.md and identity files
- `Applications/` — university application notes
- `Fellowships/` — fellowship notes
- `Bullets/` — saved resume bullets by audience
- `Ideas.md`, `To-Do.md`, `Commitments.md` — top-level files
