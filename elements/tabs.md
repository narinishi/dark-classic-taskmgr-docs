# tabs.md — tab strip and page outline

## What it is

The tab strip (`SysTabControl32`) across Applications, Processes, Performance,
Networking, Users and Services, plus the pane outline around the page body.

## How it is themed

`theme.c` handles `DrawThemeBackground` for `SysTabControl32` in
`hook_DrawThemeBackground_fd`:

- fills the pane (part 9) with `CLR_BG`, then draws one `FrameRect` using
  `DC_VIS_EDGE`;
- fills tab items (parts 5..8) with the active/inactive fill;
- draws one `FrameRect` per item;
- for the selected tab, re-fills its bottom 1px so it joins the pane;
- restores the first/last selected-tab corner connector so the pane outline
  meets the selected tab.

Inactive tabs use `DC_LIGHT`; active tabs use `DC_BG`.

`swtheme.c` applies `DarkMode_ItemsView` to `SysTabControl32`.

## Current status

The tab fill and outline are unconditional. There is no `FURTHER_DARK_DTB`
gate in uhooks5.

## Notes

- The pane/body fill is why page backgrounds can look already dark even before
  the page dialog’s `WM_ERASEBKGND`.
- Header seam alignment is separate; see `list-header.md`.
