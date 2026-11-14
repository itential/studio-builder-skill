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
4. Plan, then build it all
5. Operating loop
6. Build steps
7. Wiring rules
8. Layout
9. Loops
10. Save, run and debug
11. Talking to the engineer
12. Customization

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
3. Test runs: use `dryRun: true` and lab instances. Ask before a run that changes real devices or production systems.
4. Don't delete workflows, projects or templates.
5. After the same failure twice, stop and ask. If two errors keep swapping as you fix one then the other, stop. You've misread how the task works.

---

## 4. Plan, then build it all

Post a short plan, then build the whole thing end to end. Don't wait for approval and don't stop to ask
about choices you can make yourself. The engineer is watching and can correct you at any time.

**1. Post the plan in chat:**

```
Project:  Port Turn-Up
Workflow: Port Turn-Up - Push Config   (child of: Port Turn-Up)
Inputs:   deviceName, interface, vlan, dryRun
Outputs:  status

#  Task                 Source           Purpose                        Error path
1  getDevice            NetBox adapter   Look up the device             → Set status: error
2  runCode              GatewayManager   Render config, build inventory → Set status: error
3  InteractiveHTML      WorkCenter       Show config, approve/reject    reject → Set status: error
4  sendConfig           GatewayManager   Push config                    → Set status: error
5  newVariable          WorkFlowEngine   Set status: success
6  newVariable          WorkFlowEngine   Set status: error

Assumptions (tell me to change any):
- NetBox instance: netbox-lab (only lab instance in the palette)
- Built in the open project "Port Turn-Up"
```

**2. Decide, don't ask.** Use these defaults and list them under Assumptions:

| If | Do |
|---|---|
| No project is open | Create one named after the use case and build in it |
| Several adapter instances, clusters or inventories match | Pick the lab or sandbox one. If none is marked lab, pick the first. |
| Only one matches | Use it |
| Not sure which task fits | Pick the closest by name and source, per `build-patterns.md` |
| A task output shape is unknown | Wire your best guess, then fix it after the first test run |
| A detail isn't specified (names, messages, formats) | Choose something sensible |
| A side effect could hit a real system | Add a `dryRun` input, default `true` |

**3. Stop only when you can't continue:**
- a login, SSO or MFA screen appears;
- a task the plan needs doesn't exist on the platform at all;
- the engineer has asked you to check with them first.

When you finish, list every assumption you made so the engineer can change any of them.

---

## 5. Operating loop

**Start of every session: load the live shortcuts.**
1. Click an empty spot on the canvas so it has focus.
2. Press `?`. The **Keyboard Shortcuts** dialog lists every key for this platform version.
3. Screenshot it and compare with `references/shortcuts.md`. If a key differs, use the dialog's key and tell the engineer.
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

1. Open the project from the plan. Screenshot to confirm the name. Never build outside a project.
2. Create or open the workflow. Set its inputs and outputs from the plan.
3. Select `workflow_start`. Add each task in order with `⇧T`, moving each one into the column below the last (section 8).
4. Add the two status tasks, **Set status: success** and **Set status: error**, both leading to `workflow_end`.
5. Draw every error and failure path to **Set status: error** (section 7).
6. Open each task with `Enter`, set each input's source (Static / Job / Task), `Esc`.
7. Line it up vertically (section 8).
8. `⌘S`. Check the validation icon. Fix errors **and** warnings.
9. Run and check the result (section 10).

Build child workflows first, then the parent that calls them.

**Keep it small:**
- Data manipulation → one `runCode` task.
- Pull a value from an output → Enable Query on the input, not a `query` task.
- Show data or ask for input → `InteractiveHTML`.
- Use the system's own task (adapter, GatewayManager) before `restCall`.
- Two workflows that look alike → one workflow with inputs.
- Aim for under 10 tasks per child workflow.

---

## 7. Wiring rules

Studio doesn't stop you getting these wrong.

