# project-dev-workflow

A skill that imposes a fixed development discipline on an AI coding agent. It targets five recurring failures: coding on a misread request, declaring work done without running it, defending a design from a single viewpoint, doing only what was literally asked, and re-learning a pitfall that was already solved in an earlier session.

Instruction-only: no scripts, no hooks, no network calls. Works with any agent that loads `SKILL.md`.

## Mechanism

| Stage | Rule | Failure it prevents |
|---|---|---|
| Tiering | Three tiers. Light (unambiguous, reversible, one check) skips the ceremony; standard restates and proceeds; critical (irreversible, external, data model / public interface, wide changes) waits for confirmation, reviews the plan adversarially and uses an independent verifier. A task that grows is upgraded mid-task | Process overhead on small tasks, and too little structure on risky ones |
| Requirement baseline | Restate the request as goal, deliverables, scope in/out, constraints, acceptance criteria and explicit assumptions. Wait for confirmation when the request is ambiguous, is a goal rather than an instruction, or is critical tier. A timeout or an unanswered question is not consent | Building the wrong thing, then discovering it after the work |
| Layered plan | Split into layers, each with criteria that can be checked by a command or an observation. Unfalsifiable criteria ("good quality", "basically works") are rejected. Bug fixes start with a reproduction | Progress that cannot be verified, scope that drifts silently, fixing a bug that was never reproduced |
| Verification | Verify each layer before starting the next, with written evidence per criterion. Two failed fixes on one criterion means stop and find the root cause | Patching symptoms in place |
| Verifier isolation | For high-risk layers, critical deliveries, multi-step judging, or after a criterion failed twice, the verifier receives the baseline, the criteria, the changed files and the diff — never the implementer's reasoning. It may run tests and linters; it may not edit, commit, push or deploy. Verdicts are pass / fail / cannot verify, each with a command or `file:line` | Self-grading; a verifier that only agrees |
| Delegated work | Code written by another agent or CLI tool is a claim: read its diff, look for weakened tests and happy-path-only branches, re-run the narrowest test subset | Trusting another agent's "done" |
| Evidence rule | Nothing may be called done or passing unless it was run. The permitted wording is "implemented, not verified". Local tests passing is explicitly not user acceptance | Confident status reports that were never exercised |
| Adversarial review | Critic subagent, then multi-perspective reviewers, then explicit self-refutation and pre-mortem when subagents are unavailable. Every critique is accepted with a change or rejected with a reason; "probably fine" is not a rejection reason | Single-viewpoint design that was never seriously attacked |
| Pitfall logging | `PITFALLS.md` committed with the project for anything any machine would hit; `~/.agents/PITFALLS.local.md` for anything specific to one machine. Entries written when solved, indexed past ~150 lines or 20 entries, obsolete entries archived rather than deleted | Repeated failures across sessions, and a log that becomes too expensive to read |
| Proactive execution | Improvements inside the confirmed scope are made and reported; anything outside it, behaviour-changing, dependency-adding or irreversible is proposed with value and cost | Doing only what was named, and unrequested scope creep |
| Autonomous mode | On "just do it", decisions are made from the recommended option and recorded as assumptions, all listed at the end. Deleting data, deploying, pushing, committing, migrating and paid API calls are still held for the user | Stalling on questions in unattended runs, or taking irreversible action unreviewed |
| File-write policy | The skill's own files (pitfall logs, progress file, panorama) may be created with an announcement; other files outside the baseline, existing or new, need consent | Silent edits to rule files and configuration |
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
| "follow the workflow", "run this properly", or any multi-step development task | Full workflow; tier chosen and stated in the first reply |
| "critique this plan" (§5) | Adversarial review only |
| "verify this" (§3) | Verification only; criteria aligned with the user first if unclear |
| "log this pitfall" / "update the pitfalls" (§7) | Pitfall entry only |
| "just do it" / "don't ask me" | Autonomous mode: assumptions recorded and reported, irreversible operations still held |

## Layout

```
project-dev-workflow/
├── SKILL.md                          workflow, tiers, entry routing, evidence rules
├── references/                       loaded on demand
│   ├── subagent-prompts.md           verifier, critic, multi-perspective, consolidation templates
│   ├── pitfalls-template.md          PITFALLS.md / PITFALLS.local.md skeletons, entry format, writing rules
│   └── panorama.md                   zero-to-now progress report format
├── evals/
│   └── scenarios.md                  manual behaviour scenarios to re-check after editing SKILL.md
├── LICENSE
└── README.md
```

## Requirements and limits

- Any agent that can load `SKILL.md`. Subagent support is optional; without it the skill falls back to self-verification and self-refutation, which are weaker than an independent verifier.
- No configuration and no dependencies. Nothing executes on its own; every rule depends on the host model following instructions.
- `SKILL.md` is about 18 KB (down from 22 KB in 1.1.0) and loads in full on each trigger; templates and detailed rules live in `references/` and are read only when needed.
- No benchmarks are published. `evals/scenarios.md` lists manual behaviour checks, not measured results; claims about the skill's effect would be unsupported.

## Changelog

- **1.2.0** — Three tiers (light / standard / critical) with confirmation and independent verification reserved for ambiguous or critical work; bug fixes start with a reproduction; generic rules for verifying work delegated to another agent (replaces the dependency on an external `omp` skill); file-write policy; side-effect-free rule for pre-confirmation tests; verifier may use throwaway files; spec-consistency preflight check; panorama and pitfall-writing details moved to `references/`; shorter trigger description; behaviour scenarios added.
- **1.1.0** — English-only repository; YAML-valid frontmatter.

## License

MIT. See [LICENSE](LICENSE).
