# Changelog

Versions match the plugin manifest version (`.claude-plugin/plugin.json`) and the GitHub release tag.

## Unreleased

## 0.3.0

- Rewrote `SKILL.md` as phases with outcomes: Discover, Choose tasks, Plan, Create, Add tasks, Wire, Fill inputs, Save, Prove, Parent, Report.
- Native tasks first: added `app-map.md` (which app does what, with search words) and a pick order (native task → adapter → gateway service → `runCode` / `sendCommand`).
- Added `platform-summary.md`: a read-only snippet that lists this platform's tasks by app, adapters, integrations, gateway clusters, inventories and projects, so the build uses what's actually there instead of a fixed list.
- Rewrote `finding-tasks.md` and `canvas-actions.md` from a live build: Blank Project template, opening the Workflow card, tasks land to the right so drag them into the column, build in run order, deleting and connecting links, code editors that auto-close brackets, `runCode` timeout defaulting to 1 second and testing scripts in the editor.

## 0.2.1

- Fixed workflows coming out horizontal: Studio places each `⇧T` task to the right, so the skill now moves every new task into the column below the last one as it builds, looks for a toolbar auto-layout first, and checks the whole workflow reads top to bottom before saving.

## 0.2.0

- `studio-builder` now plans then builds end to end without stopping: it picks defaults (lab instances, `dryRun`, a new project if none is open), lists them as assumptions, and stops only for a login screen, a missing task or production runs.
- New Wiring rules section: one link per target, so error paths go to a **Set status: error** task instead of straight to `workflow_end`; error paths on every adapter, gateway and `runCode` task; regex in `contains`; Job source only for workflow inputs and outputs.
- Run checks: a `complete` job isn't proof. Check each key task's output, `runCode` return code, and `success` on every node. Fix validation warnings too.
- Reporting: no click-by-click narration; short updates at milestones and a final list of assumptions.
- Removed API-only guidance (typing `$var`, `adapter_id` as a value) in favour of the task panel's Variable Source and dropdowns.

- `studio-builder` now builds workflows vertically: main path top to bottom in one column, error paths in a side column, no lines crossing tasks. New section 7 (Layout); later sections renumbered.

- `studio-builder` now opens the Keyboard Shortcuts dialog (`?`) at the start of each session, checks it against `references/shortcuts.md`, and uses the dialog's keys when they differ.

## 0.1.0

- Added the `studio-builder` skill: builds Itential workflows on the Automation Studio canvas through Claude in Chrome. Plans first and waits for approval, works keyboard-first, verifies each step with a screenshot, and asks when unsure.
- References: Studio keyboard shortcuts, canvas actions, task palette search, platform concepts (adapters, integrations, applications), inputs/outputs and variables, build patterns, `runCode` for data manipulation, `InteractiveHTML` for human tasks, and GatewayManager device tasks (`sendCommand`, `sendConfig`, `runService`).
