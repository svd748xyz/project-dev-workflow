---
name: project-dev-workflow
description: Development discipline for AI coding agents — confirm the request, plan in verifiable layers, verify with evidence (independent verifier for risky work), attack key decisions before committing to them, and log pitfalls to PITFALLS.md. Use for multi-step development work — features touching several files or steps, bugs that need investigation, refactors, architecture or approach choices. Also use alone for "follow the workflow", "run this properly", "verify this", "critique this plan", "log this pitfall", "update the pitfalls", running only the matching section. Skip for Q&A and single obvious edits.
metadata:
  version: "1.2.0"
---

# Project Dev Workflow

A working discipline against five failures: coding on a misread request; declaring work done without running it; defending a design from one viewpoint; doing only what was named; repeating a pitfall that was already solved. Process is a cost, not a goal — match its weight to the task.

```
Entry: route single-section requests · pick a tier · interactive or autonomous
0. Preflight   — pitfall logs, project rules, workspace state
1. Understand  — restate; wait for confirmation only when a trigger applies
2. Plan        — layers with checkable completion criteria (bugs: reproduce first)
3. Verify      — every layer, with evidence; independent verifier for risky work
4. Report      — evidence, unverified items, assumptions, suggestions, pitfalls
Throughout: adversarial review (§5), proactive execution (§6), pitfall logging (§7)
```

## Entry

### Single-section requests

When the user names one stage only, run that section alone:

| User says | Run |
|---|---|
| critique / review this plan / is this approach OK | §5 only |
| verify this / check whether it's correct | §3 verification only, on the object the user named. If the criteria are unclear, align on them first |
| log this pitfall / update the pitfalls | §7 only |
| follow the workflow / run this properly | the full workflow, standard tier or higher |

Single-section runs do not declare a tier.

### Tiers

Pick a tier before starting and state it in one line in the first reply, e.g. `Tier: standard — touches API and UI`.

| Tier | When | What it adds |
|---|---|---|
| **Light** | All hold: one reasonable reading; easily reversible (no data deletion, public interface or external impact); concentrated, and one check decides the result | §0 preflight, do it, verify with one real check, §7 if a pitfall appears, short report. No restatement or layering |
| **Standard** | Everything that is neither light nor critical | Restatement (§1), layered plan (§2), self-verification with written evidence (§3), full report (§4) |
| **Critical** | Any of: irreversible operation or external impact; data model, public interface or data migration change; change spanning several modules where a wrong direction is expensive; user-facing release | Confirmation before any edit, §5 review of the plan, independent verifier on risky layers and on the final delivery |

Unsure between two tiers: pick the higher. If a task outgrows its tier mid-way (drags in other modules, ambiguity appears, an irreversible step is needed, the same criterion fails twice), upgrade immediately and do the missing steps retroactively. The bug-reproduction rule (§2) applies in every tier.

### Autonomous mode

When the user explicitly says "just do it / don't ask me / you decide" (whichever other skill that wording also triggers), or the run is unattended:

- §1 still writes out "my understanding" but does not wait. Each open decision takes the recommended option and is recorded as a **self-made assumption**.
- Irreversible or external operations — deleting data, deploying, pushing, migrating data, paid API calls — are still **not** performed: skip them and list them in the report. Autonomous mode is not consent to `git commit` either; §2's commit rule still applies.
- The final report lists every self-made assumption, so the user can correct them.
- An instruction that only governs pacing once a plan is agreed ("keep going", "report at the end"), e.g. from an autonomous-execution skill, does not enable this mode. It removes check-ins between already-agreed steps; it does not skip the confirmation §1 requires, and the holds on irreversible operations and on commits still apply.

## 0. Preflight

