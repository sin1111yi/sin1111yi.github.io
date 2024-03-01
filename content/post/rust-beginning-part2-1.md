+++
author = "sin1111yi"
title = "Rust学习笔记.part2.1"
date = "2024-02-27"
description = "千里之行，始于足下。"
tags = [
    "rust", "note"
]
series = ["Rust Study"]
+++

标量类型

<!--more-->

从零开始学习rust，参考书籍[Rust程序设计语言](https://kaisery.github.io/trpl-zh-cn/)，原始仓库 [rust-lang/book](https://github.com/rust-lang/book)。

- [数据类型](#数据类型)
  - [标量类型](#标量类型)
    - [整型](#整型)
    - [浮点](#浮点)
    - [数值运算](#数值运算)
    - [布尔类型](#布尔类型)
    - [字符类型](#字符类型)
- [一些术语](#一些术语)

## 数据类型

两种数据类型子集：标量和复合。Rust是静态类型语言，在编译时就需要知道所有变量的类型。

### 标量类型

#### 整型

Rust built-in 整数类型，数字类型默认是 `i32`，`isize` 和 `usize` 主要作为某些集合的索引。

|  长度   | 有符号 | 无符号 |
| :-----: | :----: | :----: |
|  8-bit  |   i8   |   u8   |
| 16-bit  |  i16   |  u16   |
| 32-bit  |  i32   |  u32   |
| 64-bit  |  i64   |  u64   |
| 128-bit |  i128  |  u128  |
|  arch   | isize  | usize  |

`isize` 和 `usize` 类型依赖运行程序的计算机架构：64 位架构上它们是 64 位的，32 位架构上它们是 32 位的。

多种数字类型的数字字面值允许使用类型后缀，例如 57u8 来指定类型，同时也允许使用 _ 做为分隔符以方便读数，例如1_000，它的值和 1000 相同。

```rust
let dec = 98_222; // 十进制
let hex = 0xffff; // 十六进制
let oct = 0o7878; // 八进制
let bin = 0b1111_0000; // 二进制
let chr: u8 = b'A'; // 字符，仅限 u8 类型
```

> 整型溢出

>> 比如 u8 的存储范围为0至255，如果发生将256写入，就会发生溢出，当使用 debug 模式构建时，编译器会检查这一类问题并使程序 panic，这个属于被Rust用来表明程序错误而退出。

>> 使用 `--release` flag 在 release 模式构建时，Rust不会检测会导致panic的整型溢出，而会进行二进制补码wrapping，即如果向u8中写入256，实际上会写入0，写入257，实际上会写入1，而程序不会panic。

>> 依赖整型溢出wrapping的行为被认为是一种错误。

>> 为了显式地处理溢出的可能性，可以使用这几类标准库提供的原始数字类型方法：

>> - 所有模式下都可以使用 `wrapping_*` 方法进行 wrapping，如 `wrapping_add`
>> - 如果 `checked_*` 方法出现溢出，则返回 `None` 值
>> - 用 `overflowing_*` 方法返回值和一个布尔值，表示是否出现溢出
>> - 用 `saturating_*` 方法在值的最小值或最大值处进行饱和处理

#### 浮点

Rust 也有两个原生的浮点数类型—— `f32` 和 `f64`，分别占32位和64位，默认类型都是f64，所有的浮点型都是有符号的。

```rust
fn main() {
    let x = 2.0; // f64
    let y: f32 = 3.0; // f32
}
```

浮点数采用 IEEE-754 标准表示。`f32` 是单精度浮点数，`f64` 是双精度浮点数。

#### 数值运算

所有数字类型都支持基本数学运算：加减乘除和取余。整数除法会向零舍入到最接近的整数。

```rust
fn main() {
    // addition
    let sum = 5 + 10;

    // subtraction
    let difference = 95.5 - 4.3;

    // multiplication
    let product = 4 * 30;

    // division
    let quotient = 56.7 / 32.2;
    let truncated = -5 / 3; // -1

    // remainder
    let remainder = 43 % 5;
}
```
[附录 B](https://kaisery.github.io/trpl-zh-cn/appendix-02-operators.html) 包含 Rust 提供的所有运算符的列表。

#### 布尔类型

两个真值，`true` 和 `false`。使用 `bool` 表示。
```rust
fn main() {
    let t = true;
    let f: bool = false; // with explicit type annotation
}
```

#### 字符类型

Rust 中的 `char` 是语言中最原生的字符类型。

```rust
fn main() {
    let c = 'z';
    let z: char = 'ℤ';
    let emoji = '😀';
}
```

这里的后面两个变量z和emoji的显示可能需要依赖 nerd font 和 noto-fonts-emoji。

用单引号声明字符，使用双引号声明字符串。在 Rust 中 char 类型的大小为 4 Bytes，代表了一个Unicode标量值，可以比 ASCII 表示更多内容。Unicode 标量值包含从 `U+0000` 到 `U+D7FF` 和 `U+E000` 到 `U+10FFFF` 在内的值。

## 一些术语

标量 - scalar

复合 - compound

静态类型 - statically typed

整型溢出 - integer overflow

二进制补码 wrapping - two’s complement wrapping

浮点数 - floating-point numbers