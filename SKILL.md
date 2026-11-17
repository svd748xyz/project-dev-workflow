---
name: project-dev-workflow
description: Development discipline for projects — restate the request and get it confirmed before writing code; split work into layers with checkable completion criteria and verify each layer before starting the next (delegate to an independent verifier when warranted); subject key decisions to adversarial review by a subagent or by explicit self-refutation before committing to them; push forward autonomously within the confirmed scope and propose anything outside it; log every pitfall to PITFALLS.md as it is solved. Use when starting or continuing non-trivial development work — multi-step features, bugs spanning several steps, refactors, architecture or approach choices — even if the user does not name this skill. Also use for standalone requests such as "follow the workflow", "run this properly", "verify this", "critique this plan", "log this pitfall", "update the pitfalls" (Chinese aliases: 按流程来、规范开发、开始开发、帮我做个、实现一下、验收一下、挑挑刺、评审方案、记一下踩坑、更新踩坑), running only the matching section for those. Skip for pure Q&A and trivial one-line edits.
metadata:
  version: "1.1.0"
---

# Project Dev Workflow

A working discipline applied throughout a development project. It addresses these failures: an AI starts coding on a misread request; finishes writing and then declares its own work fine; argues for a design from a single viewpoint; does only what it was told; and repeats a pitfall it already hit last time.

The weight of the discipline should match the weight of the task: small tasks take the light tier — do not run a process for the process's sake.

The whole workflow:

```
Entry: single-section routing / tier decision (light · standard) / whether to work autonomously
0. Preflight      — read the pitfall log and project rules, confirm workspace state
1. Understand     — restate the request; act only after the user confirms
2. Plan           — split into layers, each with checkable completion criteria
3. Execute & verify — verify each layer by the criteria, self or via verifier; no pass, no next layer
4. Report         — evidence, unverified items, accepted/rejected critiques, suggestions, new pitfalls
Throughout: adversarial review (§5), proactive execution (§6), real-time pitfall logging (§7)
```

## Entry: decide how to use it

### Single-section invocation

When the user names one stage only, execute that section alone; do not run the whole workflow:

| User says | Execute |
|---|---|
| critique / review this plan / is this approach OK | §5 only |
| verify this / check whether it's correct | §3 verification only. The object is what the user specified; if the completion criteria are unclear, align on them with the user before verifying |
| log this pitfall / update the pitfalls | §7 only |
| follow the workflow / run this properly | the full workflow (standard tier) |

### Light and standard tiers

Decide the tier before starting, and state which one you picked in one line in your first reply.

**Light tier** — all three conditions hold:
- the request is unambiguous, with only one reasonable reading;
- it can be rolled back easily, with no data deletion, public interfaces, or external impact;
- the change is concentrated (one feature point, few files) and a single verification step decides the result.

The light tier skips restatement, layering and subagents, but still observes §0 preflight, the §3 evidence rules, and §7 pitfall logging, and closes with the short §4 report.

**Standard tier**: everything else. When unsure, use the standard tier.

If a light-tier task turns out to be more complex than judged (it drags in other modules, ambiguity appears, an irreversible operation is needed): upgrade to the standard tier immediately and do the restatement and confirmation retroactively.

### Autonomous mode

When the user explicitly says "just do it / don't ask me / you decide", or the task is an unattended long run in a non-interactive environment:

- §1 still writes out "my understanding", but does not wait for confirmation. Each item under "decisions I need from you" is decided by taking the recommended option, recorded as a **self-made assumption**, and work proceeds;
- operations that are irreversible or have external impact (deleting data, deploying, pushing, data migration, paid API calls) are still **not** performed; skip them, list them in the report, and leave them for the user;
- the final report lists every self-made assumption together, so the user can correct them afterwards.

## 0. Preflight

- **Pitfall log**: if the project root has `PITFALLS.md`, read it before planning; if the file maintains an index at the top, read the index first, then the entries relevant to the current task. If a machine-level log at `~/.agents/PITFALLS.local.md` exists, read that too (see §7 for scope). Mention task-relevant entries by name in the restatement or the plan.
- **Project rules**: if the project has `AGENTS.md`, `CLAUDE.md`, `CONTRIBUTING.md` or similar rule files, follow them as well.
- **Workspace state**: if the project is a git repository, check `git status` first. Uncommitted changes that already exist belong to the user: remember them, do not overwrite them, and do not mix them into your own changes. If it is not a git repository and the change is risky, warn the user first, or back up the files you are about to modify.