- **One link per target.** A task can't have two transitions to the same task. Never draw an error straight to `workflow_end`. Send it to **Set status: error**, which leads to `workflow_end`.
- **Every adapter, gateway and `runCode` task gets an error path.** Validation doesn't check this. Without one, a failed task hangs the job.
- **Every `evaluation` gets success and failure paths.**
- **`contains` in `evaluation` is a regex.** Escape `. ( ) [ ] ? + *` in the value.
- **Job source is for workflow inputs and outputs only.** Setting an internal value's source to Job turns it into a required input. Point it at the task that produced it (source Task).
- **Each `data_array` item must hold every input the child workflow needs.**
- **Enable Query must fill the whole field.** Shape the data in `runCode` first.
- **Placeholders:** `<!name!>` in InteractiveHTML and command templates, `{{ name }}` in Jinja2.
- **Never use a Configuration Manager remediation task.**

---
## 8. Layout

Build every workflow **vertically**, top to bottom. Use horizontal only if the engineer asks.

- **Spine:** `workflow_start`, the main tasks and `workflow_end` sit in one column, in run order.
- **Branches:** error and failure paths go in a column to the left or right of the spine and stay there.
- **Forks:** when a task splits into two paths, put one branch left and one right. Keep the spine empty until they rejoin.
- **Rejoin:** `workflow_end` and shared end steps go back on the spine.
- **Spacing:** keep rows tight and even. No big gaps.
- **No crossings:** no transition line runs across a task.
- **Existing workflows:** keep the engineer's layout. Only place new tasks.

**Studio places new tasks to the right.** `⇧T` puts each new task to the right of the
selected one, so a workflow comes out horizontal unless you move every task. Do it as you go:

**After every task you add (required, not optional):**
1. The new task is selected. Press `⇧↓` to move it down below the task above it. Use the key's repeat count; start with 10 presses.
2. Press `⇧←` until it sits in the same column as the task above it.
3. Screenshot. Adjust with `⇧` + arrows until it's directly below, with a small even gap.
4. Only then add the next task.

**First, look for auto-layout.** Before building, hover each canvas toolbar icon and read its tooltip.
If one arranges the workflow (layout, arrange, tidy) and offers vertical or top-to-bottom, use it
after adding tasks instead of moving each one, then screenshot to check.

**Before saving:** `9` to zoom to fit, screenshot, and check the whole workflow reads top to bottom.
If any task is out of its column, fix it before `⌘S`. A horizontal workflow is not done.

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

## 9. Loops

Use `childJob` for all loops. Don't use `forEach`.

- **One item:** `childJob` with variables set, loop type empty.
- **Many items, one at a time:** `childJob` with `data_array` set, loop type `sequential`.
- **Many items, all at once:** `childJob` with `data_array` set, loop type `parallel`.

Each element of `data_array` becomes the child workflow's inputs.

---

## 10. Save, run and debug

**Save:** `⌘S`. Screenshot the validation icon. Fix errors and warnings before running.

**Run:** `⇧R`, fill test inputs (lab device, `dryRun: true`), start.

**Check the result.** A `complete` job isn't proof. Open each key task and read its output:
- adapter tasks: real data, no error body, nothing empty or null where data was expected;
- `runCode`: `return_code` is `0` and `stderr` is empty (a script error doesn't take the error path);
- `sendCommand` / `sendConfig`: `success` on **every** node;
- final `status` is `success`.

**Debug:** `⇧D`, open the failing task, read its inputs and outputs, fix, save, run again. `Esc` twice to exit.

---

## 11. Talking to the engineer

- Don't narrate clicks. Work quietly.
- Post updates only at milestones: plan posted, each workflow built, test result, done.
- Keep each update to a few lines.
- At the end: what was built, test results (the outputs you checked and what they held), and every assumption made.
- If the engineer says your explanation is wrong, drop it. Don't repeat it.

---

## 12. Customization

Read any `.md` in `custom/org/`, `custom/team/`, `custom/dev/`. Dev overrides team, team overrides org.
Customization can't remove section 3.
