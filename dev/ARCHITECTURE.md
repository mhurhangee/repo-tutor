# repo-tutor: Architecture

A coding tutor that is a git repository. Clone it, run `claude`, and work. The repo holds the tutor's rules, its memory of you, your projects, and your assessment history. No install, no server, no database.

**The three guarantees** (everything else serves these):
1. **The tutor never writes your code.** Enforced by permissions and hooks, not by prompting.
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

**Template vs instance:** the public repo is a GitHub template — state zones ship empty, `session.md` doesn't ship at all (`/onboard` creates it, so upstream never conflicts with personal copies). A student's clone is their private instance; pulling upstream merges cleanly because upstream only touches `.claude/`, `dev/`, and root docs, while instances only touch `state/`, `workspace/`, `content/`.

Three zones by **ownership**, plus config:

```
repo-tutor/
├── CLAUDE.md                  # constitution
├── README.md                  # student-facing front door
├── session.md                 # NOT shipped — created by /onboard, rewritten by /session-end
├── .claude/
│   ├── settings.json          # Ring 1: permission deny rules
│   └── skills/                # the entire command surface
│       ├── start/ onboard/ new-project/ session-end/ drill/ submit/ review/ teach/
│       └── (each: SKILL.md + optional references/)
├── content/                   # ZONE: tutor-authored / installed packs
│   ├── projects/<pack>/       #   manifest.json, brief.md, scaffold/, tests/, solutions/
│   └── quizzes/
├── state/                     # ZONE: tutor memory — gated writes only
│   ├── topics.yaml            #   numeric index: cluster → level/assessed/evidence-count
│   ├── profile.md             #   who they are (created at onboarding; not shipped)
│   ├── syllabus.md
│   ├── topics/<concept>.md    #   fixed template: confidence / evidence / gaps / next
│   ├── reviews/               #   mirror of PR verdicts + scorecards
│   └── staging/               #   mid-session jots, consumed by /session-end
├── workspace/                 # ZONE: student work — tutor NEVER writes here
│   ├── projects/<name>/       #   instantiated from content scaffolds, one branch each
│   └── quickfire/             #   student's drill answers
├── dev/                       # working ON the tutor (this doc, dev/CLAUDE.md)
└── archive/                   # completed projects, moved wholesale
```

Why zones: the entire guardrail system reduces to two rules — *never write under `workspace/`* and *durable memory (`state/topics/`, `state/syllabus.md`) is written only by the consolidation script*. Short rules are enforceable rules.

## 3. Modes

The agent is always in exactly one mode, recorded in `session.md`. Mode switches are explicit in conversation and persisted at `/session-end`.

| Mode | Purpose | May write to |
|---|---|---|
| teach  | explain, hint, discuss        | state/staging/ |
| drill  | quickfire Q&A                 | content/quizzes/, state/staging/ |
| author | design projects & tests       | content/, state/staging/ |
| review | mark a PR / drill answers     | PR comments, state/reviews/, state/staging/ |
| plan   | syllabus work                 | state/syllabus.md (proposal → student approves), state/staging/ |

Always-on rules (all modes): never write under `workspace/`; never produce the solution the student is meant to write (hint ladder: question → concept → pseudocode → stop) except via an explicit "I give up" flow, which is logged to staging as a gap; remember things by appending to `state/staging/`, never by editing `state/topics/` directly; the student merges PRs, never the tutor.

## 4. Enforcement: four rings

