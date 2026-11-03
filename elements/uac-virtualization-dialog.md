# "UAC Virtualization" — spawn and rendering

## Summary

There is no persistent “UAC Virtualization” dialog owned by the process menu.
The label is a **checkable process-context-menu item (command ID 40070)**.
Choosing it posts `WM_COMMAND 40070`, and the command handler opens a
**comctl32 TaskDialog** confirmation with the warning text and
`Change virtualization` / `Cancel` buttons. After confirmation Task Manager
toggles the process token's virtualization flag.

The menu item is only enabled for processes where the token state makes sense;
it is checked when virtualization is currently enabled.

## Evidence and artifacts

- `decompiled/UAC_context_menu__1400126f4.c`
- `decompiled/UAC_menu_load__140002c48.c`
- `decompiled/UAC_change_handler__140016e5c.c`
- `decompiled/UAC_confirm_dialog__140014e00.c`
- `decompiled/UAC_dialog_callback__140015020.c`
- `decompiled/UAC_format_text__140005718.c`
- `decompiled/UAC_toggle_worker__140012604.c`
- `decompiled/UAC_error_dialog__140002414.c`
- runtime capture: `uac-virtualization-dialog.png`

## Resources used

| resource | value |
|---|---|
| menu `RT_MENU` 111 | process context menu template; item ID `40070`, text `UAC &Virtualization` |
| string 40075 | `Unable to Set Virtualization` |
| string 40077 | `Do you want to change virtualization for '%s'?` |
| string 40078 | `Changing virtualization on a process may lead to undesired results including loss of data. You should do this only for debugging.` |
| string 40079 | `Change virtualization` |
| string 10003 | application/window title (`Windows Task Manager`) |
| string 10016 | error-message format |
| string 20028 | `UAC Virtualization` column name (Processes table) |

## Spawn chain

### 1. Context menu construction

`sub_1400126f4` (base `0x1400126f4`) gets the list view (control 1009) and the
selected process info, then loads the popup menu:

```c
v4 = GetDlgItem(a0->field_8, 1009);              // Processes SysListView32
v5 = SendMessageW(v4, LVM_GETNEXTITEM, -1, LVNI_SELECTED);
v6 = sub_140012558(a0);                          // selected process info
v7 = sub_140002c48();                            // popup menu
```

`sub_140002c48` loads menu resource 111 and detaches its first submenu:

```c
v1 = LoadMenuW(g_14002e5a0, 111);
v2 = GetSubMenu(v1, 0);
RemoveMenu(v1, 0, MF_BYPOSITION);
DestroyMenu(v1);
return v2;
```

The virtualization state is `v6->field_e0`:

```c
v10 = v6->field_e0;
if (!v10 || v10 == 1)
    EnableMenuItem(v7, 40070, MF_GRAYED | MF_DISABLED);
else if (v10 - 1 == 2)                 // state 3
    CheckMenuItem(v7, 40070, MF_CHECKED);
```

Rendering and command posting:

```c
a0->field_50 = 1;
TrackPopupMenuEx(v7, 0, x, y, a0->field_8, NULL);
a0->field_50 = 0;
DestroyMenu(v7);
```

`uFlags = 0` (no `TPM_RETURNCMD`), so user32 renders the popup and posts
`WM_COMMAND` with the item ID to the owner (the Task Manager main window).

### 2. Command dispatch

The main command dispatcher computes a subtract chain from the command ID and
lands on `0x140012fdd` for `40070`:

```asm
; esi = WM_COMMAND id
sub ecx, 0x9c60      ; 40032
je  ...
sub ecx, 1
sub ecx, 1
sub ecx, 0x15        ; 40055
sub ecx, 0xb         ; 40066
sub ecx, 4           ; 40070
je  0x140012fdd
```

```c
0x140012ff6: call sub_140016e5c   ; UAC change handler
```

### 3. Confirmation TaskDialog

`sub_140016e5c` first asks for confirmation:

```c
if (sub_140014e00(a0, pid, 40077, 40078, 40079, 0) == 1) {
    /* proceed with token change */
}
```

`sub_140014e00` formats string 40077 with the process name
(`_vsnwprintf` wrapper `sub_140005718`), fills a `TASKDIALOGCONFIG`
(`cbSize = 160` in this build) and calls the comctl32 TaskDialog entry point
(imported as **ordinal 345**, `ordinal.345.comctl32.dll`, i.e.
`TaskDialogIndirect`):

