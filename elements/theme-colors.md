# theme-colors.md — `GetThemeColor` and `DrawThemeBackground`

## `GetThemeColor`

`theme.c` hooks `GetThemeColor` and remaps, on the UI thread:

- `TMT_TEXTCOLOR` → `CLR_TEXT`
- `TMT_FILLCOLOR` → `CLR_BG`
- `TMT_BORDERCOLOR` → `DC_EDGE`

DirectUI theme handles are exempt (`theme_is_directui`) so the UAC TaskDialog
keeps native text/background colours.

## `DrawThemeBackground` allowlist

Only two classes get a replacement fill:

- `SysTabControl32` — tab pane/items and outlines;
- `msctls_statusbar32` — status background.

Every other `DrawThemeBackground` call passes through to the real theme
painter. There is no `FURTHER_DARK_DTB` gate anymore; the allowlist is the
shipped behaviour.

## Why not rewrite theme records

Record rewriting/policy-A was larger and unnecessary. Native-first drawing
plus narrow fills preserves geometry. Many native fills read theme records
directly and never call `GetThemeColor`, which is why the narrow
`DrawThemeBackground` fills remain necessary for tabs/status.
