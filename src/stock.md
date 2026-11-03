# stock.c

## Role

Hooks `gdi32!GetStockObject` and remaps, on the UI thread:

- `WHITE_BRUSH` (0) and `LTGRAY_BRUSH` (1) -> dark `DC_BG` brush;
- `WHITE_PEN` (6) -> visible `DC_GRAY` pen;
- `BLACK_PEN` (7) -> subtle `DC_EDGE` pen.

All other indices pass through.

## uhooks5 change

`CreatePen` is **not** hooked. The module resolves `CreatePen` only to create
its own replacement pens. Task Manager’s `CreatePen` calls use saturated plot
series colours that should pass through unchanged.

## Evidence

Tracing showed app-drawn bevels use stock objects, while `CreatePen` is used
for chart series. Neutral bevels that do not use stock objects remain
app-painted (see the `DavesFrameClass` group border).
