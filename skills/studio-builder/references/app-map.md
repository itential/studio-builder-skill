# App Map

Where to look for a task. The platform summary lists the tasks each app actually has;
this map says which app to search first and with which words.

| App (palette group) | Use it for | Search words |
|---|---|---|
| **ConfigurationManager** | Devices, backups, config, device groups, golden config, compliance | backup, device, config, group, compliance |
| **MOP** | Command templates: run checks with pass/fail rules, pre/post diffs | command template, check, diff |
| **TemplateBuilder** | Render Jinja2, parse with TextFSM | render, jinja, template |
| **GatewayManager** | Gateway 5: send commands, push config, run services, run Python | send command, send config, run service, run code |
| **InventoryManager** | Gateway 5 inventories and nodes | inventory, nodes |
| **LifecycleManager** | Resource models, instances, actions | resource, action, instance |
| **OperationsManager** | Jobs, triggers, automations | job, trigger, automation |
| **JsonForms** | Show a JSON form, validate data against a form | form, json form |
| **WorkCenter** | Human tasks: forms, approvals, row actions | interactive, quick form |
| **WorkFlowEngine** | Flow control and small utilities | childJob, evaluation, newVariable, ViewData |
| **AgentSessionManager** (FlowAI) | Run an AI agent from a workflow | run agent |
| **AutomationStudio** | Read projects, workflows, templates | project, workflow |
| **DirectConnect**, **WorkflowBuilder** | Platform utilities | as needed |
| **Adapters and integrations** | External systems: ServiceNow, NetBox, Infoblox, cloud, email, chat | the system's name |
| **AGManager**, **AutomationGateway** | Gateway 4 (legacy) | avoid in new builds |

## Pick in this order
1. A native app task that does the step.
2. The adapter or integration for the external system.
3. A gateway service (`runService`).
4. Last: `runCode` for data, `sendCommand` for ad hoc commands.

## Never
Configuration Manager remediation tasks (`runAutoRemediation`, `advancedAutoRemediation`,
`convertChangesToConfig`, `patchDeviceConfiguration`, `advancedPatchDeviceConfiguration`,
`ManualRemediation`). Push config with `sendConfig` instead.
