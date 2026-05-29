---
name: kbrain-query
description: Use when asked to search the brain/vault, look something up in notes, recall a past decision or session, find what's known about a project or topic, or answer any question that might be in the Obsidian vault. Searches across Projects, Sessions, Fellowships, Applications, Ideas, and all other vault notes.
argument-hint: <question or search term>
arguments: [question]
allowed-tools: Bash, AskUserQuestion
---

## Config check
!`[ -f ~/.claude/kbrain.local.md ] && echo "Config loaded." || echo "MISSING — copy kbrain.local.example.md to ~/.claude/kbrain.local.md"`

Vault path:
!`grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//'`

Query: $question

If $question is empty, use `AskUserQuestion` to ask: "What do you want to look up in your vault?" → options: Recent sessions, Active projects, Fellowship status, Other (type your question). Use the answer as the query before proceeding.

## Vault search results (auto-loaded)

Files matching the query (markdown only, excluding .obsidian and .smart-env):
!`VAULT=$(grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//'); Q="$question"; [ -z "$Q" ] && exit 0; grep -ril --include="*.md" "$Q" "$VAULT/" 2>/dev/null | grep -v '/\.obsidian/' | grep -v '/\.smart-env/' | head -20`

Matching content excerpts:
!`VAULT=$(grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//'); Q="$question"; [ -z "$Q" ] && exit 0; grep -ril --include="*.md" "$Q" "$VAULT/" 2>/dev/null | grep -v '/\.obsidian/' | grep -v '/\.smart-env/' | head -20 | while IFS= read -r f; do echo "=== $f ==="; grep -i -A 3 -B 1 "$Q" "$f" 2>/dev/null; echo; done | head -120`

Recent sessions (last 10):
!`grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//' | xargs -I{} ls -t "{}/Sessions/" 2>/dev/null | head -10`

All project notes:
!`grep "^vault_path:" ~/.claude/kbrain.local.md 2>/dev/null | sed 's/^vault_path: *//' | xargs -I{} ls "{}/Projects/" 2>/dev/null`

---

Using the search results above, answer the question: **$question**

Synthesize across all matching notes — don't just quote them. If the question spans multiple projects or sessions, connect the dots. If nothing relevant is found, say so clearly and suggest what command might help populate the brain with that information (/kbrain-res for projects, /kbrain-context for sessions).

Format your answer with:
- A direct answer first
- Supporting evidence from specific notes (cite the file name)
- Any gaps — what's missing from the brain that would make this answer stronger
