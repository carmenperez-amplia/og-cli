# Reference: `customChart`

The `customChart` widget displays data in a graphical format based on user-defined logic, integrating seamlessly with the Apache ECharts library (v5). It supports external APIs, internal data, or custom-fabricated data.

## JSON Schema Configuration
Refer to [Common Widget Fields](./commonFields.md) for grid and standard widget layout wrapping properties. Specific properties for `customChart` in `config` include:

```json
{
  "type": "customChart",
  "Ftype": "*",
  "wid": "widget-chart-id",
  "config": {
    "title": "Custom Chart",
    "reloadPeriod": "0",
    "about": "gwegwergewrg",
    "hideWidgetTitle": "dynamic",
    "boxed": true,
    "customActions": [
      {
        "icon": "ogicon-abacus",
        "_actionCode": "console.log(\"Hola Mundo!!!\");",
        "title": "Console log"
      }
    ],
    "model": "icon",
    "_extraValue": "{ \"provision.device.identifier\": {\"_value\": {\"_current\": {\"value\": \"entity_identifier\",\"provType\": \"IDENTIFIER\",\"date\": \"2019-11-26T18:11:40.501+01:00\"}}} }",
    "_extraValueRelated": "[{ \"provision.device.identifier\": {\"_value\": {\"_current\": {\"value\": \"entity_related\",\"provType\": \"IDENTIFIER\",\"date\": \"2019-11-26T18:11:40.501+01:00\"}}} }]",
    "_timeserieValue": "{ \"config\": {\"identifier\":\"timeserie_identifier\",\"name\":\"timeserie_name\",\"timeBucket\":300,\"bucketColumn\":\"end bucket name\",\"bucketInitColumn\":\"init bucket name\",\"identifierColumn\":\"identifier column name\",\"retention\":30672000,\"origin\":\"2025-04-29T22:00:00Z\"}, \"data\": { \"end bucket name\": \"value\", \"identifier column name\": \"value 2\" } }",
    "_alarmValue": "{\"identifier\":\"70fa3325-f6a8-4003-acfb-342864b3d669\",\"name\":\"alarm name\",\"rule\":\"rule name\",\"description\":\"rule description\",\"severity\":\"INFORMATIVE\",\"priority\":\"LOW\",\"organization\":\"organization\",\"channel\":\"channel\",\"entityIdentifier\":\"identifier\",\"subEntityIdentifier\":\"identifier\",\"resourceType\":\"entity.device\",\"status\":\"OPEN\",\"openingDate\":\"2025-05-20T13:39:06.947+02:00\"}",
    "_dashboardFilters": "{\"tasksSelected\":[],\"jobsSelected\":[],\"operationNameSelected\":[],\"operationStatusSelected\":[],\"operationResultSelected\":[],\"alarmNameSelected\":[],\"ruleNameSelected\":[],\"alarmSeveritySelected\":[],\"alarmStatusSelected\":[]}",
    "_widgetConfigCode": "return {\n  legend: {},\n  tooltip: {},\n  dataset: {\n    source: [\n      ['product', '2012', '2013', '2014', '2015'],\n      ['Matcha Latte', 41.1, 30.4, 65.1, 53.3],\n      ['Milk Tea', 86.5, 92.1, 85.7, 83.1],\n      ['Cheese Cocoa', 24.1, 67.2, 79.5, 86.4]\n    ]\n  },\n  xAxis: [\n    { type: 'category', gridIndex: 0 },\n    { type: 'category', gridIndex: 1 }\n  ],\n  yAxis: [{ gridIndex: 0 }, { gridIndex: 1 }],\n  grid: [{ bottom: '55%' }, { top: '55%' }],\n  series: [\n    // These series are in the first grid.\n    { type: 'bar', seriesLayoutBy: 'row' },\n    { type: 'bar', seriesLayoutBy: 'row' },\n    { type: 'bar', seriesLayoutBy: 'row' },\n    // These series are in the second grid.\n    { type: 'bar', xAxisIndex: 1, yAxisIndex: 1 },\n    { type: 'bar', xAxisIndex: 1, yAxisIndex: 1 },\n    { type: 'bar', xAxisIndex: 1, yAxisIndex: 1 },\n    { type: 'bar', xAxisIndex: 1, yAxisIndex: 1 }\n  ]\n};",
    "allowWidgetFilter": true
  }
}
```

* Field definitions:
  * `_extraValue`: Entity data (used for testing).
  * `_extraValueRelated`: Related entities data (used for testing).
  * `_timeserieValue`: Timeserie data (used for testing).
  * `_alarmValue`: Alarm data (used for testing).
  * `_dashboardFilters`: Dashboard filters (used for testing).
  * `_widgetConfigCode`: contains the javascript code that generates the chart configuration. Code is wrapped in a function:
  ```javascript
  async function (entityData, relatedEntities, timeserieData, alarmData, dashboardFilters, filters, callback){
    // _widgetConfigCode here -> returns an ECharts configuration object
  }
  ```

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
