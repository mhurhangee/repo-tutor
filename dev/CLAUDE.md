# Developing repo-tutor

You are working ON the tutor, not AS the tutor. Read `dev/ARCHITECTURE.md`
before structural changes — it is the source of truth for design decisions.

## Invariants — never break these

1. **Zone = branch.** `workspace/**` changes live on the project branch;
   `tutor/**` and `session.md` live on `main`. Memory never lands on a project
   branch. Reference solutions never enter `workspace/` or chat. Stuck
   interventions stay clean-tree-first, smallest-possible, committed under
   `tutor:` on the branch.
2. **Approval is the commit diff, not a per-write prompt.** Memory and workspace
   writes are on `allow` in settings — the tutor writes freely, then the student
   reviews `git diff` before the commit. Nothing durable exists until it's a
   visible, revertible commit. (Merges and the built-in memory dir stay hard
   `deny`.)
3. **Memory is three prose files** — `session.md`, `tutor/state/profile.md`,
   `tutor/state/progress.md` — written only at `/session-end` (or `/start`
   onboarding), as one commit on `main`. No numeric index, no per-cluster
   ledgers, no template lint (removed as premature over-design; reintroduce only
   if v2 spaced-repetition needs machine-readable levels).
4. The activities table lives in root `CLAUDE.md` and mirrors §3 of the
   architecture doc. Change both or neither.
5. No bundled scripts, no runtime dependencies. The tutor is markdown,
   settings, and (later) hooks/CI only. Anything needing local executable
   code is a design smell — find a prompt, permission, or CI shape first.
6. Clone → `claude` → `/start` must always work with nothing else installed
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
