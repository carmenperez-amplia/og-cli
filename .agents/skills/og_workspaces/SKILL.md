---
name: "og-workspaces"
description: "Guides on managing, designing, and structuring OpenGate Workspaces, Dashboards, Widgets, and their lifecycle."
---

# OpenGate Workspaces Skill

This skill is designed to assist in creating, designing, structuring, and managing OpenGate Workspaces, Dashboards, and Widgets using the local-first structure and the `og` CLI.

## Decoupled Directory Structure

To maintain a logical order and allow the CLI (`og workspace wrap` / `unwrap` / `pull`) to easily interact with local elements, OpenGate workspace projects MUST follow this exact structure:

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

You can find more information about workspace JSON structure in the [`reference/workspaces.md](./reference/workspaces.md)` file.

### 2. `dashboard.json`
Defines the global view configurations (`_id`, `title`, `icon`, etc.). 

> [!IMPORTANT]
> **CRITICAL RULE:** It must also include the `_workspaceLayout` object to link it to the workspace grid.
> ```json
> {
>   "_workspaceLayout": {
>     "x": 0, "y": 0, "width": 1, "height": 1, "w": 1, "h": 1,
>     "id": "dashboard-1"
>   },
>   "_id": "dashboard-1",
>   "title": "My Dashboard",
>   "[...]": "[...]
> }
> ```

You can find more information about dashboard configurations in the [`reference/dashboards.md](./reference/dashboards.md)` file.

### 3. `widget.json`
Inside each widget folder, a file named exactly `widget.json` must exist. It MUST be a `GridItem` object containing the layout coordinates (`w`, `h`, `x`, `y`, `i`) AND a `definition` object with the widget properties, not just the widget config directly.

---

## Widget Configurations & Shared Properties

> [!IMPORTANT]
> **CRITICAL RULE: DO NOT INVENT WIDGET TYPES**
> Under no circumstances may you invent, guess, or synthesize widget type names or configurations not documented in this repository. 
> You MUST ONLY use the following **15 verified/supported widget types**. Any type not listed below is unsupported and will fail validation or platform rendering:
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
> 13. `devicePlansBrowser`: Catalog browser widget to inspect and filter active device plans.
> 14. `connectorFunctionsBrowser`: Browser widget to list and monitor connector functions status.
> 15. `connectorFunctionsCatalogBrowser`: Catalog browser widget to inspect available connector templates.
>
> Legacy/Deprecated widget types:
> - `customAction` (Deprecated in v13.1.0; use `actionButton` instead).
> - `summaryChart` (Unverified; summarises alarm counts over a window).
> - `ExecutionsList` (Unverified; lists operation execution tables).
> - `BundlesList` (Unverified; lists software/firmware bundles).

---

## References & Advanced Guides

For complex implementations that involve advanced custom script evaluations or specific JSON widget formatting, consult the following dedicated reference guides in the `reference` folder:

* **[Common Widget Fields Reference](./reference/commonFields.md)** - Layout coords, wrapper headers, and standard title properties.
* **[Custom Table Reference (customTable)](./reference/customTable.md)** - Deep-dive into scripting context, parameters, and complex embedded charts/tables format.
* **[Custom Chart Reference (customChart)](./reference/customChart.md)** - Custom ECharts integration.
* **[Custom Action Reference (customAction)](./reference/customAction.md)** - Context manipulation, button operations, and expert-mode dynamic forms.
* **[Clock Reference (clock)](./reference/clock.md)** - Zero-config live clock widget. No Ftype or config block needed.
* **[Markdown Reference (markdown)](./reference/markdown.md)** - Markdown content widget. Full syntax support, content field format, grid wrapper example.
* **[Iframe Reference (iframeWidget)](./reference/iframeWidget.md)** - HTML iframe embedding widget. Supports URL rendering and custom button actions.
* **[Global Context & Utilities Reference](./reference/utils.md)** - Standard sandboxed global objects (`$api`, `$user`, `$moment`, `http`) and UI navigation routing.
* **[Datamodel & Datastreams Reference](./reference/datamodel.md)** - Full JSON schema for creating datamodels with categories and datastreams. Covers all fields (`period`, `access`, `schema`, `storage`, `unit`, `icon`), schema types, storage periods, and `og dm` CLI commands.
* **[Window Filter Variants Reference](./reference/windowFilterVariants.md)** - All information about window filter configurations.

---

## CLI Workspace & Dashboard Lifecycle Commands

The OpenGate CLI provides tools to sync, compile, and deploy workspaces and dashboards between your local filesystem and the platform:

### 1. Workspace Lifecycle Commands
* **Pull/Export Workspace from OpenGate**:
  To download a remote workspace from the platform and unwrap/decouple it automatically into structured local directories:
  ```bash
  ./og workspace pull <workspace-id> --dir <dest-dir>
  ```
  *Output:* Explodes a workspace into one folder per nesting level and extracts any embedded JavaScript code into standalone `.js` files.

* **Compile/Publish Workspace to Monolithic JSON**:
  To bundle your modular workspace (resolving all widget `$ref` files) into a single consolidatable JSON file suitable for manual platform import or sharing:
  ```bash
  ./og workspace wrap <workspace-dir> --out <output-file>.json
  ```

* **Deploy Local Workspace to OpenGate API**:
  To package and upload/import your local modular workspace directly to the OpenGate platform in one command:
  ```bash
  ./og workspace deploy <workspace-dir>
  ```
  > [!WARNING]
  > **CRITICAL DEPLOYMENT WARNING:** 
  > - If you are creating a workspace from scratch, OR you have added **new dashboards**, you MUST run the command WITHOUT the `--update` flag. 
  > - If you run `deploy --update` when a dashboard doesn't exist yet, it will fail to link properly in the API and the dashboard will appear empty or missing in the platform. Use `--update` ONLY when updating widgets or configurations of an existing workspace and existing dashboards.

### 2. Dashboard Lifecycle Commands
* **Pull/Extract Dashboards**:
  To extract one or more dashboards from OpenGate into organized local folders:
  ```bash
  og dashboard pull <dashboard-id> --dir <dest-dir>
  ```

* **Wrap/Bundle Dashboard**:
  To bundle a local dashboard directory back into a standard JSON file (without importing it):
  ```bash
  og dashboard wrap <dashboard-dir> --out <output-file>.json
  ```

* **Deploy Dashboard**:
  To deploy a single dashboard directory in one step (wrap + import):
  ```bash
  og dashboard deploy <dashboard-dir>
  # Use --update to overwrite
  og dashboard deploy <dashboard-dir> --update
  ```

---

## Skill Workflow

1. **Identify and Analyze Requirements**: Determine if a new workspace, dashboard, or widget is needed, and select the appropriate verified widget type from the 15 supported options.
2. **Generate Directory Structure**: Establish the workspace folder, add a dashboard subfolder (e.g. `00__dashboard-name`), and add widget subfolders (e.g. `00__myWidget`) as required.
3. **Configure JSON Files**:
   - Write `workspace.json` in the workspace root.
   - Write `dashboard.json` in the dashboard folder (making sure to include the `_workspaceLayout` block linking it to the workspace grid and specifying correct coordinates `x`, `y` and dimensions `w`, `h`).
   - Write `widget.json` inside each widget folder, wrapping the widget inside a `GridItem` (with `w`, `h`, `x`, `y`, `definition`) following the **[Common Widget Fields Reference](./reference/commonFields.md)**.
4. **Deploy & Verify**: 
   - Sync changes using `./og workspace deploy <dir>` (without `--update` for new setups, or with `--update` for modifications).
   - Alternatively, deploy single dashboards using `og dashboard deploy <dir> --update`.
   - Test platform rendering and filter/action functionality.
