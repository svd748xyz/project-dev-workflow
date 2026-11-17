# project-dev-workflow

A skill that imposes a fixed development discipline on an AI coding agent. It targets four recurring failures: coding on a misread request, declaring work done without running it, defending a design from a single viewpoint, and re-learning a pitfall that was already solved in an earlier session.

Instruction-only: no scripts, no hooks, no network calls. Works with any agent that loads `SKILL.md`.

## Mechanism

| Stage | Rule | Failure it prevents |
|---|---|---|
| Tiering | Light tier (unambiguous, reversible, one verification step) skips the ceremony; everything else runs the full workflow. A light task that grows is upgraded mid-task | Process overhead on small tasks, and skipping structure on large ones |
| Requirement baseline | Restate the request as goal, deliverables, scope in/out, constraints, acceptance criteria and explicit assumptions; act only after confirmation. A timeout or an unanswered question is not consent | Building the wrong thing, then discovering it after the work |
| Layered plan | Split into layers, each with criteria that can be checked by a command or an observation. Unfalsifiable criteria ("good quality", "basically works") are rejected | Progress that cannot be verified, and scope that drifts silently |
| Verification | Verify each layer before starting the next. Two failed fixes on one criterion means stop and find the root cause | Patching symptoms in place |
| Verifier isolation | The verifier receives the baseline, the criteria, the changed files and the diff — never the implementer's reasoning. It may run tests and linters; it may not edit, commit, push or deploy. Verdicts are pass / fail / cannot verify, each with a command or `file:line` | Self-grading; a verifier that only agrees |
| Evidence rule | Nothing may be called done or passing unless it was run. The permitted wording is "implemented, not verified". Local tests passing is explicitly not user acceptance | Confident status reports that were never exercised |
| Adversarial review | Critic subagent, then multi-perspective reviewers, then explicit self-refutation and pre-mortem when subagents are unavailable. Every critique is accepted with a change or rejected with a reason; "probably fine" is not a rejection reason | Single-viewpoint design that was never seriously attacked |
| Pitfall logging | `PITFALLS.md` committed with the project for anything any machine would hit; `~/.agents/PITFALLS.local.md` for anything specific to one machine. Entries written when solved, indexed past ~150 lines, obsolete entries archived rather than deleted | Repeated failures across sessions, and a log that becomes too expensive to read |
| Proactive execution | Improvements inside the confirmed scope are made and reported; anything outside it, behaviour-changing, dependency-adding or irreversible is proposed with value and cost | Doing only what was named, and unrequested scope creep |
| Autonomous mode | On "just do it", decisions are made from the recommended option and recorded as assumptions, all listed at the end. Deleting data, deploying, pushing, migrating and paid API calls are still held for the user | Stalling on questions in unattended runs, or taking irreversible action unreviewed |
| Panorama | On long builds, a periodic zero-to-now report: stage timeline, scale, evidence chain (what ran versus what was claimed), gap matrix. Written to `docs/`, not left in chat | Black-box increments with no view of overall progress |

## Install

Clone so that `SKILL.md` sits at the root of one skill directory:

```bash
git clone https://github.com/svd748xyz/project-dev-workflow ~/.claude/skills/project-dev-workflow   # Claude Code, personal
git clone https://github.com/svd748xyz/project-dev-workflow .claude/skills/project-dev-workflow     # Claude Code, project-scoped
```

Windows (PowerShell):

```powershell
git clone https://github.com/svd748xyz/project-dev-workflow "$env:USERPROFILE\.claude\skills\project-dev-workflow"
```

Other SKILL.md-compatible agents read from their own skills directory; on setups that share one directory this is usually `~/.agents/skills/`. An agent with no skill support can still use it: paste `SKILL.md` into the rule file it reads (`AGENTS.md`, `CLAUDE.md`, or equivalent).

## Usage

| Invocation | Effect |
|---|---|
| "follow the workflow", "run this properly", or any non-trivial multi-step development task | Full workflow, standard tier |
| "critique this plan" (§5) | Adversarial review only |
| "verify this" (§3) | Verification only, against criteria aligned with the user first |
| "log this pitfall" / "update the pitfalls" (§7) | Pitfall entry only |
| "just do it" / "don't ask me" | Autonomous mode: assumptions recorded and reported, irreversible operations still held |

## Layout

```
project-dev-workflow/
├── SKILL.md                          workflow, entry routing, evidence rules
├── references/
│   ├── subagent-prompts.md           verifier, critic, multi-perspective, consolidation templates
│   └── pitfalls-template.md          PITFALLS.md and PITFALLS.local.md skeletons, entry format, examples
├── LICENSE
└── README.md
```

## Requirements and limits

- Any agent that can load `SKILL.md`. Subagent support is optional; without it the skill falls back to self-verification and self-refutation, which are weaker than an independent verifier.
- No configuration and no dependencies. Nothing executes on its own; every rule depends on the host model following instructions.
- `SKILL.md` is about 19 KB and loads in full on each trigger. Under a tight context budget, split it into several skills or delete the sections you do not use.
- No benchmarks or evaluation results are published for this skill. Claims about its effect would be unsupported.

## License

MIT. See [LICENSE](LICENSE).
