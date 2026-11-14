# runCode — Data Manipulation in One Task

Use `runCode` for any data manipulation or transformation. Don't chain `query`,
`merge`, `newVariable`, `evaluation` and loops to reshape data.

## What it is

A **GatewayManager** task that runs a Python script on an Itential Automation Gateway
(Gateway 5) cluster. No gateway service to register: the code lives in the task.

One `runCode` task replaced 17 utility tasks in a real NetBox-to-inventory workflow,
with the same output.

## When to use it

- Reshape a list (rename fields, pick fields, build new records)
- Filter, sort, group, de-duplicate
- Branch per item (vendor, status, type)
- Combine two responses (devices + interfaces)
- Build a request body, a report or HTML for a form

**Not for** calling other platform tasks per item (adapter calls, child workflows).
Use `childJob` for that.

## Add it on the canvas

1. Select the upstream task → `⇧T` → type `runCode` → pick the **GatewayManager** entry → `Enter`.
2. `Enter` to open it. Pick the cluster, then **Open Editor** for the code. In the editor:
   - write the script (it's an Ace editor: it adds closing brackets and quotes itself);
   - set **Timeout** to 30;
   - **Inputs** tab: sample data → **Run**. It runs on the gateway without running the workflow. Check **Outputs**;
   - **Save**, then **Close**.
3. Back in the panel, **+ Add Input Data Key** for each input. New keys appear at the top as `input_N`: click the name to rename it. Choosing source **Job** creates a job variable with the same name.

Fields:

| Field | Value |
|---|---|
| Cluster | Pick from the dropdown. Prefer lab. |
| Language | `python` |
| Code | The script (below) |
| Data | One key per input, each pointing at a task output or job variable |
| Timeout | Seconds, 1–1000. **Defaults to 1 second**: set 30. |
| Packages | Optional pip packages, e.g. `requests==2.31.0`. Leave empty if not needed. |

Then close the panel and `⌘S`.

## Script shape

Every script reads `data` from stdin and prints JSON as its last line:

```python
import sys, json

data = json.load(sys.stdin)          # the Data field, keyed as you set it
devices = data["devices"]

result = [
    {"name": d["name"], "ip": d["primary_ip"], "vendor": d["manufacturer"].lower()}
    for d in devices
    if d.get("status") == "active"
]

print(json.dumps({"devices": result}))   # last line = the task's output
```

- Debug prints go to `sys.stderr`, never stdout.
- Output is in `result.stdout_json` (parsed) and `result.stdout` (string). Use `stdout_json` for objects and arrays.

## Feed Data without extra tasks

In the **Data** field, point each key straight at the producing task's output. Use
**Enable Query** (below) to drill into it. No `merge` or `query` task needed.

## Check the result

A script error does **not** take the error path. The task completes and the
workflow carries on. After a run, open the task and check:
- `result.return_code` is `0`
- `result.status` is `completed`
- `result.stderr` is empty

To branch on it, add one `evaluation` on `return_code == 0`.

---

# Enable Query — Skip the query Task

Any task field that takes a job variable or task output has an **Enable Query**
checkbox in the task panel. Tick it and enter a path to pull one value out of that
output. This replaces a separate `query` task.

1. Open the downstream task → find the field → source **Task** or **Job** → pick the output.
2. Tick **Enable Query** → enter the path, e.g. `.body.results` or `.result.stdout_json.devices`.
3. Screenshot to confirm the path is shown under the field.

Rules:
- The query must fill the whole field. It doesn't work on one key inside a typed-in object.
  Shape the data in `runCode` so it already matches the field, then query the whole thing.
- A wrong path fails the task. Check the real output shape first: open the upstream task in a finished job and read its output.
