# Rust 基本语法学习

学习内容

- 变量
- 基本类型
- 函数
- 注释
- 控制流


!!! warning "关键字"
    Rust 语言有一系列**关键字（keywords）**，和其他语言一样，这些关键字被保留下来只提供给语言作特殊使用。**请记住，这些关键字不能用作变量或函数的名称**。大多数关键字都有特殊的含义，在 Rust 程序中可使用它们执行多种任务；有少数关键字暂时没有相关联的功能，但新功能将来可能会加入到 Rust 中。在附录 A 中可找到关键字列表。

## 变量和可变性

默认情况下, **变量是不可变的(immutable)**.

意味着如果一个值绑定到一个变量名后，就不能更改该值。

如下代码

```rust
fn main() {
    let x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```

编译，会出现如下错误

```bash
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:4:5
  |
2 |     let x = 5;
  |         -
  |         |
  |         first assignment to `x`
  |         help: consider making this binding mutable: `mut x`
3 |     println!("The value of x is: {}", x);
4 |     x = 6;
  |     ^^^^^ cannot assign twice to immutable variable

For more information about this error, try `rustc --explain E0384`.
error: could not compile `variables` due to previous error
```
**上面的错误指出错误的原因是 cannot assign twice to immutable variable `x`（不能对不可变变量二次赋值），因为我们尝试给不可变的 x 变量赋值为第二个值。**

变量只是默认不可变的, 我们可以通过在变量名前加上 **mut** 使得它们可变。**增加 mut 的操作还向以后的读代码的人传达了代码的其他部分将会改变这个变量值**。

上面代码可以改为

```rust
fn main() {
    let mut x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```

## 常量

与**不可变变量**类似，常量（constant）是绑定到一个**常量名且不允许更改的值**，但是常量和变量之间存在一些差异。

- 常量不允许使用 mut。常量不仅仅默认不可变，而且自始至终不可变.
- 常量使用 **const** 关键字而不是 let 关键字来声明, 并且**值的类型必须注明**.
- 常量可以在**任意作用域**内声明，包括全局作用域.
- 常量只能设置为常量表达式

例如

```rust
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
```

Rust 常量的命名约定是**全部字母都使用大写**, 并使用下划线分隔单词.

!!! note
    将整个程序中用到的硬编码（hardcode）值命名为常量，对于将该值的含义传达给代码的未来维护者很有用。如果将来需要更改硬编码的值，则只需要在代码中改动一处就可以了。

## 遮蔽(shadowing)

Rust 中可以声明和前面变量具有相同名称的新变量，则第一个变量被第二个变量遮蔽（shadow），这意味着当我们使用变量时我们看到的会是第二个变量的值。我们可以通过使用相同的变量名并重复使用 let 关键字来遮蔽变量，如下所示：

```rust
fn main() {
    let x = 5;

    let x = x + 1;

    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {}", x);
    }

    println!("The value of x is: {}", x);
}
```

运行结果

