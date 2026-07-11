# repo-tutor

**A coding tutor that lives in a git repo — and never touches your code invisibly.**

You learn to program by building real projects under code review. The tutor
designs projects, drills you, and reviews your pull requests like a senior
engineer. It remembers what you know in plain markdown you can read and edit.
Your `git log` is your learning record.

## Three guarantees

1. **The tutor never touches your code invisibly.** By default it hints,
   questions, and points at concepts — your hands stay on the keyboard.
   Its only workspace writes are the project handout (scaffolds you
   approve file-by-file and commit yourself) and, if you're truly stuck
   and ask, a smallest-possible fix that lands as its own `tutor:` commit
   — one `git revert` from gone. Your history shows exactly where help
   happened.
2. **Assessment is real code review.** You branch, commit, and open a PR.
   The tutor reviews with line comments and a verdict. You respond, revise,
   and merge — practicing the loop professionals live in.
3. **Its memory of you is yours.** What you know, where you struggle, and
   what's next live in `tutor/state/` as versioned markdown. Read it. Correct it.
   Fork it.

## Quick start

```bash
git clone <this-repo> my-tutor && cd my-tutor
claude
/start
```

That's it. No install beyond [Claude Code](https://docs.claude.com/en/docs/claude-code/overview)
(and [`gh`](https://cli.github.com) if you want the PR review flow).

## Commands

| Skill          | What it does                                            |
|----------------|---------------------------------------------------------|
| `/start`       | Resume where you left off (runs /onboard on first use)  |
| `/onboard`     | First-run setup: goals, placement quiz, syllabus         |
| `/new-project` | Design a project together, sized to what you know       |
| `/drill`       | Quickfire questions targeting your weak topics          |
| `/submit`      | Push your branch and open a PR for tutor review         |
| `/review`      | Tutor reviews your open PR with line comments           |
| `/session-end` | Consolidate what happened into the tutor's memory       |

Between commands, just talk — ask for hints, explanations, or a mode switch.

## How this differs from similar projects

- **learn-claude-code / claude-dojo** teach you *Claude Code the tool*.
  repo-tutor uses Claude Code to teach you *programming*. (Do theirs first
  if you're new to Claude Code — they're great on-ramps.)
- **claude-tutor** (the plugin) is quiz-first learning about any topic, with
  its data in `~/.claude/`. repo-tutor is build-first: real projects, real
  commits, real review, everything versioned in the repo you're holding.

## Layout

```
workspace/   YOURS — projects (brief, tests, code), your drill answers
tutor/       ITS — solutions/ (spoilers, kept out of your way), state/
             (its memory of you: plain markdown, gated writes), archive/
.claude/     the tutor's brain: constitution, skills, guardrails
dev/         hacking on the tutor itself → start at dev/ARCHITECTURE.md
```

## Status

Early. Stage 1 of 5 (see `dev/ARCHITECTURE.md` §10). Expect sharp edges.
