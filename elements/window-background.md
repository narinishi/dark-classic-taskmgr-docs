# window-background.md — main window and page backgrounds

## What it is

The top-level Task Manager window and each tab page are `#32770` dialogs.
Their client inset and child-control backgrounds are painted by
`WM_ERASEBKGND` and `WM_CTLCOLOR*`.

## How it is themed

`uahmenu.c`:

- `uahmenu_on_top_window` subclasses the top-level window with `uah_wndproc`;
- `uah_wndproc` fills the whole client with `g_uah_bg_brush` (`DC_BG`) on
  `WM_ERASEBKGND`;
- handles `WM_CTLCOLOR*` (`0x132..0x138`): text `CLR_TEXT`, background
  `DC_BG`, returns `g_uah_bg_brush`;
- `uah_child_dialog_cb` enumerates child `#32770` page dialogs and applies the
  same subclassing.

`syscolor.c` also remaps `COLOR_BTNFACE`, `COLOR_WINDOW`, etc. to `DC_BG`.

## Current status

Page-dialog subclassing is unconditional. There is no `FURTHER_DARK_DTB` gate
in uhooks5.

## Notes

- Page dialogs are `WS_CHILD`; their `WM_CTLCOLOR*` goes to the page, not the
  top-level window, so they must be subclassed separately.
- Spawned dialogs on worker threads are intentionally left native.
- The old post-paint fill for the New Task/Run dialog was removed; that dialog
  is worker-thread native.
