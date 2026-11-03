# titlebar.md — dark title bar

## Current implementation

`titlebar.c` applies DWM’s per-window dark caption:

- `DWMWA_USE_IMMERSIVE_DARK_MODE` = 20 on current Windows 11;
- fallback 19 on older Windows 10 builds;
- property-marked per window and followed by a frame redraw.

It is called from `swtheme.on_event` for top-level windows. There is no
`TITLEBAR_DARK` build toggle; the behaviour is unconditional.

## Scope

Only top-level windows on the Task Manager UI thread receive it. Worker-thread
dialogs such as the shell properties sheet and New Task keep their native
caption.

## Notes

- The frame/outer border itself is not recoloured.
- Do not poll; apply once per window.
