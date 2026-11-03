# text.md — text colour

## Current text hooks

`textsink.c` hooks the actual draw entry points:

- `ExtTextOutW`
- `TextOutW`
- `DrawTextW`
- `DrawTextExW`

`SetTextColor` is **not** hooked in uhooks5. The draw path is the chokepoint;
it forces `CLR_TEXT` for normal UI painting.

## Disabled text

`force_light_text()` reads the current text colour and preserves the classic
disabled gray (`~0x6D6D6D`) so disabled menu items and controls remain visually
distinct from enabled ones.

## DirectUI bypass

DirectUI TaskDialog text is drawn on a window-less memory DC and on the native
light TaskDialog surface. `dui.c` marks DirectUI theme handles, paint depth and
the root dialog; `textsink.c` checks `dui_should_bypass(dc)` and leaves that
text native. This keeps the UAC virtualization prompt readable.

## Chart text

Task Manager's chart text is monochrome. The draw hooks force it to
`CLR_TEXT`; there is no special colour-preservation branch for saturated chart
text in current uhooks5.
