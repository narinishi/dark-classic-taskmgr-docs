# common.h

## Role

Shared declarations for the freestanding shim:

- Win32 types and function-pointer typedefs;
- IAT-resolved globals (`iat_*`) and shared imports used by `stub.c`;
- MinHook declarations and the `hook_begin` / `hook_attach` / `hook_commit`
  transaction helpers (implemented in `util.c`);
- palette macros (`PACHENKO`);
- per-module prototypes.

## Current build defines

Only `UHOOKS_NO_DBG` remains (`1` by default). There are no feature toggles in
uhooks5; the old `LAYER_*`, `ELEMS_*`, `FURTHER_DARK_DTB`, `SCROLLBAR_DARK`,
`GRAPH_EDGE_DARK`, `TITLEBAR_DARK`, `COMBO_DARK`, `TEXT_PRESERVE_INTENT`,
`WALKER_POLL_FOREVER` and `ELEMS_LISTGRID` switches were removed.

## Notes

- The include guard still says `UHOOKS4_COMMON_H`; this is a leftover name, not
  a second code path.
- `stock.c` no longer hooks `CreatePen`; `p_real_CreatePen` is only used to
  create the shim's own pens.
