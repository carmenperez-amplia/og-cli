---
name: "og-widgets"
description: "Guides on configuring, structuring, and deploying OpenGate Widgets."
---

# OpenGate Widgets Skill

This skill is designed to assist in configuring, structuring, and placing widgets inside OpenGate dashboards using the local-first structure.

## Decoupled Directory Structure

Each widget is placed in its own folder inside the parent dashboard folder:

```text
local_workspaces/
└── <workspace_name>/
    └── <dashboard_folder_name>/
        └── <widget_folder_name>/         # Own directory for each widget (e.g. 00__myWidget)
            └── widget.json               # Full GridItem wrapper (layout + definition)
```

### `widget.json`
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

## Skill Workflow

1. **Identify Widget Needs**: Choose the appropriate verified widget type from the 15 supported options.
2. **Create Widget Folder**: Create a directory following the format `NN__widgetType__widgetName` inside the parent dashboard.
3. **Configure widget.json**: Wrap the widget inside a `GridItem` (with `w`, `h`, `x`, `y`, `definition`) following the **[Common Widget Fields Reference](./reference/commonFields.md)**.
4. **Deploy & Verify**: Test platform rendering and filter/action functionality.
