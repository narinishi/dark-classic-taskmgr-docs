# mh_extra.cpp

## Role

Additional freestanding Win32 wrappers used by MinHook's allocator and hook
engine (memory queries/protection, module lookup, process/thread helpers,
`FlushInstructionCache`, etc.) that are not covered by `shim.cpp`.

## Task Manager interaction

Indirect only: MinHook calls these while creating/enabling hooks in the Task
Manager process. No Task Manager UI code is touched here.

## Task Manager-side evidence

No direct Task Manager calls. MinHook uses these wrappers while creating the
hooks listed in util.md; see stub.md for the single init path.

