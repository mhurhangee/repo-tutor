---
name: onboard
description: First-run onboarding for a fresh clone. Use when session.md does not exist, when the user says they're new or setting up, or when /start detects a fresh repo — even if they don't type /onboard.
---

# Onboard a new student

Runs once. Ends with `tutor/state/profile.md` and `tutor/state/progress.md`
filled in, a `session.md` created, and (ideally) a first project.

## 1. Who are they

Use AskUserQuestion for the categorical facts — experience level, hours per
week, prior languages, preferred pace. Use open conversation for what actually
matters: what they want to build and why. Never flatten goals into buttons.

## 2. Environment check

Quietly verify: `git` present, repo has a remote, `gh auth status`. If the PR
flow isn't available, say reviews will use the local fallback and move on —
never block onboarding on setup.

## 3. Placement (optional, offer it)

An adaptive drill of ~10 questions. Compose each question in conversation ONLY
after marking the previous answer — never pre-write a bank (it can't adapt, and
a file the student can open is burned). Step difficulty up on hits, down on
misses; drill-skill formats and marking rules. Afterwards write the record to
`tutor/archive/quizzes/placement-<date>.md`. Skippable — some prefer calibrating
through real work.

## 4. Propose — don't write yet

Nothing durable is written during onboarding. Append to
`tutor/state/staging/jots.md`: profile facts (background, goals, failure modes to
design against, communication preferences), what placement showed (which topics
look solid vs shaky, with the evidence), and an agreed path (propose 5–8 ordered
"next up" topics in chat, informed by goals and placement, iterate until they
approve the shape).

## 5. Consolidate

Invoke the session-end skill to close onboarding. It performs the durable writes
from the jots — `profile.md`, `progress.md`, and the first `session.md` — as one
reviewable commit on `main`.

## 6. First project

After consolidation, offer to design a first project (invoke new-project). If
they'd rather stop, that's a fine first session — close warmly.
