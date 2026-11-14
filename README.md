# Itential — Studio Builder

[![License](https://img.shields.io/badge/License-GPL--3.0-blue.svg)](LICENSE)

A Claude skill that builds Itential workflows on the **Automation Studio canvas**, in your
browser, while you watch.

It plans first, works keyboard-first, takes a screenshot after every step, and asks
when it isn't sure.

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

- Chrome with the **Claude in Chrome** extension, connected to Claude Code.
- A logged-in Itential Platform session with access to the target project.
- For device work: a Gateway 5 cluster and an inventory.

---

## Install

| Tool | Install |
|---|---|
| **Claude Code** | `/plugin marketplace add itential/studio-builder` then `/plugin install studio-builder@studio-builder` |
| **Claude Code (manual)** | Copy `skills/studio-builder/` to `~/.claude/skills/` |
| **claude.ai** | Zip `skills/studio-builder/`, then **Settings → Capabilities → Skills → Upload** |

Check it worked: start a new session and type `/studio-builder`.

Update: `/plugin marketplace update studio-builder`.

---

## How to use it

1. Open the project or workflow in Automation Studio in Chrome.
2. In Claude Code, ask, for example:
   - "Build a workflow that runs `show version` on a list of devices and shows the results."
   - "Add an approval step before the config push in this workflow."
   - "This workflow fails at the NetBox task. Debug it."
3. Review the plan Claude posts and answer its open questions.
4. Watch it build. Take over at any time.
5. Confirm inputs before it runs anything.

---

## How it builds

| Principle | In practice |
|---|---|
| Plan first | No canvas changes until you approve the plan |
| Ask, don't guess | Unsure task, several matching instances, missing task, unknown output → asks |
| Keyboard first | `⇧T` adds tasks, `C` draws transitions, `Q` finds tasks |
| Verify every step | Screenshot before and after each action |
| Keep it small | `runCode` for data, Enable Query instead of `query` tasks, under 10 tasks per child |
| Loops | `childJob` with loop type `sequential` or `parallel`. No `forEach`. |
| Human steps | `InteractiveHTML` for review and input forms |
| Device work | GatewayManager `sendCommand` / `sendConfig` |
| Safety | No credentials, no deletes, no browser dialogs, no runs without your OK |

---

## Repository layout

```
.claude-plugin/
  marketplace.json                 marketplace manifest
  plugin.json                      plugin manifest
skills/studio-builder/
  SKILL.md                         what Claude follows: sections 1–9
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

Dev overrides team, team overrides org. Overrides can't remove the safety rules or the plan-first step.

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
