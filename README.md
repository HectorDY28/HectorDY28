<!-- BANNER -->
<p align="center">
  <img src="https://i.imgur.com/8Km9tLL.png" width="100%" alt="kernel dump"/>
  <!-- TROQUE PELA SUA IMAGEM: faça upload do banner gerado no repo e troque o link acima -->
</p>

```
┌─[0x00@void]─[~/kernel]─[03:47:12]─[pts/0]
└──╼ $ whoami --verbose
> MALWARE_ANALYST // prodigio_autodidata // binary_exploiter // low_level_hacker
> STATUS: [ RING_0 ] | UPTIME: 3d 06h 14m | MODE: nocturnal_build
```

### [0x01] — IDENTITY_DUMP

```diff
@@ IDENTITY @@
+ 👾  Malware Analyst      -> unpacking, deobfuscation, anti-anti-debug
+ 🧠  Prodígio Autodidata  -> learning by breaking things since ring3
+ 📟  Binary Exploiter     -> ROP / ret2libc / heap feng shui
+ 🔌  Low-Level Hacker     -> C / ASM / kernelspace / bare-metal
```

---

### [0x02] — SUBSYSTEM & TOOLING METRICS

```ini
; kernel .config stats - build: 2024-11-02 02:17 UTC - host: nocturnal

C           [=====================================       ] 38.2%  // Core Kernel & Memory Management
C++         [========================                    ] 22.0%  // Subsystems & Driver Architectures
Zig         [============                                ] 12.1%  // Modern Tooling & Interop Layer
ASM x86_64  [========                                    ]  8.5%  // CPU Context Switching & Inline Opcodes
Bash        [======                                      ]  6.2%  // Automation & Kernel Module Loading
Lua         [====                                        ]  4.5%  // Neovim Config & Embedded Scripting
Café        [====                                        ]  3.5%  // Adenosine Receptor Antagonist & Clock Overclocking
Makefile    [===                                         ]  2.5%  // Compilation Toolchain & Build Automation
HolyC       [==                                          ]  2.5%  // Ring 0 Exploration / Divine Computations
```

---

### [0x03] — CORE_DUMPS // DO NOT TOUCH AFTER 03:00

> **Nota:** Não mexer no Memory Allocator depois das 3 da manhã. Última vez deu triple fault e corrompeu a page table. Sério.

<div style="display:flex; gap:12px; flex-wrap:wrap; font-family: 'JetBrains Mono', 'Cascadia Code', monospace;">

<!-- TERMINAL 1 -->
<div style="flex:1; min-width:340px; background:#0A0A0A; border:2px solid #5D0F12; color:#FFFFFF; padding:0;">

<div style="background:#5D0F12; color:white; padding:4px 8px; font-weight:bold; display:flex; justify-content:space-between;">
<span>vim ~ kernel/mm/slab.c [MEMORY ALLOCATOR] — RW</span><span>■ □ x</span>
</div>

```c
// kmem_cache_alloc - SLAB/SLUB - 03:11:42
// FIXME: race no free_list se preempt_count == 0
#include <linux/slab.h>
#include <linux/spinlock.h>

struct kmem_cache *cachep;
static DEFINE_SPINLOCK(alloc_lock);
static void *free_list = NULL;

void *kmalloc_simple(size_t n) {
    unsigned long flags;
    void *ptr;

    spin_lock_irqsave(&alloc_lock, flags);
    if (unlikely(!free_list)) {
        init_pool(POOL_ORDER_3); // BUG: may sleep
    }
    ptr = free_list;
    free_list = *(void **)free_list; // pop LIFO
    // poison: 0xDEADBEEF
    memset(ptr, 0xEF, n);
    spin_unlock_irqrestore(&alloc_lock, flags);
    return ptr; // 0(1) alloc < 4 cycles fastpath
}
// [ KERNEL PANIC - not syncing: corrupted free_list @ 0xffffffffc00010a0 ]
```

