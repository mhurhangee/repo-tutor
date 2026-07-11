---
name: submit
description: Submit the student's project work for formal review. Use whenever the user says they're done with a milestone, wants their work marked, reviewed, graded, or checked, or asks to open a PR — even if they don't type /submit.
---

# Submit for review

Chat help is informal; a PR is formal assessment. This skill opens the PR.
It does not review it — that's the review skill, invoked after.

## Steps

1. Confirm which project and what milestone is being submitted (check the
   brief in `tutor/content/projects/<pack>/brief.md` for scope).
2. Sanity checks, read-only: uncommitted changes on the branch? Commits
   follow the `student:` prefix? Visible tests present? Report findings;
   the student fixes anything themselves — you never commit their work.
3. Open the PR:
   ```bash
   git push -u origin <branch>
   gh pr create --title "<project>: <milestone>" \
     --body "Submitting <milestone> for review. Brief: tutor/content/projects/<pack>/brief.md"
   ```
   If there's no remote or gh auth fails, fall back: tell the student the
   review will be written to `tutor/state/reviews/<branch>.md` instead.
4. Tell the student the PR number and offer to run the review now (`/review`).

Reminder: the student merges after approval. You never merge.
