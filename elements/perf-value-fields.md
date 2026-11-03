# perf-value-fields.md — Performance tab value fields

## What it is

The numeric fields on Performance (`Total`, `Cached`, `Available`, `Free`,
CPU values, etc.) are ordinary `Edit` controls inside the Performance page
`#32770`.

## How it is themed

The page dialog is subclassed by `uahmenu.c` (`uah_child_dialog_cb`). Its
`uah_wndproc` handles `WM_CTLCOLOREDIT` and `WM_CTLCOLORSTATIC`:

- text `CLR_TEXT`;
- background `DC_BG`;
- returns `g_uah_bg_brush`.

This is what darkens the edit fields; adding `Edit` to the theme painter alone
did not work because the paint path does not expose a usable
`WindowFromDC` class.

There is no `FURTHER_DARK_DTB` gate in uhooks5; page-dialog subclassing is
unconditional.

## Result

Value fields sample `(26,26,26)` with light text.
