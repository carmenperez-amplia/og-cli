---
name: "og-cli"
description: "Comprehensive documentation and guidelines for the OpenGate CLI tool (`og`)."
---

# OpenGate CLI Skill

This skill provides comprehensive documentation and developer guidelines for the OpenGate CLI (`og`), which integrates CLI commands, an interactive TUI, and a Model Context Protocol (MCP) server for LLMs.

---

## 1. Project & Architecture Overview

`og` is a CLI tool for the **OpenGate IoT platform REST API** by Amplía Soluciones. It is built in Go using Cobra (commands), Viper (configuration), and Bubble Tea + Lip Gloss (interactive TUI).

### Three Execution Modes
The CLI has three interfaces exposing identical API capabilities:

| Mode | Invocation | Implementation | Description |
|---|---|---|---|
| **CLI** | `og <command>` | `cmd/` (Cobra) | Direct commands for scripts, operations, and one-liners. |
| **Interactive TUI** | `og` (no args) | `internal/tui/` (Bubble Tea) | Visual console UI to browse, search, and manage resources. |
| **MCP Server** | `og mcp` | `internal/mcp/` | Model Context Protocol server for seamless LLM integration. |

### Core Invariant: CLI ↔ TUI ↔ MCP Parity
**CRITICAL RULE:** Every OpenGate API operation must be exposed through all three interfaces. 
All three interfaces MUST call the same underlying method in the HTTP API client library located in `internal/client/`. Never ship functionality in one interface without implementing it in the other two.

```
cmd/<command>.go  ──→  internal/client/<method>  ←──  internal/mcp/tools.go
                              ↑
                    internal/tui/<view>.go
```

To add a new endpoint or API capability:
1. Add the Go client method in `internal/client/`.
2. Add the Cobra command wrapper in `cmd/`.
3. Add the MCP tool definition in `internal/mcp/tools.go`.
4. Add the TUI view screen in `internal/tui/`.
5. Update `internal/mcp/prompts.go` so AI models know how to use it.

---

## 2. Configuration & Authentication

### Config File
Located at `~/.og/config.yaml`. Supports environment profiles:
```yaml
default_profile: production
profiles:
  production:
    host: https://api.opengate.es
    organization: my-org
  staging:
    host: https://staging-api.opengate.es
    organization: my-org-staging
```

### Environment Variables
Variables prefixed with `OG_` override file config values:
- `OG_HOST`, `OG_PROFILE`, `OG_TOKEN`, `OG_ORG`, `OG_EMAIL`, `OG_PASSWORD`.
- *Note:* A `.env` file in the current working directory is loaded automatically.

### Web API Authentication & Concurrency
- Workspaces and dashboards live in the Web API (`/api/...`), separate from the South/North IoT APIs.
- The platform enforces a **single active web session per user**. Logging in via the browser UI invalidates the CLI session JWT.
- **Robust Auto-Reauth:** The CLI automatically intercepts `401 Unauthorized` responses, triggers a transparent background login/re-authentication using stored credentials, and retries the request seamlessly.

---

## 3. CLI Commands & Usage Examples

### 1. `login`
Authenticates and stores credentials. Prompts for password securely if not provided.
```bash
og login -e developer@company.com
og login -e developer@company.com -p mypassword --profile staging
```

### 2. `devices` (alias: `dev`)
Manage OpenGate devices and asset telemetry.
```bash
# Search devices matching a telemetry constraint
og dev search -w "wt gt 25.3"

# Select custom datastream columns for output
og dev search -s provision.device.identifier -s wt -s wp -w "provision.device.identifier like sense"

# Get full JSON model of a device
og dev get sense-001 -o json
```

### 3. `datamodels` (alias: `dm`)
Manage data models containing categories and telemetry datastreams.
```bash
# Search schemas
og dm search -w "datamodels.identifier like energy"

# Get details (prints category, datastream, schema, access type)
og dm get smart-building-energy --org my-org
```

### 4. `alarms` (alias: `al`)
Monitor and transition platform alarms.
```bash
# Get severe open alarms
og alarms search -w "alarm.status eq OPEN" -w "alarm.severity eq CRITICAL"

# Attend or Close an alarm
og alarms attend <alarm-uuid> --notes "Investigating sensor drift"
og alarms close <alarm-uuid> --notes "Replaced hardware unit"
```

### 5. `timeseries` (alias: `ts`) & `datasets` (alias: `ds`)
Query aggregate temporal databases or columnar data snapshots.
```bash
og ts list
og ts data <timeseries-id> -w "Prov Identifier eq Device-01" --limit 50
og ts export <timeseries-id> # Triggers Parquet export
```

### 6. `jobs` & `tasks`
Submit operational commands (e.g. `REBOOT_EQUIPMENT`) to device fleets.
```bash
# List operations within a specific execution job
og jobs operations <job-id>

# Submit operation job from JSON specification
og jobs create -f reboot_job.json
```

### 7. `workspace` (alias: `ws`) & `dashboard` (alias: `dash`)
Unwrap (pull), wrap, and deploy modular local-first workspaces.
```bash
# Extract workspace and all dashboards/widgets into modular files (extracting JS scripts)
og workspace pull <workspace-id> --dir local_workspaces/

# Re-deploy local folder (uses POST/PUT flows losslessly)
og workspace deploy local_workspaces/my-workspace --update
```

---

## 4. Common Search Query Syntax

All search filters (via CLI `-w` flags or MCP `query` string parameters) use an SQL-like filter syntax:

```bash
og dev search -w "provision.device.identifier like sense" -w "provision.device.administrativeState eq ACTIVE"
```

### Supported Operators
- **Relational:** `eq` (equal), `neq` (not equal), `exists`, `like` or `~` (substring), `gt` (`>`), `lt` (`<`), `gte` (`>=`), `lte` (`<=`), `in` / `nin` (array membership matching).
- **Logical:** `and`, `or`.
- **Grouping:** `()` to bundle compound expressions.

---

## 5. MCP Server Setup for AI Clients

Start the MCP server using the following:
```bash
og mcp              # Launch using stdio transport
og mcp --http :8080 # Launch using HTTP transport
```

### Claude Code Configuration
Add to your `~/.claude/settings.json`:
```json
{
  "mcpServers": {
    "opengate": {
      "command": "og",
      "args": ["mcp", "--stdio"]
    }
  }
}
```

---

## 6. Developer Documentation & References

For comprehensive developer guides on Model Context Protocol (MCP) integrations, custom prompts, and how to command the OpenGate REST and IoT APIs:

* **[MCP Integration Guide](./mcp-integration.md)**: Deep dive into the server architecture, commands execution, and dynamic datastream discovery resources.
* **[MCP Prompts Documentation](./mcp-prompts.md)**: Details few-shot learning query patterns, operators mappings, and job/operations creation structure definitions.

