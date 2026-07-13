---
name: review
description: Formally review the student's submitted PR (or drill answers) with line comments, a verdict, and a scorecard. Use whenever a PR needs marking, the user asks for their submission to be reviewed or graded, or after /submit — even if they don't type /review.
---

# Review

Review activity (announce it). Read `references/rubric.md` before marking
anything. This is the "independent reviewer" the whole design leans on — keep it
genuinely independent.

## Isolation

Grading must be blind to how much help was given in chat. Spawn a subagent
(Task tool) for the actual assessment, giving it ONLY: the PR diff, the
project brief, the visible tests, the reference solution from `tutor/solutions/<name>/` if
one exists, and the rubric. Do not pass conversation history.

## The subagent's job

1. `gh pr diff <n>` to fetch the changes; read the brief and tests.
2. Run the test suite against the student's branch. Record results.
3. Line comments via `gh pr review` / `gh pr comment`: specific, kind,
   actionable. Ask questions where a design choice is defensible-but-odd
   rather than declaring it wrong — the reply thread is part of the
   assessment.
4. Verdict: `--approve` or `--request-changes`, with a scorecard in the
   review body per the rubric.

## After the subagent returns

1. Mirror the scorecard to `tutor/state/reviews/<branch>-pr<n>.md`. This is a
   `tutor/**` file, so it belongs on `main`. Ensure the tree is clean first — if
   the student kept working after submitting, have them commit their WIP on the
   branch (you don't commit their work). Then `git switch main`, write and commit
   the mirror (`review: <project> PR #<n>`), and `git switch` back to their
   branch. Never let the review mirror land on the project branch.
2. Summarize the verdict to the student in two sentences. If changes were
   requested, name the single most important one to tackle first.
3. Append outcomes to `tutor/state/staging/jots.md` (concepts that were
   strong/weak) for session-end consolidation.
4. The student responds on the PR, pushes fixes, and merges when approved. You
   never merge.

## No-GitHub fallback

Same process, but write the full review (line-referenced comments +
scorecard) to `tutor/state/reviews/<branch>.md` instead of posting to a PR.
