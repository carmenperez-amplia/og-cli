# Reference: `clock`

The `clock` widget displays a live real-time clock inside a dashboard tile. It requires **zero configuration** — just `type` and `wid` are needed.

## JSON Schema Configuration

Refer to [Common Widget Fields](./commonFields.md) for grid and standard widget layout wrapping properties.

```json
{
    "type": "clock",
    "wid": "my-clock-001"
}
```

> Note: `clock` has **no `Ftype`** and **no `config`** block. Both must be omitted entirely.

---

## Grid Wrapper Example (complete widget entry in dashboard grid)

```json
{
    "width": 3,
    "height": 2,
    "x": 0,
    "y": 0,
    "w": 3,
    "h": 2,
    "i": "my-clock-001",
    "moved": false,
    "definition": {
        "type": "clock",
        "wid": "my-clock-001"
    }
}
```

---

## Notes

- The clock automatically uses the user's browser timezone.
- No data source, filter, or period configuration is needed or supported.
- Recommended minimum size: `width: 2, height: 2`.

---

## Verified Example

From `recursos/workspace_0` (SmartCity-Demo, OpenGate v13.1.0):

```json
{
    "width": 3,
    "height": 2,
    "x": 0,
    "y": 2,
    "definition": {
        "type": "clock",
        "wid": "1779437068808-8"
    },
    "w": 3,
    "h": 2,
    "i": "1779437068808-8",
    "moved": false
}
```

---

## Source

Verified from `recursos/workspace_0` (SmartCity-Demo, OpenGate v13.1.0).
