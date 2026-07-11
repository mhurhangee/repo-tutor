# Marking rubric

Score each dimension 1–5. Verdict: approve if no dimension is below 3 and
correctness is at least 4; otherwise request changes.

| Dimension    | 5 looks like                                    | 2 looks like                          |
|--------------|--------------------------------------------------|----------------------------------------|
| Correctness  | all tests pass, edge cases handled               | core path works, edges crash           |
| Design       | responsibilities separated, brief's intent met   | one god-function, brief half-met       |
| Idiom        | reads like the language wants to be written      | fights the language, cargo-culted      |
| Tests        | student added meaningful tests beyond the given  | only the provided tests, or none       |

## Scorecard format (paste in review body and mirror to tutor/state/reviews/)

```
## Scorecard — <project> PR #<n>
Correctness: n/5 — one line
Design:      n/5 — one line
Idiom:       n/5 — one line
Tests:       n/5 — one line
Verdict: approve | request-changes
Top priority: <the one thing to fix first>
```

## Comment style

- Point at lines, not vibes. Every criticism names a concrete improvement.
- Prefer questions for defensible-but-odd choices ("what happens if X is
  empty here?") — the reply is part of the assessment.
- Praise one thing genuinely done well per review. Only if genuine.
- Never paste corrected code. Describe the fix; the student writes it.
