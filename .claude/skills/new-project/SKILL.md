---
name: new-project
description: Design a new bespoke project with the student, tailored to their current knowledge and goals. Use whenever the user wants a new project, something to build, to practice a topic through a real task, or has finished a project and asks what's next — even if they don't type /new-project.
---

# New project

Switch to author mode (announce it). Projects are designed *with* the
student, not assigned. This is the core of the tutor: work sized to sit
just past the edge of what they can already do.

## 1. Ground it in their state

Read `tutor/state/topics.yaml` and `tutor/state/syllabus.md` (plus ledger files for
detail). Identify: what's solid (level 4–5, usable as foundation), what's
the growth edge (level 2–3, the project's deliberate focus), and what's untouched
(at most one new concept per project).

## 2. Negotiate the brief

Propose 2–3 project ideas in chat, each one line: what it is, which
edge-topics it exercises, rough size. Let the student pick or counter —
their interest beats your pedagogy; a project they care about teaches
more. Iterate scope together: a core milestone reachable in a few
sessions, plus 1–2 stretch goals.

## 3. Author the pack

Write to `tutor/content/projects/<name>/`. Everything the student needs
goes INSIDE `scaffold/` — after instantiation they should never need to
open `tutor/content/` again:

- `scaffold/brief.md` — the negotiated goal, milestones, which concepts
  it exercises, and what "done" means per milestone. Written to the
  student; travels with the handout.
- `scaffold/` also contains minimal starting files: entry point stubs, empty modules
  with docstring contracts, sample data. Stubs and signatures only —
  no logic. If a stub contains an implementation, delete the body.
- `tests/` — visible acceptance tests for milestone 1. Run them against
  the scaffold; they must fail for the right reasons (missing logic, not
  import errors).

## 4. The handout

The student copies the scaffold and creates their branch — not you. Frame
it as taking ownership of the assignment (the same ritual as Exercism or
GitHub Classroom: receive a starter, make it yours, branch), not as
filesystem plumbing:

```bash
cp -r tutor/content/projects/<name>/scaffold workspace/projects/<name>
git checkout -b project/<name>
git add workspace/projects/<name> && git commit -m "student: start <name>"
```

Then tell them plainly: from here on, everything — brief, tests, code,
data — lives in `workspace/projects/<name>/`. One directory. Mid-project
additions (e.g. milestone 2 tests) repeat the same move: you author in
`tutor/content/`, they copy in.

## 5. Log and hand off

Append to `tutor/state/staging/jots.md`: project name, target topics, negotiated
scope. Switch to teach mode and let them start milestone 1. From here the
loop is: work (teach) → /submit → /review → merge → next milestone.
