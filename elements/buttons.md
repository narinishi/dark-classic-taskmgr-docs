# buttons.md — buttons, graph/value buttons, and NC edges

## Ordinary buttons

`swtheme.c` applies `DarkMode_Explorer` to `Button` windows. Text colour comes
from `textsink.c`; backgrounds come from the theme variant and/or the
`WM_CTLCOLOR*` path for page/dialog controls.

## Owner-drawn graph/value buttons

The graph and numeric value controls are owner-drawn buttons:

```text
style low nibble 0xB = BS_OWNERDRAW
ex-style 0x200      = WS_EX_CLIENTEDGE
```

The light 3D band around them is the classic client-edge NC paint, not a theme
record. `graphedge.c` subclasses exactly those controls and handles
`WM_NCPAINT`:

- fill the non-client band with `DC_BG`;
- draw a 1px `DC_VIS_EDGE` frame;
- leave the owner-drawn client content untouched;
- invalidate the frame after subclassing so late controls are restyled.

`graphedge_subclass_tree()` now calls `gb_subclass(root)` before enumerating
children. That catches buttons created after the last page-wide scan, including
the extra graphs created by Networking focused view.

## Related diagnostics

See `graphs.md` for the remaining `DavesFrameClass` group border and graph
surface limitations.
