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

An adaptive drill of ~10 questions. Compose each question in conversation
ONLY after marking the previous answer — never pre-write a bank (a
pre-written set can't adapt, and a file the student can open is burned).
Step difficulty up on hits, down on misses; drill-skill formats and
marking rules. Afterwards, write the record (questions asked + outcomes)
to `content/quizzes/placement-<date>.md` as history. Skippable — some
prefer calibrating through real work.

## 4. Propose — don't write

Nothing durable is written during onboarding. Append to
`state/staging/jots.md`: profile facts (background, goals, failure modes
to design against, communication preferences), proposed topic levels with
their placement evidence (clusters only, e.g. `python.control-flow`; no
evidence, no entry — untested reads as unknown, never as zero), and the
syllabus as agreed in conversation (propose 5–8 ordered topics in chat,
informed by goals and placement, iterate until the student approves the
shape).

## 5. Consolidate

Invoke the session-end skill to close onboarding. It performs ALL durable
writes from the jots — `state/profile.md`, `state/topics.yaml`, ledger
files, `state/syllabus.md`, and the first `session.md` — each ask-gated.

## 6. First project

After consolidation, offer to design a first project — invoke the
new-project skill. If they'd rather stop, that's a fine first session;
close warmly.
