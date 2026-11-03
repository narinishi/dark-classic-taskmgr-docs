# scrollbars.md — listview non-client and standalone scrollbars

## ListView non-client scrollbar

`swtheme.c` applies `DarkMode_Explorer` to every `SysListView32`. This gives
the dark non-client scrollbar for Processes, Services, Networking totals, etc.
`DarkMode_ItemsView` leaves the scrollbar track light, so the ListView keeps the
Explorer variant.

## Standalone `ScrollBar`

The Networking graph area has a real `ScrollBar` child window. It is included
in the Explorer class list in `swtheme.c`, so it uses the dark track/thumb.

## Current behaviour

- No `SCROLLBAR_DARK` toggle exists in uhooks5; the Explorer behaviour is
  unconditional.
- Header/toolbar/tab/listbox controls intentionally stay on
  `DarkMode_ItemsView`.
- Do not poll `SetWindowTheme`; apply once per window.
