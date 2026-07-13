# repo-tutor: Architecture

A coding tutor that is a git repository. Clone it, run `claude`, and work. The repo holds the tutor's rules, its memory of you, your projects, and your assessment history. No install, no server, no database.

**The three guarantees** (everything else serves these):
1. **The tutor never touches the student's code invisibly.** Hands-off by default (hint ladder); interventions only at explicit request, clean-tree-first, committed separately under `tutor:` — visible in history, one revert from gone.
2. **Assessment is real code review.** You submit PRs; the tutor reviews with line comments; you respond and revise; you merge.
3. **The tutor's memory of you is yours.** Plain markdown, versioned, human-editable. `git log` is the learning record.

---

## 1. Principles

- **P1 — The repo is the tutor.** All state is versioned files. Fork the repo, fork the tutor.
- **P2 — Skills are the interface.** No CLI. Every operation is a user-invoked skill (`/start`, `/onboard`, `/new-project`, `/drill`, `/submit`, `/session-end`). Clone → `claude` → `/start` is the entire onboarding.
- **P3 — No local runtime, ever.** The tutor is markdown, permission settings, and server-side CI. No bundled scripts, no interpreter requirements — if Claude Code runs, the tutor runs, including on fresh Windows. Determinism comes from templates, permission gates, and CI lint, not local code.
- **P4 — Enforced, not requested.** Every rule that matters has a mechanical enforcement layer (§4). CLAUDE.md explains the rules; it does not enforce them.
- **P5 — Chat is informal, PRs are formal.** Conversation = hints and discussion. Pull requests = assessment, preserved in history.
- **P6 — CLAUDE.md is a constitution, skills are statutes.** Constitution stays under ~80 lines; procedure lives in skills, loaded on demand.

## 2. Repo layout

**Template vs instance, and how memory files ship:** the public repo is a GitHub template. The two durable-memory files ship as **skeletons** — `profile.md` and `progress.md` carry their headings but no student data, *filled in* at onboarding. `session.md` never ships: its *absence* is how `/start` detects a fresh clone and triggers onboarding ("stub" stays reserved for logic-free code scaffolds in `workspace/`). A student's clone is their private instance; pulling upstream merges cleanly because upstream only touches `.claude/`, `dev/`, and root docs, while instances only touch `tutor/state/`, `workspace/`, `tutor/solutions/`, `tutor/archive/`.

Three zones by **ownership**, plus config:

```
repo-tutor/
├── CLAUDE.md                  # constitution
├── README.md                  # student-facing front door
├── session.md                 # NOT shipped — its absence signals a fresh clone; created by /onboard, rewritten by /session-end
├── .claude/
│   ├── settings.json          # allow/deny rules (§4)
│   └── skills/                # the entire command surface
│       ├── start/ onboard/ new-project/ session-end/ drill/ submit/ review/ teach/
│       └── (each: SKILL.md + optional references/)
├── workspace/                 # ZONE: student work — lives on project branches
│   ├── projects/<name>/       #   scaffold + brief + tests + code + data,
│   │                          #   one directory; committed as visible diffs
│   └── quickfire/             #   student's drill answers
├── tutor/                     # ZONE: tutor-owned — lives on main
│   ├── solutions/<name>/      #   reference solutions & answer keys —
│   │                          #   the one thing git history can't hold
│   ├── state/                 #   tutor memory — three prose files + scratch
│   │   ├── profile.md         #     who they are (skeleton, filled at onboarding)
│   │   ├── progress.md        #     what they know: Solid / Shaky / Next up
│   │   ├── reviews/           #     mirror of PR verdicts + scorecards
│   │   └── staging/           #     mid-session jots, consumed by /session-end
│   └── archive/               #   history: drill records, retired solutions
├── dev/                       # working ON the tutor (this doc, dev/CLAUDE.md)
```

Why zones: ownership is answered by the first path segment — `workspace/` is the student, `tutor/` is the tutor — and the two zones map onto git branches (§4): `workspace/**` on `project/*`, `tutor/**` + `session.md` on `main`. The student's daily loop lives entirely in `workspace/projects/<name>/`; `tutor/` is backstage.

## 3. Activities

The agent is always in exactly one activity, recorded in `session.md`. Switches are explicit in conversation. Collapsed from the original five modes — the mode system's main job was gating writes, and that job now belongs to the branch model (§4), so the surface simplifies to three verbs the student can actually hold in their head.

| Activity | Purpose | Commits land on |
|---|---|---|
| Learn  | explain, hint, drill, plan          | main |
| Build  | design a project, work on it        | project branch |
| Review | mark a PR (independent blind agent) | main + PR |

Always-on rules (all activities): **zone = branch** — `workspace/**` changes on the project branch, `tutor/**` + `session.md` on `main`, memory never on a project branch; stuck interventions only on explicit request (clean tree, smallest edit, `tutor:` commit on the branch, logged); never produce the solution the student is meant to write (hint ladder: question → concept → pseudocode → stop) except via an explicit "I give up" flow, logged to staging as a gap; remember things by appending to `tutor/state/staging/`, consolidated only at `/session-end`; the student merges PRs, never the tutor.

