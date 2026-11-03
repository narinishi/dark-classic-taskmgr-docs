# run-dialog.md — Task Manager’s New Task / Create New Task

Status: **native (light) on current builds.** The New Task dialog is created on
a worker thread, so the UI-thread-scoped theming leaves it unthemed. This is
accepted.

## What this covers

Task Manager File → New Task / Create New Task is a `#32770` created by the
Task Manager process on a worker thread.

The Windows `Win+R` Run dialog belongs to Explorer/shell32 and is a different
process; it is out of scope.

## Current behaviour

- UI-thread scoping in `swtheme.c` skips the worker-thread dialog.
- `uahmenu_on_top_window` is not applied to it.
- The old post-paint fill for spawned dialogs was removed as unnecessary for
  the current worker-thread path.
- `combodark.c` exists but is not compiled in uhooks5; it would only matter if
  the dialog were brought onto the UI thread.
- The dialog therefore keeps native light background, controls, ComboBox and
  caption.

## Historical note

Older uhooks4 experiments themed the dialog when it was created on the UI
thread, including a post-paint background fill and a manually drawn ComboBox
chevron. Those paths are not part of the shipped uhooks5 artifact.
