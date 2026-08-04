<div align="center">

![header](https://capsule-render.vercel.app/api?type=waving&color=4A0000&height=200&section=header&text=HECTOR&fontColor=8B0000&fontSize=80&animation=fadeIn&fontAlignY=38&descAlignY=62&desc=SYSTEM%20LEVEL%20//%20RING%200%20ACCESS%20GRANTED&descAlign=50)

[![Status](https://img.shields.io/badge/STATUS-03%3A17_AM_%7C_KERNEL_PANIC_IMMINENT-0A0000?style=for-the-badge&labelColor=0A0000&color=8B0000)]()
[![Mode](https://img.shields.io/badge/MODE-RING0_%2F_NO_ABSTRACTIONS-0A0000?style=for-the-badge&labelColor=0A0000&color=4A0000)]()

</div>

<!-- JANELA 1: /proc/self/status -->
<table><tr><td>

```ansi
╭─ ●●● ── root@void:~# /proc/self/status ──────────────────────────────────╮
│ root@void:~# cat /proc/self/status | grep -i hector                      │
│                                                                          │
│ Name: Hector                                                             │
│ State: R (running) - 03:17 AM [ uptime: 47h:12m ]                        │
│ VmLck: 0 kB [ SWAPPING DISABLED - mlockall(MCL_FUTURE) ]                 │
│ CapEff: 000003fffffff [ CAP_SYS_ADMIN | CAP_SYS_PTRACE | CAP_SYS_RAWIO ] │
│                                                                          │
│ [ CRITICAL ] NO HIGH-LEVEL ABSTRACTIONS BEYOND THIS POINT.               │
│ [ WARNING ] YOU ARE ENTERING PRIVILEGED EXECUTION MODE.                  │
╰──────────────────────────────────────────────────────────────────────────╯

```

</td></tr></table>

<!-- JANELA 2: void_main.c -->
<table><tr><td>

```c
// ── hector@void:~/void_main.c [ -ffreestanding -nostdlib -O0 -g ] ──
__attribute__((naked, noreturn)) void _start() {
    __asm__ volatile (
        "cli\n\t"                           // IF=0
        "mov \$0x4A0000, %%rsp\n\t"           // set our own stack
        "mov \$0xDEADBEEFCAFEBABE, %%rax\n\t" // operator signature
        "call void_main\n\t"
        "hlt\n\t"
        ::: "rax", "memory"
    );
}

void void_main() {
    // [ IDT/GDT/TSS manipulation | page fault handler | cr0.WP bypass ]
    // [ ROP/JOP chaining | tcache poisoning | fastbin dup | House of Force ]
    // [ ELF packer | .text obfuscation | polymorphic x86_64 shellcode ]
    while(1) {
        trace_syscalls(); // ptrace(PTRACE_SYSCALL) + seccomp-bpf evasion
        dump_vma();       // /proc/[pid]/maps + pagemap
    }
} █
```

</td></tr></table>
