+++
author = "sin1111yi"
title = "Rust学习笔记.part1"
date = "2024-02-20"
description = "千里之行，始于足下。"
tags = [
    "rust", "note"
]
series = ["Rust Study"]

+++

变量与可变性：常量，隐藏，一些说明

<!--more-->

从零开始学习rust，参考书籍[Rust程序设计语言](https://kaisery.github.io/trpl-zh-cn/)

## 变量与可变性

Rust编译器保证，如果声明一个值不会变，它就真的不会变。Rust中默认变量是不可变的，所以如下代码是无法通过编译的，如果想要使变量可变，那么必须使用 **mut** 关键字，来表明变量可变。

```rust
// error
let x = 5;
x = 6;
// correct
let mut x = 5;
x = 6;
```

### 常量

1. 不允许对常量使用mut，不光默认不可变，且总是不可变
2. 声明常量使用 **const** 关键字，并且必须注明值的类型，如u32, usize, float等
3. 常量可以在任何作用域中声明，包括全局作用域
4. 常量只能被设置为常量表达式，而不可以是其他任何n只能在运行时计算出的值

```rust
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
```

Rust 对常量的命名约定是在单词之间使用全大写加下划线。

### 隐藏

定义一个与之前变量同名的新变量。Rustacean们称之为第一个变量被第二个隐藏了，意味着当使用该变量名称时，编译器将看到第二个变量。

此时任何使用该变量名的行为都会被视为是在使用第二个变量，直到第二个变量自己也被隐藏或第二个变量的作用域结束，可以用相同变量名来隐藏一个变量，以及重复使用 let 关键字来多次隐藏。

```rust
fn main() {
    let x = 5;
    let x = x + 1; // shadow the first x
    {
        let x = x * 2; // shadow the second x in scope
        println!("The value of x in the inner scope is: {x}"); // the third x will be printed
    }
    println!("The value of x is: {x}"); // the second x will be printed 
}
```

结果如下所示

```bash
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/variables`
The value of x in the inner scope is: 12
The value of x is: 6
```

---

隐藏与 mut 的不同：

1. 如果尝试重新赋值，没有使用let，就会导致编译错误，使用let时，可以用这个值进行一些计算，但是计算完成后变量仍然是不可变的
2. 再次使用let时，实际上是创建了一个新的变量，可以改变值的类型并重复使用变量名。

```rust
let spaces = "   ";
let spaces = spaces.len();
```

第一个 spaces 变量是字符串类型，第二个是数字类型。但是如果使用 mut 的方法，则会出现编译错误。

```rust
let mut spaces = "   ";
spaces = spaces.len();
```

如下所示

```bash
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
error[E0308]: mismatched types
 --> src/main.rs:3:14
  |
2 |     let mut spaces = "   ";
  |                      ----- expected due to this value
3 |     spaces = spaces.len();
  |              ^^^^^^^^^^^^ expected `&str`, found `usize`

For more information about this error, try `rustc --explain E0308`.
error: could not compile `variables` due to previous error
```

这个错误说明，我们不能改变变量的类型。

## 一些术语

不可变错误 immutability error

常量 constants

隐藏 shadowing


