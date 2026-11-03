# dui.c

## Role

Native-light bypass for DirectUI TaskDialogs (UAC virtualization prompt).

The TaskDialog content is DirectUI and uses theme classes such as
`TaskDialogStyle`, `TEXTSTYLE`, `CONTROLPANELSTYLE`. Its text is drawn on a
window-less memory DC inside `DirectUIHWND`; without a bypass the global text
hooks force light text onto the native light surface.

## Implementation

- Hooks `OpenThemeData`, `OpenThemeDataForDpi`, `CloseThemeData`.
- Records theme handles opened with DirectUI-only class names in a small table.
- `theme_is_directui(handle)` lets `theme.c` pass through `GetThemeColor`.
- Subclasses `DirectUIHWND`, tracks paint depth, and marks its root TaskDialog.
- `dui_should_bypass(dc)` lets `textsink.c` skip text forcing for DirectUI
  paint/DCs. If `WindowFromDC(dc)` returns null for a memory DC, it falls back
  to the foreground window and checks whether its root is a marked DUI root.
  This covers native TaskDialog button text that is drawn on window-less DCs.
- Marks the root and forces a root redraw after the DirectUI host appears.

## Result

The UAC TaskDialog remains native/light by design, with readable text.
Native TaskDialog button hot states also retain their native text colour. This
is not a regression; it is the intended behavior.

## Related

- `elements/dialogs.md`
- `reversing/uac-virtualization.md`