```bash
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/variables`
The value of x in the inner scope is: 12
The value of x is: 6
```

遮蔽(shadow) 和 mut 变量的差异：

1. 使用 let，我们可以对一个值进行转换，但在这些转换完成后，变量将是不可变的。
2. 使用 let 关键字时有效地创建了一个新的变量，所以我们可以改变值的类型，但重复使用相同的名称

例子

```rust
let spaces = "   ";
let spaces = spaces.len();
```
**=>** 以上编译没有问题， 第一行是string类型的变量，第二行是整型的变量

```rust
let mut spaces = "   ";
spaces = spaces.len();
```
**=>** 会出现编译出错，该错误表明不允许改变变量的类型

## 数据类型

Rust是中静态类型语言，每一个变量都有确定的数据类型(data type)，意味着rust必须在编译期间就要确定所有变量的类型。

数据类型分为两种类型

- 标量类型
- 复合类型

### 标量类型 (Scalar type)

有4种基本的变量类型

- 整型
- 浮点型
- 布尔型
- 字符

#### 整型类型

下表显示了Rust中内置的整型类型

| 长度   | 有符号类型 | 无符号类型 |
| ------ | ---------- | ---------- |
| 8 位   | i8         | u8         |
| 16 位  | i16        | u16        |
| 32 位  | i32        | u32        |
| 64 位  | i64        | u64        |
| 128 位 | i128       | u128       |
| arch   | isize      | usize      |

有符号类型规定的数字范围是 $-(2^{n - 1}) \sim 2^{n - 1} - 1$，其中 n 是该定义形式的位长度。所以 i8 可存储数字范围是 $-(2^7) \sim 2^7 - 1$，即 -128 ~ 127。

无符号类型可以存储的数字范围是 $0 \sim 2^{n - 1}$，所以 u8 能够存储的数字为 $0 \sim 2^8 - 1$，即 0 ~ 255。


另外， `isize` 和 `usize` 类型 取决于程序运行的计算机体系结构，
- 若 "arch" 是64位架构系统，则变表示64位
- 若 "arch" 是32位架构系统，则变表示32位

| 数字字面量       | 示例        |
| ---------------- | ----------- |
| 十进制           | 98_222      |
| 十六进制         | 0xff        |
| 八进制           | 0o77        |
| 二进制           | 0b1111_0000 |
| 字节 (仅限于 u8) | b'A'        |


#### 浮点类型

- f32: 32位
- f64: 64位

默认浮点类型是f64

所有浮点型都是有符号数

浮点数按照 IEEE-754 标准表示。f32 类型是单精度浮点型，f64 为双精度浮点型


#### 布尔类型

Rust 中的布尔类型也有两个可能的值：true 和 false

布尔值的大小为 1 个字节。Rust 中的布尔类型使用 `bool` 声明

#### 字符类型

Rust 的 char（字符）类型是该语言最基本的字母类型

!!! note "注意：字符与字符串差异"
    我们声明的 char 字面量采用单引号括起来，这与字符串字面量不同，字符串字面量是用双引号括起来


Rust 的字符类型大小为 **4 个字节**，表示的是一个 Unicode 标量值，这意味着它可以表示的远远不止是 ASCII。标音字母，中文/日文/韩文的文字，emoji，还有零宽空格(zero width space)在 Rust 中都是合法的字符类型。Unicode 值的范围为 U+0000 ~ U+D7FF 和 U+E000~U+10FFFF


### 复合类型

复合类型（compound type）可以将多个值组合成一个类型。Rust 有两种基本的复合类型：

- 元组（tuple）
- 数组（array）

#### 元组类型

元组是将多种类型的多个值组合到一个复合类型中的一种基本方式。元组的长度是固定的：声明后，它们就无法增长或缩小。

通过在小括号内写入以逗号分隔的值列表来创建一个元组。元组中的每个位置都有一个类型，并且元组中不同值的类型不要求是相同的

例如

```rust
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```

变量 tup 绑定到整个元组，因为元组被认作是单个复合元素。 想从元组中获取个别值，我们可以使用模式匹配来解构（destructure）元组的一个值，如下所示：

```rust
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("The value of y is: {}", y);
}
```

该程序首先创建一个元组并将其绑定到变量 tup 上。 然后它借助 let 来使用一个模式匹配 tup，并将它分解成三个单独的变量 x、y 和 z。 这过程称为解构（destructuring）

y即为6.4

除了通过模式匹配进行解构外，我们还可以使用一个句点（.）连上要访问的值的索引来直接访问元组元素。例如：

```rust
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);

    let five_hundred = x.0;

    let six_point_four = x.1;

    let one = x.2;
}
```

该程序创建一个元组 x，然后通过使用它们的索引为每个元素创建新的变量。和大多数编程语言一样，元组中的第一个索引为 0

!!! warning
    没有任何值的元组 () 是一种特殊的类型，只有一个值，也写成 ()。该类型被称为**单元类型**（unit type），该值被称为单元值（unit value）。如果表达式不返回任何其他值，就隐式地返回单元值。

#### 数组类型

将多个值组合在一起的另一种方式就是使用数组（array）。与元组不同，**数组的每个元素必须具有相同的类型**。与某些其他语言中的数组不同，Rust 中的数组具有固定长度。

方括号内以逗号分隔的列表形式将值写到数组中

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];
    let months = ["January", "February", "March", "April", "May", "June", "July",
                  "August", "September", "October", "November", "December"];
}
```

