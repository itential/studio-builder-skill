# Inputs, Outputs and Variables

## How data moves

Every task has **inputs** (incoming) and **outputs** (outgoing). A task reads its
inputs from three places and hands its outputs to the tasks after it.

```
workflow inputs ──▶ task A ──output──▶ task B ──output──▶ workflow outputs
   (job variables)            (task output)
```

## Set an input in the task panel

Open the task (`Enter`). Each input has a **Variable Source**:

| Source | Use for | Example |
|---|---|---|
| **Static** | A fixed value typed in | `show version`, `60`, `true` |
| **Job** | A workflow input or shared value | `deviceName` |
| **Task** | The output of an earlier task | task *Get Devices* → `response` |

Pick the source, then pick the variable or task output from the list. Screenshot to confirm.

## Pull one value out of an output

Tick **Enable Query** under the input and enter a path, e.g. `.body.results` or
`.result.stdout_json.devices`. This replaces a separate `query` task.
The query fills the whole input. It can't fill one key inside a typed-in object.

## Workflow inputs and outputs

- **Inputs** are what the operator or parent workflow passes in. They become job variables.
- **Outputs** are what the workflow returns to the operator or parent.
- Set them in the workflow's input/output settings. Name them clearly: `deviceName`, not `d1`.

## Keep variables to a minimum

- Read a value straight from the task that produced it (Source: **Task**).
- Use source **Job** only for workflow inputs and outputs. An internal value set to Job becomes a required input.
- Don't add `newVariable` or `query` tasks just to hold or move a value.

## Objects and lists

An input set to **Static** holds a fixed value. You can't mix references into a Static object or list.

- Need an object or list built from other values? Build it in `runCode`, then set the input to source **Task** → the `runCode` task, with Enable Query.
- `runCode` **Data** and `runService` **Params** have one row per key, each with its own source. Set each row directly.

## Know the output shape before wiring

Run the upstream task once, open it in the finished job, and read its real output.
Wire the path from what you see. Adapter outputs are often
wrapped (`response.body`, `result.response`).