## 1. Understand and confirm

The user's wording usually omits context, mixes goals with means, and leaves constraints unstated. Coding to the literal wording and getting it wrong costs far more than one extra round of questions.

### When restatement and confirmation are mandatory

Any one of these makes restatement and confirmation mandatory (except in autonomous mode):
- the request has two or more reasonable readings, and the readings lead to materially different outputs;
- the user gave a goal rather than an instruction ("make login nicer");
- the work involves irreversible operations, external impact, data deletion, or changes to public interfaces or the data model;
- the change spans multiple modules, or is large enough that going in the wrong direction is expensive to undo.

Light-tier tasks may be done directly, with an explanation afterwards. When unsure, treat it as "confirmation required".

### What you may do before confirmation

You may do homework: read code and documentation, run read-only commands (inspect state, run existing tests, check versions). You may not: modify files, install dependencies, change configuration, or run any command with side effects.

### Restatement format

Do the homework before restating. A restatement reconstructs the requirement in your own words; it is not a recital of the user's sentence. Expose your assumptions so the user can see at a glance where you have it wrong.

```markdown
**My understanding**
- Goal: <the problem to solve / the effect to achieve, one or two sentences>
- Deliverables: <what will exist at the end: files, features, documents…>
- In scope: <what you will do>
- Out of scope: <what you explicitly will not do, to prevent scope creep>
- Constraints: <stack, style, areas not to touch, time/resource limits>
- Acceptance criteria: <how the user will decide it is done, as observable as possible>
- My assumptions: <points not stated that you inferred yourself>

**Decisions I need from you**
1. <question>? Option A: … / Option B: … (I lean toward X because …)
```

If the environment provides a multiple-choice question tool, prefer it for the "decisions I need" part, with a recommended option.

### After confirmation

- Treat the confirmed version as the **requirement baseline**. Record it in the plan or the todo list; every later verification is judged against it.
- If the baseline turns out to be wrong, or the scope must expand: stop, explain the change and its impact, re-confirm, and continue. Do not widen or narrow the scope silently.
- After the user confirms, proceed through the plan without coming back to ask "shall I continue?" at every step. Stop only for a change of goal, an irreversible operation, or a trade-off the user must decide.
- When using a question tool, **a timeout or an unanswered question is not consent**: leave that item pending, proceed only with the parts that do not depend on it, and list the pending items again in the next report. Do not quietly proceed on the recommended option.

## 2. Layered plan

Split the task into layers (stages); each layer is an increment that can be verified on its own. Each layer states three things:

| Item | Requirement |
|---|---|
| Output | What specifically exists at the end of this layer |
| Completion criteria | Checkable conditions, e.g. "`npm test` is green", "interface X returns Z for input Y", "no horizontal scroll at 375px width", "file A no longer references B". Descriptions that cannot be checked — "good code quality", "basically works" — are forbidden |
| Verification method | Self-check, or an independent verifier (see the criteria in §3) |

- If the plan involves significant trade-offs (technology choice, architecture, data model), run one round of §5 adversarial review before finalizing it.
- Show the plan to the user in brief. For a small plan already confirmed during restatement, start directly after showing it.
- For long tasks, write the plan and progress into a todo list or task file and keep it updated, so work can resume from the breakpoint if the session is interrupted.
- **Checkpoints**: each verified layer is a checkpoint. Do not `git commit` or `git push` without the user's consent. If the user agrees to commit per layer, commit once after each layer passes, following the project's existing commit message style.

## 3. Layer-by-layer execution and verification

Verify each layer before starting the next. If verification fails, fix it and verify again. If the same criterion fails twice in a row, stop and analyze the root cause first (and consider logging a pitfall) instead of patching in place.

### Self-check or an independent verifier

Any one of these means you should hand verification to an **independent verifier**:
- the layer is user-facing, the change surface is large, or the risk is high;
- the completion criteria require actually running, operating or comparing things to judge, and not a single command;
- it is the final delivery of a standard-tier task;
- you are deeply invested in your own implementation and are likely to see everything as correct.

Self-check is acceptable for: light-tier tasks; or small changes whose criteria a single command decides, with an obvious result.

### Using the verifier

The verifier's value lies in its independence: it must not inherit the implementer's reasoning or self-justification.

