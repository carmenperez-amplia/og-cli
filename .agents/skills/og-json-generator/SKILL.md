---
name: "og-json-generator"
description: "Generates JSON widgets, dashboards, and workspaces following the OpenGate schema."
---

# OpenGate JSON Generator Skill

This skill is designed to assist in creating and structuring the JSONs required by the OpenGate platform for its Workspaces, Dashboards, and Widgets. Below is the expected structure and key examples, focusing especially on complex widgets like the custom table (`customTable`).

## Decoupled Directory Structure

To maintain a logical order and allow the CLI to easily interact with local elements, OpenGate workspace projects are structured as follows:

```text
local_workspaces/
└── <workspace_name>/                     # Workspace root directory
    ├── workspace.json                    # Base workspace definition (id, name, description...)
    └── dashboards/
        └── <dashboard_name>/             # Own directory for each dashboard
            ├── dashboard.json            # Dashboard configuration, layout (grid), and references
            └── widgets/
                ├── <widget_id_1>.json    # Individual widget configuration
                ├── <widget_id_2>.json
                └── customTable.json      # Example: Large table widget
```

### 1. `workspace.json`
Contains the high-level configuration. It should not internally embed the full content of dashboards and widgets, since the CLI (through deployment commands) can package the folder structure when necessary to upload it.

### 2. `dashboard.json`
Defines the global view configurations (e.g., `extraConfig`, `icon`, `title`) and the `grid` layout. Inside each grid element, instead of embedding the complete widget definition, a local reference pointing to the widget file can be used:
```json
{
  "width": 6,
  "height": 3,
  "x": 0,
  "y": 0,
  "widget": {
    "$ref": "dashboards/<dashboard_name>/widgets/customTable.json"
  }
}
```

---

## Widget Configurations & Shared Properties

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
./og-cli deploy [workspace_name]
```

---

## Skill Workflow

1. **Analyze Requirements**: Determine if a new widget or a full dashboard is needed.
2. **Generate Directory**: Use the hierarchical structure described above.
3. **Build the Widget**: Start by consulting the **[Common Widget Fields Reference](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/commonFields.md)** for grid wrapping. Then, apply widget-specific parameters from the references below.
4. **Link**: Ensure the `dashboard.json` has the appropriate layout grid coordinates (`w`, `h`, `x`, `y`) matching the widget's local `$ref` path.

---

## References & Advanced Guides

For complex implementations that involve advanced custom script evaluations or specific JSON widget formatting, consult the following dedicated reference guides:

* [Common Widget Fields Reference](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/commonFields.md) - Layout coords, wrapper headers, and standard title properties.
* [Custom Table Reference (customTable)](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/customTable.md) - Deep-dive into scripting context, parameters, and complex embedded charts/tables format.
* [Custom Chart Reference (customChart)](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/customChart.md) - Custom ECharts integration.
* [Custom Action Reference (customAction)](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/customAction.md) - Context manipulation, button operations, and expert-mode dynamic forms.
* [Global Context & Utilities Reference](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/utils.md) - Standard sandboxed global objects (`$api`, `$user`, `$moment`, `http`) and UI navigation routing.

---

## Additional Integration & CLI Documents

For comprehensive developers guides on Model Context Protocol (MCP) integrations, custom prompts, and how to command the OpenGate REST and IoT APIs through LLM tooling:

* **[MCP Integration Guide](file:///home/ubuntu/development/og-cli/doc/mcp-integration.md)**: Deep dive into the server architecture, commands execution, and dynamic datastream discovery resources.
* **[MCP Prompts Documentation](file:///home/ubuntu/development/og-cli/doc/mcp-prompts.md)**: Details few-shot learning query patterns, operators mappings, and job/operations creation structure definitions.
