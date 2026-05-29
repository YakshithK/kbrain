---
name: kbrain-res
description: Use when asked to add a project to the resume, document a project, generate resume bullets, interview about a project, or update the resume. Runs a structured interview, saves a note to the Obsidian vault, generates audience-specific bullets (cs-admission/fellowship/technical/general), and optionally patches resume.tex and compiles PDF.
argument-hint: <project-name>
arguments: [project]
disable-model-invocation: false
allowed-tools: Read, Write, Edit, Bash, AskUserQuestion
---

You are running a structured resume interview for: **$project**

Do NOT wait for the user to volunteer information. Drive the entire workflow yourself. Ask questions, collect answers, write outputs, generate bullets - all in one end-to-end run.

## Auto-loaded context

Config check:
!`[ -f ~/.claude/kbrain.local.md ] && echo "OK" || echo "MISSING - copy kbrain.local.example.md to ~/.claude/kbrain.local.md"`

Existing project note (if any):
!`grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//' | xargs -I{} cat "{}/Projects/$project.md" 2>/dev/null || echo "[NEW PROJECT]"`

Resume:
!`grep "^resume_tex:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^resume_tex: *//' | xargs cat 2>/dev/null || echo "[No resume.tex configured]"`

---

## PHASE 1 - COLLECT BASICS

Read the existing note above. For any field already answered clearly, skip it.

Use `AskUserQuestion` to collect structured fields first:

Ask these in one call:
- "What is the current status of $project?" → options: Active (still building), Shipped (live/released), Shelved (paused), Exploring (early stage)
- "What is your role on $project?" → options: Solo builder, Lead (with collaborators), Contributor (part of a team), Exploring / undefined
- "Which audiences should this target?" (multiSelect) → options: cs-admission, fellowship, technical, general

Then ask in a second call (free text via Other - no options match):
- "Describe $project in one sentence - what it does and who it's for." → options: [suggest three different framing styles as options, user picks or types own]
- "What is the tech stack and the key architecture decision you made?" → options: [suggest common stacks relevant to context, or let user type]

Then ask:
- "What are the hardest specific technical problem you solved and how?" → free text
- "What are the numbers? (downloads, users, stars, uptime, latency, accuracy - any metric)" → free text
- "Any external validation? (awards, press, GitHub activity, fellowship mentions, advisor feedback)" → free text
- "When did you start, and what's left to build?" → free text

Batch the free-text questions as 3–4 per `AskUserQuestion` call (max 4 questions per call). Do NOT send them as chat messages - use the tool.

---

## PHASE 2 - GAP PROBE

Review all collected answers. Identify the 2–3 weakest spots (missing metrics, vague stack, unclear personal contribution). Ask targeted follow-ups using `AskUserQuestion`. Maximum one call, maximum 3 questions. Then stop - move to Phase 3 regardless.

---

## PHASE 3 - WRITE AND GENERATE

Execute all steps below automatically without asking permission between them.

### Step 1: Write the project note

Determine the vault path:
!`grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//'`

Write to `$vault_path/Projects/$project.md` using this schema:

```
---
name: [Title Case name]
status: active|shipped|shelved
started: YYYY-MM
stack: [comma-separated]
links:
  github: [username/repo or null]
  npm: [package name or null]
  other: [url or null]
metrics:
  github_stars: [number or null]
  npm_downloads_weekly: [number or null]
  npm_downloads_total: [number or null]
  users: [number or null]
  other: [description or null]
role: solo|lead|contributor
team_size: [number or null]
audiences: [cs-admission, fellowship, technical, general]
last_updated: YYYY-MM-DD
---

## What it does
## Technical depth
## Impact and traction
## Hardest problem solved
## Interview notes
## Related
```

Confirm the file was written.

### Step 2: Generate resume bullets

For each audience - cs-admission, fellowship, technical, general - generate **exactly 3 bullets** using the XYZ formula: "Accomplished X, as measured by Y, by doing Z."

The resume standard is 3 bullets per entry, no exceptions. Every project/experience gets exactly 3 - not 2, not 4.

Rules:
- Lead with the strongest metric or outcome
- Action verbs only: Built, Shipped, Reduced, Increased, Designed, Implemented, Engineered, Trained, Deployed, Led, Architected
- One bullet = one achievement, max 2 lines
- Numbers > scope > nothing
- Never use em dashes (—). Use commas, colons, or semicolons instead, or restructure the sentence.

For each bullet:
- The bullet text
- Rating: STRONG / DECENT / WEAK
  - STRONG: metric + action verb + technical specificity
  - DECENT: 2 of 3
  - WEAK: vague, no numbers, generic verb
- One-line reasoning
- If DECENT or WEAK: one specific change to make it STRONG

Rank best-first within each audience.

### Step 3: Offer resume patch

Use `AskUserQuestion` to ask:
- "Do you want to add any bullets to your resume?" → options: Yes - pick bullets next, No - skip for now

If yes, use `AskUserQuestion` to ask which specific bullets (list them as options, multiSelect true).

Then:
1. Read the resume tex path from config
2. Edit resume.tex - insert bullets in the correct section, separated from the previous entry with `\vspace{4pt}` (the resume standard — not 2pt)
3. Compile: `grep "^resume_tex:" ~/.claude/kbrain.local.md | sed 's/^resume_tex: *//' | xargs -I{} sh -c 'cd "$(dirname "{}")" && pdflatex -interaction=nonstopmode "$(basename "{}")" && cp resume.pdf /mnt/c/Users/prabh/Desktop/Resume.pdf'`
4. Confirm PDF compiled and copied to Windows Desktop
