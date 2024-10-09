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

1. （1周）弄清楚方法论
    1. 只支持跟踪tokio还是说只在变造后的 async 函数上下功夫，从而能支持所有的 async runtime，具体怎么做
1. （1周）通过在源代码中添加编译记号实现尽可能多地保留异步函数相关的信息
1. （1周）尝试只用同步函数的跟踪功能，从而确认为了支持跟踪异步函数，彭淳毅的那个模块要做出哪些修改
1. （3周）修改彭淳毅的那个模块