```c
v4 = 160;                        // cbSize
v5 = parent_hwnd;
v6 = g_14002e5a0;                // hInstance
v7 = 8;                          // TDF_ALLOW_DIALOG_CANCELLATION
v8 = 10003;                      // window title string
v9 = &formatted_content;         // 40077 + process name
v10 = 40078;                     // warning/main instruction
v12 = &button_id;                // custom button id 1
v14 = callback_data;
v15 = sub_140015020;             // TaskDialog callback
ordinal.345.comctl32.dll(&v4, &button, 0, &verification);
```

Observed dialog tree (runtime, same process, **same UI thread as Task
Manager**):

```
#32770 'Windows Task Manager'
  DirectUIHWND
    CtrlNotifySink / SysLink / ScrollBar
    CtrlNotifySink
    Button 'Change virtualization'
    CtrlNotifySink
    Button 'Cancel'
```

The dialog callback (`sub_140015020`) sends message `1135`
(`TDM_ENABLE_BUTTON`) to enable/disable button 1 in response to TaskDialog
notifications.

If `TaskDialogIndirect` fails, the code falls back to `MessageBoxW` with the
application title and string 40078.

### 4. Applying the change

After the user accepts (custom button id 1):

```c
OpenProcess(PROCESS_QUERY_INFORMATION, FALSE, pid);
/* enumerate SystemProcessInformation (sub_140012604) and find the PID */
OpenProcessToken(process, TOKEN_ADJUST_DEFAULT, &token);
enabled = (state == 2);
SetTokenInformation(token, TokenVirtualizationEnabled, &enabled, sizeof(enabled));
CloseHandle(token);
CloseHandle(process);
```

State mapping seen in `sub_1400126f4`:

- `0` / `1` — menu item disabled (not applicable);
- `2` — item enabled/unchecked; selecting enables virtualization;
- `3` — item checked; selecting disables virtualization.

Any failure is reported through `sub_140002414`:

```c
LoadStringW(instance, 40075, title, 260);      // "Unable to Set Virtualization"
LoadStringW(instance, 10016, fmt,   260);
FormatMessageW(FORMAT_MESSAGE_FROM_SYSTEM, ..., error, fmt, ...);
MessageBoxW(hwnd, text, title, MB_ICONSTOP);
```

## Rendering behaviour under the shim

### Context menu

Standard user32 popup (`#32768`), painted by user32/menu theming. The shim's
menu theme (`SetPreferredAppMode` + `FlushMenuThemes`) and text sink apply; the
item shows a check mark when state 3 and is grayed for states 0/1 (the text
sink deliberately preserves the disabled-gray tone).

Runtime UIA for the item:

```
ControlType: MenuItemControl
AutomationId: 40070
Name: 'UAC Virtualization'
IsEnabled: True (for a normal process; disabled states gray it)
Patterns: Invoke; no Toggle/ExpandCollapse (it is a normal command item)
```

### TaskDialog

The TaskDialog is a `#32770` owned by the main window and is created on the
main UI thread (`GetWindowThreadProcessId(dialog) == main tid`), so the shim's
window-scoped theming sees it. However, its content is a `DirectUIHWND` tree,
not classic Button/Static/Edit controls. The captured dialog under `uhooks5`
is mostly native DirectUI artwork:

```
white (255,255,255) ~47,973 px
dark  (32,32,32)    ~12,897 px
near-white 240      ~1,777 px
black text          ~1,730 px
```

So the context menu is dark-themed, but the TaskDialog's DirectUI content does
not fully inherit the shim's classic-control palette. The caption is string
10003, which is why the window title is `Windows Task Manager` rather than
`UAC Virtualization`.

## Flow diagram

```
right-click process
  -> sub_1400126f4 builds popup from menu resource 111
     -> enable/check item 40070 from process state +0xE0
     -> TrackPopupMenuEx(..., flags=0, ...)
        -> WM_COMMAND 40070
           -> command dispatcher -> sub_140016e5c
              -> sub_140014e00 (TaskDialogIndirect, comctl32 ord 345)
                 -> 40077 content, 40078 warning, 40079 "Change virtualization"
                 -> Cancel / Change virtualization
              -> OpenProcess / OpenProcessToken
              -> SetTokenInformation(TokenVirtualizationEnabled)
              -> on failure: MessageBoxW(40075 "Unable to Set Virtualization")
```