- **Pitfall logs**: if the project root has `PITFALLS.md`, read it before planning — the index first if it has one, then the relevant entries. Also read `~/.agents/PITFALLS.local.md` if it exists. Cite task-relevant entries by title in the restatement or plan.
- **Project rules**: follow `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, `CONTRIBUTING.md` or similar files.
- **Workspace state**: in a git repository, run `git status` first. Pre-existing uncommitted changes belong to the user: do not overwrite them or mix them into your changes. Outside git, warn before risky changes or back up the files you will touch.
- **Specs** (only when the task rests on several specs, or specs revised more than once): scan for stale status markers (a "draft" that is already implemented), older specs with no "superseded by" pointer, and conflicting rules across documents. Raise conflicts before implementing; do not silently pick one. Give your review findings their own ID prefix so they never collide with the spec's IDs.

### Files this skill writes

- At any time, even before confirmation, announcing it the first time: `PITFALLS.md`, `~/.agents/PITFALLS.local.md`.
- Once work has started, without asking, announcing it the first time: the plan/progress file for a long task (the host's todo/task facility if it has one, otherwise the project's existing notes or `docs/` location — name it in the report so the user can keep or delete it) and the panorama document (§4).
- Throwaway files (scratch scripts, experiment output) go outside the repository or in an ignored scratch directory, and are removed when done.
- Any other file outside the requirement baseline — editing an existing one (`AGENTS.md`, CI config, unrelated docs) or adding a new one — is an out-of-scope suggestion (§6): ask first.

## 1. Understand and confirm

Building the literal reading of a request and getting it wrong costs far more than one round of questions.

### When to wait for confirmation

Wait for the user's confirmation before editing anything if any holds:
- the request has two or more reasonable readings that lead to materially different results;
- the user gave a goal, not an instruction ("make login nicer");
- the task is critical tier.

Unsure whether a request is ambiguous: treat it as ambiguous.

Otherwise, in the standard tier, post the restatement and proceed; the user can interrupt. Decisions are handled by materiality, wherever they come up (restatement, plan, mid-task): a decision that would materially change the result means the request is ambiguous — ask it under "Decisions I need from you" and wait. A minor choice is not asked: record the recommended option under "My assumptions" and proceed; the user can override. Light tier skips restatement. Autonomous mode never waits (see Entry).

### Before confirmation

Homework only: read code and docs, run read-only commands (state, versions), and run existing tests **only if they are known to be side-effect free** — no database writes, snapshot updates, fixture rewrites or network calls. No file edits (except pitfall logs), installs, configuration changes or other side effects.

### Restatement format

Do the homework first. Reconstruct the requirement in your own words and expose your assumptions so the user can see at a glance where you are wrong.

```markdown
**My understanding**
- Goal: <the problem to solve / effect to achieve, one or two sentences>
- Deliverables: <what will exist at the end>
- In scope: <what you will do>
- Out of scope: <what you explicitly will not do>
- Constraints: <stack, style, areas not to touch, limits>
- Acceptance criteria: <how the user will decide it is done, as observable as possible>
- My assumptions: <points you inferred, not stated>

