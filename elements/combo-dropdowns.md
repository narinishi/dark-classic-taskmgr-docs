# combo-dropdowns.md — ComboBox controls

Status: **no ComboBox-specific module is compiled in uhooks5.**

`src/combodark.c` still exists, but it is not in `build.py`'s `MODULES` and is
not part of `uhooks5.exe`.

## Current behaviour

- The only Task Manager ComboBox is in the New Task / Create New Task dialog.
  That dialog is created on a worker thread and is intentionally native/light
  (see `run-dialog.md`).
- On the UI thread, `swtheme.c` would apply `DarkMode_Explorer` to a
  `ComboBox`; ordinary `WM_CTLCOLOR*` handling would theme its edit area.
- The opened dropdown list (`ComboLBox`) is a separate popup and remains
  unverified.

## Historical note

`combodark.c` used to paint a dark ComboBox client, edge and chevron for the
New Task dialog when that dialog was reachable from the UI thread. It is kept
only as reference; re-enabling it would require adding it to `MODULES` and
verifying the current worker-thread dialog path.
