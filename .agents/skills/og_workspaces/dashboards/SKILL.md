---
name: "og-dashboards"
description: "Guides on managing, designing, and structuring OpenGate Dashboards and layouts."
---

# OpenGate Dashboards Skill

This skill is designed to assist in creating, designing, and structuring OpenGate Dashboards and layouts, and deploying them using the `og` CLI.

## Decoupled Directory Structure

To link dashboards with workspaces in the local-first structure, dashboards are placed directly inside the workspace directory as subfolders:

```text
local_workspaces/
└── <workspace_name>/
    └── <dashboard_folder_name>/          # Own directory for each dashboard (e.g. 00__dashboard1)
        ├── dashboard.json                # Dashboard configuration AND _workspaceLayout block
        └── <widget_folder_name>/         # Widgets contained in this dashboard
```

### `dashboard.json`
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
>   "title": "My Dashboard"
> }
> ```

---

## CLI Dashboard Commands & Lifecycle

You can manage individual dashboards using the `og dashboard` commands:

### 1. Pull/Extract Dashboards
To extract one or more dashboards from OpenGate into organized local folders:
```bash
og dashboard pull <dashboard-id> --dir <dest-dir>
```

### 2. Wrap/Bundle Dashboard
To bundle a local dashboard directory back into a standard JSON file (without importing it):
```bash
og dashboard wrap <dashboard-dir> --out <output-file>.json
```

### 3. Deploy Dashboard
To deploy a single dashboard directory in one step (wrap + import):
```bash
og dashboard deploy <dashboard-dir>
# Use --update to overwrite
og dashboard deploy <dashboard-dir> --update
```

---

## Skill Workflow

1. **Create Dashboard Directory**: Add a subfolder under the workspace (e.g. `01__my-dashboard-name`).
2. **Configure dashboard.json**: Define metadata, icon, and the key `_workspaceLayout` block linking it to the workspace grid.
3. **Organize Layout**: Specify correct grid dimensions (`w`, `h`) and positioning (`x`, `y`) matching other dashboards.
4. **Deploy**: Sync changes using `og dashboard deploy <dir> --update`.
