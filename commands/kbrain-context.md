---
name: kbrain-context
description: Use when asked to save/log/debrief a session, update the brain/vault/Obsidian, capture an idea, update a project or fellowship note, tidy the vault, or check if anything needs to go on the resume. Handles session debriefs, note updates, idea capture, and vault audits. Run with no args for a session debrief.
argument-hint: "[session | update <note> | idea | tidy | resume]"
disable-model-invocation: false
---

Vault: /mnt/c/Users/prabh/Desktop/obsidian/braindump
Resume: /home/yakshith/resume/resume.tex
Arguments: $ARGUMENTS

Current directory:
!`pwd`

Recent git activity:
!`git log --oneline -5 2>/dev/null || echo "[Not a git repo]"`


---

## MODES

**No args or "session"** → SESSION DEBRIEF: interview conversationally, one question at a time:
1. What were you working on?
2. What got done?
3. Key decisions and why?
4. Anything learned that wasn't obvious?
5. What's blocked?
6. Next concrete step?

Reference git log if it shows clear work — don't re-ask what's visible there.

Write session note to `/mnt/c/Users/prabh/Desktop/obsidian/braindump/Sessions/YYYY-MM-DD-[slug].md`:
```
---
date: YYYY-MM-DD
project: [[Projects/Name]]
tags: [session, ai|systems|hardware|frontend]
status: [in-progress | shipped | blocked | exploring]
next: [one sentence]
---
## What happened
## Decisions
## Learnings
## Blockers
## Next steps
```
After writing → run RESUME CHECK.

**"update \<note\>"** → Find the note in the vault, read it, ask what to update, patch it, then run RESUME CHECK.

**"idea"** → Ask about the idea, append to `/mnt/c/Users/prabh/Desktop/obsidian/braindump/Ideas.md`. No resume check.

**"tidy"** → Read vault structure and Profile.md, surface: missing frontmatter, broken wikilinks, orphaned notes, stale active projects. Fix what user confirms.

**"resume"** → Skip straight to RESUME CHECK.

---

## RESUME CHECK

Read recent sessions and resume.tex, then check:
- New metrics or outcomes not yet on the resume
- Shipped features or milestones worth a bullet
- Technical depth that would sharpen existing bullets

If something's worth adding:
> **Resume update spotted:** [project] — [what changed] — [suggested bullet]
> Want me to patch resume.tex and compile a new PDF?

If nothing: say so in one line.

---

Based on the arguments above, pick the right mode and respond to the user now. If no arguments, start the session debrief by asking: **"What were you working on in this session?"**
