**Rust异步函数跟踪与操作系统调试 - 详细工作计划**&#x20;

### 1. 引言

本计划旨在实现对Rust异步函数和 Linux 操作系统的调试跟踪。最终愿景是用户可以在`code-debug` 操作系统调试工具中利用 GDB 跟踪 Linux，利用 eBPF 跟踪Rust 异步函数（这个异步函数可能运行于内核态，也可能运行于用户态）。

### 2. 研究背景与目标

- **研究背景**：

  - rust 自身没有支持完整的异步功能，无官方 async runtime。rust 自身只负责处理 async 语法糖（见第三点），具体的调度功能由第三方 runtime (tokio, async-std) 实现
  - gdb 不能跟踪宏，而 runtime 用大量宏，因此很难用 gdb 跟踪异步rust，必须用 eBPF
  - 最末端的 async 函数只有 ready 和 pending 两个状态，嵌套的 async 函数按照 await 被分割成多份，每一份都是一个状态（见 <[https://www.eventhelix.com/rust/rust-to-assembly-async-await/](https://www.eventhelix.com/rust/rust-to-assembly-async-await/)> ）

- **目标（2025年8月）**：

  - 结合 eBPF, 探索合适的 Rust 异步函数跟踪方法；

  - 扩展现有的`code-debug`工具，使其具备追踪Rust异步函数和Linux的能力；

  - 撰写并发表一篇关于本项目的学术论文。

### 3. 工作计划（边设计边施工）

- **第1-2周**：确定追踪什么信息（函数调用时间点，异步函数调用关系，内存/io等）真实开发的时候调试异步函数的需求，新/老方法处理新/老问题

  - 了解 Rust 异步函数的生成方式，特别是 Release 编译模式下的
    - 参考：<https://www.eventhelix.com/rust/rust-to-assembly-async-await/>
  - 参考相关论文，如“Automatic Tracing in Task-Based Runtime Systems”和“CAT: Context Aware Tracing for Rust Asynchronous Programs”，学习现有的异步函数跟踪方法
  - 确定要处理的**问题**：参考相关论文/博客/直接问人，确定现有跟踪工具的缺陷/不方便的地方，进而确定本工具需要实现的功能
  - 确定实现这些功能的**方法**（eBPF还是uftrace还是GDB等）

- **第3-4周**：搭建基础设施

  - 为异步函数跟踪部分搭建基础设施（比如基于eBPF或uftrace），跟踪主函数中的异步函数的调用（能够跟踪到异步函数被调用即可）。

- **第5-6周**：实现异步函数的调用栈跟踪

  - 完善基础设施，添加对异步函数调用栈的捕获和记录功能。

- **第7-8周**：实现异步函数的状态机跟踪

  - 深入实现状态机跟踪功能，跟踪每个异步函数的状态转换（如`ready`和`pending`状态）。

#### **3.2 文档撰写与论文发表（1-2月）**

#### **3.3 阶段四：Linux调试跟踪（3-8月）**


- **第1-2周**：用 eBPF 调试 Linux 内的用户态 rust 代码（包含异步函数）
- **第3-5周**：尝试用 code-debug 调试工具调试 Linux，找到问题并修复
- **第6周**：添加用 eBPF 调试异步代码的用户界面
