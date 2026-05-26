# Reference: `markdown`

The `markdown` widget renders static or dynamic Markdown content inside a dashboard tile. Useful for documentation panels, instructions, dashboards headers, or any rich-text block.

## JSON Schema Configuration

Refer to [Common Widget Fields](./commonFields.md) for grid and standard widget layout wrapping properties.

```json
{
    "type": "markdown",
    "wid": "my-markdown-001",
    "config": {
        "title": "Markdown",
        "reloadPeriod": "0",
        "about": "",
        "hideWidgetTitle": "visible",
        "boxed": false,
        "customActions": null,
        "content": "# My Title\n\nSome **bold** text and *italic* text.\n\n- Item 1\n- Item 2"
    }
}
```

> Note: `markdown` has **no `Ftype`** field — omit it entirely.

---

## Config Fields

| Field | Type | Required | Description |
|---|---|---|---|
| `content` | `string` | ✅ | The Markdown string to render. Supports all standard Markdown syntax (headings, lists, bold/italic, code blocks, tables, etc.). Use `\n` for newlines inside the JSON string. |
| `title` | `string` | — | Widget header text (shown if `hideWidgetTitle` is `"visible"`). |
| `hideWidgetTitle` | `string` | — | `"visible"` or `"hidden"`. Default `"visible"`. |
| `boxed` | `boolean` | — | Wrap in a card border. Default `false`. |
| `reloadPeriod` | `string` | — | Reload interval in seconds. `"0"` disables. |
| `customActions` | `null\|array` | — | Optional custom row actions. Usually `null`. |

---

## Supported Markdown Syntax

The `content` field supports standard CommonMark Markdown:

```markdown
# H1 Heading
## H2 Heading
### H3 Heading

**Bold text**
*Italic text*
~~Strikethrough~~
`inline code`

- Unordered list item
    - Nested item

1. Ordered item 1
2. Ordered item 2

```python
def example():
    pass
```

| Col 1 | Col 2 |
|-------|-------|
| A     | B     |

[Link text](https://url.com)
```

---

## Verified Example

From `recursos/workspace_0` (SmartCity-Demo, OpenGate v13.1.0):

```json
{
    "type": "markdown",
    "wid": "1779437118780-9",
    "config": {
        "title": "Markdown",
        "reloadPeriod": "0",
        "about": "",
        "hideWidgetTitle": "visible",
        "boxed": false,
        "customActions": null,
        "content": "```markdown\n# Documento de Prueba en Markdown\n\n## Características de Markdown\n\n### 1. Elementos de Texto\n* **Texto en negrita**\n* *Texto en cursiva*\n* ~~Texto tachado~~\n* Texto con `código en línea`\n```"
    }
}
```

---

## Source

Verified from `recursos/workspace_0` (SmartCity-Demo, OpenGate v13.1.0).
