# misc-controls.md — other themed classes

`swtheme.c` applies dark variants to these classes when they appear:

| class | variant | notes |
|---|---|---|
| `Edit` | `DarkMode_Explorer` | also gets page-dialog `WM_CTLCOLOR*` (Performance fields) |
| `ComboBox` | `DarkMode_Explorer` | no UI-thread combo in current build; `combodark` is not compiled (see `combo-dropdowns.md`) |
| `SysTreeView32` | `DarkMode_Explorer` | e.g. services tree if present |
| `SysListBox32` | `DarkMode_ItemsView` | |
| `SysHeader32` | `DarkMode_ItemsView` | see `list-header.md` |
| `ToolbarWindow32` | `DarkMode_ItemsView` | |
| `SysTabControl32` | `DarkMode_ItemsView` | fills/outline from `theme.c` |
| `msctls_progress32` | `DarkMode_Explorer` | if a progress bar is shown |
| `msctls_trackbar32` | `DarkMode_Explorer` | |
| `ReBarWindow32` | `DarkMode_Explorer` | |
| `tooltips_class32` | `DarkMode_Explorer` | tooltips |
| `ScrollBar` | `DarkMode_Explorer` | standalone scrollbars (see `scrollbars.md`) |

Text colour for all of these comes from `textsink.c`; backgrounds come from the
variant and/or the `GetSysColor` map.

Some classes may not exist in a given Task Manager build; the mapping is
harmless if unused.

