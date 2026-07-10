---
name: onboard
description: First-run onboarding for a fresh clone. Use when session.md does not exist, when the user says they're new or setting up, or when /start detects a fresh repo — even if they don't type /onboard.
---

# Onboard a new student

Runs once. Ends with: a seeded `state/topics.yaml`, an approved
`state/syllabus.md`, a created `session.md`, and (ideally) a first project.

## 1. Who are they

Use structured multiple-choice questions (AskUserQuestion) for the
categorical facts — experience level, hours per week, prior languages,
preferred pace. Use open conversation for the things that matter:
what they want to build and why. Never flatten goals into buttons.

## 2. Environment check

Quietly verify: `git` present, repo has a remote, `gh auth status`. If the
PR flow isn't available, say reviews will use the local fallback and move
on — never block onboarding on setup.

## 3. Placement (optional, offer it)

An adaptive drill of ~10 questions across the likely range, stepping
difficulty up on hits, down on misses (drill-skill formats and marking
rules). Skippable — some prefer calibrating through real work.

## 4. Seed the index and ledger

Create `state/topics.yaml` per the schema in the shipped skeleton:
cluster-level topics only (e.g. `python.control-flow`, not
`python.for-loops`), levels justified by placement evidence, everything
else omitted — the tree grows only when evidence exists to attach.
For each assessed cluster also create `state/topics/<cluster>.md` per
`state/topics/_TEMPLATE.md`. These writes are permission-prompted; the
student approves each.

## 5. Syllabus

Switch to plan mode: propose 5–8 ordered topics in chat, informed by
goals and placement. Iterate until approved, then write
`state/syllabus.md`. Note the direct write in `state/staging/jots.md`.

## 6. Create session.md

Write it fresh (ask-gated): today's date, mode `plan`, no active project,
next focus = first syllabus topic.

## 7. First project

Offer to design one now — invoke the new-project skill. If they'd rather
stop, that's a fine first session: commit
`git add state/ session.md && git commit -m "memory: onboarding"` and
close warmly.