使用方括号编写数组的类型，其中包含每个元素的类型、分号，然后是数组中的元素数，如下所示：

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
```

i32 是每个元素的类型。分号之后，数字 5 表明该数组包含 5 个元素


如果要为每个元素创建包含相同值的数组，可以指定初始值，后跟分号，然后在方括号中指定数组的长度，如下所示

```rust
let a = [3; 5];
```

变量名为 a 的数组将包含 5 个元素，这些元素的值初始化为 3。这种写法与 let a = [3, 3, 3, 3, 3]; 效果相同，但更简洁。


**访问数组元素**

数组是可以在栈上分配的已知固定大小的单个内存块。可以使用索引访问数组的元素

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];

    let first = a[0];
    let second = a[1];
}
```

**无效的数组元素访问**

如果尝试访问超出数组末尾的数组元素,Rust 将检查你指定的索引是否小于数组长度。如果索引大于或等于数组长度，Rust 会出现 panic。这种检查必须在运行时进行，尤其是在这种情况下，因为编译器可能无法知道用户之后运行代码时将输入什么值。

## 函数

前面例子已经见过 main 函数，它是很多程序的入口点。你也见过 fn 关键字，它用来声明新函数

Rust 代码中的函数和变量名使用下划线命名法（snake case，直译为蛇形命名法）规范风格。在下划线命名法中，所有字母都是小写并使用下划线分隔单词

Rust 中的函数定义以 fn 开始，后跟着函数名和一对圆括号。大括号告诉编译器函数体在哪里开始和结束

```rust
fn main() {
    println!("Hello, world!");

    another_function();
}

fn another_function() {
    println!("Another function.");
}
```

源码中 another_function 定义在 main 函数**之后**；也可以定义在**之前**。Rust 不关心函数定义于何处，只要定义了就行


### 参数

函数也可以被定义为拥有参数（parameter），参数是特殊变量，是函数签名的一部分。当函数拥有参数（**形参**）时，可以为这些参数提供具体的值（**实参**）

```rust
fn main() {
    another_function(5);
}

fn another_function(x: i32) {
    println!("The value of x is: {}", x);
}
```

another_function 的声明中有一个命名为 x 的参数。x 的类型被指定为 i32。当将 5 传给 another_function 时，println! 宏将 5 放入格式化字符串中大括号的位置。

当一个函数有多个参数时，使用逗号分隔

```rust
fn main() {
    print_labeled_measurement(5, 'h');
}

fn print_labeled_measurement(value: i32, unit_label: char) {
    println!("The measurement is: {}{}", value, unit_label);
}
```

### 语句和表达式

函数体由一系列语句组成，也可选地以表达式结尾。目前为止，我们介绍的函数还没有包含结尾表达式，

!!! note
    Rust 是一门基于表达式（expression-based）的语言


!!! note "语句 vs 表达式"
    语句（statement）是执行一些操作但不返回值的指令。表达式（expression）计算并产生一个值。


使用 let 关键字创建变量并绑定一个值是**一个语句**

```rust
fn main() {
    let y = 6;
}
```

函数定义也是语句，上面整个例子本身就是一个语句。

**语句不返回值**

因此，不能把 let 语句赋值给另一个变量，就像下面的代码尝试做的那样，会产生一个错误：

```rust
fn main() {
    let x = (let y = 6);
}
```

let y = 6 语句并不返回值，所以没有可以绑定到 x 上的值。

这与其他语言不同，例如 C 和 Ruby，它们的赋值语句会返回所赋的值。
在这些语言中，可以这么写 x = y = 6，这样 x 和 y 的值都是 6；
**Rust 中不能这样写**。


