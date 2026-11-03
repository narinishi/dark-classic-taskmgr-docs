# Source <-> Task Manager interaction notes

This directory maps the files in `src/` to the Task Manager code they
influence, with decompiled evidence from `taskmgr-dark-neo.exe`
(image base `0x140000000`).

Current artifact: **uhooks5**. `walker.c` and `mh_compat.c` were removed in
uhooks5; their historical notes are kept only to explain the migration.
`listgrid.c` and `combodark.c` exist but are **not compiled** into the current
build.

## Tooling

- `tools/api_xrefs.py` — maps imported APIs to their IAT slot VAs and finds
  `call [rip+slot]` sites in `.text`. Outputs `api_xrefs.md` / `api_xrefs.json`.
- `tools/decompile_batch.py` — loads the binary in angr, builds `CFGFast`, and
  decompiles selected functions into `decompiled/`.
- `decompiled/INDEX.md` — generated index of decompiled functions.

Environment: `uv-angr` (angr 10.0.4.post1). Unicorn is unavailable in this
install; decompilation does not need it.

## Reading order

Infrastructure first, then per-feature hooks:

- `stub.md`, `tramp.md`, `shim.md`, `mh_extra.md`, `mh_imp.md`, `util.md`,
  `common.md`
- `syscolor.md`, `textsink.md`, `theme.md`, `stock.md`
- `swtheme.md`, `uahmenu.md`, `titlebar.md`, `dui.md`
- `graphedge.md`, `listgrid.md`, `combodark.md`
- `properties.md` — why the shell Properties dialog is native (thread scope)

## File map

| src file | document | built? |
|---|---|---|
| `src/common.h` | `common.md` | yes |
| `src/dui.c` | `dui.md` | yes |
| `src/graphedge.c` | `graphedge.md` | yes |
| `src/mh_extra.cpp` | `mh_extra.md` | yes |
| `src/mh_imp.cpp` | `mh_imp.md` | yes |
| `src/shim.cpp` | `shim.md` | yes |
| `src/stock.c` | `stock.md` | yes |
| `src/stub.c` | `stub.md` | yes |
| `src/swtheme.c` | `swtheme.md` | yes |
| `src/syscolor.c` | `syscolor.md` | yes |
| `src/textsink.c` | `textsink.md` | yes |
| `src/theme.c` | `theme.md` | yes |
| `src/titlebar.c` | `titlebar.md` | yes |
| `src/tramp.S` | `tramp.md` | yes |
| `src/uahmenu.c` | `uahmenu.md` | yes |
| `src/util.c` | `util.md` | yes |
| `src/combodark.c` | `combodark.md` | no |
| `src/listgrid.c` | `listgrid.md` | no |
| `src/walker.c` | `walker.md` | removed |
| `src/mh_compat.c` | `mh_compat.md` | removed |

## Conventions

- Addresses are VAs in the clean base image.
- `sub_XXXXXXXX` names come from angr; labels such as "CTLCOLOR handler" were
  assigned by reading the decompiled control flow.
- Decompiled files are evidence, not shipped code. Some angr type artifacts
  (`IntPtr`, `st_*`) can be ignored.
