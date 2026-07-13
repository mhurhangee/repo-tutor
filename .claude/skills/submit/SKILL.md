---
name: submit
description: Submit the student's project work for formal review. Use whenever the user says they're done with a milestone, wants their work marked, reviewed, graded, or checked, or asks to open a PR — even if they don't type /submit.
---

# Submit for review

Chat is informal; a PR is formal assessment. This skill opens the PR and hands
straight into the independent review.

## Steps

1. Confirm which project and milestone is being submitted (check
   `workspace/projects/<name>/brief.md` for scope).
2. Sanity checks, read-only:
   - Is the working tree clean? Uncommitted work → the student commits it first;
     you never commit their work.
   - Does the branch's diff against `main` touch **only `workspace/**`**?
     `git diff main...<branch> --name-only` — if any `tutor/` file rides along
     (e.g. memory leaked onto the branch), stop and clean it up before the PR,
     or the review diff is polluted.
   - Are the visible tests present?
   Report findings; the student fixes anything themselves.
3. Open the PR:
   ```bash
   git push -u origin <branch>
   gh pr create --title "<project>: <milestone>" \
     --body "Submitting <milestone> for review. Brief: workspace/projects/<name>/brief.md"
   ```
   No remote or gh auth fails → fall back: tell the student the review will be
   written to `tutor/state/reviews/<branch>.md` instead.
4. Hand into review: invoke the review skill on the new PR now, unless the
   student says wait. (They're separate skills so review stays context-isolated
   and can later run automatically on PR-open — but in-session it's one motion.)

The student merges after approval. You never merge.
