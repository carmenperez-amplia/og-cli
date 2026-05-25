# Reference: `iframeWidget`

The `iframeWidget` allows embedding external web pages or dashboards directly into your OpenGate dashboard using an HTML `<iframe>`. It can render the page directly or display a button that launches the page.

## JSON Schema Configuration

Refer to [Common Widget Fields](./commonFields.md) for grid and standard widget layout wrapping properties.

```json
{
    "type": "iframeWidget",
    "wid": "my-iframe-001",
    "config": {
        "title": "Iframe Documentation",
        "reloadPeriod": "0",
        "about": "Embedded external web documentation",
        "hideWidgetTitle": "visible",
        "boxed": false,
        "customActions": null,
        "url": "https://documentation.opengate.es/",
        "showAsButton": false
    }
}
```

> Note: `iframeWidget` has **no `Ftype`** field — omit it entirely.

---

## Config Fields

| Field | Type | Required | Description |
|---|---|---|---|
| `url` | `string` | ✅ | The absolute HTTP/HTTPS URL of the page to embed. |
| `showAsButton` | `boolean` | — | If `true`, the widget will render a button that opens the URL instead of embedding it directly inside an iframe. Default `false`. |
| `title` | `string` | — | Widget header or button text. |
| `hideWidgetTitle` | `string` | — | `"visible"` or `"hidden"`. Default `"visible"`. |
| `boxed` | `boolean` | — | Wrap in a card border. Default `false`. |
| `reloadPeriod` | `string` | — | Reload interval in seconds. `"0"` disables. |
| `customActions` | `null\|array` | — | Optional custom actions. Usually `null`. |

---

## Verified Example

From `recursos/workspace_0` (SmartCity-Demo, OpenGate v13.1.0):

```json
{
    "width": 6,
    "height": 3,
    "x": 0,
    "y": 0,
    "w": 6,
    "h": 3,
    "i": "1779443119259-10",
    "definition": {
        "type": "iframeWidget",
        "wid": "1779443119259-10",
        "config": {
            "title": "Iframe",
            "reloadPeriod": "0",
            "about": "",
            "hideWidgetTitle": "visible",
            "boxed": false,
            "customActions": null,
            "url": "https://documentation.opengate.es/",
            "showAsButton": true
        }
    }
}
```

---

## Source

Verified from `recursos/workspace_0` (SmartCity-Demo, OpenGate v13.1.0).