- **Give it**: the requirement baseline, this layer's completion criteria, the relevant file paths, how to run things, and the change range (`git diff` or a commit range). The change range is a fact, not a line of reasoning, and it helps the verifier find places that should have changed but did not.
- **Do not give it**: your implementation reasoning, your trade-off justifications, or anything like "I think it's fine now".
- **Permissions**: it may not modify source, configuration or documentation. It may run local verification commands (tests, builds, linters), and the resulting build artifacts are fine. Commands with external side effects are forbidden — deploying, data migration, `git commit` / `git push`, deleting data, calling production or paid APIs.
- Require it to check every criterion for real (run the command, read the code, inspect the output) and to report **pass / fail / cannot verify** per item with evidence, plus any problems it found outside the criteria, ordered by severity.
- Fixes are yours to make, so that the verifier and the implementer do not become the same person again.
- If the implementation was done by a CLI coding agent (omp / codex / claude-code), verify according to the Verification section of the `omp` skill: read the diff it produced, look specifically for condition branches that only cover the happy path, run the smallest subset independently with `--filter`, and send rework back as "file:line + pass/fail criterion + expected assertion".
- Prompt templates are in the "Verifier subagent" section of [references/subagent-prompts.md](references/subagent-prompts.md).

If the current environment has no subagent capability, or the user or host rules forbid subagents: switch to the verifier role yourself, check every criterion against the same template, and write out the evidence, rather than ticking boxes from memory.

### Handling verification results

- **Failed criteria**: fix, then verify again (continue with the same verifier or dispatch a new one).
- **You believe the verifier is wrong**: you must produce counter-evidence (a command and its output, a file:line) to overturn the verdict, and state in the report which verdict you overturned and on what basis. Do not talk yourself out of it by argument alone.
- **Problems found outside the criteria**: only "severe" ones — failing the requirement baseline, wrong data, crashes, security issues — block the next layer. Record the rest and list them in the report, or move them into out-of-scope suggestions.

### Evidence rules

- Anything not actually run or inspected may not be called "done" or "passing". The only permitted phrasing is "implemented, not verified".
- Local tests passing is not user acceptance. Unit tests passing is not a working feature. State which level you actually verified.

## 4. Final report

**Standard tier**, one report after all layers are complete:

```markdown
**Completion**: <each item of the requirement baseline, addressed in turn>
**Verification evidence**: <what was run and what it returned; which items a verifier checked; which verification verdicts you overturned and on what basis>
**Unverified / known limitations**: <paths not exercised, known gaps>
**Self-made assumptions**: <autonomous mode only: what you decided yourself, which irreversible operations you skipped>
**Critique outcome**: <which critiques the key decisions received, which were accepted and which rejected, and why>
**Out-of-scope suggestions**: <proposals from §6 that need the user's decision, with value and cost>
**New / updated pitfalls**: <titles of the entries written>
```

The "Unverified / known limitations" line is mandatory; when there genuinely are none, write "none" and explain why you are confident. The other lines may be omitted when empty.

**Light tier**, the short form:

```markdown
**What was done**: <one or two sentences>
**How it was verified**: <command + result>
**Known limitations**: <only if any>
```

The report states facts and evidence only. No self-assessment such as "perfectly implemented", "very elegant", "massive improvement".

**What users complain about most on long projects is black-box progress**: reporting only this increment leaves them unable to judge where the whole thing stands. Periodically — or whenever asked — deliver a panorama from zero to now: stage timeline and each stage's output, code and test scale, the evidence chain (what actually ran versus what was merely claimed), and a gap matrix to delivery or release. Write the panorama into a document under `docs/` (or the project's existing documentation directory) and keep it; do not leave it only in the chat. In the chat, give the conclusion and the key table, and point to the document for detail.

## 5. Adversarial review of decisions

A decision made from a single viewpoint usually sees only the benefits of its own option. Anything that needs judging should first be seriously attacked, then settled.

### When to review adversarially

- technology choice, architecture, data model, interface design;
- a trade-off between two or more candidate approaches;
- before an irreversible or expensive operation;
- the plan for a large implementation, before work starts;
- when you are about to claim "this is the best approach".

Everyday small decisions (naming, local style) do not need it.

### How, by capability

1. **Critic subagent**: hand the approach and its context to a subagent and ask it only to find problems — missed scenarios, hidden costs, simpler alternatives, conditions under which it fails. Do not ask for a balanced view, but require severity and confidence on each item.
2. **Multi-perspective subagents**: for important decisions, dispatch reviewers with different stances in parallel, each giving an independent opinion. By default pick the 2–3 stances most relevant to the decision, not every stance.
3. **Self-refutation when no subagent is available**: write out explicitly, step by step —
   - the strongest opposing argument: if I were the opponent, what is the most powerful case;
   - pre-mortem: assume that in three months this decision proved wrong — what is the most likely reason;
   - the option I rejected: under what conditions would it actually be better.

