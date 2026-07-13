---
name: session-end
description: Close a tutoring session and consolidate memory. Use whenever the user says they're done, wrapping up, signing off, ending the session, or asks to save progress — even if they don't type /session-end.
---

# End a session

This is the ONLY point in a session where durable memory is written:
`tutor/state/topics.yaml`, `tutor/state/topics/`, `tutor/state/profile.md`,
`tutor/state/syllabus.md`, and `session.md`. The student is the gate:
these paths are permission-prompted, so every edit you propose appears as
a diff for them to approve. Propose carefully.

## Steps

1. Read `tutor/state/staging/jots.md` (may be empty) and reflect on the session.
   Only consolidate topics actually touched; base every claim on something
   that happened — a drill score, a commit, a PR, a moment in conversation.
   No aspirational entries.

2. For each touched topic, update `tutor/state/topics/<cluster>.md` following
   `tutor/state/topics/_TEMPLATE.md` EXACTLY. Non-negotiable rules:
   - Filename is the `topics.yaml` cluster key verbatim, plus `.md`:
     cluster `python.control-flow` → `python.control-flow.md`. The dot
     separates domain from cluster; hyphens join words within a cluster.
     Identity mapping, so the file for a key is never ambiguous — check
     for the existing file before creating a new one.
   - **Evidence is append-only.** Add today's dated bullet at the bottom.
     Never rewrite, summarize, compress, or delete prior bullets — they
     are the historical record.
   - Level (0-5), Gaps, and Next are replaced with current values. The
     ledger Level must match the `level` you set for this cluster in
     `tutor/state/topics.yaml` — one number, two places, never divergent.
   - No extra sections, no renamed headings, no editorializing.

3. Update `tutor/state/topics.yaml` (rules and level anchors are in the file's
   header comments): adjust levels ONLY where a new evidence bullet
   justifies it, and say which bullet when proposing the edit. Levels 4-5
   require their demonstration types (reviewed PR; taught-it-back). Update
   `assessed` dates and `evidence` counts. Propose new clusters here if
   the session opened genuinely new ground — check for overlap first.

4. Update `tutor/state/profile.md` if the jots contain profile-grade facts:
   background, goals, failure modes to design against, working
   preferences. It ships as a skeleton — fill it in (drop the placeholder
   bullets at onboarding). The four sections are FIXED: never
   add, rename, or remove them. Entries are dated and append-only within a
   section; supersede a stale fact with a newer dated bullet rather than
   deleting the old one. This is who they are — topics are what they know.

5. If the jots contain an agreed syllabus (onboarding) or agreed syllabus
   changes, fill in `tutor/state/syllabus.md` (it ships as a skeleton).

6. Rewrite `session.md`: last-session date, mode at end, active project,
   next focus (one sentence), topics touched. (Create it if this was the
   first session.)

7. Housekeeping: perform any archive moves the jots flag — e.g. a
   completed project's `tutor/solutions/<name>/` into `tutor/archive/`.

8. Clear staging: empty `tutor/state/staging/jots.md` (keep the file).

9. Commit: `git add tutor/state/ session.md && git commit -m "memory: consolidate session <date>"`

10. Close with a two-line human summary: what moved forward, what's next.
   Do not ask them to keep chatting.

If the student rejects a proposed edit, ask what's wrong and re-propose —
their correction is better data than your draft.
