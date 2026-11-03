# status-bar.md — `msctls_statusbar32`

## How it is themed

- `theme.c` hooks `DrawThemeBackground`; for `msctls_statusbar32` it fills the
  background with `CLR_BG` and returns without calling the native painter.
- `swtheme.c` applies `DarkMode_Explorer` to the status bar.
- `textsink.c` forces light text.

There is no `FURTHER_DARK_DTB` toggle; this is the shipped behaviour.

## Notes

- The status bar’s native theme fill is not driven by a usable
  `GetThemeColor` lookup, which is why the narrow `DrawThemeBackground` fill is
  used.
- Status text is painted through the normal text draw hooks.
