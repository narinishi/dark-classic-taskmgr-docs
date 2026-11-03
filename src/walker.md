# walker.c — removed in uhooks5

`walker.c` no longer exists in `src/` and is not compiled.

It was a runtime PE import-table walker that patched `GetSysColor` /
`GetSysColorBrush` IAT slots in every loaded module, with an optional background
polling thread.

uhooks5 uses inline MinHook hooks on the real exports in `syscolor.c`, which
cover all in-process callers without a module walker or polling thread.

This file is kept as migration history; see `syscolor.md` and `util.md` for the
current implementation.
