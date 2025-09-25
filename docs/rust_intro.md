## rust 开发工具安装

学习内容:

1. Ubuntu下安装Rust以及相关工具链
2. 搭建VSCode开发环境
3. Cargo介绍
4. 实现 "Hello Rust" 程序


### 安装rustup

```bash
curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
```

这个命令将下载一个脚本并开始安装 rustup 工具，此工具将安装 Rust 的最新稳定版本。可能会提示你输入管理员密码。

如果安装成功，将出现下面这行：

```
Rust is installed now. Great!
```

OK, 这样就已经完成 Rust 安装啦。



## 数据类型

- 基本类型
    最小化原子类型，无法解构为其他类型
    - 数值类型
        - i8
        - i16
        - i32
        - i64
        - isize

        - u8
        - u16
        - u32
        - u64
        - usize

        - f32
        - f64

    - 字符串:
    - 布尔类型
        - true
        - false

    - 字符类型
        - 单个Unicode字符
    - 单元类型
        - ()

- 复合类型

执行如下命令安装rustlings

```bash
cargo install --force --path .
```

报错

```bash
...
   Compiling indicatif v0.16.2
error[E0432]: unresolved import `console::Term`
  --> /home/w101/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/indicatif-0.16.2/src/state.rs:10:5
   |
10 | use console::Term;
   |     ^^^^^^^^^^^^^ no `Term` in the root
   |
note: found an item that was configured out
  --> /home/w101/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/console-0.16.1/src/lib.rs:92:42
   |
92 |     user_attended, user_attended_stderr, Term, TermFamily, TermFeatures, TermTarget,
   |                                          ^^^^
note: the item is gated behind the `std` feature
  --> /home/w101/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/console-0.16.1/src/lib.rs:90:7
   |
90 | #[cfg(feature = "std")]
   |       ^^^^^^^^^^^^^^^
help: consider importing this variant instead
   |
10 - use console::Term;
10 + use crate::state::ProgressDrawTargetKind::Term;
   |

error[E0432]: unresolved imports `console::measure_text_width`, `console::Style`
  --> /home/w101/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/indicatif-0.16.2/src/style.rs:1:15
   |
1  | use console::{measure_text_width, Style};
   |               ^^^^^^^^^^^^^^^^^^  ^^^^^ no `Style` in the root
   |               |
   |               no `measure_text_width` in the root
   |
note: found an item that was configured out
  --> /home/w101/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/console-0.16.1/src/lib.rs:96:44
   |
96 |     colors_enabled, colors_enabled_stderr, measure_text_width, pad_str, pad_str_with,
   |                                            ^^^^^^^^^^^^^^^^^^
note: the item is gated behind the `std` feature
  --> /home/w101/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/console-0.16.1/src/lib.rs:94:7
   |
94 | #[cfg(feature = "std")]
   |       ^^^^^^^^^^^^^^^
note: found an item that was configured out
  --> /home/w101/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/console-0.16.1/src/lib.rs:98:19
   |
98 |     Color, Emoji, Style, StyledObject,
   |                   ^^^^^
note: the item is gated behind the `std` feature
  --> /home/w101/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/console-0.16.1/src/lib.rs:94:7
   |
94 | #[cfg(feature = "std")]
   |       ^^^^^^^^^^^^^^^

error[E0432]: unresolved imports `console::measure_text_width`, `console::Style`
  --> /home/w101/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/indicatif-0.16.2/src/utils.rs:7:15
   |
7  | use console::{measure_text_width, Style};
   |               ^^^^^^^^^^^^^^^^^^  ^^^^^ no `Style` in the root
   |               |
   |               no `measure_text_width` in the root
   |
note: found an item that was configured out
  --> /home/w101/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/console-0.16.1/src/lib.rs:96:44
   |
96 |     colors_enabled, colors_enabled_stderr, measure_text_width, pad_str, pad_str_with,
   |                                            ^^^^^^^^^^^^^^^^^^
note: the item is gated behind the `std` feature
  --> /home/w101/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/console-0.16.1/src/lib.rs:94:7
   |
94 | #[cfg(feature = "std")]
   |       ^^^^^^^^^^^^^^^
note: found an item that was configured out
  --> /home/w101/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/console-0.16.1/src/lib.rs:98:19
   |
98 |     Color, Emoji, Style, StyledObject,
   |                   ^^^^^
note: the item is gated behind the `std` feature
  --> /home/w101/.cargo/registry/src/index.crates.io-1949cf8c6b5b557f/console-0.16.1/src/lib.rs:94:7
   |
94 | #[cfg(feature = "std")]
   |       ^^^^^^^^^^^^^^^

   Compiling console v0.15.11
For more information about this error, try `rustc --explain E0432`.
error: could not compile `indicatif` (lib) due to 3 previous errors
warning: build failed, waiting for other jobs to finish...
```