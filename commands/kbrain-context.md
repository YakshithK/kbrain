---
name: kbrain-context
description: Use when asked to save/log/debrief a session, update the brain/vault/Obsidian, capture an idea, update a project or fellowship note, tidy the vault, or check if anything needs to go on the resume. Handles session debriefs, note updates, idea capture, and vault audits. Run with no args for a session debrief.
argument-hint: "[session | update <note> | idea | tidy | resume]"
disable-model-invocation: false
---

## Config check
!`[ -f ~/.claude/kbrain.local.md ] && echo "Config loaded." || echo "⚠️  No config found. Copy kbrain.local.example.md to ~/.claude/kbrain.local.md and set your vault_path."`

Vault path:
!`grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//'`

## Auto-loaded context

Current directory:
!`pwd`

Recent git activity:
!`git log --oneline -5 2>/dev/null || echo "[Not a git repo]"`

Recent brain sessions:
!`grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//' | xargs -I{} ls -t "{}/Sessions/" 2>/dev/null | head -5 || echo "[No sessions yet]"`

Arguments passed: $ARGUMENTS

---

## STEP 1 — DETECT MODE

If $ARGUMENTS is empty or "session": → SESSION DEBRIEF mode
If $ARGUMENTS starts with "update": → UPDATE NOTE mode (note name follows)
If $ARGUMENTS is "idea" or "ideas": → CAPTURE IDEA mode
If $ARGUMENTS is "tidy": → VAULT TIDY mode
If $ARGUMENTS is "resume": → RESUME CHECK only (skip to Phase R)
Otherwise: interpret the freeform intent and pick the closest mode, confirm with user.

---

## MODE A — SESSION DEBRIEF

Interview conversationally, one question at a time (3–5 min total):
- What was the main thing being worked on?
- What actually got done?
- Key decisions and why?
- Anything learned that wasn't obvious?
- What's blocked or unclear?
- What's the next concrete step?

If git log shows clear work, reference it — don't re-ask what's visible.

Write session note to Sessions/YYYY-MM-DD-[slug].md in the vault:

```
---
date: YYYY-MM-DD
project: [[Projects/Name]]
tags:
  - session
  - [ai | systems | hardware | frontend | …]
status: [in-progress | shipped | blocked | exploring]
next: [one sentence]
---

## What happened
[2–3 sentences]

## Decisions
[bullet list with reasoning]

## Learnings
[bullet list, non-obvious things only]

## Blockers
[what's in the way]

## Next steps
[concrete actions]
```

Sessions folder:
!`grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//' | xargs -I{} echo "{}/Sessions/"`

Confirm file written, then → Phase R (resume check).

---

## MODE B — UPDATE NOTE

Identify which note to update from the argument (match against Projects/, Fellowships/, Applications/, Me/).

Search for matching notes:
!`grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//' | xargs -I{} grep -ril "$ARGUMENTS" "{}/" 2>/dev/null | head -5`

Ask what to add or change — new metrics, decisions, traction, status change, technical depth, links.
Patch the note in place. Update `last_updated` frontmatter field.
Confirm what changed, then → Phase R (resume check).

---

## MODE C — CAPTURE IDEA

Ask: what's the idea, what problem does it solve, any early thinking on approach?

Ideas file:
!`grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//' | xargs -I{} echo "{}/Ideas.md"`

Append a timestamped entry:

```
## [YYYY-MM-DD] [Idea title]
[Problem]: …
[Approach]: …
[Why now]: …
[Next action if pursuing]: …
```

Ask if this should also become a Project stub (→ write Projects/<name>.md with skeleton frontmatter).
Stop after writing. No resume check.

---

## MODE D — VAULT TIDY

Vault structure:
!`grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//' | xargs -I{} find "{}" -name "*.md" | sort`

Profile note:
!`grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//' | xargs -I{} cat "{}/Me/Profile.md" 2>/dev/null`

Surface:
- Notes missing frontmatter
- Broken [[wikilinks]] (links to notes that don't exist)
- Orphaned notes (nothing links to them)
- Active projects in Profile with stale or missing notes
- last_updated dates older than 60 days on active projects

Ask which issues to fix, then fix them. Confirm changes made. No resume check after tidy.

---

## Phase R — RESUME RELEVANCE CHECK

(Runs after Session Debrief and Update Note modes.)

Recent sessions:
!`grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//' | xargs -I{} ls -t "{}/Sessions/" 2>/dev/null | head -5`

Current resume:
!`grep "^resume_tex:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^resume_tex: *//' | xargs cat 2>/dev/null || echo "[No resume configured]"`

Look for:
- New metrics, traction, or outcomes not yet on the resume
- Shipped features or milestones worth adding
- Technical depth that would sharpen existing bullets

If something's worth updating:
> **Resume update spotted:** [project] — [what changed] — [suggested bullet]
> Want me to patch resume.tex and compile a new PDF?

If nothing has changed that's worth adding, say so in one line and stop.
