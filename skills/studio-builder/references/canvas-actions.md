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
| Insert in the middle | Don't. Adding from a task that already links onward adds a parallel branch. Build in run order. |
| Spacing | Toolbar **Spread Tasks** slider. It changes spacing only; there's no auto-layout. |

## Links
| Action | How |
|---|---|
| Connect two tasks | Click source → shift-click target → `C`. Default type is success; press `E` / `F` while connecting for error / failure. |
| Delete a link | Click the line → `Delete`. If two lines overlap, drag one task aside first, then drag it back. |
| Delete & Merge (`⇧⌫`), Detach & Merge (`⌥⇧⌫`) | Only work when the task has exactly one link in and one out, of the same type. |
| Right-click menu (`⇧F10`) | Copy, Delete, View References, View Task Details, Zoom To Selection. No link actions. |

## Task panel
| Action | How |
|---|---|
| Open / close | Select → `Enter` (or double-click) / the panel's ✕ |
| Inputs | Each has **Static / Job / Task**. Dropdowns appear for clusters, adapter instances and workflows. |
| JSON and list fields | Code editors (Ace). They add closing `]`, `}` and `"` as you type: type the opening half only, or check and delete the extra character. |
| Summary and description | **Details** tab. Rename tasks to say what they do. |

## Save and check
| Action | How |
|---|---|
| Save | `⌘S` |
| Validation | Icon in the top bar, next to Save. Open it to read errors and warnings. |
| Check the whole flow | `9` zoom to fit → screenshot |

## Read back what was saved (optional)
If the JavaScript tool is available, read the saved workflow to confirm tasks and links:
```js
(await (await fetch('/automation-studio/workflows?limit=2000&include=name,_id', {credentials:'include'})).json())
  .items.filter(w => w.name.endsWith('<workflow name>'))
```
then `fetch('/automation-studio/workflows?equals[_id]=<id>')` and read `tasks` and `transitions`.
Read only. Make every change on the canvas.
