---
name: "og-json-generator"
description: "Generates JSON widgets, dashboards, and workspaces following the OpenGate schema."
---

# OpenGate JSON Generator Skill

This skill is designed to assist in creating and structuring the JSONs required by the OpenGate platform for its Workspaces, Dashboards, and Widgets. Below is the expected structure and key examples, focusing especially on complex widgets like the custom table (`customTable`).

## Decoupled Directory Structure

To maintain a logical order and allow the CLI (`og workspace wrap` / `unwrap`) to easily interact with local elements, OpenGate workspace projects MUST follow this exact structure:

```text
local_workspaces/
└── <workspace_name>/                     # Workspace root directory
    ├── workspace.json                    # Base workspace definition (_id, name, description...)
    └── <dashboard_folder_name>/          # Own directory for each dashboard (e.g. 00__dashboard1)
        ├── dashboard.json                # Dashboard configuration AND _workspaceLayout block
        └── <widget_folder_name>/         # Own directory for each widget (e.g. 00__myWidget)
            └── widget.json               # Full GridItem wrapper (layout + definition)
```

### 1. `workspace.json`
Contains the high-level configuration (`_id`, `name`, `others`). It MUST NOT internally embed the `dashboards` array; the CLI reads the subdirectories automatically.

### 2. `dashboard.json`
Defines the global view configurations (`_id`, `title`, `icon`, etc.). **CRITICAL:** It must also include the `_workspaceLayout` object to link it to the workspace grid.
```json
{
  "_workspaceLayout": {
    "x": 0, "y": 0, "width": 1, "height": 1, "w": 1, "h": 1,
    "id": "dashboard-1"
  },
  "_id": "dashboard-1",
  "title": "My Dashboard"
}
```

### 3. `widget.json`
Inside each widget folder, a file named exactly `widget.json` must exist. It MUST be a `GridItem` object containing the layout coordinates (`w`, `h`, `x`, `y`, `i`) AND a `definition` object with the widget properties, not just the widget config directly.

---

## Widget Configurations & Shared Properties

> [!IMPORTANT]
> **CRITICAL RULE: DO NOT INVENT WIDGET TYPES**
> Under no circumstances may you invent, guess, or synthesize widget type names or configurations not documented in this repository. 
> You MUST ONLY use the following **12 verified/supported widget types**. Any type not listed below is unsupported and will fail validation or platform rendering:
>
> 1. `customTable`: Configurable tables with custom JavaScript evaluations.
> 2. `customChart`: Custom ECharts v5 integration (line, bar, stats charts).
> 3. `actionButton`: Action buttons and dynamic schemas (replacing deprecated `customAction`).
> 4. `clock`: Live real-time clock showing browser timezone (zero-configuration).
> 5. `markdown`: Static Markdown content renderer supporting CommonMark formatting.
> 6. `iframeWidget`: Embedded external web pages or custom button launch frames.
> 7. `DatapointsList`: Datastream datapoints list showing entity telemetry tables.
> 8. `FullDevicesList`: Comprehensive list of devices/assets and operational status.
> 9. `DeviceAlarmsList`: Live list of alarms with severity and rule matches.
> 10. `maps`: Google Maps widget showing GPS locations of devices.
> 11. `entityTimeseriesMultipleHistory`: Historical time series charts using dataset arrays.
> 12. `datamodelBrowser`: Read-only browser for data models and datastreams catalog.
>
> Legacy/Deprecated widget types:
> - `customAction` (Deprecated in v13.1.0; use `actionButton` instead).
> - `summaryChart` (Unverified; summarises alarm counts over a window).
> - `ExecutionsList` (Unverified; lists operation execution tables).
> - `BundlesList` (Unverified; lists software/firmware bundles).

To optimize configuration schema structures and reduce redundancy, all common properties (such as grid layout coords, `type`, `wid`, and general configuration options like `title`, `boxed`, and `hideWidgetTitle`) have been centralized.

* Refer to the **[Common Widget Fields Reference](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/commonFields.md)** before writing any widget JSON block to learn about shared parameters.

### Specific Widget Configurations
The main complex widgets have dedicated technical specifications:

