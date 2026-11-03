# tramp.S

## Role

Freestanding entry stub placed in the appended `.dark` section and used as the
PE entry point of the injected image (`-Wl,-e,entry_stub`).

It preserves the volatile registers that the displaced entry bytes and
`orig_ctor` expect, calls the C runtime initialiser and the shim, then
continues at the original entry continuation.

## Pseudocode

```c
void entry_stub(void) {
    push(rax, rcx, rdx, r8, r9, r10, r11, rbx);
    rsp -= 0x28;                 // shadow space
    orig_ctor();                 // 0x140001984  _security_init_cookie
    dark_init();                 // shim init
    rsp += 0x28;
    pop(rbx, r11, r10, r9, r8, rdx, rcx, rax);
    jmp orig_ret;                // 0x1400011c0  __wgetmainargs
}
```

## Task Manager interaction

The stub is only reachable through the entry-point trampoline patched by
`inject.py`:

```
entry VA 0x140001440:  e9 <rel32>  ->  .dark VA 0x140069000
```

`build.py` derives `orig_ctor`/`orig_ret` from the displaced relative jumps:

```
orig_ctor = 0x140001984   (_security_init_cookie)
orig_ret  = 0x1400011c0   (__wgetmainargs)
```

This ordering guarantees every MinHook hook is live before Task
Manager's CRT and WinMain code run, so the first window already sees the dark
colour hooks.

## Evidence

- `decompiled/entry__140001440.c`
- `decompiled/orig_ctor__140001984.c`
- `decompiled/orig_ret__140001170.c`

