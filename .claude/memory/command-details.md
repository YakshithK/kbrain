---
name: command-details
description: Phase-by-phase logic for all three kbrain commands, including the AskUserQuestion-driven interview pattern
metadata:
  type: project
---

## Design principle (post-rewrite)

Both `kbrain-res` and `kbrain-context` are end-to-end workflows. They drive the conversation — they do NOT wait for the user to volunteer info in chat. All user input is collected via `AskUserQuestion` (structured UI), then all write steps execute automatically without pausing.

Both have `allowed-tools: Read, Write, Edit, Bash, AskUserQuestion` in frontmatter to avoid mid-flow permission prompts.

---

## kbrain-context modes

Invoked as `/kbrain-context [mode]`. Default = session.

**session (default):** 7-step pipeline, fully automatic after one `AskUserQuestion` call
1. Quick interview — single `AskUserQuestion` call (max 4 questions), skip anything inferable from git log/cwd/conversation
2. Write `Sessions/YYYY-MM-DD-slug.md` with frontmatter: date, project wikilink, tags, status, next
3. Sync project note in `Projects/` — update or create using full schema
4. Update `Me/Profile.md` — add/remove/update active projects
5. Update related notes (Summer 2026.md, fellowship notes, dependency projects)
6. Sync Claude memory at `~/.claude/projects/-home-yakshith-kbrain/memory/`
7. Resume scan — if resume-worthy material found, use `AskUserQuestion` to offer patch; compile PDF if yes

**idea:** `AskUserQuestion` for idea/problem/stage, then append to `Ideas.md`

**tidy:** Vault audit — missing frontmatter, broken wikilinks, orphaned notes, stale status — confirm fixes via `AskUserQuestion` in batches

**resume:** Read resume.tex + last 5 session notes → surface bullets → `AskUserQuestion` to pick which to patch → compile PDF

---

## kbrain-res phases

**Phase 1 — Collect basics:**
- Call 1: `AskUserQuestion` for status, role, audiences (structured options)
- Call 2: `AskUserQuestion` for project description and stack (suggest options, user can type Other)
- Call 3: `AskUserQuestion` for hardest problem, metrics, validation, timeline (3–4 free-text questions batched)

Skip any field already answered in the existing project note.

**Phase 2 — Gap probe:**
- Identify 2–3 weakest areas
- One `AskUserQuestion` call, max 3 questions, then stop regardless

**Phase 3 — Write + Generate (fully automatic):**
- Step 1: Write project note to `$vault_path/Projects/$project.md` using full schema
- Step 2: Generate 2–3 bullets per audience (cs-admission, fellowship, technical, general) with STRONG/DECENT/WEAK ratings
- Step 3: `AskUserQuestion` — offer resume patch; if yes, pick bullets (multiSelect), edit resume.tex, compile, copy to Windows Desktop

---

## Project note schema (frontmatter)

Fields: name, status (active|shipped|shelved), started (YYYY-MM), stack, links (github/npm/other), metrics (github_stars/npm_downloads_weekly/npm_downloads_total/users/other), role (solo|lead|contributor), team_size, audiences, last_updated

Sections: What it does, Technical depth, Impact and traction, Hardest problem solved, Interview notes, Related

---

## kbrain-query flow

1. Config check — verify `~/.claude/kbrain.local.md` exists
2. grep vault recursively for the question string
3. Load recent 10 session file names + all project note names
4. Synthesize across all matches — cite file names, connect dots
5. If nothing found: suggest `/kbrain-res` (projects) or `/kbrain-context` (sessions)
