---
name: session-end
description: Close a tutoring session and consolidate memory. Use whenever the user says they're done, wrapping up, signing off, ending the session, or asks to save progress — even if they don't type /session-end.
---

# End a session

This is the ONLY point in a session where durable memory is written:
`state/topics.yaml`, `state/topics/`, `state/profile.md`,
`state/syllabus.md`, and `session.md`. The student is the gate:
these paths are permission-prompted, so every edit you propose appears as
a diff for them to approve. Propose carefully.

## Steps

1. Read `state/staging/jots.md` (may be empty) and reflect on the session.
   Only consolidate topics actually touched; base every claim on something
   that happened — a drill score, a commit, a PR, a moment in conversation.
   No aspirational entries.

2. For each touched topic, update `state/topics/<topic-name>.md` following
   `state/topics/_TEMPLATE.md` EXACTLY. Non-negotiable rules:
   - Filename: lowercase, hyphens, no spaces (`context-managers.md`).
     Check for an existing file before creating a new one.
   - **Evidence is append-only.** Add today's dated bullet at the bottom.
     Never rewrite, summarize, compress, or delete prior bullets — they
     are the historical record.
   - Confidence (1-5), Gaps, and Next are replaced with current values.
   - No extra sections, no renamed headings, no editorializing.

3. Update `state/topics.yaml` (rules and level anchors are in the file's
   header comments): adjust levels ONLY where a new evidence bullet
   justifies it, and say which bullet when proposing the edit. Levels 4-5
   require their demonstration types (reviewed PR; taught-it-back). Update
   `assessed` dates and `evidence` counts. Propose new clusters here if
   the session opened genuinely new ground — check for overlap first.

4. Update `state/profile.md` if the jots contain profile-grade facts:
   background, goals, failure modes to design against, working
   preferences. Sections stay stable; entries are dated. Create the file
   at onboarding. This is who they are — topics are what they know.

5. If the jots contain an agreed syllabus (onboarding) or agreed syllabus
   changes, write `state/syllabus.md`.

6. Rewrite `session.md`: last-session date, mode at end, active project,
   next focus (one sentence), topics touched. (Create it if this was the
   first session.)

7. Housekeeping: move burned or completed quiz files from
   `content/quizzes/` to `archive/`, and completed projects' content
   packs likewise, if the jots flag any.

8. Clear staging: empty `state/staging/jots.md` (keep the file).

9. Commit: `git add state/ session.md && git commit -m "memory: consolidate session <date>"`

10. Close with a two-line human summary: what moved forward, what's next.
   Do not ask them to keep chatting.

If the student rejects a proposed edit, ask what's wrong and re-propose —
their correction is better data than your draft.
