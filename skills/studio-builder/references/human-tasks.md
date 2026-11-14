# Human Tasks — Forms, Approvals and Reviews

Use **InteractiveHTML** for anything a person needs to see or fill in. Use `ViewData`
only for a bare yes/no with nothing to show.

## Pick the task

| Person needs to | Task | You get back |
|---|---|---|
| Review data and approve, choose options, tick items, type values | **WorkCenter `InteractiveHTML`** | `export`: each input's value, keyed by its `name`, plus which button was pressed |
| Act on each row of a list | WorkCenter `QuickForm` | `rows`: each row with the person's input |
| Plain yes/no, nothing to show | WorkFlowEngine `ViewData` | Which button (success / failure path) |

Don't use `ViewHTML`. It only displays and returns nothing.

## Add InteractiveHTML on the canvas

1. Select the upstream task → `⇧T` → type `InteractiveHTML` → pick the **WorkCenter** entry → `Enter`.
2. `Enter` to open it and fill:

| Field | Required | Value |
|---|---|---|
| Header | no | Title of the work item |
| Body | yes | An HTML page with your form. CSS inline or in a `<style>` block. |
| Variables | no | Fills `<!name!>` placeholders in the body |
| Success button | yes | e.g. `Approve` |
| Failure button | no | e.g. `Reject`. Leave empty for acknowledge-only. |

3. Wire both paths: success → continue, failure → stop or roll back.
4. `Esc`, `⌘S`.

## Body example

```html
<style>td{padding:4px 8px} table{border-collapse:collapse}</style>
<h3>Upgrade <!device!></h3>
<p>Current version: <!version!></p>
<p><label>Target version
  <select name="target">
    <option value="17.9.4">17.9.4</option>
    <option value="17.12.2">17.12.2</option>
  </select></label></p>
<p><label><input type="checkbox" name="backup_done"> Backup confirmed</label></p>
<p><label>Change ticket <input type="text" name="ticket"></label></p>
```

Result: `export = {"target": "17.9.4", "backup_done": true, "ticket": "CHG0039299"}`.

Rules:
- Every input needs a `name`. That name is the key in `export`.
- Placeholders `<!name!>` are filled as plain text from **Variables**.
- To show a table of runtime data, build the whole HTML in a `runCode` task and point **Body** at its output with Enable Query. One task builds the page, one task shows it.

## Skip approval in unattended runs

Add an `autoApprove` input. Put one `evaluation` before the human task:
`autoApprove == true` → skip ahead; otherwise → InteractiveHTML.
