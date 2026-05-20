# Reference: `customAction`

The `customAction` widget allows coding actions to be performed, triggered by a button or a custom form. It can dynamically construct a form using a JSON schema and process inputs via custom scripts.

## JSON Schema Configuration
Refer to [Common Widget Fields](file:///home/ubuntu/development/og-cli/.agents/skills/og-json-generator/references/commonFields.md) for grid and standard widget layout wrapping properties. Specific properties for `customAction` in `config` include:

```json
{
    "type": "customAction",
    "wid": "widget-action-id",
    "config": {
        "title": "Action Title",
        "description": "Description of the action",
        "icon": "ogicon-play",
        "formSchema": {
            "type": "object",
            "properties": {
                "fieldName": {
                    "type": "string",
                    "title": "Field Label"
                }
            }
        }
    }
}
```

## Code Configuration

There are two distinct scripting blocks supported:
1. **Expert Code**: Runs before the widget loads, allowing dynamic modification of the widget config (like building the form's schema based on dynamic platform data).
2. **Action Code**: The script executed when the button is clicked or the form is submitted.

---

### 1. Expert Code Scripting

Runs before widget load to configure dynamic forms or configurations.

#### Expert Parameters:
- `entityData`, `alarmData`, `relatedEntities`, `timeserieData`
- `config`: The raw configuration of the widget to be modified.
- `callback`: Function to return the updated configuration (e.g. `callback(newConfig);`). Alternatively, `return newConfig;`.

#### Expert Function Signature:
```javascript
async function main(entityData, alarmData, relatedEntities, timeserieData, config, callback) {
    // Dynamic schema generation here
    // config.formSchema.properties.dynamicField = { type: "string", title: "New Field" };
    return config;
}
```

---

### 2. Action Code Scripting

Runs upon user action trigger (form submission or button press).

#### Action Parameters:
- `entityData`, `alarmData`, `relatedEntities`, `timeserieData`
- `value`: The value in string format entered in the value field (if enabled).
- `model`: A JSON object containing all the custom form values (from the JSON schema).

#### Action Function Signature:
```javascript
async function main(entityData, alarmData, relatedEntities, timeserieData, value, model) {
    // Action implementation here
    // e.g. Execute an API request
    const devId = entityData['provision.device.identifier']._value._current.value;
    const result = await $api.operationsSearchBuilder()
        // API call setup
        .execute();
        
    // If no exception is thrown, the action is marked as successful.
}
```
