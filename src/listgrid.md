# listgrid.c

## Role

Optional body-column separator painter. It subclasses `SysListView32`, reads
header item rects, and after `WM_PAINT`/`WM_PRINT`/`WM_PRINTCLIENT` draws a 1px
line at each column boundary.

## Current status

`listgrid.c` is **not compiled** in uhooks5. It is absent from `build.py`'s
`MODULES`.

Current dark ListView rendering already includes native body separators, and
the header seam is fixed separately in `swtheme.c`. Enabling `listgrid` as-is
would draw at the body-side coordinate (`ir.right - 1`), not move the header
line.

## Not the header-seam fix

The header seam fix shifts native `SysHeader32` divider pixels left one; see
`reversing/listview.md`. It does not use `listgrid`.
