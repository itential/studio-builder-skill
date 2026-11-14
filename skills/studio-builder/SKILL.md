---
name: studio-builder
description: Build, edit, test and debug Itential workflows by driving the Automation Studio canvas in the user's browser (Claude in Chrome), keyboard-first, while the engineer watches. Use for phrases like "build this workflow in Studio", "add a task to the open workflow", "wire the error path", "fix this workflow on the canvas", or "run it in debug mode".
---

# Studio Builder

Build Itential workflows on the Studio canvas: plan first, keyboard first, screenshot after every step, save often.

## Sections

1. Scope
2. Reference files
3. Safety rules
4. Plan before you build
5. Operating loop
6. Build steps
7. Layout
8. Loops
9. Save, run and debug
10. Customization

---

## 1. Scope

**Input:** a request, a design, or a workflow the engineer has open.
**Output:** saved, validated, tested workflows in the target project.

---

## 2. Reference files

| File | Load when |
|---|---|
| `references/shortcuts.md` | Before the first canvas action |
| `references/canvas-actions.md` | Before an operation you haven't done this session |
| `references/platform-concepts.md` | When the request names adapters, integrations, templates or other assets |
| `references/finding-tasks.md` | Before adding any task |
| `references/variables.md` | Before filling any task's inputs |
| `references/build-patterns.md` | Before writing the plan |
| `references/run-code.md` | Before any data manipulation or transformation |
| `references/human-tasks.md` | Before adding an approval, review or input form |
| `references/device-tasks.md` | Before running commands or pushing config to devices |

---

## 3. Safety rules

1. Don't trigger browser dialogs. If a button may open one, tell the engineer to click it.
2. Don't type credentials. If a login screen appears, stop and ask the engineer.
3. Don't run a job until the engineer confirms the inputs.
4. Don't delete workflows, projects or templates.
5. After 2–3 failed attempts at the same action, stop and ask.

---

## 4. Plan before you build

Don't touch the canvas until the engineer approves a plan. Building the wrong thing
wastes more time than asking.

**1. Write the plan in chat:**

```
Workflow: Port Turn-Up - Push Config   (child of: Port Turn-Up)
Inputs:   deviceName, interface, vlan, dryRun
Outputs:  status, configPushed

#  Task                 Source           Purpose                     Error path
1  getDevice            NetBox adapter   Look up the device          → set status error
2  runCode              GatewayManager   Render config, build inventory
3  InteractiveHTML      WorkCenter       Show config, approve/reject  reject → end
4  sendConfig           GatewayManager   Push config                 → set status error
5  runCode              GatewayManager   Check success on every node

Open questions:
- Which NetBox adapter instance: netbox-lab or netbox-prod?
- Which gateway cluster and inventory?
```

**2. List every open question.** Ask instead of guessing when:
- you're not sure which task fits the step;
- more than one adapter, instance, cluster or inventory matches;
- a task you expected isn't in the palette;
- the output shape of a task is unknown and the next step depends on it.

**3. Wait for approval.** Build only what was approved. If something changes while
building (a task is missing, a field doesn't exist), stop, update the plan, and ask again.

---

## 5. Operating loop

**Start of every session: load the live shortcuts.**
1. Click an empty spot on the canvas so it has focus.
2. Press `?`. The **Keyboard Shortcuts** dialog opens with every key for this platform version.
3. Screenshot it and compare with `references/shortcuts.md`. If a key differs, use the dialog's key and tell the engineer so the reference can be updated.
4. Press `Esc` or click **Close**.

Every action is **screenshot → act → screenshot**.

1. **Screenshot.** Confirm the canvas has focus and no panel is open.
2. **Act.** Use a keyboard shortcut. Click only when no shortcut exists.
3. **Screenshot.** Confirm the change happened before the next step.

Core keys:
- `⇧T` → type task name → `Enter`: add a task after the selected one and connect it.
- `C` → arrows → `S` / `E` / `F` → `Enter`: draw a success, error or failure transition.
- `Q` → type name → `Enter`: select a task.
- `Enter`: open the selected task. `Esc`: close it.
- `⌘S`: save. `9`: zoom to fit.
- `?`: show all shortcuts.

---

## 6. Build steps

1. Open the project. Screenshot to confirm the name.
2. Create or open the workflow. Set its inputs and outputs from the plan.
3. Select `workflow_start`. Add each task in order with `⇧T`.
4. Add error paths with `C` → `E`.
5. Open each task with `Enter`, set each input's source (Static / Job / Task), `Esc`.
6. Line it up vertically (section 7).
7. `⌘S`. Check the validation icon. Fix any errors.
8. Run and check the result (section 9).

Build child workflows first, then the parent that calls them.

**Keep it small:**
- Data manipulation → one `runCode` task.
- Pull a value from an output → Enable Query on the input, not a `query` task.
- Show data or ask for input → `InteractiveHTML`.
- Read outputs straight from the task that made them. Don't copy them into variables.
- Aim for under 10 tasks per child workflow.

---

## 7. Layout

Build every workflow **vertically**, top to bottom. Use horizontal only if the engineer asks.

- **Spine:** `workflow_start`, the main tasks and `workflow_end` sit in one column, in run order.
- **Branches:** error and failure paths go in a column to the left or right of the spine and stay there.
- **Forks:** when a task splits into two paths, put one branch left and one right. Keep the spine empty until they rejoin.
- **Rejoin:** `workflow_end` and shared end steps go back on the spine.
- **Spacing:** keep rows tight and even. No big gaps.
- **No crossings:** no transition line runs across a task.
- **Existing workflows:** keep the engineer's layout. Only place new tasks.

**After adding tasks:**
1. `9` to zoom to fit. Screenshot.
2. If a new task landed off the spine, select it and move it with `⇧` + arrows.
3. Screenshot again to confirm the column lines up.

```
        workflow_start
              │
          Get Device ───error──▶ Set status: error
              │                          │
           runCode                       │
              │                          │
        InteractiveHTML ──failure──▶     │
              │                          │
          sendConfig ────error──▶────────┤
              │                          │
         workflow_end ◀──────────────────┘
```

---

## 8. Loops

Use `childJob` for all loops. Don't use `forEach`.

- **One item:** `childJob` with variables set, loop type empty.
- **Many items, one at a time:** `childJob` with `data_array` set, loop type `sequential`.
- **Many items, all at once:** `childJob` with `data_array` set, loop type `parallel`.

Each element of `data_array` becomes the child workflow's inputs.

---

## 9. Save, run and debug

**Save:** `⌘S`. Screenshot the validation icon. Fix errors before running.

**Run:** `⇧R`, fill the confirmed inputs, start. Screenshot the result.

**Debug:** `⇧D`, open the failing task, read its inputs and outputs, fix, save, run again. `Esc` twice to exit.

---

## 10. Customization

Read any `.md` in `custom/org/`, `custom/team/`, `custom/dev/`. Dev overrides team, team overrides org.
Customization can't remove section 3 or section 4.
