---
name: "og-workspaces"
description: "Guides on managing and structuring OpenGate Workspaces and their lifecycle."
---

# OpenGate Workspaces Skill

This skill is designed to assist in creating, managing, and structuring OpenGate Workspaces and their lifecycle commands using the `og` CLI.

## Decoupled Directory Structure

To maintain a logical order and allow the CLI (`og workspace wrap` / `unwrap` / `pull`) to easily interact with local elements, OpenGate workspace projects MUST follow this exact structure:

```text
local_workspaces/
└── <workspace_name>/                     # Workspace root directory
    ├── workspace.json                    # Base workspace definition (_id, name, description...)
    └── <dashboard_folder_name>/          # Own directory for each dashboard (e.g. 00__dashboard1)
```

### `workspace.json`
Contains the high-level configuration (`_id`, `name`, `others`). It MUST NOT internally embed the `dashboards` array; the CLI reads the subdirectories automatically.

---

## CLI Workspace Lifecycle & Commands

The OpenGate Local-First GUI Creator CLI provides commands to seamlessly sync, compile, and deploy workspaces between your local filesystem and the platform:

### 1. Pull/Export Workspace from OpenGate
To download a remote workspace from the platform and unwrap/decouple it automatically into structured local directories:
```bash
# Using CLI
./og workspace pull <workspace-id> --dir <dest-dir>
```
*Output:* Explodes a workspace into one folder per nesting level and extracts any embedded JavaScript code into standalone `.js` files.

### 2. Compile/Publish Workspace to Monolithic JSON
To bundle your modular workspace (resolving all widget `$ref` files) into a single consolidatable JSON file suitable for manual platform import or sharing:
```bash
./og workspace wrap <workspace-dir> --out <output-file>.json
```

### 3. Deploy Local Workspace to OpenGate API
To package and upload/import your local modular workspace directly to the OpenGate platform in one command:
```bash
./og workspace deploy <workspace-dir>
```

> [!WARNING]
> **CRITICAL DEPLOYMENT WARNING:** 
> - If you are creating a workspace from scratch, OR you have added **new dashboards**, you MUST run the command WITHOUT the `--update` flag. 
> - If you run `deploy --update` when a dashboard doesn't exist yet, it will fail to link properly in the API and the dashboard will appear empty or missing in the platform. Use `--update` ONLY when updating widgets or configurations of an existing workspace and existing dashboards.

---

## Skill Workflow

1. **Analyze Requirements**: Determine if a new workspace or modifications to an existing one are needed.
2. **Generate Directory**: Establish the workspace folder and place `workspace.json` inside it.
3. **Deploy**: Use `./og workspace deploy <dir>` (without `--update` for new setups, with `--update` for modifications).