Prompt templates are in the "Critic subagent" and "Multi-perspective review" sections of [references/subagent-prompts.md](references/subagent-prompts.md).

### Converging

Handle each critique in turn: **accept** (state how it changes the approach) or **reject** (give a specific reason; "the impact is small" is not enough). For low-confidence critiques, verify cheaply where possible before deciding. The final conclusion states the choice, the reasoning, and the risks that remain. One or two rounds is usually enough — do not loop indefinitely. If a critique exposes a trade-off that only the user can settle, present the options and their costs to the user.

## 6. Proactive execution

You are not only an executor. If, while making progress, you find a better approach, a latent hazard, or an improvement that is cheap to make, act on it within these boundaries:

- **Inside the scope, just do it**: improvements that serve the confirmed goal, are reversible, and do not change user-visible behaviour or public interfaces (adding missing tests, removing obvious duplication within modules in the plan, adding error handling, adding necessary comments). Report them afterwards.
- **Bugs found in passing**: if they share the root cause of the current task and the task is not complete without fixing them, they are in scope. Every other bug, however small, goes into out-of-scope suggestions — fixing a bug changes behaviour, and the user may depend on the current behaviour or want it handled separately.
- **Outside the scope, propose first**: anything beyond the requirement baseline, anything that changes product behaviour, adds a dependency, is clearly costly, or is irreversible goes into "out-of-scope suggestions" with its value and cost, and waits for the user's decision.
- Proactive work must not hold up the main line: deliver the main line as planned first, then handle the extra improvements.
- While the current layer is blocked and waiting on the user, you may advance other work that does not depend on their reply instead of idling.

## 7. Real-time pitfall logging

Purpose: each pitfall is hit once. Across sessions, across people, across models, it stays visible.

### What counts as a pitfall

- a problem that took clearly longer than expected to locate;
- a non-obvious root cause (environment differences, hidden tool or library behaviour, version compatibility, platform quirks, encoding issues);
- an assumption or practice that looked reasonable and proved wrong;
- a rule this project must follow that the documentation does not state.

Ordinary typos and small problems fixed on the first attempt do not count.

### Where to write: decide the scope first

| Scope | Criterion | Location |
|---|---|---|
| Project-level | Any machine or any person would hit it: dependencies, frameworks, builds, project conventions, data and interfaces | `PITFALLS.md` in the project root, committed with the repository |
| Machine-level | Only this machine: local shell, paths, broken local tooling, permissions, proxy settings | `~/.agents/PITFALLS.local.md`, never committed to any repository |

When unsure, write it as project-level and note "may be machine-specific" under applicability.

### When to write

- Write **immediately** after the pitfall is solved; do not batch it for later — the session may be interrupted and batched notes are lost.
- Found but not yet solved: write an entry first with status "unsolved" and record the directions already ruled out; update the same entry once solved.
- An existing entry turns out to be incomplete or out of date: edit the original entry rather than adding a duplicate.

### How to write

- If the file does not exist, create it on the first pitfall using the matching template in [references/pitfalls-template.md](references/pitfalls-template.md).
- Each entry contains: title, date, status, scope, symptom, root cause, fix / workaround, applicability. Write it for someone with none of your current context.
- Append new entries under the matching category.
- **Control the size**: once the file exceeds roughly 150 lines or 20 entries, maintain an index at the top (one line per entry: title + category) and move obsolete entries to an "Archive" category at the end, so that preflight reading stays fast.
- Never write secrets, tokens or personal data into it; use placeholder descriptions instead.
- After writing, briefly tell the user "logged to PITFALLS.md: <title>" in the current reply.

### Making it visible to agents that did not load this skill

Only when this skill is triggered does an agent go and read `PITFALLS.md`. The first time a project-level `PITFALLS.md` is created, check whether the project has `AGENTS.md`, `CLAUDE.md`, `GEMINI.md` or another agent rule file; if so, **propose** adding one line — "read PITFALLS.md in the repository root before starting work". This modifies a project file, so change it only after the user agrees; in autonomous mode do not change it, and list it as an out-of-scope suggestion instead.

### Relationship to handoff documents

If `project-handoff` is also used: keep only the session-relevant points under "pitfalls and failure paths" in the handoff, and point to `PITFALLS.md`; `PITFALLS.md` is the project's long-term pitfall library.
