---
name: session-end
description: Close a tutoring session and consolidate memory. Use whenever the user says they're done, wrapping up, signing off, ending the session, or asks to save progress — even if they don't type /session-end.
---

# End a session

The only point where durable memory is written: `session.md`,
`tutor/state/profile.md`, `tutor/state/progress.md`. All three live on `main`.
There are no per-file prompts — you write freely, then the student approves the
single `git diff` before you commit. That diff is the gate.

## Steps

1. Read `tutor/state/staging/jots.md` (may be empty) and reflect on the session.
   Consolidate only what actually happened — a drill score, a commit, a PR, a
   moment in conversation. No aspirational entries.

2. **Get to a clean `main`.**
   - If the tree is dirty on a project branch, help the student commit their
     work first (`student:` prefix — a WIP commit is fine, and it's safe on the
     branch). You don't commit their work for them.
   - Note which branch they were on, then `git switch main`. Their project files
     will drop out of the tree while you're on `main` — that's expected; step 9
     puts them back.

3. Update `tutor/state/progress.md`: move topics between **Solid** (shown
   independently — clearest signal is a reviewed PR), **Shaky** (needs hints or
   recently missed — the growth edge), and **Next up** (ordered path). Prose, no
   scores. Every line traces to evidence from this session.

4. Update `tutor/state/profile.md` only if the jots hold profile-grade facts:
   background, goals, failure modes to design against, working preferences. The
   four sections are FIXED. Entries are dated and append-only within a section;
   supersede a stale fact with a newer dated bullet rather than deleting.

5. Rewrite `session.md`: date, active project + its branch, next focus (one
   sentence), topics touched. Create it if this was the first session.

6. Any archive moves the jots flag — e.g. a finished project's
   `tutor/solutions/<name>/` into `tutor/archive/`.

7. Empty `tutor/state/staging/jots.md` (keep the file).

8. **Show the diff, then commit as one change.** Run `git status` and `git diff`,
   walk the student through what you're about to save, and commit:
   ```bash
   git add tutor/ session.md && git commit -m "memory: consolidate <date>"
   ```
   If they want something changed, edit and re-show before committing — their
   correction is better data than your draft.

9. If they were on an unmerged project branch, switch back so next session
   resumes in place: `git switch project/<name>`. Their files reappear.

10. Close with a two-line human summary: what moved forward, what's next. Don't
    ask them to keep chatting.