表达式会计算出一个值，并且你接下来要用 Rust 编写的大部分代码都由表达式组成。

- 考虑一个数学运算，比如 5 + 6，这是一个表达式并计算出值 11。
- 表达式可以是语句的一部分：在示例 3-1 中，语句 let y = 6; 中的 6 是一个表达式，它计算出的值是 6。
- 函数调用是一个表达式。
- 宏调用是一个表达式。
- 我们用来创建新作用域的大括号（代码块） {} 也是一个表达式

例如

```rust
fn main() {
    let y = {
        let x = 3;
        x + 1
    };

    println!("The value of y is: {}", y);
}
```

其中如下是一个表达式

```rust
{
    let x = 3;
    x + 1
}
```
是一个代码块，在这个例子中计算结果是4. 这个值作为 let 语句的一部分被绑定到 y 上

!!! note "注意"
    x + 1 行的末尾**没有分号**，这与你目前见过的大部分代码行不同。**表达式的结尾没有分号**。

    **如果在表达式的末尾加上分号，那么它就转换为语句，而语句不会返回值.**


### 带有返回值的函数

函数可以向调用它的代码返回值。我们并不对返回值命名，但要在**箭头（->）后声明它的类型**。
在 Rust 中，**函数的返回值等同于函数体最后一个表达式的值**。
使用 **return 关键字和指定值，可从函数中提前返回**；但**大部分函数隐式的返回最后的表达式**。这是一个有返回值的函数的例子：

```rust
fn five() -> i32 {
    5
}

fn main() {
    let x = five();

    println!("The value of x is: {}", x);
}
```

在 five 函数中没有函数调用、宏，甚至没有 let 语句——只有数字 5 本身。这在 Rust 中是一个完全有效的函数。注意，函数返回值的类型也被指定好，即 `-> i32`

five 函数的返回值是 5，所以返回值类型是 i32


```rust
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {}", x);
}

fn plus_one(x: i32) -> i32 {
    x + 1
}
```

运行代码会打印出 The value of x is: 6

如果在包含 x + 1 的行尾加上一个分号，把它从表达式变成语句，我们将得到一个错误

```rust

fn main() {
    let x = plus_one(5);

    println!("The value of x is: {}", x);
}

fn plus_one(x: i32) -> i32 {
    x + 1;
}

```

运行代码会产生一个错误，如下：

```bash
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
error[E0308]: mismatched types
 --> src/main.rs:7:24
  |
7 | fn plus_one(x: i32) -> i32 {
  |    --------            ^^^ expected `i32`, found `()`
  |    |
  |    implicitly returns `()` as its body has no tail or `return` expression
8 |     x + 1;
  |          - help: consider removing this semicolon

For more information about this error, try `rustc --explain E0308`.
error: could not compile `functions` due to previous error
```

主要的错误信息 "mismatched types"（类型不匹配）揭示了这段代码的核心问题

函数 plus_one 的定义说明它要返回一个 i32 类型的值，不过语句并不会返回值，此值由单位类型 () 表示，表示不返回值。因为不返回值与函数定义相矛盾，从而出现一个错误。在输出中，Rust 提供了一条信息，可能有助于纠正这个错误：它建议删除分号，这将修复错误。


## 注释


有时需要在源码中对程序进行解释。在这种情况下，开发者在他们的源码中留下**注释**，编译器将会忽略掉这些内容，但阅读源码的人可能会发现有用

Rust 中，惯用的注释形式以两个斜杆开头，直到该行尾结束。对于超出单行的注释，需要在每行的行首加上 //，如下所示：

```rust
// 我们在这里处理一些复杂事情，需要足够长的解释，使用
// 多行注释可做到这点。哇！我们希望这个注释将解释接下
// 来要实现的内容。
```

注释也可以放在包含代码的行后面：

```rust
fn main() {
    let lucky_number = 7; // I’m feeling lucky today
}
```

不过下面的这种格式会更常见，将注释放到需要解释的代码上面的单独行：

