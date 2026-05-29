---
name: kbrain-query
description: Use when asked to search the brain/vault, look something up in notes, recall a past decision or session, find what's known about a project or topic, or answer any question that might be in the Obsidian vault. Searches across Projects, Sessions, Fellowships, Applications, Ideas, and all other vault notes.
argument-hint: <question or search term>
arguments: [question]
---

## Config check
!`[ -f ~/.claude/kbrain.local.md ] && VAULT=$(grep "^vault_path:" ~/.claude/kbrain.local.md | sed 's/^vault_path: *//') && echo "Vault: $VAULT" || echo "⚠️  No config found. Copy kbrain.local.example.md to ~/.claude/kbrain.local.md and set your vault_path."`

You are querying the Brain vault to answer: $question

## Vault search results (auto-loaded)

Files matching the query:
!`VAULT=$(grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//'); grep -ril "$question" "$VAULT/" 2>/dev/null | head -20`

Matching content excerpts:
!`VAULT=$(grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//'); grep -ri --include="*.md" -l "$question" "$VAULT/" 2>/dev/null | xargs -I{} sh -c 'echo "=== {} ==="; grep -i -A 3 -B 1 "'"$question"'" "{}" 2>/dev/null; echo' | head -100`

Recent sessions (last 10):
!`VAULT=$(grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//'); ls -t "$VAULT/Sessions/"*.md 2>/dev/null | head -10 | xargs -I{} sh -c 'echo "--- {} ---"; head -10 "{}"' 2>/dev/null`

All project notes:
!`VAULT=$(grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//'); ls "$VAULT/Projects/"*.md 2>/dev/null | xargs -I{} basename {} .md`

---

Using the search results above, answer the question: **$question**

Synthesize across all matching notes — don't just quote them. If the question spans multiple projects or sessions, connect the dots. If nothing relevant is found, say so clearly and suggest what command might help populate the brain with that information (/kbrain-res for projects, /kbrain-context for sessions).

Format your answer with:
- A direct answer first
- Supporting evidence from specific notes (cite the file name)
- Any gaps — what's missing from the brain that would make this answer stronger
