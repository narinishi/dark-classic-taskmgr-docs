# swtheme.c

## Role

Window-creation/show handling and `SetWindowTheme` variants.

`swtheme_init`:

- sets uxtheme preferred dark mode:
  - ordinal 135 `SetPreferredAppMode(2)`
  - ordinal 104 `RefreshImmersiveColorPolicyState()`
  - ordinal 136 `FlushMenuThemes()`
- installs `SetWinEventHook` for create/destroy/show (`0x8000..0x8002`).

## Theme variants

| class | variant |
|---|---|
| `SysListView32` | `DarkMode_Explorer` |
| `SysHeader32`, `ToolbarWindow32`, `SysTabControl32`, `SysListBox32` | `DarkMode_ItemsView` |
| `Button`, `Edit`, `ComboBox`, `msctls_statusbar32`, `msctls_progress32`, `ReBarWindow32`, `msctls_trackbar32`, `SysTreeView32`, `ScrollBar`, `tooltips_class32` | `DarkMode_Explorer` |

## Event path

`on_event` is scoped to the Task Manager UI thread. It calls:

- `dui_on_window`
- `graphedge_subclass_tree`
- `apply_swtheme`
- `uahmenu_on_top_window` (top-level)
- `titlebar_apply_window` (top-level)
- child enumeration / theme refresh where applicable

`g_busy` prevents re-entrant event handling. Late-created controls are caught by
the per-event tree scans; `graphedge` additionally checks the event window
itself.

## Header seam

`header_seam_install` subclasses every `SysHeader32`. After native paint it
shifts the native divider pixel group left one with `BitBlt`, handling
`WM_PAINT`, `WM_PRINT` and `WM_PRINTCLIENT`. This aligns report-list headers
with the native body separators. See `reversing/listview.md`.

## Removed in uhooks5

- `SCROLLBAR_DARK` / `FURTHER_DARK_DTB` / class toggles;
- spawned-dialog post-paint fill;
- references to `combodark_subclass_tree` and `listgrid_subclass` (modules are
  not in the build).
