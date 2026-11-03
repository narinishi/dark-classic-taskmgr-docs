# menu-popups.md — dropdown/context menus

Status: **themed via the app-wide uxtheme dark-menu calls.** The normal
surfaces are dark; the less-obvious surfaces below are still worth verifying.

## Why they appear dark

`swtheme_init` resolves and calls undocumented uxtheme ordinals:

```
ordinal 135  SetPreferredAppMode(2)
ordinal 104  RefreshImmersiveColorPolicyState()
ordinal 136  FlushMenuThemes()
```

`SetPreferredAppMode(2)` + `FlushMenuThemes()` enable the system dark menu
rendering for this process, so normal dropdown menus (`#32768`) come up dark
without us subclassing them. `textsink.c` keeps item text light.

Disabled items: the text sink preserves the neutral disabled gray (`~0x6D6D6D`), so grayed-out menu commands are visually distinct from enabled ones.

## What to verify

Less obvious menu surfaces that may not follow the same path:

- window **system menu** (`Alt+Space`)
- **right-click context menus** on the process list / column header
- **cascading submenus**
- menus of owned dialogs / message boxes

Capture with a menu open (`capture_win.py --menu-accel <key>` and
`--popups-prefix`) and sample the popup pixels. If all are dark, this element
is done.

## If some surface is still light

Fallback (not implemented): subclass the popup window on show and handle
`WM_ERASEBKGND` + `WM_CTLCOLOR*`/`WM_DRAWITEM` â†’ fill `DC_BG`, highlight
`DC_MENUHILITE`, text `CLR_TEXT`. Owner-drawing the menu is a larger option.