```rust
fn main() {
    // I’m feeling lucky today
    let lucky_number = 7;
}
```

## 控制流

根据条件是否为真来决定是否执行某些代码，或根据条件是否为真来重复运行一段代码，是大部分编程语言的基本组成部分。Rust 代码中最常见的用来控制执行流的结构是 **if 表达式** 和 **循环**。

### if 表达式

**if 表达式都以 if 关键字开头，其后跟一个条件**, 也可以包含一个可选的 else 表达式来提供一个在条件为假时应当执行的代码块.

if 表达式允许根据条件执行不同的代码分支。你提供一个条件并表示 "如果条件满足，运行这段代码；如果条件不满足，不运行这段代码。"

```rust
fn main() {
    let number = 3;

    if number < 5 {
        println!("condition was true");
    } else {
        println!("condition was false");
    }
}
```

if 表达式中与条件关联的代码块有时被叫做**分支**

!!! warning
    if 表达式中的条件必须是 bool 值。如果条件不是 bool 值，我们将得到一个错误

例如（错误示例）

```rust
fn main() {
    let number = 3;

    if number {
        println!("number was three");
    }
}
```
if 条件的值是 3，Rust 抛出了一个错误

```bash
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
error[E0308]: mismatched types
 --> src/main.rs:4:8
  |
4 |     if number {
  |        ^^^^^^ expected `bool`, found integer

For more information about this error, try `rustc --explain E0308`.
error: could not compile `branches` due to previous error
```

这个错误表明 Rust 期望一个 bool 却得到了一个整数.

Rust 并不会尝试自动地将非布尔值转换为布尔值。你必须自始至终显式地使用布尔值作为 if 的条件

#### 使用 else if 处理多重条件

**可以将 if 和 else 组成的 else if 表达式来实现多重条件**

例如

```rust
fn main() {
    let number = 6;

    if number % 4 == 0 {
        println!("number is divisible by 4");
    } else if number % 3 == 0 {
        println!("number is divisible by 3");
    } else if number % 2 == 0 {
        println!("number is divisible by 2");
    } else {
        println!("number is not divisible by 4, 3, or 2");
    }
}
```

**当执行这个程序时，它按顺序检查每个 if 表达式并执行第一个条件为真的代码块**

!!! warning
    注意即使 6 可以被 2 整除，也不会输出 number is divisible by 2，更不会输出 else 块中的 number is not divisible by 4, 3, or 2。原因是 Rust 只会执行**第一个条件为真**的代码块，并且一旦它找到一个以后，甚至都不会检查剩下的条件了。

使用过多的 else if 表达式会使代码显得杂乱无章，所以如果有多于一个 else if 表达式，最好重构代码。为处理这些情况，第 6 章会介绍一个强大的 Rust 分支结构（branching construct），叫做 **match**

#### 在 let 语句中使用 if

因为 if 是一个表达式，我们可以在 let 语句的右侧使用它来将结果赋值给一个变量，例如：

```rust
fn main() {
    let condition = true;
    let number = if condition { 5 } else { 6 };

    println!("The value of number is: {}", number);
}
```

number 变量将会绑定到表示 if 表达式结果的值上。

**代码块的值是其最后一个表达式的值，而数字本身就是一个表达式**

if 的每个分支的可能的**返回值都必须是相同类型**

### 循环表达式

多次执行同一段代码是很常用的，Rust 为此提供了多种循环（loop），它们遍历执行循环体中的代码直到结尾并紧接着回到开头继续执行

Rust 有三种循环：

- loop
- while
- for

#### loop

loop 关键字告诉 Rust 一遍又一遍地执行一段代码直到你明确要求停止

```rust
fn main() {
    loop {
        println!("again!");
    }
}
```

!!! note "关键字break和continue在循环中的用途"
    **Rust 也提供了一种从代码中跳出循环的方法。可以使用 break 关键字来告诉程序何时停止循环**,

    **循环中的 continue 关键字告诉程序跳过这个循环迭代中的任何剩余代码，并转到下一个迭代。**

    如果存在嵌套循环，break 和 continue 应用于此时最内层的循环。你可以选择在一个循环上指定一个循环标签（loop label），然后将标签与 break 或 continue 一起使用，使这些关键字应用于已标记的循环而不是最内层的循环。

