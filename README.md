# Reversing notes

This directory documents the classic Task Manager UI and the `uhooks5` dark
theme shim injected into the clean Task Manager binary
(`taskmgr-dark-neo.exe`).

The notes are split by concern:

- `elements/` describes visible Task Manager controls and behaviours.
- `src/` maps each shim source file to the Task Manager code it affects, with
  decompiled evidence where available.

## Layout

```text
reversing/
  README.md       this index
  elements/       per-element UI notes
  src/            source-to-Task-Manager notes and file map
```

## `elements/` — UI-element notes

Start with `elements/general.md` for the overall architecture and palette.
`elements/not-yet-themed.md` indexes known gaps and intentional native areas.

| document | contents |
|---|---|
| `general.md` | shim architecture, module roles, palette, build/capture notes |
| `not-yet-themed.md` | remaining gaps and deliberately native surfaces |
| `dialogs.md` | page dialogs, spawned windows, DirectUI TaskDialogs |
| `window-background.md` | main-window and page backgrounds |
| `titlebar.md` | dark title bar |
| `menu-bar.md` | File / Options / View / Help bar |
| `menu-popups.md` | dropdown and context menus |
| `tabs.md` | tab strip and page outline |
| `status-bar.md` | `msctls_statusbar32` |
| `text.md` | text colour |
| `system-colors.md` | `GetSysColor` / `GetSysColorBrush` |
| `theme-colors.md` | `GetThemeColor` and `DrawThemeBackground` |
| `stock-objects.md` | stock pens and brushes |
| `buttons.md` | buttons, graph/value buttons, NC edges |
| `combo-dropdowns.md` | ComboBox controls |
| `misc-controls.md` | other themed window classes |
| `scrollbars.md` | list-view non-client and standalone scrollbars |
| `list-view.md` | `SysListView32` report tables |
| `list-header.md` | `SysHeader32` column-header band |
| `graphs.md` | graph frames and graph surfaces |
| `perf-value-fields.md` | Performance tab value fields |
| `run-dialog.md` | Task Manager’s New Task / Create New Task dialog |
| `uac-virtualization-dialog.md` | UAC virtualization menu item and TaskDialog |

## `src/` — source-to-Task-Manager notes

`src/README.md` contains the full source-file map, reading order, and
decompilation tooling notes. In brief:

| group | documents |
|---|---|
| infrastructure | `stub.md`, `tramp.md`, `shim.md`, `mh_extra.md`, `mh_imp.md`, `util.md`, `common.md` |
| hooks and painting | `syscolor.md`, `textsink.md`, `theme.md`, `stock.md`, `swtheme.md`, `uahmenu.md`, `titlebar.md`, `dui.md`, `graphedge.md` |
| not built / historical | `listgrid.md`, `combodark.md`, `walker.md`, `mh_compat.md` |
| shell dialog note | `properties.md` |

`listgrid.c` and `combodark.c` are not compiled into the current artifact.
`walker.c` and `mh_compat.c` were removed in `uhooks5`; their documents are kept
for migration history.

## Reading order

1. `elements/general.md`
2. the specific element document for the area being changed
3. the matching `src/` document for implementation and hook details
4. `src/README.md` for the complete source map and evidence conventions

## Conventions

- Addresses in the notes are virtual addresses in the clean base image, with
  image base `0x140000000`.
- `sub_XXXXXXXX` names come from angr. Descriptive labels were assigned by
  reading the decompiled control flow.
- Decompiled files and captures are evidence, not shipped code.
