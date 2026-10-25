# MyAGTSamples

Sample .NET applications for Microsoft Agent Governance scenarios.

This repository contains six focused examples covering policy evaluation, agent identity and trust, Microsoft Agent Framework (MAF) integration, MCP governance, and audit/telemetry export.

## What's in this repository

| Project | Focus | Target Framework | Status |
|---|---|---|---|
| `AGTPolicyApp01` | Basic policy-driven tool call evaluation with a local YAML policy | `net9.0` | ✅ Completed |
| `AGTPolicyWithMAFApp02` | MAF + Azure OpenAI with governance middleware and tool blocking | `net9.0` | ✅ Completed |
| `AGTIdentityApp01` | Agent identity (DID/public key) and trust score basics | `net9.0` | ✅ Completed |
| `AGTIdentityWithMAFApp02` | Trust-score-aware tool execution in a MAF agent flow | `net9.0` | ✅ Completed |
| `AGTAuditBlobTelemetryApp01` | Governance audit to Azure Blob and telemetry to Application Insights | `net10.0` | ✅ Completed |
| `AGTMcpGovernedServerApp01` | MCP server with governance enforcement and unsafe tool blocking | `net9.0` | ✅ Completed |

## Solution structure

- `MyAGTSamples.sln` contains all six projects above.
- Policy files are under each project's `policies/` folder where applicable.
- `AGTAuditBlobTelemetryApp01` includes `BlobAuditSink.cs` and a project-specific README with deeper audit/telemetry details.
- `AGTMcpGovernedServerApp01` demonstrates MCP server governance through `WithGovernance(...)`.

## Prerequisites

1. .NET SDKs:
   - .NET 9 SDK for most projects
   - .NET 10 SDK for `AGTAuditBlobTelemetryApp01`
2. Azure CLI (`az`) signed in when running MAF/OpenAI or Azure-based samples
3. Access to Azure OpenAI for MAF/OpenAI-based projects
4. For audit samples: Azure Storage and Application Insights configured

## License

MIT License. See `AGTAuditBlobTelemetryApp01/README.md` for the project-level details.

## Build all projects

From the repository root:

```powershell
dotnet restore
dotnet build MyAGTSamples.sln
```

## Run each sample

From the repository root:

### 1) AGTPolicyApp01

```powershell
dotnet run --project AGTPolicyApp01/AGTPolicyApp01.csproj
```

Behavior:
- Loads `AGTPolicyApp01/policies/default.yaml`
- Evaluates sample tool calls such as `file_write` and `http_request`
- Prints allow/deny decisions

### 2) AGTIdentityApp01

```powershell
dotnet run --project AGTIdentityApp01/AGTIdentityApp01.csproj
```

Behavior:
- Creates an agent identity
- Prints DID/public key/status information
- Loads a trust score from the local trust store

### 3) AGTPolicyWithMAFApp02

Required environment variables:
- `AZURE_OPENAI_ENDPOINT`
- `AZURE_OPENAI_DEPLOYMENT_NAME` (optional; defaults in code when unset)

Run:

```powershell
dotnet run --project AGTPolicyWithMAFApp02/AGTPolicyWithMAFApp02.csproj
```

Behavior:
- Uses governance middleware with MAF tools
- Applies policy from `AGTPolicyWithMAFApp02/policies/default.yaml`
- Demonstrates a blocked `GetWeather` call and a normal non-tool response

### 4) AGTIdentityWithMAFApp02

Required environment variables:
- `AZURE_OPENAI_ENDPOINT`
- `AZURE_OPENAI_DEPLOYMENT_NAME` (optional; defaults in code when unset)

Run:

```powershell
dotnet run --project AGTIdentityWithMAFApp02/AGTIdentityWithMAFApp02.csproj
```

Behavior:
- Uses trust-based policy from `AGTIdentityWithMAFApp02/policies/trust-based.yaml`
- Starts at a trust score of 500 and then applies penalties
- Shows one allowed tool call followed by a blocked tool call

### 5) AGTAuditBlobTelemetryApp01

Required environment variables:
- `AZURE_OPENAI_ENDPOINT`
- `AZURE_OPENAI_DEPLOYMENT_NAME` (optional; defaults to `gpt-4.1-mini` when unset)
- `APPLICATIONINSIGHTS_CONNECTION_STRING`
- `AZURE_STORAGE_ACCOUNT_NAME`
- `AZURE_TENANT_ID` (optional)
- `AUDIT_STORAGE_CONTAINER` (optional; defaults to `agt-audit`)

Run:

```powershell
dotnet run --project AGTAuditBlobTelemetryApp01/AGTAuditBlobTelemetryApp01.csproj
```

Behavior:
- Writes governance events to an Azure Blob append blob (JSONL)
- Exports metrics/traces/logs to Application Insights
- Demonstrates allow/deny policy decisions and direct governance evaluation

### 6) AGTMcpGovernedServerApp01

Run:

```powershell
dotnet run --project AGTMcpGovernedServerApp01/AGTMcpGovernedServerApp01.csproj
```

Behavior:
- Starts an MCP server with governance applied via `WithGovernance(...)`
- Uses `default_action: deny`
- Blocks unsafe tools such as `shell_exec`
- Sanitizes responses and requires agent identity for authenticated governance enforcement

## Policy files

| File | Default action | Notable rule |
|---|---|---|
| `AGTPolicyApp01/policies/default.yaml` | `deny` | Rate limits `http_request` at `100/minute` |
| `AGTPolicyWithMAFApp02/policies/default.yaml` | `allow` | Denies `GetWeather` |
| `AGTIdentityWithMAFApp02/policies/trust-based.yaml` | `allow` | Denies calls when `trust_score < 500` |
| `AGTAuditBlobTelemetryApp01/policies/default.yaml` | `deny` | Allows specific tools and denies `execute_shell` |
| `AGTMcpGovernedServerApp01/policies/default.yaml` | `deny` | Blocks unsafe MCP tools and enforces sanitized output |

## Common troubleshooting

- Build fails due to SDK mismatch:
  - Install the required .NET 9 or .NET 10 SDK, then re-run `dotnet build`.
- Azure auth errors:
  - Run `az login` and verify the active subscription and tenant.
- Environment variable errors:
  - Confirm required variables are set for the project you are running.
- Policy file not found:
  - Build first so policy files are copied to the output directory.

## Notes

- This repository intentionally mixes simple local-only samples with Azure-connected samples.
- For deeper audit/telemetry details, see `AGTAuditBlobTelemetryApp01/README.md`.
- For the MCP governance sample, see `AGTMcpGovernedServerApp01/README.md`.
