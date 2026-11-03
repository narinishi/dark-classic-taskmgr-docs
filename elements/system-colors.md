# system-colors.md — `GetSysColor` / `GetSysColorBrush`

## Current implementation

`syscolor.c` defines the colour map and provides:

- `hook_syscolor(int index)`
- `hook_sysbrush(int index)`

`stub.c` installs both as inline MinHook hooks on the real user32 exports.

There is **no module-IAT walker** in uhooks5. `walker.c` and its background
polling thread were removed; inline hooks cover all in-process callers.

## Thread scope

Non-UI-thread calls are passed through to the original functions. This keeps
worker-thread dialogs such as the shell properties sheet and New Task native.

## Brush cache

`hook_sysbrush` caches created brushes per index and recreates one if the
remapped colour changes. The cache is initialized in `stub.c`.

## DirectUI

DirectUI TaskDialogs intentionally use native theme/colour paths; see
`dui.c` and `dialogs.md`.
