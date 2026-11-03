# list-header.md — `SysHeader32` column header band

## What it is

The column header band above the report-mode list views (Applications,
Processes, Services, Users, Networking totals, etc.).

## How it is themed

- `swtheme.c` applies `DarkMode_ItemsView` to `SysHeader32`.
- `textsink.c` forces header text light.
- The header is intentionally **excluded** from the `DarkMode_Explorer`
  override: it has no scrollbar, and overriding it caused a white-flash fight
  with the listview variant.
- The header divider is shifted one pixel left after paint so it lines up with
  the native ListView body separator.

## Header seam fix

`swtheme.c` subclasses every `SysHeader32`. For each header item it reads the
live `HDM_GETITEMRECT` and shifts the native divider group left one pixel with
`BitBlt`:

```text
x-1 -> x-2   anti-alias
x   -> x-1   bright divider
x+1 -> x     background / clear old divider
```

It runs for `WM_PAINT`, `WM_PRINT` and `WM_PRINTCLIENT`. No ListView column
widths are changed, so text and hit-testing stay put. Verified on Applications,
Processes, Services and Users.

The full investigation is in `reversing/listview.md`.

## Notes

- Do not change header item widths to fix the seam: changing header item width
  also changes ListView column geometry and moves the body separator with it.
- Don't poll `SetWindowTheme`; it is a state change and causes repaint flashing.
