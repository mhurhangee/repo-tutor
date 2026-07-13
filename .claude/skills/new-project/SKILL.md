---
name: new-project
description: Design a new bespoke project with the student, tailored to their current knowledge and goals. Use whenever the user wants a new project, something to build, to practice a topic through a real task, or has finished a project and asks what's next — even if they don't type /new-project.
---

# New project

Build activity (announce it). Projects are designed *with* the student, sized to
sit just past the edge of what they can already do. This is the core of the tutor.

## 1. Ground it in their state

Read `tutor/state/profile.md` and `tutor/state/progress.md`. Identify: what's
solid (the foundation), the shaky growth edge (the project's deliberate focus),
and at most one brand-new concept.

## 2. Negotiate the brief

Propose 2–3 project ideas in chat, each one line: what it is, which edge-topics
it exercises, rough size. Let the student pick or counter — their interest beats
your pedagogy. Agree on a core milestone reachable in a few sessions, plus 1–2
stretch goals parked in a LATER section. Defend scope actively.

## 3. Set up git (you drive this)

Start from a clean `main`:

- `git switch main`. If the tree is dirty, don't branch on top of it — commit
  the student's WIP first (session-end step 2's recipe: a `student:` WIP commit
  on the branch they're on), or resolve it with them, then switch.
- Author the reference solution **and its milestone-1 tests together** in
  `tutor/solutions/<name>/`, and run the tests there — they must pass against the
  solution. Commit on `main` (`tutor: solution for <name>`). This is the answer
  key; it never touches the student's branch or chat.
- Create the project branch: `git switch -c project/<name>`.

Narrate each move in one line so the student always knows what branch they're on
and why.

## 4. Scaffold the branch

Write the scaffold into `workspace/projects/<name>/`. These land as visible
commits the student reviews and owns — not silent edits:

- `brief.md` — goal, milestones, concepts exercised, what "done" means per
  milestone, a **LATER** section for parked scope, and — critically — an
  **implementation order** for milestone 1: which unit to build first, which
  test covers it, what to reach for next. This is the on-ramp; without it the
  student faces a wall.
- Stub modules with docstring contracts. Signatures only — if a stub contains
  logic, delete the body.
- The milestone-1 tests — the same ones you verified against the solution in
  step 3, copied into the student's project so they're visible and runnable.
  Run them from the project dir: all fail for the right reason (missing logic,
  not import errors).

## 5. Hand off with a warm-up, not "go"

The student reviews what landed and makes the start commit themselves:

```bash
git add workspace/projects/<name> && git commit -m "student: start <name>"
```

Then don't dump the whole thing on them. Point at the **first unit** from the
brief's implementation order and give them the loop to work it in isolation:
`pytest -k <unit>` (or the project's equivalent) — one failing test, one
function, green, next unit. Switch to Learn and coach milestone 1 from there.

## 6. Log

Append to `tutor/state/staging/jots.md`: project name, target topics, agreed
scope, parked scope. From here the loop is: work (Learn) → `/submit` → review →
merge → next milestone.
