# Itential — Studio Builder

[![License](https://img.shields.io/badge/License-GPL--3.0-blue.svg)](LICENSE)

Describe the automation. Studio Builder plans it, builds it in Automation Studio, tests it,
and hands back a working workflow.

It works in the Studio session you already have open: no API keys, no export and import.
Every task, transition and input it sets is on the canvas, where you can change it or
take over at any point.

---

## Table of Contents

- [What it does](#what-it-does)
- [Prerequisites](#prerequisites)
- [Install](#install)
- [How to use it](#how-to-use-it)
- [How it builds](#how-it-builds)
- [Repository layout](#repository-layout)
- [Customization](#customization)
- [Contributing](#contributing)
- [License](#license)

---

## What it does

- Turns a request or design into a plan: workflows, tasks, inputs, outputs, open questions.
- Builds the approved plan on the canvas: adds tasks, wires success and error paths, fills inputs.
- Saves, validates, runs and debugs each workflow.

---

## Prerequisites

- Chrome with the **Claude in Chrome** extension (side panel), or Claude Code connected to it.
- A logged-in Itential Platform session with access to the target project.
- For device work: a Gateway 5 cluster and an inventory.

---

## Install

| Tool | Install |
|---|---|
| **Claude Code** | `/plugin marketplace add itential/studio-builder-skill` then `/plugin install studio-builder@studio-builder` |
| **Claude Code (manual)** | Copy `skills/studio-builder/` to `~/.claude/skills/` |
| **claude.ai / Claude in Chrome** | Download `studio-builder.zip` from the [latest release](https://github.com/itential/studio-builder-skill/releases/latest), then **Settings → Capabilities → Skills → Upload** |

Check it worked: start a new session and type `/studio-builder`.

### Update

| Installed via | Update |
|---|---|
| **Claude Code plugin** | `/plugin marketplace update studio-builder`, then restart Claude Code |
| **Claude Code (manual)** | `git pull` this repo and copy `skills/studio-builder/` over `~/.claude/skills/studio-builder/` |
| **claude.ai / Claude in Chrome** | Download `studio-builder.zip` from the [latest release](https://github.com/itential/studio-builder-skill/releases/latest). In **Settings → Capabilities → Skills**, remove the old `studio-builder` and upload the new zip. Open a new chat. |

Uploaded zips don't update themselves. Re-upload after every release.

---

## How to use it

1. Open the project or workflow in Automation Studio in Chrome.
2. Open the Claude side panel (or Claude Code) and ask, for example:
   - "Build a workflow that runs `show version` on a list of devices and shows the results."
   - "Add an approval step before the config push in this workflow."
   - "This workflow fails at the NetBox task. Debug it."
3. Claude posts a short plan with its assumptions, then builds the whole thing.
4. Watch it build. Correct an assumption or take over at any time.
5. It tests with `dryRun` and lab instances, and asks before touching production.

---

## How it builds

| Principle | In practice |
|---|---|
| Plan, then build | Posts a plan with assumptions, then builds end to end without stopping |
| Decide, don't stop | Picks sensible defaults (lab instances, `dryRun`), lists them, keeps going |
| Wiring | Every error path goes to a status task; `evaluation` gets success and failure |
| Proven, not complete | Checks each task's output after a run, not just the job status |
| Keyboard first | `⇧T` adds tasks, `C` draws transitions, `Q` finds tasks |
| Verify every step | Screenshot before and after each action |
| Keep it small | `runCode` for data, Enable Query instead of `query` tasks, under 10 tasks per child |
| Layout | Vertical: main path top to bottom, error paths in a side column |
| Loops | `childJob` with loop type `sequential` or `parallel`. No `forEach`. |
| Human steps | `InteractiveHTML` for review and input forms |
| Device work | GatewayManager `sendCommand` / `sendConfig` |
| Safety | No credentials, no deletes, no browser dialogs, asks before touching production |

---

## Repository layout

```
.claude-plugin/
  marketplace.json                 marketplace manifest
  plugin.json                      plugin manifest
skills/studio-builder/
  SKILL.md                         what Claude follows: sections 1–12
  references/
    shortcuts.md                   Studio keyboard shortcuts
    canvas-actions.md              key sequences for common canvas actions
    finding-tasks.md               searching the task palette
    platform-concepts.md           adapters, integrations, applications, assets
    variables.md                   inputs, outputs, Variable Source, Enable Query
    build-patterns.md              how to structure workflows
    run-code.md                    runCode for data manipulation
    human-tasks.md                 InteractiveHTML, QuickForm, ViewData
    device-tasks.md                sendCommand, sendConfig, runService, MOP
  custom/
    org/  team/  dev/              your overrides
CHANGELOG.md
LICENSE
```

---

## Customization

Add `.md` files to `skills/studio-builder/custom/`:
- `org/`: company-wide rules (naming, required approvals).
- `team/`: team conventions (default cluster, inventory, adapter instances).
- `dev/`: your own preferences.

Dev overrides team, team overrides org. Overrides can't remove the safety rules.

---

## Contributing

When you find a new canvas move or platform behaviour:
- add the key sequence to `references/canvas-actions.md`;
- update `references/shortcuts.md` if a release changes a key;
- add the rule to the reference file where a builder would look for it;
- add a line under **Unreleased** in `CHANGELOG.md`.

Keep the voice direct: short sentences, tables over prose, no history.

---

## License

GPL-3.0-or-later. See [LICENSE](LICENSE).
