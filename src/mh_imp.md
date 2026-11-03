# mh_imp.cpp

## Role

Defines the `__imp_*` data slots that MinGW-generated code expects for
dllimported functions, pointing them at the freestanding wrappers from
`shim.cpp` / `mh_extra.cpp`.

## Task Manager interaction

None directly. Without these slots, MinHook and the shim would emit imports
that the freestanding link cannot resolve. The effect on Task Manager is the
same as `shim.cpp`: it only enables the hook engine.

## Task Manager-side evidence

No direct Task Manager calls. These slots exist only so MinHook and the hook
bodies link in the freestanding blob; the Task Manager code paths are the
decompiled call sites listed in pi_xrefs.md.
