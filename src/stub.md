# stub.c

## Role

Entry point for the injected `.dark` section. `dark_init`:

1. resolves GDI32/user32/kernel32/uxtheme imports;
2. creates shared brushes and the system-colour brush cache;
3. calls each module's init;
4. runs one MinHook transaction:
   - `theme_attach`
   - `textsink_attach`
   - `stock_attach`
   - inline `GetSysColor` / `GetSysColorBrush` hooks
   - `dui_attach`
5. installs the WinEvent hook.

## Current init order

```text
theme_init
textsink_init
stock_init
uahmenu_init
graphedge_init
titlebar_init
swtheme_init
dui_init

hook_begin
theme_attach
textsink_attach
stock_attach
GetSysColor / GetSysColorBrush inline hooks
dui_attach
hook_commit
```

There is no walker thread, no `listgrid_init`, no `combodark_init`, and no
feature-toggle branching.

## Notes

- `g_ui_tid` is initialized from the current thread before hooks are attached.
- `dbg` logging is compiled out by default.
- `stub.c` keeps the original entry-point trampoline contract used by
  `inject.py`.
