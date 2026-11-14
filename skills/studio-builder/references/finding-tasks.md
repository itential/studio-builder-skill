# Finding Tasks

Screenshot after every search to confirm what you picked.

## Add a new task: Task Palette
1. Select the task the new one goes after.
2. `⇧T` opens the palette.
3. Type a keyword: the task name (`merge`, `childJob`), the action (`create change`, `get devices`) or the system (`servicenow`, `netbox`).
4. `↓` / `↑` to the right entry. Check its source on the screenshot: the adapter or application name shown with the task.
5. `Enter` adds it after the selected task and connects them.

The palette lists **assets** too (workflows, templates). Pick a workflow there to call it as a child.

## Reuse a task you just used
`⌥T` opens Recent Tasks.

## Find a task already on the canvas
`Q` (or `⌘F`) → type the task name or summary → `Enter`.

## See where a variable or task is used
Select the task → `R` (View References).

## Search tips
- Search the **action** first. Many systems have similar task names; the source name tells you which one.
- If two entries match, pick the one whose source matches the system in the request. Prefer lab or sandbox instances.
- If nothing matches, try other keywords (action, system, synonyms). If the task truly doesn't exist, the adapter or integration isn't installed: stop and say which one is missing.
- Look for a purpose-built task before building logic yourself. A filtered get task beats fetching everything and filtering.
