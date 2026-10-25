# AGTMcpGovernedServerApp01

This sample demonstrates how to attach Microsoft Agent Governance to an MCP server using `WithGovernance(...)` from the governance extensions package.

## What it shows

- MCP server registration with `AddMcpServer(...)`
- Governance enforcement at server/tool boundaries via `WithGovernance(...)`
- Deny-by-default policy behavior
- Unsafe tool blocking with `FailOnUnsafeTools = true`
- Response sanitization with `SanitizeResponses = true`
- Agent identity enforcement via `RequireAuthenticatedAgentId = true`

## Governance configuration

The sample enables the following settings:

| Setting | Value | Purpose |
|---|---|---|
| `PolicyPaths` | `policies/default.yaml` | Load the YAML policy file |
| `ConflictStrategy` | `DenyOverrides` | Deny decisions override allow rules |
| `FailOnUnsafeTools` | `true` | Prevent unsafe tools from registering or running |
| `SanitizeResponses` | `true` | Strip sensitive data from tool output |
| `RequireAuthenticatedAgentId` | `true` | Enforce a valid agent identity |

## Included tools

The sample intentionally includes both safe and unsafe tools:

- `weather_lookup` — allowed
- `stock_price` — allowed
- `knowledge_search` — allowed
- `shell_exec` — intentionally unsafe and blocked by policy

## Run it

From the repository root:

```powershell
dotnet run --project AGTMcpGovernedServerApp01/AGTMcpGovernedServerApp01.csproj
```

From the project folder:

```powershell
dotnet run
```

## Policy file

The project uses `policies/default.yaml` to enforce rules such as:

- `default_action: deny`
- allow specific safe tools
- block unsafe tools such as `shell_exec`

## Notes

This sample is designed to show the governance boundary around an MCP server, not just a single agent call. The goal is to demonstrate that tool exposure and execution can be filtered before a tool is allowed to run.
