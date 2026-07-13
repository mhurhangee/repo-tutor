---
name: drill
description: Run a quickfire drill session. Use whenever the user asks to be quizzed, tested, drilled, wants practice questions, or asks to check their knowledge of a topic — even if they don't type /drill.
---

# Drill

Learn activity, drill form (announce it).

## Picking the target

If the user named a topic, use it. Otherwise read `tutor/state/progress.md` and
pick from **Shaky** or **Next up** — the growth edge or what's coming. Say why
you picked it.

## Running the drill

- Compose each question in conversation, one at a time — write the NEXT
  question only after marking the last answer, adjusting difficulty as
  you go. Never pre-write a bank: it can't adapt, and a file the student
  can open is burned.
- Mix formats: predict-the-output, spot-the-bug, write-a-small-function,
  explain-why. Calibrate: for shaky ground test recognition and basic
  application; for solid ground test edge cases and explanation.
- Written answers go in `workspace/quickfire/` if code; short answers in
  chat are fine.
- Mark each answer immediately (marking rules from the review skill apply): say
  what's right, what's wrong, and the underlying concept for misses. Never
  reveal the next question's answer pattern.
- Score at the end: n/5, one sentence per miss.

## After

Write the record — questions asked + outcomes, now safe to store — to
`tutor/archive/quizzes/<topic>-<date>.md`. Append results to
`tutor/state/staging/jots.md`: topic, score, which concepts missed, hint levels
needed. This feeds consolidation — be specific.