## 4. Enforcement: the branch model + three rings

The load-bearing enforcement is the **branch model**, not per-write prompts. `workspace/**` changes live on `project/<name>` branches; `tutor/**` and `session.md` live on `main`; the tutor drives the switching. Session zero proved that a prefix taxonomy without a branch model collapses — memory commits piled onto the student's feature branch and poisoned the PR diff. Zone = branch fixes that at the root: the two ownership zones are physically separated by git, and a `project/*` branch that only ever touches `workspace/**` cannot carry memory into a PR.

1. **Settings allow/deny rules** (`.claude/settings.json`): Write/Edit on `workspace/**` and `tutor/**` are on `allow` — the tutor writes freely and the **student's approval is reviewing the `git diff` before the commit**, not a dialog per file. This is the deliberate reversal of the original ask-gate, which fatigued the student with ~7 forced prompts per session-end and could not be cleared by accept-all (an `ask` rule outranks it by design). Merging (`git merge` / `gh pr merge`) stays hard-denied; the built-in memory directory stays denied by path and disabled at the feature level (`autoMemoryEnabled: false`, §6).
2. **The commit diff as the gate**: nothing durable exists until it is a commit. Every memory write, scaffold, and intervention lands as a visible, revertible commit the student sees. Approval moved from the write to the commit — same oversight, one review instead of many prompts.
3. **PreToolUse hook** (optional, stage 2+): would catch workspace writes attempted via shell that path rules miss, and could enforce zone = branch mechanically (reject a `tutor/` write while on a `project/*` branch). Hooks are local executable code, so this bends the no-runtime principle — add only if real sessions show leakage.
4. **CI (GitHub Actions)** (stage 3): on every push, reject a `project/*` diff that touches `tutor/` (the mechanical backstop for zone = branch), validate commit-prefix conventions and layout integrity — deterministic, server-side, zero local setup. (Pattern validated by claude-dojo's log-before-tick CI check.)

Commit prefixes: `student:` and `tutor:` are the two the student sees — `tutor:` tags both stuck-intervention edits on the branch and authoring commits (e.g. reference solutions) on `main`; `memory:` and `review:` are internal tutor commits on `main`. The numeric-level ledger system and its template-lint were removed as premature (§6).

## 5. The assessment loop

```
student: branch → commit "student: ..." → /submit (push + gh pr create)
tutor:   /review → fetch diff (gh pr diff), run tests, line comments +
         verdict (gh pr review), scorecard mirrored to tutor/state/reviews/
student: respond in thread, push fixes, loop → student merges
```

- Verdicts: approve / request-changes + short scorecard (correctness, design, style, tests).
- Review runs blind to hint history: `/review` in-session spawns a **marker subagent** (fresh context: diff + brief + tests + rubric only). If invoked headlessly (see §7), isolation is free — a `claude -p` run only contains what it's given.
- v2: reverse-review — tutor authors a deliberately flawed branch, the *student* reviews it, tutor grades the review. Code reading is the undertrained skill.
- No-GitHub fallback: identical review format written to `tutor/state/reviews/<branch>.md`.

## 6. Memory

Three prose files, all on `main`, written only at session-end:

- `session.md` — where the student is: date, active project + its branch, next focus, topics touched. Its *absence* is how `/start` detects a fresh clone.
- `tutor/state/profile.md` — who the student is, not what they know: background, goals, failure modes to design against, working preferences. Four fixed sections, dated append-only bullets. Session zero showed onboarding's richest output is non-topic knowledge.
- `tutor/state/progress.md` — what the student knows, as plain prose in three sections: **Solid** (shown independently, clearest signal a reviewed PR), **Shaky** (the growth edge), **Next up** (the ordered path). No scores, no schema; every line traces to something that happened.

**What was removed and why:** the original design had a numeric index (`topics.yaml`, levels 0–5), per-cluster evidence ledgers (`topics/<cluster>.md`), a syllabus file, and a template-lint discipline. It was a cathedral for a tool that had run one project — the leveling machinery was the source of session-end ceremony and the prompt fatigue. Cut to three prose files. The numeric/machine-readable layer returns in v2 **only if** spaced repetition needs it — not before there's data to feed it.

**Single write point:** all durable memory is written only during session-end consolidation (onboarding ends by invoking it), as one reviewable commit — the student approves the diff, not each file. **Claude Code's built-in memory directory (`~/.claude/projects/**/memory/`) is banned** (constitution rule 4): unversioned and ungated, it silently breaks guarantee 3. Observed writing there unprompted in session zero — the ban stays explicit. Enforced by **`autoMemoryEnabled: false`** (disables the feature, verifiable via `/memory`) plus `deny` rules on the path as defense-in-depth. A shell write still slips past path rules until the stage-2 hook exists.
- Mid-session: agent appends jots to `tutor/state/staging/`.
- `/session-end`: agent switches to `main`, writes the three files, rewrites `session.md`, clears staging, shows the diff, commits `memory:`, and switches back to the student's branch.
- Spaced repetition (v2): drill selector weights topics by staleness. No algorithm machinery until real progress data exists.

## 7. Headless & automation (optional layer)

Nothing requires it, but any skill can be driven without opening a session:

```bash
claude -p "/review PR 4" --allowedTools "Read,Bash(gh pr *),Bash(pytest*)" \
  --max-turns 15 --output-format json
```

- Runs on the same Claude Code install and login. As of mid-2026, `claude -p` and Agent SDK usage draw from subscription usage limits (the separate credit pool was paused June 15, 2026) — re-check the Help Center before relying on it; policy can shift.
- **Hazard**: if `ANTHROPIC_API_KEY` is set in the environment, `-p` bills per-token to the API account instead. Any wrapper script must strip it. Don't use `--bare` (skips OAuth, CLAUDE.md, and skills).
- Natural extension: a GitHub Action running `/review` on PR-open, so submission triggers review automatically.
- A branded `tutor` wrapper command remains possible later; it would be ~50 lines around `claude -p`. Not v1.

## 8. Content: bespoke first, packs later

**Bespoke authoring is the core (v1).** The `/new-project` skill designs projects *with* the student, grounded in `progress.md` and `profile.md`: solid concepts as foundation, growth-edge concepts as the deliberate focus, at most one untouched concept per project. The tutor drives the git setup (§4): on clean `main`, write and commit the reference solution to `tutor/solutions/<name>/`, then branch `project/<name>` and write the scaffold — brief.md (with a milestone-1 implementation order, the warm-up on-ramp), logic-free stubs, failing-for-the-right-reason tests — into `workspace/projects/<name>/` as visible commits the student reviews and start-commits themselves. No pristine copy is kept — the start commit IS the pristine copy; reset is `git checkout`. The solution stays on `main`, never on the branch or in chat. Placement at onboarding seeds `progress.md` so authoring is calibrated from session one.

**Packs and registry are a distribution layer (stage 4)** — a way to share briefs that worked, not the primary content source:

- Registry = second repo; each pack = directory with `manifest.json` (name, type, topics, difficulty) + payload; `index.json` for search.
- Install = an `/add-pack` skill: fetch the pack, write its scaffold into `workspace/` via the same scaffold handout as bespoke projects (new-project §4), solution into `tutor/solutions/`. Community contribution = PR to registry; pack CI runs tests against the bundled reference solution.
- Solutions ship in packs, honor system (a self-directed learner cheats only themselves). Revisit if this ever becomes classroom-grade.
- Skills are packs too: swap in a harsher `review` rubric the same way. Customization = edit files or install packs.
- Distribution note: plugin-marketplace packaging (as claude-tutor uses) doesn't fit the core product — a plugin can't ship the workspace you live in. The repo *is* the product; a plugin form could later exist for the skills alone.

## 9. Prior art & positioning

| | teaches | interface | memory | writes your code? | assessment |
|---|---|---|---|---|---|
| learn-claude-code | Claude Code itself | /course skill | course progress | n/a | guided build |
| claude-dojo (Aru9) | Claude Code itself | /daily /quiz /boss | repo files + CI checks | n/a | quizzes, boss tasks |
| claude-tutor (kirilxd) | any topic (quiz-first) | plugin, natural language | JSON in ~/.claude | n/a | MCQ/SM-2 quizzes |
| **repo-tutor** | **programming, by building** | **repo + skills** | **versioned markdown** | **never (enforced)** | **real PR review** |

Lead the README with the three guarantees; they are what nothing else has.

## 10. Build order

Each stage must survive a real study session before the next starts.

1. **Skeleton + constitution** — this tree, CLAUDE.md, settings rules, skills including /new-project and placement. First project authored live with the student. *Proves: activities, hint ladder, and bespoke authoring hold.* (Session zero ran here and forced the branch-model + commit-gate + memory-simplification redesign now in §3/§4/§6.)
2. **Memory + branch discipline** — zone = branch and the commit-diff gate exercised over real sessions; add the stage-2 hook / CI zone check if leakage appears. *Proves: memory and PRs stay clean without per-write prompts.*
3. **Assessment loop** — /submit + /review with gh, marker subagent, reviews mirror, GitHub Action. *Proves: the PR protocol is pleasant enough to use.*
4. **Drills + packs** — topic-weighted /drill, registry, /add-pack.
5. **v2** — reverse-review, spaced repetition, `tutor` wrapper, help-adjusted marking.

## 11. Risks

- **Tutor drift in long sessions** — the known failure mode of every tutor agent. Mitigations: short constitution, activity confirmation at /start, hooks. Measure before adding machinery.
- **Marking inconsistency** — rubric lives as a reference file inside the review skill; every verdict mirrored to tutor/state/reviews/ so marker behavior can be diffed over time; skill evals once transcripts exist.
- **Hook friction** — over-aggressive Ring 2 makes sessions miserable. Log rejections, tune.
- **Solo-PR setup friction** — needs a GitHub remote + gh auth; a /doctor check inside /start should verify and explain. Local fallback exists.
- **Scope magnetism** — the registry, dashboards, and spaced repetition all attract effort. The activities table + branch model + commit gate + PR loop are the product. Everything else is a pack.
