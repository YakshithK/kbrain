---
name: kbrain-res
description: Use when asked to add a project to the resume, document a project, generate resume bullets, interview about a project, or update the resume. Runs a structured interview, saves a note to the Obsidian vault, generates audience-specific bullets (cs-admission/fellowship/technical/general), and optionally patches resume.tex and compiles PDF.
argument-hint: <project-name>
arguments: [project]
disable-model-invocation: false
---

You are conducting a resume interview for: $project

## Config check
!`[ -f ~/.claude/kbrain.local.md ] && echo "Config loaded." || echo "⚠️  No config found. Copy kbrain.local.example.md to ~/.claude/kbrain.local.md and set your vault_path."`

## Existing context (loaded automatically)

Existing project note (empty if new project):
!`VAULT=$(grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//'); cat "$VAULT/Projects/$project.md" 2>/dev/null || echo "[No existing note — this is a new project]"`

Current resume source:
!`RESUME=$(grep "^resume_tex:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^resume_tex: *//'); cat "$RESUME" 2>/dev/null || echo "[No resume.tex configured]"`

---

## PHASE 1 — STRUCTURED EXTRACTION

Read the existing note above. If it already has solid answers for any item below, skip that item.

Conduct a conversational interview — ask naturally, one topic at a time. Do NOT present these as a numbered list. Before moving to Phase 2 you must have clear answers to all of the following. If an answer is vague, push back and ask for the specific number or detail before continuing.

Required:
- What the project/experience is (one sentence, plain language)
- Tech stack and specific architecture decisions (not just "I used React")
- Personal role vs. collaborators — exact contribution split
- Quantifiable outcomes: downloads, users, stars, revenue, uptime, latency, accuracy. If they say "some users", ask how many.
- External validation: awards, press, acceptances, fellowship mentions, advisor feedback, GitHub activity
- The hardest specific technical problem solved and exactly how
- Timeline: when started, current status, what's left
- What problem it solves and who has the problem

## PHASE 2 — GAP PROBE

Identify the 2–3 weakest areas in what you collected — typically missing metrics, vague tech description, or unclear personal contribution. Ask targeted follow-ups only for those gaps. Maximum 3 questions. Then stop.

---

## PHASE 3 — WRITE AND GENERATE

**Step 1: Write the structured project note.**

Use this schema for the note frontmatter and sections:

Frontmatter fields: name, status (active|shipped|shelved), started (YYYY-MM), stack, links (github/npm/other), metrics (github_stars/npm_downloads_weekly/npm_downloads_total/users/other), role (solo|lead|contributor), team_size, audiences, last_updated

Sections: What it does, Technical depth, Impact and traction, Hardest problem solved, Interview notes, Related (wikilinks)

Normalize project name: Title Case, spaces allowed (e.g. "Level0", "Agent Regression Lab").

Write to:
!`VAULT=$(grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//'); echo "$VAULT/Projects/$project.md"`

Confirm the file was written.

**Step 2: Generate resume bullets.**

For each audience — cs-admission, fellowship, technical, general — generate 2–3 bullets using the XYZ formula: "Accomplished X, as measured by Y, by doing Z." Lead with the strongest metric or outcome. Use action verbs only (Built, Shipped, Reduced, Increased, Designed, Implemented, Engineered, Trained, Deployed, Led, Architected). One bullet = one achievement. Max 2 lines.

For each bullet provide:
- The bullet text
- Rating: STRONG / DECENT / WEAK
  - STRONG: metric + action verb + technical specificity
  - DECENT: 2 of 3, one gap
  - WEAK: vague, no numbers, generic verb
- One line of reasoning
- If WEAK or DECENT: one specific change that makes it STRONG

Rank within each audience section, best first.

**Step 3: Offer to update the resume.**

Ask: "Do you want to add any of these to your resume? Tell me which ones and I'll patch resume.tex and compile a new PDF."

If yes:
- Read the resume path from config
- Edit the resume file — insert bullets in the correct section
- Run: `RESUME=$(grep "^resume_tex:" ~/.claude/kbrain.local.md | sed 's/^resume_tex: *//'); cd "$(dirname "$RESUME")" && pdflatex -interaction=nonstopmode "$(basename "$RESUME")"`
- Confirm PDF compiled and report path
