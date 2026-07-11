# Developing repo-tutor

You are working ON the tutor, not AS the tutor. Read `dev/ARCHITECTURE.md`
before structural changes — it is the source of truth for design decisions.

## Invariants — never break these

1. The tutor never writes in `workspace/` invisibly: interventions are
   explicit-request-only, ask-gated, clean-tree-first, smallest-possible,
   and committed separately under `tutor:`. Every enforcement ring must
   preserve the visibility, and the write stays ask-gated in settings.
2. `tutor/state/topics/`, `tutor/state/syllabus.md`, and `session.md` are written only
   during /session-end (or /start onboarding), always following
   `tutor/state/topics/_TEMPLATE.md`, always permission-prompted so the student
   approves the diff. Evidence sections are append-only.
3. The mode table lives in root `CLAUDE.md` and mirrors §3 of the
   architecture doc. Change both or neither.
4. No bundled scripts, no runtime dependencies. The tutor is markdown,
   settings, and (later) hooks/CI only. Anything needing local executable
   code is a design smell — find a prompt, permission, or CI shape first.
5. Clone → `claude` → `/start` must always work with nothing else installed
   (beyond Claude Code and, for PR flow, `gh`).

## Working rules

- Keep root `CLAUDE.md` under ~80 lines. New procedure goes in a skill.
- Skill descriptions should be pushy about when to trigger (undertriggering
  is the common failure); bodies under ~500 lines; rubrics and templates as
  bundled reference files.
- Test hook and script changes against fixture files before committing.
- Headless invocations (`claude -p`) in any automation: strip
  `ANTHROPIC_API_KEY` from the environment first, never use `--bare`,
  always cap with `--max-turns`.
- Dev mode is by nature a guardrail bypass. It is acceptable for a personal
  tool; before any multi-user release, gate it behind an explicit flag.

## Current stage

Stage 1 (skeleton + constitution). See §10 of ARCHITECTURE.md for the build
order and the exit criterion for each stage. Do not start stage N+1 until
stage N has survived a real study session.
