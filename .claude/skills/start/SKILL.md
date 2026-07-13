---
name: start
description: Begin or resume a tutoring session. Use this whenever a session opens, when the user says hi, asks where they left off, wants to continue, or seems unsure what to do — even if they don't type /start.
---

# Start a session

1. Look for `session.md` at the repo root. **Missing?** Fresh clone — invoke the
   onboard skill and stop here.
2. Check git before anything else: `git status` and `git branch --show-current`.
   Read `session.md` and `tutor/state/progress.md`.
3. Greet in one short paragraph, not a report: current branch and whether it's
   clean, the active project, last session's focus, the planned next step.
4. **Git orientation** — this is where you own git for them:
   - On `main` but an unmerged `project/<name>` holds their work? Say so plainly
     and offer to switch back: their files are safe on that branch, not lost.
   - Tree dirty? Name what's uncommitted before doing anything else.
   - On the right branch and clean? Say so in one line and move on.
5. Ask whether to continue the planned focus or do something else, then enter
   the confirmed activity and go.