1. **Settings deny rules** (`.claude/settings.json`): Write/Edit denied on `workspace/**`, `state/topics/**`, `state/syllabus.md`; `git merge` / `gh pr merge` denied. Coarse, zero-effort, exists from commit one.
2. **PreToolUse hook** (optional, stage 2+): would catch workspace writes attempted via shell (`echo >`, `sed -i`, heredocs) that path-based deny rules miss. Hooks are local executable code, so this bends the no-runtime principle — add only if real sessions show ring 1 leaking, and keep it to something shell-portable.
3. **The student as memory gate**: durable memory paths are on the settings `ask` list, so every proposed edit to `state/topics/`, `state/syllabus.md`, or `session.md` surfaces as a diff the student approves. Combined with `state/topics/_TEMPLATE.md` and the session-end skill's append-only evidence rule, this addresses the schema-drift failure mode (the one claude-tutor documents and patched post-hoc) with zero runtime.
4. **CI (GitHub Actions)** (stage 3): lints topic files against the template, validates pack manifests, commit-prefix conventions, and layout integrity on every push — deterministic enforcement that runs server-side, costing the student no local setup. (Pattern validated by claude-dojo's log-before-tick CI check.)

Commit prefixes: `student:` `tutor:` `quiz:` `review:` `memory:`. Tutor-side commits validated (Ring 2/4); student-side warn-only.

## 5. The assessment loop

```
student: branch → commit "student: ..." → /submit (push + gh pr create)
tutor:   /review → fetch diff (gh pr diff), run tests, line comments +
         verdict (gh pr review), scorecard mirrored to state/reviews/
student: respond in thread, push fixes, loop → student merges
```

- Verdicts: approve / request-changes + short scorecard (correctness, design, style, tests).
- Review runs blind to hint history: `/review` in-session spawns a **marker subagent** (fresh context: diff + brief + tests + rubric only). If invoked headlessly (see §7), isolation is free — a `claude -p` run only contains what it's given.
- v2: reverse-review — tutor authors a deliberately flawed branch, the *student* reviews it, tutor grades the review. Code reading is the undertrained skill.
- No-GitHub fallback: identical review format written to `state/reviews/<branch>.md`.

## 6. Memory

Three layers, one rule binding the first two:

- `state/topics.yaml` — the numeric index. Dynamic cluster-granularity tree (`python.control-flow`, `tooling.git`); demonstration-anchored levels 0–5 (4 requires a reviewed PR, 5 requires teaching it back); schema and anchors live as comments in the shipped skeleton. Machine-readable: drill targeting, project calibration, and any future gamification read this, not prose.
- `state/topics/<cluster>.md` — the evidence ledger: dated append-only bullets, gaps, next. Student-editable by design; corrections are signal.
- **The binding rule: a level changes only when a new evidence bullet justifies it, and the proposed edit cites the bullet.** A number without a receipt is vibes; the ledger keeps the index honest. No evidence, no entry: untested clusters are absent, never 0.
- `state/profile.md` — who the student is, not what they know: background, goals, failure modes to design against, working preferences. Session zero showed onboarding's richest output is non-topic knowledge; without this layer it leaks into ungated stores.

**Single write point:** all durable memory (index, ledger, profile, syllabus, session.md) is written only during session-end consolidation — onboarding ends by invoking it. **Claude Code's built-in memory directory (`~/.claude/projects/**/memory/`) is banned** (constitution rule 3): it is unversioned and ungated, so it silently breaks guarantee 3 and the template/instance model. Observed writing there unprompted in session zero — the ban must stay explicit.
- Mid-session: agent appends jots to `state/staging/`.
- `/session-end`: agent proposes template-conformant, append-only edits to topic files (student approves each via the ask-gate), rewrites `session.md`, clears staging, commits `memory:`. Evidence bullets are never rewritten — the history is the record.
- Spaced repetition (v2): drill selector weights topics by low confidence × stale evidence. No algorithm machinery until real topic files exist.

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

**Bespoke authoring is the core (v1).** The `/new-project` skill designs projects *with* the student, grounded in `state/topics/`: solid concepts as foundation, growth-edge concepts as the deliberate focus, at most one untouched concept per project. The tutor authors brief + logic-free scaffold + failing-for-the-right-reason tests into `content/projects/`; the *student* instantiates into `workspace/` and creates the branch (keeps the write-ring absolute; teaches branch workflow). Placement assessment at onboarding seeds initial topic confidences through the same consolidation gate, so authoring is calibrated from session one.

**Packs and registry are a distribution layer (stage 4)** — a way to share briefs that worked, not the primary content source:

- Registry = second repo; each pack = directory with `manifest.json` (name, type, topics, difficulty) + payload; `index.json` for search.
- Install = an `/add-pack` skill whose bundled script sparse-fetches the pack into `content/` and validates the manifest. Community contribution = PR to registry; pack CI runs tests against the bundled reference solution.
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

1. **Skeleton + constitution** — this tree, CLAUDE.md, settings deny rules, skills including /new-project and placement. First project authored live with the student. *Proves: modes, hint ladder, and bespoke authoring hold.*
2. **Memory gate** — ask-gate + template discipline exercised over real sessions; add CI topic-lint early if drift appears. *Proves: memory survives sessions without drift.*
3. **Assessment loop** — /submit + /review with gh, marker subagent, reviews mirror, GitHub Action. *Proves: the PR protocol is pleasant enough to use.*
4. **Drills + packs** — topic-weighted /drill, registry, /add-pack.
5. **v2** — reverse-review, spaced repetition, `tutor` wrapper, help-adjusted marking.

## 11. Risks

- **Tutor drift in long sessions** — the known failure mode of every tutor agent. Mitigations: short constitution, mode confirmation at /start, hooks. Measure before adding machinery.
- **Marking inconsistency** — rubric lives as a reference file inside the review skill; every verdict mirrored to state/reviews/ so marker behavior can be diffed over time; skill evals once transcripts exist.
- **Hook friction** — over-aggressive Ring 2 makes sessions miserable. Log rejections, tune.
- **Solo-PR setup friction** — needs a GitHub remote + gh auth; a /doctor check inside /start should verify and explain. Local fallback exists.
- **Scope magnetism** — the registry, dashboards, and spaced repetition all attract effort. The mode table + memory gate + PR loop are the product. Everything else is a pack.
