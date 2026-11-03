# util.c

## Role

Shared runtime helpers:

- `hook_begin()` — `MH_Initialize`, accepting already-initialized;
- `hook_attach()` — `MH_CreateHook` wrapper using the resolved target pointer;
- `hook_commit()` — `MH_EnableHook(MH_ALL_HOOKS)`;
- `resolve()` — `GetProcAddress` wrapper;
- `on_ui_thread()` — compares the current thread id with `g_ui_tid`;
- `eqw`, `eqi`, `streq`, `patch_slot`, `lum_of`, `abs_int`;
- disabled-by-default file logging (`dbg`, `dbg_hex`).

## uhooks5 change

The old `mh_compat.c` Detours-compatible layer was removed. Its
`hook_begin` / `hook_attach` / `hook_commit` API now lives directly in
`util.c`; all modules use those helpers.

## Notes

- `patch_slot` remains available for IAT-slot writes, but current colour hooks
  are inline MinHook hooks, not a module walker.
- Logging writes to the legacy uhooks4 path only when built with
  `-DUHOOKS_NO_DBG=0`.
