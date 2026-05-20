# Common Widget Fields Reference

All OpenGate widgets embedded in a dashboard's layout grid share a set of common wrapper fields and base configuration properties. To optimize the skill and avoid repetition, these common properties are documented here.

---

## 1. Grid Layout Fields
Inside the dashboard's `grid` array, each item defines the placement and container size of a widget:

| Field | Type | Description |
|---|---|---|
| `x` | `integer` | Horizontal position in the grid layout (0-indexed). |
| `y` | `integer` | Vertical position in the grid layout (0-indexed). |
| `width` / `w` | `integer` | Grid cell width span. Must match each other. |
| `height` / `h` | `integer` | Grid cell height span. Must match each other. |
| `i` | `string` | Grid item identifier. **Must exactly match** the `wid` inside the `definition`. |
| `moved` | `boolean` | Indicates if the widget has been moved (default `false`). |
| `definition` | `object` | Contains the actual widget's functional configuration. |

---

## 2. Base Widget Wrapper (`definition`)
Every widget definition has the following root fields:

| Field | Type | Description |
|---|---|---|
| `type` | `string` | The widget class type identifier (e.g., `customTable`, `customChart`, `maps`, `DeviceAlarmsList`). |
| `Ftype` | `string` | The primary functional resource category context (e.g. `*`, `alarms`, `maps`, `datasets`). |
| `wid` | `string` | Unique widget instance ID. Usually formatted as `{timestamp}-{counter}`. |
| `config` | `object` | Specific parameters required by the widget class type. |

---

## 3. Common Configuration Properties (`definition.config`)
These standard options are shared across the `config` blocks of most widgets:

| Field | Type | Default | Description |
|---|---|---|---|
| `title` | `string` | - | The user-visible header text of the widget. |
| `hideWidgetTitle` | `string` | `"visible"` | Title bar visibility state (`"dynamic"`, `"visible"` or `"hidden"`). |
| `boxed` | `boolean` | `true` / `false` | Wraps the widget container in a visual card border outline. |
| `reloadPeriod` | `string` / `integer` | `"0"` | Period (seconds) to automatically refresh widget data (`"0"` to disable). |
| `about` | `string` | `""` | User-defined descriptive markdown text shown in the widget's info panel. |
| `customActions` | `array` | [] | Array of custom actions to add to the widget's title bar. Each action has an icon, a title, and the code to execute when clicked. Default is `[]`. Definition can be found in [Custom Actions Object](#custom-actions-object). |

#### Custom Actions Object
| Field | Type | Default | Description |
|---|---|---|---|
| `icon` | `string` | - | The icon to display for the action. |
| `_actionCode` | `string` | - | The javascript code to execute when the action is clicked. |
| `title` | `string` | - | The title of the action. |
