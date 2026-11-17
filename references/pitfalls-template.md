# PITFALLS templates

Two kinds of log, chosen by the scope of the pitfall (the criterion is in §7 of SKILL.md):

| File | Scope | Committed |
|---|---|---|
| `PITFALLS.md` in the project root | project-level | yes, committed with the repository |
| `~/.agents/PITFALLS.local.md` | machine-level | no |

If the file does not exist yet, copy the content between the `---` markers in the matching template below to create it, then fill in the first entry. Add or remove categories as the situation requires; empty categories may be deleted.

## Project-level template (PITFALLS.md)

---

# Pitfall log

> Problems actually hit during development of this project. Read it once before starting work; append new entries as soon as a problem is solved; edit an existing entry directly when it is out of date or incomplete.
> Status: `solved` / `unsolved` / `obsolete` (keep the reason, move it to "Archive", do not delete).
> Problems that concern only one machine do not belong here; they go to that machine's `~/.agents/PITFALLS.local.md`.

<!-- Once the log exceeds ~20 entries, maintain an index here: - [category] title -->

## Dependencies and versions

## Build and deployment

## Code and frameworks

## Data and interfaces

## Project conventions

## Archive

---

## Machine-level template (~/.agents/PITFALLS.local.md)

---

# Local pitfall log

> Problems that concern only this machine (local shell, paths, tool installation, permissions, network proxy). Never committed to any repository.

## Environment and tooling

## Archive

---

## Entry format

```markdown
### <one-line title: what problem occurs under what circumstances>
- Date: YYYY-MM-DD | Status: solved | Scope: project-level
- Symptom: <the error or abnormal behaviour observed; one line of the key error text if useful>
- Root cause: <the actual cause, not the surface symptom>
- Fix / workaround: <exactly what to do; give the command or the code when possible>
- Applicability: <which OS, version, configuration, scenario triggers it>
- Ruled out: <optional; especially important while unsolved, so others do not retry the same dead ends>
```

## Examples

Project-level:

```markdown
### Frontend cannot read .env variables in a Vite project
- Date: 2026-11-16 | Status: solved | Scope: project-level
- Symptom: `API_BASE=...` is set in `.env`, but `import.meta.env.API_BASE` in frontend code is always `undefined`, with no error.
- Root cause: Vite only exposes variables prefixed with `VITE_` to client code; all others are filtered deliberately to avoid leaking server-side secrets.
- Fix / workaround: rename the variables the frontend needs with the `VITE_` prefix (e.g. `VITE_API_BASE`); restart the dev server after editing `.env`.
- Applicability: frontend projects built with Vite, with no custom `envPrefix`.
```

Machine-level:

```markdown
### bash points to a broken WSL install on Windows and every shell command fails
- Date: 2026-11-16 | Status: solved | Scope: machine-level
- Symptom: every bash command fails with `Bash/Service/CreateInstance/MountDisk/HCS/ERROR_FILE_NOT_FOUND`.
- Root cause: the bash on PATH is the WSL launcher, and the disk file ext4.vhdx of the corresponding WSL distribution has been deleted.
- Fix / workaround: use PowerShell instead; or repair / unregister that WSL distribution, or install Git Bash and put it ahead of WSL on PATH.
- Applicability: Windows, where WSL was installed but the distribution is corrupted or deleted.
```
