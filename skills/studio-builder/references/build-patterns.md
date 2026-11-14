# Build Patterns

How to structure workflows well.

## 0. Keep it small
Fewer tasks are easier to read, run and debug. Before adding a task, ask:
- Can `runCode` do this along with the step before or after it?
- Can **Enable Query** on the next task's field replace this `query`?
- Can the next task read the producing task's output directly instead of copying it into a variable?

A typical child workflow: **get data → runCode → act → error path**. Aim for under 10 tasks.

| Instead of | Use |
|---|---|
| `query` → `merge` → `newVariable` chains | One `runCode` |
| A `query` task to pull one value | Enable Query on the field that needs it |
| `forEach` + per-item tasks | `runCode` (data only) or `childJob` loop (calls per item) |
| `ViewData` + `ViewHTML` | One `InteractiveHTML` |
| Copying outputs into job variables | Reference the task output directly |

## 1. Split into child workflows
- Build each child so it can run and be tested on its own.
- Build children first. The parent only calls children with `childJob`.
- Split any workflow that grows past about 20 tasks.
- Check whether a workflow that does the job already exists before building a new one.

## 2. Loops with childJob
| Need | Set on childJob |
|---|---|
| Run once | variables set, loop type empty |
| Run per item, one at a time | `data_array` set, loop type `sequential` |
| Run per item, all at once | `data_array` set, loop type `parallel` |

Each element of `data_array` becomes one child run's inputs, so each element must carry every input the child needs.

## 3. Handle errors
- Give every adapter, integration and gateway task an **error** path.
- In a child workflow, set a status at the end of each path:
  ```
  task ──success──▶ set status = "success" ──▶ end
       └─error────▶ set status = "error"   ──▶ end
  ```
- In the parent, after `childJob`, read the status and branch with `evaluation` (success / failure).

## 4. Shape data with runCode
- Do all data manipulation in one `runCode` task (GatewayManager, Python). See `run-code.md`.
- Pull values out of outputs with **Enable Query** on the field, not a `query` task.
- Don't chain `query`, `merge`, `newVariable` and `evaluation` to reshape data.

## 5. Human tasks with InteractiveHTML
- Use `InteractiveHTML` for review, approval and input forms. See `human-tasks.md`.
- Use `ViewData` only for a bare yes/no.
- Add an `autoApprove` input and an `evaluation` before the human task so unattended runs can skip it.
- To let an operator retry, use a **revert** transition back to the step to redo.

## 6. Network config changes
1. Test the CLI commands on the device first.
2. Command template (MOP) for pre-checks.
3. Jinja2 template to generate the config.
4. Push with `GatewayManager` send config (or the push workflow the engineer names).
5. Command template for post-checks.

## 7. Make it safe to test
- Add a `dryRun` input (default `true`) and skip side effects when it's set.
- Use the lab or sandbox adapter instance while building.

## 8. Lay out vertically
- Main path top to bottom in one column. Error and failure paths in a side column.
- No transition crossing a task. Keep the engineer's layout on existing workflows.

## 9. Name things clearly
- Workflows: `<Use case> - <Action>`, e.g. `Port Turn-Up - Push Config`.
- Task summaries: say what the step does, e.g. `Create change ticket`, not `createChangeRequest`.
