# Reference: `customTable`

The `customTable` widget allows displaying data in a list format based on logic encoded by the user. The data source can be external, internal, both, or even fabricated.

## JSON Schema Minimum Configuration
Refer to [Common Widget Fields](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/commonFields.md) for grid and standard widget layout wrapping properties. Specific properties for `customTable` in `config` include:

```json
{
    "type": "customTable",
    "wid": "widget-id",
    "config": {
        "title": "Table Title",
        "allowPagination": 0,
        "pageElements": 15,
        "compactTable": true,
        "columns": [
            {
                "type": "text",
                "text": "Column Header",
                "value": "internal.json.field",
                "isOgIdentifier": true,
                "sortable": true,
                "filterable": true
            }
        ]
    }
}
```

## Columns Configuration
For each column you can define:
- **`text`**: Name to show in headers.
- **`value`**: The JSON field to read from the data returned by the function.
- **`sortable`**: Permits sorting for this column.
- **`groupable`**: Allows grouping by this field in the table.
- **`filterable`**: Allows filtering by this field in the table.
- **`type`**: Data type of the filter (only used when filterable is true).
- **`divider`**: Draws a separator between this column and the next.

## Script Parameters Context

When providing code in the widget (e.g., inside the platform UI, which is then mapped to the JSON), the script execution receives several parameters depending on the configuration:

1. **`entityData`**: Information about the entity if the dashboard is opened in an entity context.
2. **`filters`**: Introduced by the user. Includes:
   - `generic`: generic text filter.
   - `period`: `{ "from": "...", "to": "..." }`.
   - `column`: JSON object with filters per column (e.g., `{ "column_value": { "operator": "eq", "value": "text" } }`).
   - `sort`: Array containing sorting preferences.
3. **`pageElements`** and **`page`**: Used for server-side pagination.
4. **`callback`**: Function used to send table data only when the API/HTTP calls are resolved (e.g., `callback(data);`).

## Expected Return Format

The script MUST return an array of JSON objects compatible with the configured columns.

### Simple JSON Data
```json
{
    "jsonfield": "value to display. It can be HTML."
}
```

### Complex JSON Data
```json
{
    "jsonfield": {
        "value": "value to display. It can be HTML",
        "_style": "cell custom style",
        "_chart": "displays an echarts chart. Overrides others in this item",
        "_extension": "combines _chart and _table elements when expandable rows are enabled"
    }
}
```

### Embedded Table Data
Using `_table` displays a nested table inside the column:
```javascript
{
    columns: ['each', 'item', 'is', 'a', 'column'],
    data: [
        ['data 1', 'in', 'columns', 'order', { /* simple/complex object */ }],
        ['data 2', 'in', 'columns', 'order', { /* simple/complex object */ }]
    ]
}
```
