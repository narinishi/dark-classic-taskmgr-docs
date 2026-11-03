# mh_compat.c — removed in uhooks5

`mh_compat.c` no longer exists in `src/` and is not compiled.

It mapped the old Detours-style API onto MinHook. uhooks5 folded that small
surface into `util.c`:

- `hook_begin()` -> `MH_Initialize`
- `hook_attach()` -> `MH_CreateHook`
- `hook_commit()` -> `MH_EnableHook(MH_ALL_HOOKS)`

All modules now call those `util.c` helpers directly. See `util.md` and
`stub.md`.
