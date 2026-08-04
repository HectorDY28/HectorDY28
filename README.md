<div align="center">

# HectorDY

[![Typing SVG](https://readme-typing-svg.herokuapp.com?font=JetBrains+Mono&weight=500&size=20&pause=1000&color=FFFFFF&center=true&vCenter=true&width=600&lines=C+%7C+C%2B%2B+%7C+Rust+%7C+Zig+%7C+Assembly+%7C+HolyC)](https://git.io/typing-svg)

> Reverse Engineer | 88 nights of code | C++/Performance > all.

</div>

---

### 🧠 sobre mim

Autodidata em Engenharia de Software, Kernels & RAM.
Amante de Maquinas, Algoritmos e Malwares.

- ☢️ Não mexer no Memory Allocator depois das 3 da manhã...
- ⚙️ Ponteiros, Scheduler, Deadlocks, Registers e Assembly
- 🦎 Usuário de Zig, aprendi num fim de semana só de bobeira.

---

### ⚙️ stack principal

<p align="center">
  <img src="https://skillicons.dev/icons?i=c,cpp,rust,zig" />
</p>

<p align="center">
  <img src="https://img.shields.io/badge/C-0A0A0A?style=for-the-badge&logo=c&logoColor=white" />
  <img src="https://img.shields.io/badge/C++-0A0A0A?style=for-the-badge&logo=cplusplus&logoColor=white" />
  <img src="https://img.shields.io/badge/Rust-0A0A0A?style=for-the-badge&logo=rust&logoColor=white" />
  <img src="https://img.shields.io/badge/Zig-0A0A0A?style=for-the-badge&logo=zig&logoColor=F7A41D" />
  <img src="https://img.shields.io/badge/Assembly-0A0A0A?style=for-the-badge" />
  <img src="https://img.shields.io/badge/HolyC-0A0A0A?style=for-the-badge&logo=templeos&logoColor=white" />
</p>

| Linguagem | Utilidades |
| :--- | :--- |
| **C** | Meu xodô, primeiro contato com programação |
| **C++** | Manual de avião, mas é interessante |
| **Rust** | O compilador que me odeia |
| **Zig** | Uso as vezes pra testar uns negócios ai |
| **Assembly** | x86_64 / AArch64. Quando C já não tanca |
| **HolyC** | Só pra brincar |

---

### 🛠️ Ideias

```c
#include <linux/kernel.h>
#include <linux/slab.h>
#include <linux/errno.h>

struct device_context {
    void *buffer;
    size_t size;
};

int device_initialize(struct device_context *ctx)
{
    if (!ctx)
        return -EINVAL;

    ctx->size = PAGE_SIZE;

    ctx->buffer = kmalloc(ctx->size, GFP_KERNEL);

    if (!ctx->buffer) {
        pr_err("device: failed to allocate %zu bytes\n", ctx->size);
        return -ENOMEM;
    }

    memset(ctx->buffer, 0, ctx->size);

    pr_info("device: initialized successfully\n");

    return 0;
}

void device_shutdown(struct device_context *ctx)
{
    if (!ctx)
        return;

    kfree(ctx->buffer);
    ctx->buffer = NULL;
}
```
