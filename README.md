# Itential — Studio Builder

[![License](https://img.shields.io/badge/License-GPL--3.0-blue.svg)](LICENSE)

Describe the automation. Studio Builder plans it, builds it in Automation Studio, tests it,
and hands back a working workflow.

It works in the Studio session you already have open: no API keys, no export and import.
Every task, transition and input it sets is on the canvas, where you can change it or
take over at any point.

---

## Table of Contents

- [Prerequisites](#prerequisites)
- [Install](#install)
- [How to use it](#how-to-use-it)
- [Customization](#customization)
- [Contributing](#contributing)
- [License](#license)

---

## Prerequisites

- Chrome with the **Claude in Chrome** extension (side panel), or Claude Code connected to it.
- A logged-in Itential Platform session with access to the target project.

---

## Install

| Tool | Install |
|---|---|
| **Claude in Chrome / claude.ai** | Download **[studio-builder.zip](https://github.com/itential/studio-builder-skill/releases/latest/download/studio-builder.zip)**, then **Settings → Capabilities → Skills → Upload**. Don't use GitHub's **Code → Download ZIP**; that's the whole repo, not the skill. |
| **Claude Code** | `/plugin marketplace add itential/studio-builder-skill` then `/plugin install studio-builder@studio-builder` |

Start a new chat after installing.

### Update

| Installed via | Update |
|---|---|
| **Claude Code plugin** | `/plugin marketplace update studio-builder`, then restart Claude Code |
| **Claude in Chrome / claude.ai** | Download **[studio-builder.zip](https://github.com/itential/studio-builder-skill/releases/latest/download/studio-builder.zip)**. In **Settings → Capabilities → Skills**, remove the old `studio-builder` and upload the new zip. Open a new chat. |

Uploaded zips don't update themselves. Re-upload after every release.

---

## How to use it

1. Open the project or workflow in Automation Studio in Chrome.
2. Open the Claude side panel (or Claude Code) and ask, for example:
   - "Build a workflow that runs `show version` on a list of devices and shows the results."
   - "Add an approval step before the config push in this workflow."
   - "This workflow fails at the NetBox task. Debug it."
3. Watch it build. Step in at any time.

---

## Customization

Add `.md` files to `skills/studio-builder/custom/`:
- `org/`: company-wide rules (naming, required approvals).
- `team/`: team conventions (default cluster, inventory, adapter instances).
- `dev/`: your own preferences.

Dev overrides team, team overrides org. Overrides can't remove the safety rules.

---

## Contributing

Open an issue or a pull request. Add a line under **Unreleased** in `CHANGELOG.md`.

---

## License

GPL-3.0-or-later. See [LICENSE](LICENSE).
