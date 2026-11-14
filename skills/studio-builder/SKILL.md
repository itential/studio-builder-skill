---
name: studio-builder
description: Build, edit, test and debug Itential workflows by driving the Automation Studio canvas in the user's browser (Claude in Chrome), keyboard-first, while the engineer watches. Use for phrases like "build this workflow in Studio", "add a task to the open workflow", "wire the error path", "fix this workflow on the canvas", or "run it in debug mode".
---

# Studio Builder

Turn a request into a working, tested workflow on the Automation Studio canvas.
Work through the phases in order. Each phase ends with an outcome; don't start the next
phase until the outcome is true.

## Phases

| # | Phase | Outcome |
|---|---|---|
| 0 | Discover | Platform summary in hand: apps, tasks, adapters, clusters, inventories |
| 1 | Choose tasks | Every step mapped to a task on this platform, native first |
| 2 | Plan | Plan and assumptions posted in chat |
| 3 | Create | Project and empty workflow open |
| 4 | Add tasks | All tasks on the canvas, in run order, in one column |
| 5 | Wire | Every path ends at END; every external task has an error path |
| 6 | Fill inputs | Every required input set |
| 7 | Save | Saved; validation shows no errors or warnings |
| 8 | Prove | Test run done and outputs checked |
| 9 | Parent | Parent workflow calls the tested child with `childJob` |
| 10 | Report | Engineer has what was built, test results and assumptions |

## Reference files

| File | Load in phase |
|---|---|
| `references/platform-summary.md` | 0 |
| `references/app-map.md` | 1 |
| `references/finding-tasks.md` | 1, 4 |
| `references/build-patterns.md` | 2 |
| `references/canvas-actions.md` | 3–7 |
| `references/shortcuts.md` | 3 |
| `references/variables.md` | 6 |
| `references/run-code.md` | 6, when using `runCode` |
| `references/human-tasks.md` | 6, when a person approves or fills a form |
| `references/device-tasks.md` | 1, 6, when touching devices |
| `references/platform-concepts.md` | Any time a term is unclear |

---

## Rules

**Safety (can't be overridden)**
1. Don't trigger browser dialogs. If a button may open one, tell the engineer to click it.
2. Don't type credentials. If a login screen appears, stop and ask.
3. Test with lab instances and `dryRun: true`. Ask before a run that changes real devices or production systems.
4. Don't delete workflows, projects or templates unless the engineer asks.
5. Same failure twice: stop and ask. Two errors that swap back and forth mean you've misread how the task works.

**Native tasks first.** The platform already has tasks for most jobs (backups, compliance,
rendering, tickets). Use them. Build from `runCode` or `sendCommand` only when no native task fits.

**Decide, don't stop.** Pick sensible defaults (lab instances, `dryRun`, a new project if none is
open), list them as assumptions, and keep going. Stop only for a login screen, a task that doesn't
exist on the platform, or a production run.

**Talk at milestones.** Don't narrate clicks. Post short updates when a phase finishes.

---

## 0. Discover

Run the platform summary in the Studio tab (`references/platform-summary.md`). It reads, never writes.
If it can't run, open the task palette (`⇧T`) and browse the app groups instead.

**Outcome:** you know which apps, tasks, adapters, gateway clusters and inventories exist.

## 1. Choose tasks

For each step of the request, pick a task in this order:
1. A native app task that does the job (`app-map.md` says which app to look in).
2. The adapter or integration for the external system.
3. A gateway service (`runService`).
4. Last: `runCode` for data, `sendCommand` for ad hoc commands.

**Outcome:** every step has a named task and its app, all present in the summary.

## 2. Plan

Post in chat:

```
Project:  Device Backup
Workflow: Device Backup - Single Device   (child of: Device Backup - All Devices)
Inputs:   deviceName
Outputs:  status

#  Task                Source                 Error path
1  Back Up Device      ConfigurationManager   → Set status: error
2  Set status: success WorkFlowEngine
3  Set status: error   WorkFlowEngine

Assumptions (tell me to change any):
- Built in a new project "Device Backup"
```

Then go straight on. Don't wait for approval.

**Outcome:** plan and assumptions posted.

## 3. Create

1. Projects → **New Project** → name → template **Blank Project** → Create.
2. **Add New Asset** → click the **Workflow** label, press `Enter` → name → Continue.
3. Load the live shortcuts: click the canvas, press `?`, compare with `shortcuts.md`, close.

**Outcome:** empty workflow open, START at the top, END at the bottom.

## 4. Add tasks

Add tasks **in run order**, top to bottom:
1. Select the last task in the column (START first).
2. `⇧T` → type a search word → `↓` to the right entry → `Enter`. The task is added to the right and connected.
3. **Drag** the new task to just below the previous one. It snaps into line.
4. **Click it again** (a drag leaves nothing selected), then repeat from step 2.

Adding from a task that already links onward makes a parallel branch, so don't insert in the middle.
Add the status tasks (**Set status: success**, **Set status: error**) at the end. For the error one: add it from the task that can fail, then click its new link and press `E`. Drag END up under the column.

**Outcome:** every task on the canvas in one column, in run order.

## 5. Wire

- Every adapter, gateway, `runCode` and Configuration Manager task: an **error** path to **Set status: error**.
- Every `evaluation`: success and failure paths.
- A task can't link twice to the same target. Error paths go to **Set status: error**, never straight to END.
- Both status tasks link to END.

How: select source → shift-click target → `C` (success). Change a link's type: click it → `E` / `F`. Delete: click it → `Delete`.

**Outcome:** every path ends at END; no task without an error path.

## 6. Fill inputs

Open each task (`Enter`). For each input pick **Static**, **Job** or **Task** (`variables.md`).
- Job only for workflow inputs and outputs. Internal values come from the task that made them.
- Pick from dropdowns where they exist (cluster, adapter instance, workflow).
- Workflow outputs: on the task's **Output** tab, tick **Store Output as Job Variable** and name it.
- Code and JSON fields are editors that add closing brackets and quotes themselves (`canvas-actions.md`).

**Outcome:** every required input set; nothing left on a default you didn't choose.

## 7. Save

`⌘S`. Check the validation icon in the top bar. Fix every error **and** warning.
`9` to zoom to fit; screenshot; the workflow reads top to bottom.

**Outcome:** saved, valid, vertical.

## 8. Prove

`⇧R`, lab inputs, **Run**. The job opens in Operations Manager; read the **Job Variables** tab. A `complete` job isn't proof. Check:
- native and adapter tasks: real data, no error body, nothing empty where data was expected;
- `runCode`: `return_code` 0, empty `stderr`;
- device tasks: `success` on every node;
- the final `status`.

On failure: `⇧D` debug mode, open the failing task, read its inputs and outputs, fix, save, run again.

**Outcome:** a run whose outputs you've checked and can quote.

## 9. Parent

For many items, build a parent: `childJob` → pick the child ("… from <Project>") → **Enable Looping** →
**Sequential** or **Parallel** → Loop Array from a Job variable (e.g. `devices`). Each item holds every
input the child needs. Never `forEach`. Prove it by opening each child job's Job Variables.

**Outcome:** parent tested over at least two items.

## 10. Report

A few lines: what was built (project, workflows), the test result (outputs checked and what
they held), and every assumption. Then stop.

---

## Customization

Read any `.md` in `custom/org/`, `custom/team/`, `custom/dev/`. Dev overrides team, team overrides org.
Customization can't remove the safety rules.
