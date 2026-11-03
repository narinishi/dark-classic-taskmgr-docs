# not-yet-themed.md — remaining gaps

Quick index of what is still missing, intentionally native, or unverified.

| element | status | notes |
|---|---|---|
| `DavesFrameClass` **group frame** around each graph (Performance + Networking) | **remaining** | light etched 3D line on left/right/bottom; app paint, needs a post-`WM_PAINT` border overlay or full paint replacement. See `graphs.md`. |
| ComboBox opened dropdown list (`ComboLBox`) | unverified | `combodark.c` is not compiled; the only combo is in the native worker-thread New Task dialog. |
| DirectUI UAC TaskDialog | **intentional native** | `dui.c` keeps the DirectUI TaskDialog native/light so its text remains readable. |
| New Task / Create New Task dialog | **intentional native** | created on a worker thread; UI-thread scoping leaves it light. |
| shell file-properties / system dialogs | out of scope | worker/other-process dialogs are not themed. |
| less-obvious menu surfaces (system menu, context menus, submenus) | likely dark, verify | normal popups use uxtheme preferred dark mode. See `menu-popups.md`. |
| graph interior / grid / plot pixels | non-goal | app paint; intentionally left as-is. |
| window border / DWM frame | minor | caption is dark; outer frame colour is not pursued. |

Themed and covered elsewhere: title bar, menu bar, tabs + pane outline, status
bar, list + header seam + body separators, list/standalone scrollbars, text,
buttons, graph/value button NC borders, Performance value fields, page/dialog
backgrounds, system colours, stock pens/brushes, and all report-list headers
(Applications, Processes, Services, Users).

The direct UI TaskDialog and worker-thread dialogs are deliberate native
islands, not regressions.
