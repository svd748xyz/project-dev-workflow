# Panorama report

The most common complaint on long projects is black-box progress: increment-by-increment reports leave the user unable to judge where the whole effort stands. A panorama answers "where are we, from zero to now?".

## When

- On request ("where are we", "give me the full picture", "overall status").
- Periodically on long projects: roughly every few completed layers, at the end of each multi-session milestone, or before a release decision.
- Not for light-tier or single-session standard tasks — the §4 report already covers them.

## Where

Write it to a document under `docs/` (or the project's existing documentation directory), e.g. `docs/progress-panorama.md`, and update the same file each time instead of creating new ones. Announce it the first time it is created. In the chat, give only the conclusion and the key table, and link the document.

## Contents

```markdown
# Panorama — <project>, as of <YYYY-MM-DD>

**Conclusion**: <one or two sentences: overall state and the biggest gap to delivery>

## Stage timeline
| # | Stage / layer | Output | Status (verified / implemented, not verified / pending) | Evidence |
|---|---|---|---|---|

## Scale
- Code: <files / lines changed or total, by area>
- Tests: <count, what they cover, last run result and date>

## Evidence chain
| Claim | Actually run / inspected | How (command, file:line, verifier) |
|---|---|---|
<list what was merely claimed separately from what was exercised>

## Gap matrix to delivery
| Requirement-baseline item | State | What is missing | Blocking? | Owner (agent / user) |
|---|---|---|---|---|

## Open decisions and risks
- <pending user decisions, self-made assumptions not yet confirmed, residual risks>
```

## Rules

- The same evidence rules apply as in §3: an item that was not run or inspected is "implemented, not verified", never "done".
- Keep it factual. No self-assessment ("going great", "nearly there") without the gap matrix to back it.
