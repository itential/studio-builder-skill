# Changelog

Versions match the plugin manifest version (`.claude-plugin/plugin.json`) and the GitHub release tag.

## Unreleased

- `studio-builder` now opens the Keyboard Shortcuts dialog (`?`) at the start of each session, checks it against `references/shortcuts.md`, and uses the dialog's keys when they differ.

## 0.1.0

- Added the `studio-builder` skill: builds Itential workflows on the Automation Studio canvas through Claude in Chrome. Plans first and waits for approval, works keyboard-first, verifies each step with a screenshot, and asks when unsure.
- References: Studio keyboard shortcuts, canvas actions, task palette search, platform concepts (adapters, integrations, applications), inputs/outputs and variables, build patterns, `runCode` for data manipulation, `InteractiveHTML` for human tasks, and GatewayManager device tasks (`sendCommand`, `sendConfig`, `runService`).
