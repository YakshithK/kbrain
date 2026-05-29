---
name: resume-bullets
description: Resume bullet writing rules, XYZ formula, audience calibration, and rating system used across kbrain
metadata:
  type: project
---

All bullet generation in kbrain follows these rules (mirrored from CLAUDE.md):

## XYZ Formula

"Accomplished X, as measured by Y, by doing Z"
- Lead with strongest metric or outcome first
- Action verbs only: Built, Shipped, Reduced, Increased, Designed, Implemented, Engineered, Trained, Deployed, Led, Architected
- One bullet = one achievement (not a job description)
- Max 2 lines per bullet
- Cut filler: "responsible for", "worked on", "helped with", "was involved in"
- Numbers make bullets strong — extract every quantifiable metric
- If no metric: use scope ("across 3 models", "handling 10k events/day")

## Rating system

- **STRONG**: has metric + action verb + technical specificity
- **DECENT**: has 2 of 3, one gap
- **WEAK**: vague, no numbers, generic verb

Each rated bullet gets: the text, rating, one-line reasoning, and (if DECENT/WEAK) one specific change to make it STRONG.

## Audience calibration

- **cs-admission**: technical depth, architecture decisions, algorithms, scale
- **fellowship**: traction metrics, users, downloads, validation, narrative
- **technical**: stack specifics, hard problems solved, system design choices
- **general**: broadest framing, accessible to non-technical readers

Rank bullets best-first within each audience section.