* **Custom Table (`customTable`)**: Configured with `allowPagination`, `pageElements`, `compactTable`, and a robust `columns` array definition. See [customTable.md](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/customTable.md) for full context.
* **Custom Chart (`customChart`)**: Seamless v5 ECharts integrations. See [customChart.md](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/customChart.md).
* **Custom Action (`customAction`)**: Multi-script operations (Expert Mode and Action submissions). See [customAction.md](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/customAction.md).

---

## CLI Workspace Lifecycle & Commands

The OpenGate Local-First GUI Creator CLI (`og-cli`) provides commands to seamlessly sync, compile, and deploy workspaces between your local filesystem and the platform:

### 1. Pull/Export Workspace from OpenGate
To download a remote workspace from the platform and unwrap/decouple it automatically into structured local directories under `workspaces/`:
```bash
./og-cli pull [workspace_name]
```

### 2. Compile/Publish Workspace to Monolithic JSON
To bundle your modular workspace (resolving all widget `$ref` files) into a single consolidatable JSON file suitable for manual platform import or sharing:
```bash
./og-cli publish [workspace_name]
```
*Output:* Consolidates the workspace to `[workspace_name]_compiled.json` in the project root.

### 3. Deploy Local Workspace to OpenGate API
To package and upload/import your local modular workspace directly to the OpenGate platform in one command:
```bash
./og workspace deploy <workspace-dir>
```
**CRITICAL DEPLOYMENT WARNING:** 
- If you are creating a workspace from scratch, OR you have added **new dashboards**, you MUST run the command WITHOUT the `--update` flag. 
- If you run `deploy --update` when a dashboard doesn't exist yet, it will fail to link properly in the API and the dashboard will appear empty or missing in the platform. Use `--update` ONLY when updating widgets or configurations of an existing workspace and existing dashboards.

---

## Skill Workflow

1. **Analyze Requirements**: Determine if a new widget or a full dashboard is needed.
2. **Generate Directory**: Use the correct hierarchical structure (`workspace_name/dashboard_name/widget_name/widget.json`).
3. **Build the Dashboard & Layout**: Ensure `dashboard.json` contains BOTH `_id` and `_workspaceLayout` with matching IDs.
4. **Build the Widget (`widget.json`)**: Wrap the widget inside a `GridItem` (with `w`, `h`, `x`, `y`, `definition`) following the **[Common Widget Fields Reference](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/commonFields.md)**.
5. **Deploy**: Use `./og workspace deploy <dir>`. Do NOT use `--update` if there are new dashboards.

---

## References & Advanced Guides

For complex implementations that involve advanced custom script evaluations or specific JSON widget formatting, consult the following dedicated reference guides:

* [Common Widget Fields Reference](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/commonFields.md) - Layout coords, wrapper headers, and standard title properties.
* [Custom Table Reference (customTable)](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/customTable.md) - Deep-dive into scripting context, parameters, and complex embedded charts/tables format.
* [Custom Chart Reference (customChart)](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/customChart.md) - Custom ECharts integration.
* [Custom Action Reference (customAction)](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/customAction.md) - Context manipulation, button operations, and expert-mode dynamic forms.
* [Clock Reference (clock)](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/clock.md) - Zero-config live clock widget. No Ftype or config block needed.
* [Markdown Reference (markdown)](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/markdown.md) - Markdown content widget. Full syntax support, content field format, grid wrapper example.
* [Iframe Reference (iframeWidget)](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/iframeWidget.md) - HTML iframe embedding widget. Supports URL rendering and custom button actions.
* [Global Context & Utilities Reference](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/utils.md) - Standard sandboxed global objects (`$api`, `$user`, `$moment`, `http`) and UI navigation routing.
* [Datamodel & Datastreams Reference](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/datamodel.md) - Full JSON schema for creating datamodels with categories and datastreams. Covers all fields (`period`, `access`, `schema`, `storage`, `unit`, `icon`), schema types, storage periods, and `og dm` CLI commands.

---

## Additional Integration & CLI Documents

For comprehensive developers guides on Model Context Protocol (MCP) integrations, custom prompts, and how to command the OpenGate REST and IoT APIs through LLM tooling:

* **[MCP Integration Guide](file:///home/ubuntu/development/og-cli/doc/mcp-integration.md)**: Deep dive into the server architecture, commands execution, and dynamic datastream discovery resources.
* **[MCP Prompts Documentation](file:///home/ubuntu/development/og-cli/doc/mcp-prompts.md)**: Details few-shot learning query patterns, operators mappings, and job/operations creation structure definitions.
