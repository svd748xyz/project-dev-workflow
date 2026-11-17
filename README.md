# project-dev-workflow

**A development discipline for AI coding agents.** Restate the request before writing code. Split the work into layers you can actually verify. Make the agent prove it ran something before it says "done". Write down every pitfall so no future session repeats it.

Works with any agent that loads `SKILL.md` — Claude Code, Codex CLI, Cursor, OpenCode, Copilot agent mode, and other SKILL.md-compatible tools.

[中文说明](README.zh-CN.md) · MIT licensed · no scripts, no network calls, pure instructions

---

## The four failure modes it targets

1. **Coding before understanding.** The agent picks one reading of an ambiguous request and runs with it.
2. **Grading its own homework.** It writes the code, then declares it correct without executing anything.
3. **Single-viewpoint design.** It argues for its own architecture and never seriously considers being wrong.
4. **Amnesia.** The same environment quirk burns you again three sessions later, because nobody wrote it down.

## The loop

```
Entry:  single-section routing / light vs standard tier / autonomous mode
0. Preflight        read PITFALLS.md and project rules, check git status
1. Understand       restate the request, name your assumptions, wait for confirmation
2. Plan             split into layers, each with checkable completion criteria
3. Execute & verify one layer at a time; an independent verifier checks the criteria
4. Report           evidence, unverified items, accepted/rejected critiques, new pitfalls
Cross-cutting: adversarial review (5), proactive execution (6), pitfall logging (7)
```

**Two tiers.** *Light* — unambiguous, cheaply reversible, single verification step — skips the ceremony and just does the work. *Standard* runs the full loop. A light task that turns out to be bigger gets upgraded mid-flight. The rule the skill keeps repeating: discipline should match the size of the task; don't run a process for the process's sake.

## What actually makes it different

### 1. A verifier that did not write the code

