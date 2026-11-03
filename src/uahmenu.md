# uahmenu.c

## Role

- Menu-bar painter using undocumented UAH messages.
- Top-level window and child `#32770` page-dialog subclassing for background
  and `WM_CTLCOLOR*`.

## Menu bar

`uahmenu_on_top_window` subclasses the top-level dialog:

- `WM_UAHDRAWMENU` (0x91): fill the menu-bar rect from `GetMenuBarInfo`.
- `WM_UAHDRAWMENUITEM` (0x92): fill selected/hot item and draw the label with
  raw `SetTextColor`/`DrawTextW` helpers so the text sink does not force every
  item to the same colour.
- Disabled items use `DC_GRAY`.

The bar is only handled when the menu belongs to the window.

## Page/dialog backgrounds

`uah_wndproc` handles:

- `WM_ERASEBKGND`: fill client with `g_uah_bg_brush`;
- `WM_CTLCOLORMSGBOX..WM_CTLCOLORSTATIC`: text `CLR_TEXT`, background `DC_BG`,
  return `g_uah_bg_brush`.

`uahmenu_on_top_window` enumerates child `#32770` dialogs and subclasses them
through `uah_child_dialog_cb`.

## uhooks5 changes

- Page-dialog subclassing is unconditional (no `FURTHER_DARK_DTB`).
- The spawned-dialog post-paint fill was removed; the New Task dialog is
  worker-thread native.
- `graphedge_subclass_tree` is still called from `uahmenu_on_top_window`.
