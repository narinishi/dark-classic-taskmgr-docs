# graphedge.c

## Role

Dark NC border and erase handling for graph/value controls.

Detection:

```text
class == Button
(style & 0xF) == 0xB     BS_OWNERDRAW
(ex & 0x200) == 0x200    WS_EX_CLIENTEDGE
```

The subclass handles:

- `WM_NCPAINT`: fill the NC band with `DC_BG`, draw a 1px `DC_VIS_EDGE`
  frame, return without the native light 3D edge;
- `WM_ERASEBKGND`: dark fill;
- separates the enclosing `DavesFrameClass` host by geometry and subclasses it
  for dark erase.

The host frame subclass also drops an identical `WM_SETTEXT` from the graph
caption to prevent tick-time flicker.

## Implementation details

- `graphedge_init` resolves `GetPropW`, `SetPropW`, `GetWindowDC`,
  `ReleaseDC`, `GetWindowRect`, `EnumChildWindows`, `GetParent`,
  `GetClientRect`, and `GetWindowTextW`, then creates the cached `DC_BG` and
  `DC_VIS_EDGE` brushes.
- `gb_subclass` records the original subclass proc in `uhooks4_gb_orig` and
  marks the button with `uhooks4_gb_mark`. The host uses the parallel
  `uhooks4_gb_host_orig` / `uhooks4_gb_host_mark` properties.
- The host is found by enumerating the button parent's `DavesFrameClass`
  children and selecting the one whose window rectangle contains the button
  rectangle.
- After subclassing, the button is invalidated with
  `INVALIDATE | FRAME | UPDATENOW` so an already-painted native edge is
  replaced by the dark `WM_NCPAINT` path.
- `gb_proc` returns 0 after painting the NC band; `hostdark_proc` returns 1
  after the dark erase and drops unchanged caption text.

## Task Manager interaction

Task Manager creates the graph/value buttons and their frames in
`sub_14000be14`:

```c
v5 = CreateWindowExW(WS_EX_CLIENTEDGE, L"BUTTON", &g_140025b44,
                     0x48000A0B, 0, 0, 0, 0,
                     parent, id + 2500, NULL, NULL);
...
v8 = CreateWindowExW(WS_EX_NOPARENTNOTIFY, L"DavesFrameClass", &g_140025b44,
                     0x40000007, 0, 0, 0, 0,
                     parent, NULL, NULL, NULL);
SendMessageW(v8, WM_SETTEXT, caption_len, NULL);
```

`0x48000A0B` is `WS_CHILD|WS_VISIBLE|...|BS_OWNERDRAW` with
`WS_EX_CLIENTEDGE`; `DavesFrameClass` is the graph host frame. The Performance
page uses the same constructor with IDs `5000+` (`sub_14000ad24`).

## Current tree scan

```c
void graphedge_subclass_tree(HWND root) {
    if (!root || !p_gb_enum) return;
    gb_subclass(root);          /* late-created button itself */
    p_gb_enum(root, gb_cb, 0);  /* descendants */
}
```

The root self-check is required for controls created after a page-wide scan.
It was added after Networking focused view: the extra adapter graphs
(`Wi-Fi 9`, `vEthernet (WSL ...)`) received creation events but were never
subclassed because the old scan only enumerated their children.

## Thread scope

`graphedge_subclass_tree` is only called from `swtheme.on_event` /
`uahmenu_on_top_window`, which skip non-UI threads.

## Status

The graph/value Button NC border is fully themed, including focused Networking
view. The outer `DavesFrameClass` group border remains light app paint; see
`elements/graphs.md`.

## Evidence

- `decompiled/CreateWindowExW__14000be14.c` (`0x14000bedb`, `0x14000bf9d`)
- `decompiled/CreateWindowExW__14000ad24.c` (`0x14000aef5`, IDs `5000+`)
- `elements/graphs.md`
