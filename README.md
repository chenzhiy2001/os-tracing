# os-tracing

async rust 跟踪难点：

1. rust 自身没有支持完整的异步功能，无官方 async runtime。rust 自身只负责处理 async 语法糖（见第三点），具体的调度功能由第三方 runtime (tokio, async-std) 实现
2. gdb 不能跟踪宏，而 runtime 用大量宏，因此很难用 gdb 跟踪异步rust，必须用 eBPF
3. 最末端的 async 函数只有 ready 和 pending 两个状态，嵌套的 async 函数按照 await 被分割成多份，每一份都是一个状态（见 <https://www.eventhelix.com/rust/rust-to-assembly-async-await/> ）

参考对象：

- tokio - console
- [Improving Debugging For *Optimized* Rust Code On Embedded Systems](https://www.diva-portal.org/smash/get/diva2:1720169/FULLTEXT01.pdf)
    - 支持 rust 宏
    - 支持调试优化后的 rust 代码
    - 不支持调试异步函数
- https://www.eventhelix.com/rust/rust-to-assembly-async-await/

## todo

- （1周）调研异步跟踪可以怎么做（读paper）
- 列一个宏观的细化到明年8月底的计划。就像写文献综述那样。包括异步+linux
- 修改编译器/在源代码里添加宏的做法在 release 不能用，因为 release 版编译器会对状态机代码进行 inline

## paper - list

```
[PDF] Concurrency Techniques and Hardware Abstraction Layer Concepts for Embedded Systems in Rust
MW Kröning - 2023 - publications.rwth-aachen.de
… probe.rs2 embedded toolchain provides flashing and debugging methods for embedded
devices. … The purpose of this thesis was to examine Rust’s async/.await ecosystem in baremetal …
保存 引用 相关文章 所有 2 个版本 
CAT: Context Aware Tracing for Rust Asynchronous Programs
TY Wang, SH Wang, CH Tu, WY Liang - Proceedings of the 38th ACM …, 2023 - dl.acm.org
… Note that to use our proposed framework, the Rust program should be compiled and run
in debug mode. We have tested the asynchronous programs in Rust built with the following …
保存 引用 被引用次数：1 相关文章 所有 2 个版本
[图书] Code Like a Pro in Rust
B Matthews - 2024 - books.google.com
… introducing Rust-specific design patterns, coding shortcuts, and veteran skills like asynchronous
programming and integrating Rust with other languages. You’ll also meet amazing Rust …
保存 引用 相关文章 
[PDF] arxiv.org
Deadlock-free asynchronous message reordering in rust with multiparty session types
Z Cutner, N Yoshida, M Vassor - Proceedings of the 27th ACM SIGPLAN …, 2022 - dl.acm.org
… , can be quite opaque and difficult to understand or debug. Nevertheless, the top-down
approach has the advantage of using asynchronous subtyping rather than 𝑘-MC—analysing the …
保存 引用 被引用次数：31 相关文章 所有 7 个版本
[PDF] pdx.edu
Proceedings of The Rust-Edu Workshop
B Massey - 2022 - pdxscholar.library.pdx.edu
… This project proposes to implement a remote debugging tool for rust kernel based on Visual
… Async Rust, we then turn to the implementation of the Future trait and adaptors4 and async/…
保存 引用 相关文章 所有 2 个版本 
[PDF] diva-portal.org
Improving Debugging For Optimized Rust Code On Embedded Systems
N Lundberg - 2022 - diva-portal.org
… This thesis presents a debugger named ERDB and a debugging library named rust-debug. …
The debugger is implemented asynchronously because all the modules handle input from …
保存 引用 相关文章 
[PDF] patricklam.ca
A study of common bug fix patterns in Rust
M Robati Shirzad, P Lam - Empirical Software Engineering, 2024 - Springer
… Also, for G.attr.struct.drop, we demonstrated a case where the developer removed a Debug
… ’s grammar, such as the introduction of new keywords like async/await in version 1.39, have …
保存 引用 被引用次数：3 相关文章 所有 5 个版本
[PDF] researchgate.net
[PDF] An Experimental Integration of io uring and Tokio: An Asynchronous Runtime for Rust
AS Moslehian - researchgate.net
… I used the LLDB debugger for Rust to meticulously go through functions and follow the
stack trace of all the threads[10]. The information presented in chapter 4 was all gathered by …
保存 引用 相关文章 

```