</div>

<!-- TERMINAL 2 -->
<div style="flex:1; min-width:340px; background:#0A0A0A; border:2px solid #5D0F12; color:#FFFFFF; padding:0;">

<div style="background:#5D0F12; color:white; padding:4px 8px; font-weight:bold; display:flex; justify-content:space-between;">
<span>vim ~ kernel/sched/core.c [SCHEDULER] — RW</span><span>■ □ x</span>
</div>

```c
// scheduler - CFS + rt - context_switch - 03:44:09
// NOTE: nao mexer no vruntime depois de cafe
#include <linux/sched.h>
#include <asm/switch_to.h>

struct rq *rq = cpu_rq(smp_processor_id());
struct task_struct *prev = rq->curr, *next;

next = pick_next_task(rq, prev, &rf);
if (likely(prev != next)) {
    rq->nr_switches++;
    RCU_INIT_POINTER(rq->curr, next);
    // switch_mm_irqs_off(prev->mm, next->mm, next);
    context_switch(rq, prev, next); // __switch_to_asm
    // unreachable: jmp to next->thread.sp
}
// sched_debug: load: 0.42 1.03 0.88 | runnable: 3 | iowait: 0.12%
// [  241.992341] allocd[003]: segfault at 0 ip 00007f... sp 00007ff... 
```

</div>

</div>

<div style="margin-top:12px; background:#0A0A0A; border:1px dashed #5D0F12; color:#A0A0A0; font-family:monospace; padding:8px; font-size:12px;">
<span style="color:#722F37;">></span> htop ~ processes <span style="float:right">[up 3d 06:14 - load: 0.42 1.03 0.88 - mem: 128/512MB]</span><br>
PID &nbsp;&nbsp;TASK&nbsp;&nbsp;&nbsp;&nbsp;MEM&nbsp;&nbsp;STATUS&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;EIP<br>
001&nbsp;&nbsp;&nbsp;idle&nbsp;&nbsp;&nbsp;&nbsp;4K&nbsp;&nbsp;&nbsp;sleep&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;0xffffffff810003a1<br>
002&nbsp;&nbsp;&nbsp;kworker&nbsp;16K&nbsp;&nbsp;&nbsp;run&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;0xffffffff8101a2ff<br>
003&nbsp;&nbsp;&nbsp;allocd&nbsp;&nbsp;&nbsp;128K&nbsp;&nbsp;run&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;0x00007f3a1c00deaf // &lt;-- leak<br>
> hacker@night:~$ <span style="background:white; color:black;">&nbsp;</span>
</div>

---

### [0x04] — NIGHT_OPS

```bash
$ cat ~/.nocturnal.conf

BUILD_HOURS="22:00-05:00"
EDITOR="nvim --listen /tmp/nvim.sock"
SHELL="zsh + starship + tmux"
THEME="wine#5D0F12 + #FFFFFF + #0A0A0A"
CAFFEINE_LEVEL=9001
MUSIC="phonk // dark ambient // $random_lofi"

$ ./build.sh --release --no-sleep --unsafe
[ok] kernel built in 4.21s - 0 warnings (suppressed)
```

<p align="center">
  <img src="https://img.shields.io/badge/RING-0-5D0F12?style=for-the-badge&logo=linux&logoColor=white" />
  <img src="https://img.shields.io/badge/MODE-NOCTURNAL-0A0A0A?style=for-the-badge&logo=clockify&logoColor=white" />
  <img src="https://img.shields.io/badge/CAFE-OVERFLOW-FFFFFF?style=for-the-badge&labelColor=5D0F12" />
  <br>
  <sub>built at 03:47 — if it crashes, it was intentional // stack smashing is a feature</sub>
</p>

<!-- glitch footer -->
```
01001001 11010010 00110011 1010100  // wine / white / void / root
[ kernel-6.8.1 ] — built 2024-11-02 02:17 UTC — tainted: G O E
```
