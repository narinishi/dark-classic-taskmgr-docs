# dialogs.md — page dialogs, spawned windows, DirectUI TaskDialogs

## Page dialogs (`#32770` children)

Each tab page is a child `#32770` dialog. `uahmenu.c` subclasses every child
`#32770` through `uah_child_dialog_cb`; `uah_wndproc` handles
`WM_ERASEBKGND` and every `WM_CTLCOLOR*` with `DC_BG` background and
`CLR_TEXT` text. This is what darkens page backgrounds and Performance value
fields.

## Spawned/owned dialogs

Top-level `#32770` windows created on the UI thread (Options/About/message
boxes) go through the same `uahmenu_on_top_window` path and get dark
erase/CTLCOLOR handling. Captions use `titlebar.c` (DWM dark caption).

## Thread scoping

Theming is scoped to Task Manager's main UI thread. Windows created on other
threads are left native: colour/theme/text/stock hooks pass through and the
WinEvent handler skips them.

Consequences:

- the shell file-properties sheet remains native/light;
- Task Manager's New Task / Run dialog remains native/light;
- the DirectUI UAC virtualization TaskDialog is intentionally native/light.

## DirectUI TaskDialogs (UAC virtualization)

The UAC prompt is a `DirectUIHWND` TaskDialog. Its content uses DirectUI-only
theme classes (`TaskDialogStyle`, `TEXTSTYLE`, `CONTROLPANELSTYLE`), and the
text is drawn on a window-less memory DC. Without handling, the text draw hooks
would force light text on the native light surface, making it unreadable.

`dui.c`:

- hooks `OpenThemeData` / `OpenThemeDataForDpi` / `CloseThemeData` and marks
  those DirectUI theme handles;
- subclasses `DirectUIHWND`, tracks paint depth, and marks the root TaskDialog;
- makes `theme.c` pass through `GetThemeColor` for DirectUI handles;
- makes `textsink.c` bypass text forcing for DirectUI paint/DCs.

The result is an intentionally native/light TaskDialog with readable text.

## What is not guaranteed

- System/common dialogs from other processes are out of scope.
- Worker-thread dialogs are deliberately native.
- Coverage of every custom-painted dialog is not guaranteed.
