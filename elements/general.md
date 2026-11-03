# general.md — how uhooks5 themes the Task Manager window

`uhooks5` is a per-process dark theme shim injected into the clean classic Task
Manager binary (`taskmgr-dark-neo.exe`). It never changes global system state
(no `SetSysColors`, no registry, no shared theme tables); every effect is local
to the process and scoped to the Task Manager UI thread.

## Architecture

| module | role |
|---|---|
| `stub.c` | resolves shared imports, owns `dark_init`, runs the hook transaction |
| `util.c` | `resolve`, hook transaction helpers (`hook_begin`/`hook_attach`/`hook_commit`), logging helpers |
| `syscolor.c` | `GetSysColor` / `GetSysColorBrush` palette remap |
| `theme.c` | `GetThemeColor` remap + narrow `DrawThemeBackground` allowlist (tab/status fills + tab outlines) |
| `textsink.c` | forces light text through `ExtTextOutW` / `TextOutW` / `DrawTextW` / `DrawTextExW`; DirectUI bypass |
| `stock.c` | remaps `GetStockObject` brushes/pens |
| `swtheme.c` | dark `SetWindowTheme` variants, WinEvent re-application, header seam fix, module dispatch |
| `uahmenu.c` | menu-bar painter, top-level/page-dialog background + `WM_CTLCOLOR*` |
| `graphedge.c` | dark NC border for owner-drawn graph/value buttons + graph erase/flicker handling |
| `titlebar.c` | per-window DWM dark caption |
| `dui.c` | DirectUI TaskDialog theme/text bypass (UAC virtualization prompt) |

`listgrid.c` and `combodark.c` still exist in `src/` but are **not compiled**
in the shipped build. `walker.c` and `mh_compat.c` were removed; their roles are
covered by inline MinHook hooks in `syscolor.c` and the helpers in `util.c`.

`dark_init` order: resolve imports â†’ create brushes â†’ per-module `*_init` â†’
one MinHook transaction (`hook_begin`/attach/`hook_commit`) â†’ `SetWinEventHook`
for window creation/show events.

### Hooking backend

MinHook (`minhook-master/`) is used for all inline hooks. `shim.cpp`,
`mh_extra.cpp` and `mh_imp.cpp` provide the freestanding Win32/CRT symbols
MinHook needs. Build uses LTO and no C runtime. Debug logging is compiled out
by default (`UHOOKS_NO_DBG=1` in `common.h`; build with
`-DUHOOKS_NO_DBG=0` to re-enable the file log).

There are no feature toggles in the shipped source anymore. The uhooks5
simplification removed `LAYER_*`, `ELEMS_*`, `FURTHER_DARK_DTB`,
`SCROLLBAR_DARK`, `GRAPH_EDGE_DARK`, `TITLEBAR_DARK`, `COMBO_DARK`,
`TEXT_PRESERVE_INTENT`, `WALKER_POLL_FOREVER`, and `ELEMS_LISTGRID`.
`UHOOKS_NO_DBG` is the only build define still used.

## Palette (`common.h`, `PACHENKO` build)

| macro | value |
|---|---|
| `DC_BG` / `CLR_BG` | `26,26,26` |
| `DC_FG` | `200,200,200` |
| `CLR_TEXT` | `200,200,200` |
| `DC_EDGE` | `2,2,2` |
| `DC_VIS_EDGE` | `115,115,115` |
| `DC_LIGHT` | `57,51,50` |
| `DC_GRAY` | `69,69,69` |
| `DC_ACC` | `90,103,121` |
| `DC_MENUHILITE` | `57,51,50` |

## Layout

Sources (`.c`, `.h`, `.cpp`, `.S`) live in `src/`; intermediate objects
(`.o`, `.elf`, `.bin`, generated `proto.ld`) go in `bin/`; the final
`uhooks5.exe` stays in the project root.

## Build / capture

```powershell
uv-disasm\.venv\Scripts\python.exe build.py uhooks5.exe -DELEMS_LISTGRID=0
uv-disasm\.venv\Scripts\python.exe uhooks5\capture_win.py --process uhooks5 --tab <Tab> --output <png>
```

`-DELEMS_LISTGRID=0` is historical and has no effect now; `listgrid` is not in
`MODULES`.

`capture_win.py` uses occlusion-proof `PrintWindow(PW_RENDERFULLCONTENT)` and
content-verifies the requested tab via UIA. It waits about 0.3 s before capture.

## Current behaviour notes

- Applications, Processes, Services, Users and other report-list headers use a
  post-paint `BitBlt` shift of the native header divider pixels, so the header
  seam aligns with the native body separators. See `list-header.md`.
- DirectUI TaskDialogs (including the UAC virtualization prompt) intentionally
  bypass the dark text/theme forcing so their native text stays readable; see
  `dialogs.md`.
- The shell file-properties sheet and Task Manager's New Task/Run dialog are
  created on worker threads and remain native/light.
- Graph/value owner-drawn buttons are themed; the outer `DavesFrameClass` group
  border remains light (see `graphs.md`).
- `combodark` and `listgrid` are not part of the current artifact.

## Element docs

See the other files in this folder for per-element notes. The remaining gaps
are indexed in `not-yet-themed.md`.