例如

```rust
fn main() {
    let mut count = 0;
    'counting_up: loop {
        println!("count = {}", count);
        let mut remaining = 10;

        loop {
            println!("remaining = {}", remaining);
            if remaining == 9 {
                break;
            }
            if count == 2 {
                break 'counting_up;
            }
            remaining -= 1;
        }

        count += 1;
    }
    println!("End count = {}", count);
}
```

外层循环有一个标签 **counting_up**, 它将从 0 数到 2。没有标签的内部循环从 10 向下数到 9。
**第一个没有指定标签的 break 将只退出内层循环**。
**break 'counting_up; 语句将退出外层循环**。

#### 从循环返回

loop 的一个用例是重试可能会失败的操作，比如检查线程是否完成了任务。然而你可能会需要将操作的结果从循环中传递给其它的代码。为此，你可以在用于停止循环的 break 表达式添加你想要返回的值；该值将从循环中返回，以便您可以使用它

```rust
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    println!("The result is {}", result);
}
```

在循环之前，我们声明了一个名为 counter 的变量并初始化为 0。接着声明了一个名为 result 来存放循环的返回值。
循环的每一次迭代中，我们将 counter 变量加 1，接着检查计数是否等于 10。当相等时，使用 break 关键字返回值 counter * 2。循环之后，我们通过分号结束赋值给 result 的语句。最后打印出 result 的值，也就是 20。

#### while

在程序中计算循环的条件也很常见。当条件为真，执行循环。当条件不再为真，调用 break 停止循环。这个循环类型可以通过组合 loop、if、else 和 break 来实现；
然而，这个模式太常用了，Rust 为此内置了一个语言结构，它被称为 **while 循环**

```rust
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{}!", number);

        number -= 1;
    }

    println!("LIFTOFF!!!");
}
```

这种结构消除了很多使用 loop、if、else 和 break 时所必须的嵌套，这样更加清晰。当条件为真就执行，否则退出循环。

#### for

可以使用 while 结构来遍历集合中的元素，比如数组。例如，示例 3-4 中的循环打印数组 a 中的每个元素。

```rust
fn main() {
    let a = [10, 20, 30, 40, 50];
    let mut index = 0;

    while index < 5 {
        println!("the value is: {}", a[index]);

        index += 1;
    }
}
```
在这里，代码对数组中的元素进行计数。它从索引 0 开始，并接着循环直到遇到数组的最后一个索引（即 index < 5 不再为真时）

这个过程很容易出错；如果索引值或测试条件不正确会导致程序 panic。
这也使程序更慢，因为编译器增加了运行时代码来对每次循环进行条件检查，以确定在循环的每次迭代中索引是否在数组的边界内。


作为更简洁的替代方案，可以使用 `for` 循环来对一个集合的每个元素执行一些代码

```rust
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a {
        println!("the value is: {}", element);
    }
}
```
这段代码结果和上面一致。但是增强了代码安全性，并消除了可能由于超出数组的结尾或遍历长度不够而缺少一些元素而导致的 bug。

使用 `for` 循环的话，就不需要惦记着在改变数组元素个数时修改其他的代码。

`for` 循环的安全性和简洁性使得它成为 Rust 中使用最多的循环结构

即使是在想要循环执行代码特定次数时，大部分 Rust 开发人员也会使用 `for` 循环。这么做的方式是使用 `Range`,它是标准库提供的类型，用来生成从一个数字开始到另一个数字之前结束的所有数字的序列。

下面是一个使用 for 循环来倒计时的例子，它还使用了一个我们还未讲到的方法，`rev`，用来反转区间（range）:

```rust
fn main() {
    for number in (1..4).rev() {
        println!("{}!", number);
    }
    println!("LIFTOFF!!!");
}
```

