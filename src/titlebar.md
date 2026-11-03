# titlebar.c

## Role

Applies the DWM dark caption per top-level window:

- `DWMWA_USE_IMMERSIVE_DARK_MODE` = 20;
- fallback 19 for older Windows 10;
- property-marked, then a frame redraw.

Called from `swtheme.on_event`. No `TITLEBAR_DARK` gate in uhooks5.

## Scope

Only top-level UI-thread windows. Worker-thread dialogs keep native captions.
The outer DWM frame colour is not changed.
