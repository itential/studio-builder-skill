# Finding Tasks

## The task palette
- Opens with `⇧T`, or automatically when a new workflow is created.
- **Tasks** tab: every task, grouped by app. **Assets** tab: workflows and templates in reach
  (use it to add a child workflow).
- The filter matches **display names and method names**: `sendCommand` finds "Send Command"
  (GatewayManager) and "Netmiko Send Command" (AG Manager, AutomationGateway).
- Results stay grouped by app. Read the app line under each name before picking.
- `↓` / `↑` highlight, `Enter` adds after the selected task and connects it.
- `⌥T` opens recent tasks.

## Search in this order
1. **Intent word:** `backup`, `compliance`, `render`, `ticket`.
2. **App name** from `app-map.md`, to see everything that app offers.
3. **Method name** from the platform summary.

## Picking between matches
- Prefer the app `app-map.md` names for the job. Avoid AG Manager / AutomationGateway (Gateway 4).
- For adapters, pick the instance for this environment (lab while building).
- Nothing matches after three searches: the adapter or integration isn't installed. Stop and say which.

## Already on the canvas
- `Q` → name → `Enter` selects a task.
- `R` shows where a task's output is used.
