# Reference: `customChart`

The `customChart` widget displays data in a graphical format based on user-defined logic, integrating seamlessly with the Apache ECharts library (v5). It supports external APIs, internal data, or custom-fabricated data.

## JSON Schema Configuration
Refer to [Common Widget Fields](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/commonFields.md) for grid and standard widget layout wrapping properties. Specific properties for `customChart` in `config` include:

```json
{
    "type": "customChart",
    "wid": "widget-chart-id",
    "config": {
        "title": "Chart Title",
        "allowWidgetFilter": true
    }
}
```
*Note: Depending on how the data is loaded (via mocks in the widget builder or direct code injection), you might find `_timeserieValue`, `_alarmValue`, etc., alongside the `config`.*

## Script Parameters Context

The custom script receives the following parameters:

1. **`entityData`**: Provided if the dashboard is opened in a device/entity context.
2. **`relatedEntities`**: Related entities data.
3. **`timeserieData`** & **`alarmData`**: Contextual data from the platform.
4. **`filters`**: 
   - `generic`: Widget generic text filter.
   - `period`: Date range `{ "from": "...", "to": "..." }`.
5. **`callback`**: Optional function used to send chart data when using asynchronous calls (`callback(chartConfig)`). Alternatively, the script can just `return chartConfig`.

## Expected Return Format

The script MUST return a valid ECharts configuration object.

### Example ECharts Return Structure
```javascript
return {
  title: {
    text: 'Example Chart',
    left: 'center'
  },
  tooltip: {
    trigger: 'item'
  },
  legend: {
    orient: 'vertical',
    left: 'left'
  },
  series: [
    {
      name: 'Access From',
      type: 'pie',
      radius: '50%',
      data: [
        { value: 1048, name: 'Search Engine' },
        { value: 735, name: 'Direct' },
        { value: 580, name: 'Email' }
      ]
    }
  ]
};
```

## Built-in Utilities

The execution context exposes several specific utilities for charts:
- **`echarts`**: The core ECharts library instance.
- **`ecStat`**: ECharts statistics library.
- **`addChartEvent(event, handler, query)`**: Adds an event listener to the chart instance (e.g., handling clicks on data points).
- **`$api`**: The standard OpenGate API client builder for fetching data.
