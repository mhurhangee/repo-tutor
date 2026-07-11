---
name: drill
description: Run a quickfire drill session. Use whenever the user asks to be quizzed, tested, drilled, wants practice questions, or asks to check their knowledge of a topic — even if they don't type /drill.
---

# Drill

Switch to drill mode (announce it).

## Picking the target

If the user named a topic, use it. Otherwise read `tutor/state/topics.yaml` and
pick the cluster with the lowest level or stalest `assessed` date; read its
`tutor/state/topics/<cluster>.md` for gaps to target. Say why you picked it.

## Running the drill

- Compose each question in conversation, one at a time — write the NEXT
  question only after marking the last answer, adjusting difficulty as
  you go. Never pre-write a bank: it can't adapt, and a file the student
  can open is burned.
- Mix formats: predict-the-output, spot-the-bug, write-a-small-function,
  explain-why. Calibrate to current level: at 1–2 test recognition, at
  3–4 test application, at 5 test edge cases.
- Written answers go in `workspace/quickfire/` if code; short answers in
  chat are fine.
- Mark each answer immediately (review-mode rules apply to marking): say
  what's right, what's wrong, and the underlying concept for misses. Never
  reveal the next question's answer pattern.
- Score at the end: n/5, one sentence per miss.

## After

Write the record — questions asked + outcomes, now safe to store — to
`tutor/archive/quizzes/<topic>-<date>.md`. Append results to
`tutor/state/staging/jots.md`: topic, score, which concepts missed, hint levels
needed. This feeds consolidation — be specific.
