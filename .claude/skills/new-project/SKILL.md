---
name: new-project
description: Design a new bespoke project with the student, tailored to their current knowledge and goals. Use whenever the user wants a new project, something to build, to practice a topic through a real task, or has finished a project and asks what's next — even if they don't type /new-project.
---

# New project

Switch to author mode (announce it). Projects are designed *with* the
student, not assigned. This is the core of the tutor: work sized to sit
just past the edge of what they can already do.

## 1. Ground it in their state

Read `tutor/state/topics.yaml`, `tutor/state/profile.md`, and
`tutor/state/syllabus.md` (plus ledger files for detail). Identify:
what's solid (level 4–5, foundation), the growth edge (level 2–3, the
project's deliberate focus), and at most one untouched concept.

## 2. Negotiate the brief

Propose 2–3 project ideas in chat, each one line: what it is, which
edge-topics it exercises, rough size. Let the student pick or counter —
their interest beats your pedagogy. Iterate scope together: a core
milestone reachable in a few sessions, plus 1–2 stretch goals. Defend
scope actively (park extras in the brief's LATER section).

## 3. Branch, then scaffold

The student creates their branch first:

```bash
git checkout -b project/<name>
```

Then write the scaffold directly into `workspace/projects/<name>/` —
each write is ask-gated, so the student sees and approves every file
(constitution rule 1a):

- `brief.md` — negotiated goal, milestones, concepts exercised, what
  "done" means per milestone, and a LATER section for parked scope.
- Stub modules with docstring contracts. Signatures only — if a stub
  contains logic, delete the body.
- Visible acceptance tests for milestone 1.
- Sample data if needed.

Run the tests from the project directory: all must fail for the right
reasons (missing logic, not import errors).

Write the reference solution to `tutor/solutions/<name>/` — never into
workspace, and don't show it in chat. Verify the tests pass against it.

## 4. Student takes ownership

The student reviews what landed and makes the start commit:

```bash
git add workspace/projects/<name> && git commit -m "student: start <name>"
```

From here their entire loop is `workspace/projects/<name>/` — brief,
tests, code, data, one directory. Mid-project additions (e.g. milestone 2
tests) are written the same way: ask-gated, into their project, committed
by them.

## 5. Log and hand off

Append to `tutor/state/staging/jots.md`: project name, target topics,
negotiated scope, parked scope. Switch to teach mode and let them start
milestone 1. From here the loop is: work (teach) → /submit → /review →
merge → next milestone.
