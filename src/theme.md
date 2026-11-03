# theme.c

## Role

Two uxtheme hooks:

1. `GetThemeColor` remap for UI-thread painting:
   - text -> `CLR_TEXT`
   - fill -> `CLR_BG`
   - border -> `DC_EDGE`
2. `DrawThemeBackground` narrow allowlist:
   - `SysTabControl32`: dark pane/item fills, shared 1px tab dividers, and
     selected-tab/pane corner connectors;
   - `msctls_statusbar32`: dark fill.

Every other theme draw passes through to the native painter.

## Tab item edges

For tab items (`part` 5..8):

- the item is filled with the active (`DC_BG`) or inactive (`DC_LIGHT`) fill;
- the pane (`part` 9) supplies the single divider under unselected tabs;
- inactive tabs clear an inset bottom row, preserving the vertical edge pixels
  one row lower so dividers meet the pane;
- non-last inactive tabs (`part` 5/6) clear their right edge, leaving the next
  tab's left edge as the single 1px divider;
- the last tab (`part` 7/8) keeps its right edge;
- selected tabs use a full-width bottom clear, so their right divider does not
  extend too far down, then draw the pane-side connector.

## DirectUI exception

`hook_GetThemeColor` returns the real colour unchanged when
`theme_is_directui(theme)` is true. This keeps the UAC virtualization
TaskDialog native and readable; see `dui.md`.

## Current status

The `FURTHER_DARK_DTB` gate was removed in uhooks5. The tab/status allowlist is
the shipped behavior. Edit value fields are handled by page-dialog
`WM_CTLCOLOR*`, not by this hook.

## Notes

Many native fills read theme records directly and never call
`GetThemeColor`; that is why the narrow `DrawThemeBackground` replacements are
still needed for tabs/status.
