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

> [!NOTE]
> En la exportación directa de la plataforma (`recursos/workspace_0`), el widget `clock` omite las claves `Ftype` y `config`. Sin embargo, en ciertos entornos o versiones de OpenGate con esquemas de validación estrictos, es posible requerir o inicializar por defecto la propiedad `config` como un objeto vacío (`{}`) y `Ftype` como `null` o vacío (`""`) para pasar la validación JSON.
> Ambos formatos son válidos y compatibles.

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
