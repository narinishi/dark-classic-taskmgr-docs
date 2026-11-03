# list-view.md — `SysListView32` report tables

## What it is

Task Manager's main data tables are `SysListView32` common controls in report
mode, each with a child `SysHeader32` column-header band. The report tables
appear on Applications, Processes, Services, Users, and the Networking
totals/adapter areas. The Processes table is dialog control `1009`.

The list view owns the rows/columns, selection, scrolling, and the non-client
scrollbar. The header is a separate child window owned by the list view; it is
not an owner-drawn band inside the list view.

## Runtime hierarchy

For the Applications page at 125% DPI:

```text
root window        #32770        Windows Task Manager
page dialog        #32770        Applications
list               SysListView32  Tasks
header             SysHeader32    child of the list view
```

The same pattern applies to the other report pages: a page dialog contains a
`SysListView32`, and that list contains a `SysHeader32`.

Relevant window relationships for Applications:

```text
ListView window rect: x = 5466
ListView client origin: x = 5468   (2 px non-client border)
Header window rect:    x = 5468
Header client origin:  x = 5468
```

The header client origin and list client origin are the same. The 2 px inset is
the ListView non-client border, not a header-specific offset.

Runtime UIA exposes the pair as:

```text
ListControl  ''
  HeaderControl 'Header Control'
    HeaderItemControl 'Task'
    HeaderItemControl 'Status'
```

## Report mode and selection

The tables use `LVS_REPORT`. The Applications list's extended style is
`0x10020`:

```text
LVS_EX_FULLROWSELECT  (0x00000020)
LVS_EX_DOUBLEBUFFER   (0x00010000)
```

It does **not** request `LVS_EX_GRIDLINES`. Clicking a row selects the full row,
and the list uses double-buffered painting.

The column header is obtained from the list with `LVM_GETHEADER`. Header item
rectangles are relative to the header's client area (`HDM_GETITEMRECT`), while
column widths come from `LVM_GETCOLUMNWIDTH`.

## Column verticals

The header control paints a divider at each header item's right edge. The
ListView body verticals are supplied by the active list-view theme, not by the
Task Manager list style: `LVS_EX_GRIDLINES` is not set.

With the dark Explorer list-view theme, the list view paints a vertical body
separator at each column boundary minus one pixel. Applying the ItemsView
list-view theme removes those body separators. Under that dark Explorer
presentation, Applications at 125% DPI shows:

```text
header: x=374 anti-aliased (45,45,45), x=375 bright (76,76,76)
body:   x=373 anti-aliased (35,35,35), x=374 bright (49,52,54), x=375 falloff
```

The header bright center is one pixel to the right of the body bright center.
Processes shows the same offset: header bright at x=249, body bright at x=248.
Column widths themselves come from the same boundary; this is a difference
between the painted header divider and the painted body separator.

## Header/list coupling

Header item widths and ListView column widths are coupled. Changing a header
item width with `HDM_SETITEMW` and `HDI_WIDTH` also changes
`LVM_GETCOLUMNWIDTH`, and changing the ListView column width changes what the
header reports. Resizing only one side therefore does not move the header
relative to the column boundary.

During `HDM_SETITEMW`, the ListView receives the normal header notifications:

```text
WM_NOTIFY code = 0xfffffec0  HDN_ITEMCHANGINGW
WM_NOTIFY code = 0xfffffff4  NM_CUSTOMDRAW
WM_NOTIFY code = 0xfffffebf  HDN_ITEMCHANGEDW
```

Swallowing those notifications or the corresponding `LVM_SETCOLUMNWIDTH` does
not decouple the two: `LVM_GETCOLUMNWIDTH` still follows the header item's new
width.

The header window is also laid out by the ListView. A one-pixel
`SetWindowPos` move is overwritten during normal layout, and the header uses
parent-client coordinates for layout while `GetWindowRect` returns
screen-relative coordinates. Header geometry is therefore not independently
stable across relayouts.

## Related

- `list-header.md` — `SysHeader32` band and divider behavior.
- `scrollbars.md` — list-view non-client scrollbar.
- `tabs.md` — page dialogs and tab strip around the report tables.
