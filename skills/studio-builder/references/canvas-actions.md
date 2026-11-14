# Canvas Actions

Measured on Automation Studio. Screenshot before and after each action.

## Create
| Action | How |
|---|---|
| New project | Projects → **New Project** → name → template **Blank Project** → Create. Without a template, Create does nothing. |
| New workflow in a project | **Add New Asset** → click the **Workflow** label, then press `Enter` (a plain click on the card doesn't open it) → name → Continue |
| What you get | START at the top, END at the bottom, palette open on the left |

## Add and place tasks
| Action | How |
|---|---|
| Add after a task | Select it → `⇧T` → search → `↓` → `Enter`. Lands to the **right**, connected with success. |
| Put it in the column | **Drag** it below the previous task. One drag; it snaps into line. (`⇧` + arrows moves only ~8px a press.) |
| Next task | **Click the task again** after a drag, then `⇧T`. Without a selected task the new one isn't connected. |
| END | Starts far down the canvas. Drag it up under the last task. |
| Insert in the middle | Don't. Adding from a task that already links onward adds a parallel branch. Build in run order. |
| Spacing | Toolbar **Spread Tasks** slider. It changes spacing only; there's no auto-layout. |

## Links
| Action | How |
|---|---|
| Connect two tasks | Click source → shift-click target → `C`. The link is success. |
| Change a link's type | Click the line → `E` (error, turns red) / `F` (failure) / `S` (success). |
| Delete a link | Click the line → `Delete`. If two lines overlap, drag one task aside first, then drag it back. |
| Delete & Merge (`⇧⌫`), Detach & Merge (`⌥⇧⌫`) | Only work when the task has exactly one link in and one out, of the same type. |
| Right-click menu (`⇧F10`) | Copy, Delete, View References, View Task Details, Zoom To Selection. No link actions. |

## Task panel
| Action | How |
|---|---|
| Open / close | Select → `Enter` (or double-click) / the panel's ✕ |
| Inputs | Each has **Static / Job / Task**. Dropdowns appear for clusters, adapter instances and workflows. |
| JSON and list fields | Code editors (Ace). They add closing `]`, `}` and `"` as you type: type the opening half only, or check and delete the extra character. |
| Job variable names | Choosing **Job** fills in a name (often the input's own name). Rename: pencil → type → ✓. |
| Keep a task's output | **Output** tab → tick **Store Output as Job Variable** → `+` → name it → ✓. Use this for workflow outputs. |
| `newVariable` | **name** → Value; **value** → Variable Type (e.g. String) → Value. |
| Description | **Details** tab (description only; the task title isn't editable there). |

## Child workflows (`childJob`)
| Action | How |
|---|---|
| Add | Tasks tab → `childJob`. (Dragging from the **Assets** tab doesn't work for automation, and the keyboard can't pick there.) |
| Pick the child | **Workflow** dropdown → type to filter. The project's copy reads "<name> **from <Project>**"; pick that one. Its inputs appear below. |
| Loop | **Enable Looping** → Loop Type value **Sequential** (default) or **Parallel** → Loop Array source **Job** → rename the variable (e.g. `devices`). The Static template shows the shape: one object per item with the child's inputs. |

## Save and check
| Action | How |
|---|---|
| Save | `⌘S` |
| Validation | Icon in the top bar, next to Save. Open it to read errors and warnings. |
| Check the whole flow | `9` zoom to fit → screenshot |

## Run and read results
| Action | How |
|---|---|
| Run | `⇧R` → the dialog lists the workflow's inputs as JSON (a code editor) → fill → **Run**. The job opens in a new Operations Manager tab. |
| Read results | Job page → **Job Variables** tab → JSON view. Task list shows each task's status. |
| Child runs | Each child job says "Initiated by Job …" and has a **Job Loop** picker for iterations. |

## Read back what was saved (optional)
If the JavaScript tool is available, read the saved workflow to confirm tasks and links:
```js
(await (await fetch('/automation-studio/workflows?limit=2000&include=name,_id', {credentials:'include'})).json())
  .items.filter(w => w.name.endsWith('<workflow name>'))
```
then `fetch('/automation-studio/workflows?equals[_id]=<id>')` and read `tasks` and `transitions`.
Read only. Make every change on the canvas.
