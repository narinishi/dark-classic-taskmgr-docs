# properties.md â€” shell Properties dialog

This is the feature-level companion to
`reversing/properties/FINDINGS.md`; that file holds the raw measurements and
disassembly dumps.

## Task Manager code

The `Properties` context-menu command builds a `SHELLEXECUTEINFOW` and invokes
the shell (function `0x14001691c`, call at `0x1400169cb`):

```c
SHELLEXECUTEINFOW sei = {0};
sei.cbSize = 0x70;
sei.fMask  = SEE_MASK_INVOKEIDLIST | SEE_MASK_UNICODE;   // 0x400C
sei.hwnd   = <Task Manager main window>;
sei.lpVerb = L"properties";                              // 0x140025da0
sei.lpFile = <selected process image path>;
ShellExecuteExW(&sei);
```

`ShellExecuteExW` is a delay import (SHELL32, IAT `0x140031020`). It returns
after ~30 ms, before the dialog is closed.

## Why it looked partially themed

The shell creates the file-properties property sheet on a **dedicated worker
thread inside the Task Manager process**:

```
#32770 'firefox.exe Properties'   tid=12108
#32770 'Windows Task Manager'     tid=5628    (same PID)
```

Older process-global hook experiments darkened the sheet's
background/text but not its shell/comctl32 chrome â€” the â€œpartially/glitchedâ€
look.

## Current patch

`swtheme.on_event` records the main window's owning thread (`g_ui_tid`) and
skips every window owned by another thread; all colour/theme/text/stock hooks
also pass through on non-UI threads (`on_ui_thread()`).

Result: the shell Properties sheet is 100% native/light; the main UI stays
dark. The New Task/Run dialog is also created on a worker thread on current
builds, so it is likewise native (accepted).

## Evidence

- `reversing/properties/FINDINGS.md`
- `reversing/properties/xref_1400169cb.asm`
- `reversing/properties/test_props.c` / `test_props.exe`
- `decompiled/ShellExecuteExW__140011b04.c`, `ShellExecuteExW__1400095a0.c`

