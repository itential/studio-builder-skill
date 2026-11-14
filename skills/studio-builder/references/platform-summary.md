# Platform Summary

Find out what this platform has before planning. Run this once per session in the Studio tab
with the JavaScript tool. It only reads (`GET`), using the engineer's logged-in session.

```js
const get = async (u) => { try { const r = await fetch(u, {credentials: 'include'}); return r.ok ? r.json() : null; } catch { return null; } };
const list = (d) => Array.isArray(d) ? d : (d?.results || d?.items || d?.data || d?.result?.data || []);

const [tasks, apps, adapters, integrations, gateways, inventories, projects] = await Promise.all([
  get('/workflow_builder/tasks/list'),
  get('/health/applications'),
  get('/health/adapters'),
  get('/integrations'),
  get('/gateway_manager/v1/gateways'),
  get('/inventory_manager/v1/inventories?pageSize=100'),
  get('/automation-studio/projects?limit=100'),
]);

const byApp = {}, byAdapter = {};
for (const t of list(tasks)) {
  const m = t.location === 'Adapter' ? byAdapter : byApp;
  (m[t.app] ||= []).push(t.name);
}

({
  tasksByApp: Object.fromEntries(Object.entries(byApp).sort((a, b) => b[1].length - a[1].length)
    .map(([app, names]) => [app, names.length > 40 ? `${names.length} tasks` : names.sort()])),
  adapterInstances: Object.fromEntries(Object.entries(byAdapter).map(([a, n]) => [a, n.length])),
  runningApps: list(apps).map(a => a.id || a.name),
  adapters: list(adapters).map(a => `${a.id} (${a.package_id || ''}) ${a.state || a.connection?.state || ''}`),
  integrations: list(integrations).map(i => i.name || i.id),
  gatewayClusters: list(gateways).map(g => g.cluster_id || g.gateway_name),
  inventories: list(inventories).map(i => i.name),
  projects: list(projects).map(p => p.name),
});
```

**Read the result:**
- `tasksByApp`: platform app tasks (ConfigurationManager, MOP, GatewayManager…). Large apps show a count;
  filter for the step you need (below).
- `adapterInstances`: adapter tasks are grouped by **instance name** (`servicenow-prod`, `netbox-lab`), with a task count each.
- `adapters` / `integrations`: the external systems you can call, with instance names.
- `gatewayClusters` / `inventories`: what device tasks can target. Empty inventories means
  GatewayManager device tasks have nothing to run against yet.

To look inside one app, filter the same task list:

```js
(await (await fetch('/workflow_builder/tasks/list', {credentials:'include'})).json())
  .filter(t => t.app === 'ConfigurationManager' && /backup/i.test(t.name))
  .map(t => ({name: t.name, summary: t.summary, inputs: Object.keys(t.variables?.incoming || {})}))
```

**If the script can't run:** open the palette (`⇧T`), clear the filter, and scroll the app groups.
