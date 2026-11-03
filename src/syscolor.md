# syscolor.c

## Role

Defines the `GetSysColor` palette map and hooks:

- `hook_syscolor(int index)`
- `hook_sysbrush(int index)`

`stub.c` installs them as inline MinHook hooks on the real user32 exports. The
hook path is UI-thread scoped: other threads pass through unchanged.

## Palette

The map covers the standard UI indices used by Task Manager: window/menu/button
faces, text, 3D shadows/highlights, gray text, active caption, etc. The exact
values are in `elements/system-colors.md`.

## uhooks5 change

No module-IAT walker. `walker.c` and its polling background thread were
removed; inline hooks now cover all in-process callers, including comctl32.

## Brush cache

`hook_sysbrush` caches one brush per index and recreates it when the mapped
colour changes. `stub.c` initializes the cache.
