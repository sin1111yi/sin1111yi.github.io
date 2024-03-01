+++
author = "sin1111yi"
title = "Rust学习笔记.part2.2"
date = "2024-03-01"
description = "千里之行，始于足下。"
tags = [
    "rust", "note"
]
series = ["Rust Study"]
+++

复合类型

<!--more-->

从零开始学习rust，参考书籍[Rust程序设计语言](https://kaisery.github.io/trpl-zh-cn/)，原始仓库 [rust-lang/book](https://github.com/rust-lang/book)。

- [数据类型](#数据类型)
  - [复合类型](#复合类型)
    - [元组类型](#元组类型)
    - [数组类型](#数组类型)
      - [访问数组元素](#访问数组元素)
      - [无效的数组元素访问](#无效的数组元素访问)
- [一些术语](#一些术语)

## 数据类型

两种数据类型子集：标量和符合。Rust是静态类型语言，在编译时就需要知道所有变量的类型。

### 复合类型

Rust 有两个原生的复合类型：元组和数组。

#### 元组类型

元组长度固定：一旦声明，其长度不会增大或缩小。使用圆括号中的逗号分隔的值列表来创建元组。

```rust
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```

在上面的代码中 tup元素绑定到整个元组上，元组是一个单独的复合元素。可以使用*模式匹配*来*解构*元组值。

```rust
fn main() {
    let tup = (500, 6.4, 1);
    let (x, y, z) = tup;
    println!("The value of y is: {y}");
}
```

可以使用点号跟值的索引来直接访问

```rust
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);
    let a = x.0; // 500
    let b = x.1; // 6.4
    let c = 1;   // 1
}
```

不带任何值的元组叫做 *单元* 元组

#### 数组类型

想要在栈（stack）而不是在堆（heap）上为数据分配空间，或者是想要确保总是有固定数量的元素时，数组非常有用。但是数组并不如 vector 类型灵活。vector 类型是标准库提供的一个允许增长和缩小长度的类似数组的集合类型。推荐使用 `vector`。

数组中每个元素的类型必须相同，Rust中数组的长度是固定的。数组的值写在方括号内，用逗号分隔。

```rust
fn main() {
    let a = [1, 3, 4, 5];
}
```

在方括号中包含每个元素的类型，后跟分号，再后跟数组元素的数量。

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
```

还可以通过在方括号中指定初始值加分号再加元素个数的方式来创建一个每个元素都相同的数组。

```rust
let a = [3; 5];
```

##### 访问数组元素

数组是可以在栈上分配的已知固定大小的单个内存块，可以使用索引来访问数组的元素。

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];
    let first = a[0];
    let second = a[1];
}
```

##### 无效的数组元素访问

```rust
use std::io;

fn main() {
    let a = [1, 2, 3, 4, 5];
    
    println!("Please enter an array index.");

    let mut index = String::new();

    io::stdin().read_line(&mut index).expect("Failed to read line");

    let index: usize = index.trim().parse().expect("Index entered was not a number");

    let element = a[index];

    println!("The value of the element at index {index} is: {element}");
}
```

使用 cargo run 运行此代码并输入 0、1、2、3 或 4，程序将在数组中的索引处打印出相应的值。

如果输入一个超过数组末端的数字，如 10，则程序发生 panic

```bash
thread 'main' panicked at 'index out of bounds: the len is 5 but the index is 10', src/main.rs:19:19
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

程序在索引操作中使用一个无效的值导致 **运行时** 错误。程序带着错误信息退出，并且没有执行最后的 `println!` 语句。当尝试用索引访问一个元素时，Rust 会检查指定的索引是否小于数组的长度。如果索引超出了数组长度，Rust 会 *panic*。这是 Rust 术语，它用于程序因为错误而退出的情况。这种检查必须在运行时进行，特别是在这种情况下，因为编译器不可能知道用户在以后运行代码时将输入什么值。

## 一些术语

元组 - tuple

数组 - array

模式匹配 - pattern matching

解构 - destructure