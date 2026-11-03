# graphs.md — graph frames and graph surfaces

## What it is

Performance and Networking draw each graph inside a `DavesFrameClass` window
(grouped frame with a caption), containing an owner-drawn `Button` that paints
the graph and a numeric overlay.

## What is themed

- Frame captions/labels: light text via `textsink.c`.
- The graph/value **button's NC 3D outline**: dark via `graphedge.c`.
- The owner-draw button and its enclosing graph frame erase dark, removing the
  light erase flash on graph ticks.
- Redundant caption updates: `graphedge.c` drops an identical `WM_SETTEXT` from
  the frame, preventing the caption clear/redraw flicker.
- Standalone graph `ScrollBar` on Networking: dark via `swtheme.c`.
- Graph/value button subclassing is applied to newly created controls too:
  `graphedge_subclass_tree()` checks the event window itself before enumerating
  children. This is required for focused Networking view, which creates two
  extra adapter graphs after the initial page scan.
- `GetStockObject` remaps `WHITE_BRUSH`/`LTGRAY_BRUSH` to `DC_BG`,
  `WHITE_PEN` to `DC_GRAY`, and `BLACK_PEN` to `DC_EDGE`.
  `CreatePen` is **not** hooked; the app's saturated series pens pass through.

## What is NOT fully themed

The `DavesFrameClass` **group frame** around each graph is still light 3D.

- left/right/bottom edge lines are app paint, not the Button NC edge;
- the frame's erase is dark, but the etched border itself remains;
- taskmgr does not call `CreatePen`/`CreateSolidBrush` for those neutral edge
  colours, and `GetSysColor` is already remapped.

Candidate fix would be a post-`WM_PAINT` border-band overlay on
`DavesFrameClass`, or replacing the frame paint. Not implemented.

### Graph interior — non-goal

The graph background/grid/plot is app-painted and intentionally left alone.
Remaining coloured/plot pixels are not a theme gap.

## Focused Networking view

The initial Networking page shows only connected adapters. Double-clicking a
graph switches to focused view and can create additional adapter graphs
(`Wi-Fi 9`, `vEthernet (WSL ...)` in the test system). The root self-check fix
ensures those late controls receive the same Button/host handling as the
original four.
