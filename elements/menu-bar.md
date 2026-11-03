# menu-bar.md — File / Options / View / Help bar

## What it is

The horizontal menu bar of the main window. Its text is painted in user32 /
win32k kernel mode, so no GDI text hook sees the normal (non-hover) draw.

## How it is themed

`uahmenu.c` subclasses the top-level `#32770` and handles the undocumented
UAH messages:

- `WM_UAHDRAWMENU` (`0x91`): `paint_uah_menu` fills the bar rect (from
  `GetMenuBarInfo`) with `g_uah_bg_brush`.
- `WM_UAHDRAWMENUITEM` (`0x92`): `paint_uah_menu_item` fills the item
  (selected/hot uses `g_uah_sel_brush` = `DC_MENUHILITE`) and draws the label
  itself with `DrawTextW` in `CLR_TEXT` (disabled items `DC_GRAY`).
- The item font is fetched with `WM_GETFONT`; text mode is `TRANSPARENT`.
- The painter uses the raw (pre-hook) `SetTextColor`/`DrawTextW` helpers so
  the text sink does not force everything to `CLR_TEXT`; disabled items are
  drawn with `DC_GRAY`.

The bar is only handled when `GetMenu(h)` matches the window's menu, so child
controls are unaffected.

## Notes

- Hover repaint already goes through a user-mode path, which is why the base
  bug looked like "only hovered items are visible".
- Popup/dropdown menus are separate; see `menu-popups.md`.

