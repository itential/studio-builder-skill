# Device Tasks — Commands, Config and Services

Use **GatewayManager** tasks for anything that touches a device. They run through
Itential Automation Gateway 5 against nodes in an inventory.

## Pick the task

Check for a native task first: backups, device config and compliance are **ConfigurationManager** tasks (e.g. **Back Up Device**); pass/fail checks are **MOP** command templates. Use the GatewayManager tasks below when nothing native fits.

| Need | Task | Key inputs | Output |
|---|---|---|---|
| Run show commands | `sendCommand` | Cluster, `commands` (list), `inventory` | `result.results[]`: one `{name, command, output, success}` per node and command |
| Push config | `sendConfig` | Cluster, `config` (one string), `inventory` | `result.results[]`: one `{name, host, output, success}` per node |
| Run a gateway service (Python, Ansible, OpenTofu) | `runService` | Cluster, service name, `params`, `inventory` | `result.stdout`, `result.stderr`, `result.return_code` |
| Transform data | `runCode` | Cluster, code, `data` | `result.stdout_json` |
| Pre/post checks with pass/fail rules | Command template (MOP) | Template name, devices | Pass/fail per rule |

Pick the **cluster** and **inventory** from the dropdowns. Prefer lab ones. List your pick under Assumptions.

## Inventory input

`inventory` is a list of `{inventory, nodeNames}`:
```json
[{"inventory": "lab-inventory", "nodeNames": ["router1", "router2"]}]
```
It's a list, so build it in `runCode` and point the input at it with Enable Query.

## Push config the standard way

```
runCode (render config + build inventory)
  → MOP pre-check
  → sendConfig
  → runCode (check success on every node)
  → MOP post-check
```

- `config` is one string, e.g. the rendered Jinja2 output.
- The node needs `become: true` in its driver options to enter config mode. Without it the push times out at the config prompt.
- There's no overall pass/fail. Check `success` on **every** node.

## Rules

- Test the commands by hand on one device before building.
- Use a lab inventory while building.
- MOP command templates only check. They never push config.
- Don't use Configuration Manager remediation tasks to push config.
- Device unreachable shows as an odd timestamp error (`failed to parse start_time`). It's the device, not the workflow.