The skill defines what the verifier **gets** (requirement baseline, this layer's criteria, changed files, `git diff` range, how to run the tests) and what it must **not** get (your implementation reasoning, your trade-off justifications, "I think it's fine"). It can run tests, builds and linters; it cannot modify source, commit, push, deploy or touch production. It reports **pass / fail / cannot verify** per criterion, each with a command or `file:line` as evidence. If you disagree with its verdict, you must overturn it with counter-evidence, not argument.

### 2. Claims are limited to what was executed

No run, no "done" — the phrasing is "implemented, not verified". Passing local tests is explicitly not the same as user acceptance. This one rule kills most of the confident nonsense agents produce in status updates.

### 3. Adversarial review with a fallback ladder

Critique subagent, then multi-perspective reviewers, then self-refutation plus pre-mortem when no subagents exist. Every critique comes with severity and confidence, and gets explicitly **accepted** (how it changes the plan) or **rejected** (why) — "it's probably fine" is not an allowed rejection reason. The prompt templates are in [`references/subagent-prompts.md`](references/subagent-prompts.md).

### 4. A pitfalls library split by scope

Pitfalls that any machine would hit go into the project's `PITFALLS.md` and get committed. Pitfalls that only exist on *this* machine — local shell, paths, broken toolchain, proxy settings — go into `~/.agents/PITFALLS.local.md` and never enter a repo. The skill also caps the file size (index at ~150 lines or 20 entries, archive stale items) so it stays cheap to load, and it offers to add "read PITFALLS.md first" to the project's `AGENTS.md` / `CLAUDE.md` so agents that never load this skill still see the history.

### 5. No black-box progress

On long builds the complaint is never "the last commit was bad", it's "I have no idea where this stands". The skill prescribes a periodic panorama: stage timeline and outputs, code/test scale, an evidence chain separating what actually ran from what was merely claimed, and a gap matrix to shipping — written to `docs/` and kept, not buried in chat.

### 6. Autonomous mode that still refuses irreversible actions

Say "just do it" and it stops asking: it records every self-made decision as an assumption and lists them all at the end. It still refuses to delete data, deploy, push, migrate, or call paid APIs without you.

## Install

Clone into your agent's skills directory so that `SKILL.md` sits at the root of one skill folder:

```bash
git clone https://github.com/svd748xyz/project-dev-workflow ~/.claude/skills/project-dev-workflow   # Claude Code, personal
git clone https://github.com/svd748xyz/project-dev-workflow .claude/skills/project-dev-workflow     # Claude Code, project-scoped
```

Windows (PowerShell):

```powershell
git clone https://github.com/svd748xyz/project-dev-workflow "$env:USERPROFILE\.claude\skills\project-dev-workflow"
```

Other SKILL.md-compatible agents read from their own skills directory — on setups that share one directory that is usually `~/.agents/skills/`. If your agent has no skills support at all, paste the contents of `SKILL.md` into the rules file it does read (`AGENTS.md`, `CLAUDE.md`, or equivalent).

Expected layout:

```
<skills-dir>/project-dev-workflow/
├── SKILL.md
├── LICENSE
├── README.md
└── references/
    ├── pitfalls-template.md
    └── subagent-prompts.md
```

Nothing to install, nothing to configure. Subagent support is optional but recommended: verification, critique and multi-perspective review are all better with it, and the skill degrades to a self-review role when it is unavailable.

## Usage

- **Full loop** — "follow the workflow", "按流程来", "规范开发". It also auto-triggers on multi-step development work.
- **Single section** — only the part you name: "critique this plan" / "挑挑刺" (5), "verify this" / "验收一下" (3), "log this pitfall" / "记一下踩坑" (7).
- **Autonomous** — "just do it" / "直接做": no confirmation stops, assumptions listed at the end, irreversible operations still held for you.

## Summary of the rules, in English

For readers deciding whether it's worth installing. The skill body itself is written in Chinese; this is the condensed English version of what it instructs.

1. Before starting: read the pitfalls file(s) and project rules; check `git status` and never overwrite the user's uncommitted work.
2. Restate ambiguous or destructive requests as: goal, deliverables, in scope, out of scope, constraints, acceptance criteria, my assumptions — plus the open questions you need answered.
3. After confirmation that restatement is the baseline; every later verification is judged against it. Widening or narrowing scope requires re-confirmation.
4. A timeout or unanswered question is **not** consent.
5. Plan in layers, each with criteria that can be checked ("`npm test` green", "X returns Y for input Z"), never "code quality is good".
6. Verify each layer before starting the next. Two failed fixes on the same criterion means stop and find the root cause instead of patching in place.
7. Report evidence, not adjectives. "Unverified / known limits" is a mandatory section.
8. Log each pitfall the moment it is solved: symptom, root cause, fix, applicability. Project-scoped or machine-local.
9. Small improvements inside the confirmed scope: just do them. Anything outside it, or that changes behaviour, or that adds a dependency: propose it with value and cost.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | Main file: entry routes (single-section routing, light/standard tier, autonomous mode), steps 0-7, and the evidence rules |
| `references/subagent-prompts.md` | Prompt templates: verifier, critic, multi-perspective review, consolidating multiple opinions |
| `references/pitfalls-template.md` | Skeletons and item format for project-level `PITFALLS.md` and machine-level `PITFALLS.local.md` |
| `LICENSE` | MIT |

## Honest limits

- It is a methodology, not a tool. There is no benchmark here and none is claimed; the quality of the outcome depends on the model actually following instructions.
- One 18 KB `SKILL.md` loads in full every time it triggers. If your context budget is tight, split it into several skills or delete the sections you don't use.
- The skill body is Chinese. Agents handle that fine; humans who want to read and edit it may not. A full English translation is not included — the summary above is the English bridge.
- Subagent-dependent paths (independent verification, parallel critics) are the strongest parts and are also the parts a weak environment cannot run. The fallbacks exist, but they are weaker by nature.

## License

MIT — see [LICENSE](LICENSE). Use it, fork it, ship it inside commercial products, no attribution required beyond keeping the notice.
