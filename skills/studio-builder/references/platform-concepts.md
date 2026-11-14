# Platform Concepts

What things are on the Itential Platform and how they show up on the canvas.

## Building blocks

| Thing | What it is | On the canvas |
|---|---|---|
| **Workflow** | A flow of tasks joined by transitions. Runs as a job. | The canvas itself |
| **Task** | One step in a workflow. Comes from an adapter, an application or an integration. | A box |
| **Transition** | The line between tasks. Type is success, error or failure. | A line between boxes |
| **Job** | One run of a workflow, with its own variables and results. | Opened by Run (`⇧R`) |
| **Child workflow** | A workflow called by another workflow through `childJob`. | A `childJob` task |
| **Project** | A folder that holds workflows, templates and forms for one use case. | Left panel |

## Where tasks come from

| Source | What it is | Example tasks | How to spot it |
|---|---|---|---|
| **Adapter** | A connector to an external system (ServiceNow, NetBox, Infoblox, email). Installed and configured by an admin. | `createChangeRequest`, `getDevices` | Has an **adapter_id** dropdown in the task panel |
| **Adapter instance** | One configured connection of an adapter, e.g. `servicenow-prod`. One adapter type can have several. | — | What you pick in the **adapter_id** dropdown |
| **Integration** | A connector built from an OpenAPI spec inside the platform, no adapter install needed. | API operations from the spec | Named after the integration model |
| **Application** | A built-in platform service. | `WorkFlowEngine` (merge, query, childJob, evaluation), `GatewayManager` (runCode, run services, send config), `ConfigurationManager`, `TemplateBuilder` | No adapter_id dropdown |
| **Gateway service** | A Python script, Ansible playbook or OpenTofu plan registered on Itential Automation Gateway. | Called with `GatewayManager` run service | You pick the service name |

**Rule:** pick the lab or sandbox instance in the adapter_id dropdown while building. List it under Assumptions.

## Other assets a workflow uses

| Asset | Use it for |
|---|---|
| **Jinja2 template** | Generate config or text from variables |
| **TextFSM template** | Parse CLI output into structured data |
| **Command template (MOP)** | Run show commands and check the output against rules. Read-only, never pushes config. |
| **JSON form** | The input form an operator fills before a run |
| **Trigger** (Operations Manager) | How a workflow starts: manual form, schedule or API endpoint |

## Variables in one minute

- **Job variables** are the workflow's inputs and outputs. In the task panel: source **Job**.
- **Task outputs** are what one task returns. In the task panel: source **Task**, then pick the task and output.
- To pull one value out of an output, use **Enable Query**. To build or reshape data, use `runCode`.
- Details: `variables.md`.
