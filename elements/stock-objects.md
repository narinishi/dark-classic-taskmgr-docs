# stock-objects.md — stock pens/brushes

## Current implementation

`stock.c` hooks `gdi32!GetStockObject` only. `CreatePen` is **not** hooked.

Mapping on the UI thread:

| requested | returned |
|---|---|
| `WHITE_BRUSH` (0) | dark `DC_BG` brush |
| `LTGRAY_BRUSH` (1) | dark `DC_BG` brush |
| `WHITE_PEN` (6) | visible `DC_GRAY` pen |
| `BLACK_PEN` (7) | subtle `DC_EDGE` pen |

All other stock objects pass through. `CreatePen` is resolved only to create
the shim's own pens.

## Why CreatePen is not hooked

Tracing showed Task Manager's `CreatePen` colours are saturated plot-series
colours, not neutral bevel colours. Passing them through is intentional so the
graph series keep their colours. Neutral bevels and separators come from stock
objects or non-hooked app paint.

## Thread scope

Non-UI-thread calls pass through unchanged.
