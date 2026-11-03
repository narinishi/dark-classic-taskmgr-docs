# textsink.c

## Role

Hooks the text draw entry points:

- `ExtTextOutW`
- `TextOutW`
- `DrawTextW`
- `DrawTextExW`

`force_light_text()` sets `CLR_TEXT`, except:

- disabled gray is preserved (`is_disabled_gray`);
- DirectUI paint/DCs bypass via `dui_should_bypass`.

`SetTextColor` is **not** hooked in uhooks5. `textsink_raw_*` helpers expose the
pre-hook functions for `uahmenu.c`’s menu painter so menu item colours are not
overridden.

## Thread scope

Non-UI-thread calls pass through unchanged.

## DirectUI

`dui.c` marks DirectUI theme handles, paint depth and the root TaskDialog.
`dui_should_bypass(dc)` is checked before forcing light text, keeping the UAC
TaskDialog’s native text readable.
