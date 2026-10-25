# AGTAuditBlobTelemetryApp01

A runnable sample that shows how to send Microsoft Agent Governance events to two destinations:

- Audit: append each governance event to an Azure Blob Storage append blob as newline-delimited JSON
- Telemetry: export governance metrics and traces to Application Insights via Azure Monitor OpenTelemetry

## What this sample demonstrates

- Creating a `GovernanceKernel` with a YAML policy file
- Subscribing to governance events and writing them to Blob Storage
- Exporting `agent_governance.*` metrics to Application Insights
- Wiring the governance layer into a Microsoft Agent Framework agent
- Running blocked and allowed governance checks so you can see the audit flow in action

## Required environment variables

Set these before running the sample:

- `AZURE_OPENAI_ENDPOINT`
- `AZURE_OPENAI_DEPLOYMENT_NAME` (optional; defaults to `gpt-4.1-mini` when unset)
- `APPLICATIONINSIGHTS_CONNECTION_STRING`
- `AZURE_STORAGE_ACCOUNT_NAME`

Optional:

- `AZURE_TENANT_ID`
- `AUDIT_STORAGE_CONTAINER` (default: `agt-audit`)

## Run it

From the project folder:

```powershell
dotnet run
```

Or from the repository root:

```powershell
dotnet run --project AGTAuditBlobTelemetryApp01/AGTAuditBlobTelemetryApp01.csproj
```

## Files

| File | Purpose |
|---|---|
| `Program.cs` | Main program with helper methods for governance evaluation and telemetry setup |
| `AppConfiguration.cs` | Loads configuration from environment variables |
| `BlobAuditSink.cs` | Thread-safe append-blob writer for audit logs |
| `policies/default.yaml` | Policy file that allows weather/time/location tools and blocks unsafe commands |

## Code structure

The project is organized to separate responsibilities:

- `AppConfiguration`: loads environment values and creates the Azure identity credential
- `BlobAuditSink`: writes each governance event to Azure Blob Storage
- `Program`: builds the agent, configures governance, and emits telemetry

## Governance policy

Tool invocations are controlled by `policies/default.yaml` using a deny-by-default strategy with explicit allow rules:

| Tool | Action | Description |
|---|---|---|
| `GetWeather` | Allow | Returns weather information for a city |
| `GetTime` | Allow | Returns the current time for a timezone |
| `GetLocation` | Allow | Returns location information for a city |
| `execute_shell` | Deny | Shell command execution is intentionally blocked |

## Output destinations

| Data | Destination | Details |
|---|---|---|
| Audit logs | Azure Blob Storage | `governance-audit-YYYYMMDD.jsonl` append blob |
| Metrics | Application Insights | `customMetrics` table |
| Traces | Application Insights | `traces` / `dependencies` tables |
| Logs | Application Insights | `traces` table (`severityLevel` column) |

## Dependencies

| Package | Version |
|---|---|
| `Azure.AI.OpenAI` | `2.9.0-beta.1` |
| `Azure.Identity` | `1.21.0` |
| `Azure.Monitor.OpenTelemetry.Exporter` | `1.8.2` |
| `Azure.Storage.Blobs` | `12.29.1` |
| `Microsoft.AgentGovernance` | `4.0.0` |
| `Microsoft.AgentGovernance.Extensions.Microsoft.Agents` | `4.0.0` |
| `Microsoft.Agents.AI.OpenAI` | `1.12.0` |
| `OpenTelemetry` | `1.16.0` |

## Target framework

- `.NET 10`

## License

MIT License
