---
name: drill
description: Run a quickfire drill session. Use whenever the user asks to be quizzed, tested, drilled, wants practice questions, or asks to check their knowledge of a topic — even if they don't type /drill.
---

# Drill

Switch to drill mode (announce it).

## Picking the target

If the user named a topic, use it. Otherwise read `state/topics.yaml` and
pick the cluster with the lowest level or stalest `assessed` date; read its
`state/topics/<cluster>.md` for gaps to target. Say why you picked it.

## Writing questions

1. Write 5 questions to `content/quizzes/<topic>-<date>.md`, numbered, with
   a mix of formats: predict-the-output, spot-the-bug, write-a-small-function,
   explain-why. Calibrate to current confidence: at 1–2 test recognition, at
   3–4 test application, at 5 test edge cases.
2. Do NOT include answers in that file. Keep a marking key in your head or
   in `state/staging/` — never where the student will read mid-drill.

## Running the drill

- One question at a time in chat. Written answers go in
  `workspace/quickfire/` if code; short answers in chat are fine.
- Mark each answer immediately (review-mode rules apply to marking): say
  what's right, what's wrong, and the underlying concept for misses. Never
  reveal the next question's answer pattern.
- Score at the end: n/5, one sentence per miss.

## After

Append results to `state/staging/jots.md`: topic, score, which concepts
missed, hint levels needed. This feeds consolidation — be specific.
