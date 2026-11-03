# combodark.c

## Role

Historical ComboBox painter: dark client fill, 1px edge, chevron, and edit
reprint on `WM_PRINT`.

## Current status

`combodark.c` is **not compiled** in uhooks5. It is absent from `build.py`'s
`MODULES`.

The only Task Manager ComboBox is in the New Task/Create New Task dialog,
which is created on a worker thread and is intentionally native/light. The
shipped build therefore has no UI-thread ComboBox to theme.

## Re-enabling

Would require adding `combodark` to `MODULES` and verifying the dialog’s
current thread/creation path. See `elements/combo-dropdowns.md`.