**Decisions I need from you**
1. <question>? Option A: … / Option B: … (I lean toward X because …)
```

If the environment has a multiple-choice question tool, prefer it for the decisions, with a recommended option.

### After confirmation

- The confirmed version is the **requirement baseline**. Record it in the plan or todo list; every verification is judged against it.
- If the baseline proves wrong or the scope must change: stop, explain the change and its impact, re-confirm. Never widen or narrow scope silently.
- Then proceed through the plan without asking "shall I continue?" at each step. Stop only for a change of goal, an irreversible operation, or a trade-off only the user can make.
- **A timeout or unanswered question is not consent** (this concerns questions you actually asked, i.e. material ones): leave that item pending, continue only with work that does not depend on it, and repeat the pending item in the next report.

## 2. Layered plan

Split the task into layers, each a verifiable increment, each stating:

| Item | Requirement |
|---|---|
| Output | What specifically exists at the end of the layer |
| Completion criteria | Checkable conditions: "`npm test` is green", "endpoint X returns Z for input Y", "no horizontal scroll at 375px", "file A no longer references B". Uncheckable wording — "good quality", "basically works" — is forbidden |
| Verification | Self-check or independent verifier (criteria in §3) |

- **Bug fixes: the first layer is a reproduction** — a failing test, a command, or exact steps that show the bug. Change code only once it reproduces; the same reproduction passing is a completion criterion of the fix. If it cannot be reproduced, say so and agree on how to proceed instead of fixing blind.
- Critical tier, a plan of four or more layers, or any significant trade-off (technology, architecture, data model): one round of §5 review before finalizing the plan.
- Show the plan briefly, then start. If the plan raises a new material decision, ask and wait (§1); minor ones are recorded as assumptions.
- Long tasks: keep plan and progress in a todo or task file so work can resume after an interruption.
- **Checkpoints**: each verified layer is a checkpoint. No `git commit` or `git push` without the user's consent. If the user agrees to per-layer commits, commit after each passing layer in the project's existing message style.

## 3. Execute and verify

Verify each layer before starting the next. If a criterion fails, fix and re-verify. If the same criterion fails twice in a row, stop patching and find the root cause first (and consider a pitfall entry).

### Who verifies

Use an **independent verifier** when any holds:
- final delivery of a critical-tier task (for a single-section "verify this": a change that would qualify as critical);
- the layer is high-risk: security, data integrity, money, or a user-facing release;
- judging a criterion needs a multi-step manual procedure (operating a UI flow, comparing outputs by eye) rather than commands with a clear pass/fail result;
- the same criterion has already failed twice — by now your view of the code is shaped by your own fixes (this applies in every tier).

Otherwise self-verify, but write the evidence per criterion (command + output, or `file:line`) rather than ticking boxes from memory.

### Using the verifier

Its value is independence. Use the "Verifier subagent" template in [references/subagent-prompts.md](references/subagent-prompts.md), which fixes its permissions (local tests and builds yes; edits, commits, deploys no) and its output (**pass / fail / cannot verify** per criterion with evidence).

- **Give it**: the requirement baseline, the layer's criteria, relevant paths, how to run things, and the change range (`git diff` or a commit range) — a fact that helps it spot places that should have changed but did not.
- **Do not give it**: your implementation reasoning, trade-off justifications, or "I think it's fine now".
- Fixes are yours, so verifier and implementer never become the same agent again.

No subagent capability, or subagents forbidden: switch into the verifier role yourself, apply the same template, and write out the evidence.

### Work delegated to another coding agent

When another agent or CLI tool wrote the code, its "done" is a claim. Read the diff yourself; look for branches that only handle the happy path, tests that were weakened, skipped or deleted, and hard-coded results; re-run the narrowest relevant test subset yourself; send rework as `file:line` + the failed criterion + the expected assertion.

### Handling results

- **Failed criterion**: fix, then re-verify (same verifier or a new one).
- **You think the verifier is wrong**: overturn the verdict only with counter-evidence (command + output, `file:line`), and say in the report which verdict you overturned and why. Argument alone is not enough.
- **Problems outside the criteria**: only severe ones — failing the baseline, wrong data, crashes, security — block the next layer. List the rest in the report or as out-of-scope suggestions.

### Evidence rules

- Nothing not actually run or inspected may be called "done" or "passing". The permitted wording is "implemented, not verified".
- Local tests passing is not user acceptance; unit tests passing is not a working feature. State which level you actually verified.

## 4. Report

**Standard and critical tiers**, once all layers are done:

```markdown
**Completion**: <each item of the requirement baseline, in turn>
**Verification evidence**: <what ran and what it returned; what a verifier checked; verdicts you overturned and why>
**Unverified / known limitations**: <paths not exercised, known gaps>
**Self-made assumptions**: <decisions taken on the recommended option; in autonomous mode also the irreversible operations you skipped>
**Pending decisions**: <questions still unanswered, and the work waiting on them>
**Critique outcome**: <critiques received on key decisions; accepted / rejected and why>
**Out-of-scope suggestions**: <§6 proposals needing the user's decision, with value and cost>
**New / updated pitfalls**: <entry titles>
```

"Unverified / known limitations" is mandatory; if there are none, write "none" and why you are confident. Omit other empty lines.

**Light tier**:

```markdown
**What was done**: <one or two sentences>
**How it was verified**: <command + result, or what was inspected>
**Known limitations**: <only if any>
**Assumptions / skipped operations**: <only if any>
```

Facts and evidence only — no self-assessment such as "perfectly implemented" or "massive improvement".

**Long projects** (many layers or sessions): periodically, or when asked, write a zero-to-now panorama so progress is never a black box. Format and rules: [references/panorama.md](references/panorama.md).

## 5. Adversarial review of decisions

**When**: the plan of a critical-tier task or of four or more layers; technology choice, architecture, data model, interface design; a trade-off between candidate approaches; before an irreversible or expensive operation; whenever you are about to claim "this is the best approach". Not for naming or local style.

**How, by capability**:
1. **Critic subagent** — asked only to find problems (missed scenarios, hidden costs, failure conditions, simpler alternatives), each with severity and confidence.
2. **Multi-perspective reviewers** — for important decisions, 2–3 stances most relevant to the decision, in parallel.
3. **Self-refutation** when no subagent is available — write out explicitly: the strongest opposing case; a pre-mortem ("three months later this proved wrong — most likely why?"); the conditions under which the rejected option would be better.

Templates and the consolidation table: [references/subagent-prompts.md](references/subagent-prompts.md).

**Converging**: handle each critique — **accept** (state the change) or **reject** (a specific reason; "the impact is small" is not one). Check low-confidence critiques cheaply before deciding. Conclude with the choice, the reasoning and the residual risk. One or two rounds is enough. A trade-off only the user can settle goes to the user with options and costs.

## 6. Proactive execution

You are not only an executor. When you find a better approach, a latent hazard or a cheap improvement:

- **Inside scope, just do it, then report**: reversible changes that serve the confirmed goal without changing user-visible behaviour or public interfaces — missing tests, obvious duplication within planned modules, necessary comments, error handling that only surfaces failures more clearly (never swallows exceptions, adds retries or changes return values).
- **Bugs found in passing**: in scope only if they share the current task's root cause and the task is incomplete without them. Any other bug, however small, is an out-of-scope suggestion — fixing changes behaviour someone may depend on.
- **Outside scope, propose first**: anything beyond the baseline, changing product behaviour, adding a dependency, clearly costly or irreversible — list it with value and cost and wait.
- Main line first; extras after. While blocked on the user, advance work that does not depend on the answer.

## 7. Pitfall logging

Each pitfall should be hit once — across sessions, people and models.

**What counts**: a problem that took clearly longer than expected to locate; a non-obvious root cause (environment, hidden tool or library behaviour, versions, platform, encoding); a reasonable-looking assumption that proved wrong; an unwritten rule this project must follow. Typos and first-attempt fixes do not count.

**Where**:

| Scope | Criterion | Location |
|---|---|---|
| Project | Any machine or person would hit it: dependencies, frameworks, builds, conventions, data, interfaces | `PITFALLS.md` in the project root, meant to be committed |
| Machine | Only this machine: local shell, paths, broken tooling, permissions, proxy | `~/.agents/PITFALLS.local.md`, never committed |

Unsure: project-level, noting "may be machine-specific" under applicability.

**When**: immediately after solving it — batched notes are lost when a session dies. Found but unsolved: write it now with status `unsolved` and the directions ruled out, update it once solved. An existing entry is incomplete or stale: edit it, do not duplicate it.

**How**: entry format, file templates, size control and the handoff relationship are in [references/pitfalls-template.md](references/pitfalls-template.md). Never write secrets, tokens or personal data. After writing, tell the user `logged to PITFALLS.md: <title>`.

**First creation**: if the project has an agent rule file (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md`…), *propose* adding one line — "read PITFALLS.md in the repository root before starting work" — so agents that never load this skill still see the log. Edit it only after the user agrees; in autonomous mode list it as a suggestion.
