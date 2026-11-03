# shim.cpp

## Role

Freestanding C++ wrappers for the Win32/CRT imports that MinHook and the
generated code would otherwise pull from a CRT that the blob does not link.
It provides the small set of user32/kernel32 functions as plain functions
(no MSVC import thunks).

## Task Manager interaction

No direct interaction. The wrappers are called by MinHook
(`VirtualAlloc/VirtualProtect/VirtualFree`, `LoadLibraryW`/`GetProcAddress`,
`GetSystemInfo`, etc.) and by the `__imp_*` slots in `mh_imp.cpp`.

All Task Manager code is reached through the inline hooks installed via
`util.c`, not through this file.

## Notes

- Compiled with `-fno-exceptions -fno-rtti -nostdlib`.
- Must not reference libstdc++/libgcc helpers that are absent from the
  freestanding link (see `build.py` `CXXFLAGS`).

## Task Manager-side evidence

No direct Task Manager calls. The only Task Manager code that reaches this
file is the entry trampoline calling dark_init (	ramp.md); all subsequent
interaction goes through the inline hooks set up through util.c.


