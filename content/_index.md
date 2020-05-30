+++
weight = 1
sort_by = "weight"
template = "index.html"
#insert_anchor_links = "right"
+++

<img id="logo" src="logo.png" alt="Ferris holding a cheat sheet."></img>
<div class="title">Rust 语言备忘清单</div>
<div class="subtitle">{{ date() }}</div>



> 参考书： 
> **Rust 程序设计语言** {{ book(page="") }}（中文）、
> **通过例子学 Rust** {{ ex(page="") }}（中文）、
> **标准库文档** {{ std(page="std") }}、
> **Rust 死灵书** {{ nom(page="") }}、
> **Rust 参考手册** {{ ref(page="") }}。
> <br>凡例：
> **已废弃** {{ deprecated() }}、
> **最低版本** {{ edition(ed="'18") }}、
> **施工中** {{ experimental() }}、
> **不好的写法** {{ bad() }}。

<div class="noprint">

<div class="controls">
    <a id="toggle_ligatures" href="javascript:toggle_ligatures()">Fira Code 连字 (<code>..=, =></code>)</a>
    <a href="javascript:toggle_night_mode()">暗色模式 &#x1f4a1;</a>
</div>

<div class="toc">

<div class="column">

**语言架构**
* [数据结构](#data-structures)
* [引用和指针](#references-pointers)
* [函数和行为](#functions-behavior)
* [控制流程](#control-flow)
* [代码组织](#organizing-code)
* [类型别名和转换](#type-aliases-and-casts)
* [宏和属性](#macros-attributes)
* [模式匹配](#pattern-matching)
* [泛型和约束](#generics-constraints)
* [字符串和字符](#strings-chars)
* [注释](#comments)
* [其他](#miscellaneous)

**增强设施**
* [语法糖](#language-sugar)


**数据类型**
* [基本类型](#basic-types)
* [自定义类型](#custom-types)
* [引用和指针](#references-pointers-ui)
* [闭包](#closures-data)
* [标准库类型](#standard-library-types)



</div>


<div class="column">

**标准库**
* [Trait](#traits)
* [字符串转换](#string-conversions)
* [字符串格式化](#string-formatting)
<!-- * [Marker Traits](#XXX) -->


**工具**
* [项目结构](#project-anatomy)
* [Cargo](#cargo)
* [交叉编译](#cross-compilation)


**编码指南**
* [Rust 惯用法](#idiomatic-rust)
* [Async-Await 101](#async-await-101)
* [闭包 API](#closures-in-apis)
* [理解生命周期](#reading-lifetimes)
* [Unsafe, Unsound, Undefined](#unsafe-unsound-undefined)
* [API 稳定性](#api-stability)


**附录**
* [外链和服务](#links-services)
* [打印 PDF](#printing-pdf)


</div>

</div>
</div>




<div class="noprint">

## 你好, Rust！

如果你之前从来没用过 Rust，或者你想试点什么东西，都可以在这里跑一下：

<div id="hellostatic">


```rust
fn main() {
    println!("Hello, world!");
}
```


</div>
<div id="helloplay"></div>
<div id="helloctrl"><a href="javascript:show_playground(true);">▶️ 编辑运行</a></div>

</div>


### 数据结构 {#data-structures}

数据类型和内存位置由关键字定义。

<div class="cheats">

| 示例 | 说明 |
|---------|-------------|
| `struct S {}` | 定义包含命名字段的**结构体**{{ book(page="ch05-00-structs.html") }} {{ ex(page="custom_types/structs.html") }} {{ std(page="std/keyword.struct.html") }} {{ ref(page="expressions/struct-expr.html") }}。 |
| {{ tab() }} `struct S { x: T }` | 定义包含 `T` 类型命名字段 `x` 的结构体。 |
| {{ tab() }} `struct S` &#8203;`(T);` | 定义 `T` 类型数字字段 `.0` 的“元组”结构体。 |
| {{ tab() }} `struct S;` | 定义一个**零大小**{{ nom(page="exotic-sizes.html#zero-sized-types-zsts")}} 单元的结构体。不占空间。 |
| `enum E {}` | 定义**枚举**{{ book(page="ch06-01-defining-an-enum.html") }} {{ ex(page="custom_types/enum.html#enums") }} {{ ref(page="items/enumerations.html") }}。参见[数字数据类型](https://en.wikipedia.org/wiki/Algebraic_data_type)、[标签联合](https://en.wikipedia.org/wiki/Tagged_union)。 |
| {{ tab() }}  `enum E { A, B`&#8203;`(), C {} }` | 定义变体枚举，它可以是单元 `A`=元组 `B` &#8203;`()` 或者结构体风格的 `C{}`。 |
| {{ tab() }}  `enum E { A = 1 }` | 如果所有变体都是单元值，则允许判别式值，可用于 FFI。 |
| `union U {}` | 不安全的 C 风格**联合体**{{ ref(page="items/unions.html") }}，用于兼容 FFI。 |
| `static X: T = T();`  | 有 `'static` 生命周期的**全局变量** {{ book(page="ch19-01-unsafe-rust.html#accessing-or-modifying-a-mutable-static-variable") }} {{ ex(page="custom_types/constants.html#constants") }} {{ ref(page="items/static-items.html#static-items") }}，内存位置独立。 |
| `const X: T = T();`  | 定义**常量** {{ book(page="ch03-01-variables-and-mutability.html#differences-between-variables-and-constants") }} {{ ex(page="custom_types/constants.html") }} {{ ref(page="items/constant-items.html") }}。使用时会临时复制一份。 |
| `let x: T;`  | 在栈{{ note( note="1") }}上分配 `T` 大小的字节并命名为 `x`。一旦分配，不可修改。 |
| `let mut x: T;`  | 类似 `let`，但允许修改和可变借用。{{ note( note="2") }} |
| {{ tab() }} `x = y;` | 将 `y` 移动到 `x`，如果 `T` 不能 `Copy`，`y` 将不再可用，否则会复制一份 `y`。|

</div>

<div class="footnotes">

<sup>1</sup> 同步代码中，它们生存在栈上. 但对于 `async` 代码，这些变量将会成为异步状态机的一部分，它们最终是在堆上。<br>
<sup>2</sup> 注意术语**可变**和**不可变**并不准确. 尽管你有一个不可变绑定或者共享引用，它也有可能包含一个 [Cell](https://doc.rust-lang.org/std/cell/index.html)，它仍支持 _内部可变性_。

</div>


{{ tablesep() }}

下面列出了如何创建和访问数据结构，包括一些**神奇的**类型。

<div class="cheats">

| 示例 | 说明 |
|---------|-------------|
| `S { x: y }` | 创建 `struct S {}`，或 `use` 的 `enum E::S {}` 字段 `x` 设置为 `y`。 |
| `S { x }` | 同上，但字段 `x` 会设置为局部变量 `x`。 |
| `S { ..s }` | 用 `s` 填充剩余字段，常配合 [Default](https://doc.rust-lang.org/std/default/trait.Default.html) 使用。 |
| `S { 0: x }` | 类似下面的 `S` &#8203;`(x)` 但是用结构体语法初始化字段 `.0`。  |
| `S`&#8203; `(x)` | 创建 `struct S` &#8203;`(T)`，或 `use` 的 `enum E::S`&#8203; `()` 其中字段 `.0` 设置为 `x`。 |
| `S` | 表示 `struct S;` 或以 `S` 为值创建 `use` 来的 `enum E::S`。 |
| `E::C { x: y }` | 创建枚举变体 `C`。 上面的方法依然可用。 |
| `()` | 空元组，既是字面量也是类型，又称**单元**。 {{ std(page="std/primitive.unit.html") }} |
| `(x)` | 括号表达式。 |
| `(x,)` | 单元素**元组**表达式。 {{ ex(page="primitives/tuples.html") }} {{ std(page="std/primitive.tuple.html") }} {{ ref(page="expressions/tuple-expr.html") }} |
| `(S,)` | 单元素元组类型。 |
| `[S]` | 未指明长度的数组类型，如**切片**。{{ std(page="std/primitive.slice.html") }}  {{ ex(page="primitives/array.html") }}  {{ ref(page="types.html#array-and-slice-types") }} 不能生存在栈上。 {{ note( note="*") }} |
| `[S; n]` | 元素类型为 `S` 定长为 `n` 的**数组类型**{{ ex(page="primitives/array.html") }}  {{ std(page="std/primitive.array.html") }}。 |
| `[x; n]` | 由 `n` 个 `x` 的副本构成的数组实例。 {{ ref(page="expressions/array-expr.html") }} |
| `[x, y]` | 由给定元素 `x` 和 `y` 构成的数组实例。 |
| `x[0]` | 组合的索引。 可重载 [Index](https://doc.rust-lang.org/std/ops/trait.Index.html) 和 [IndexMut](https://doc.rust-lang.org/std/ops/trait.IndexMut.html)。 |
| `x[..]` | 组合的切片式索引，全部范围 [RangeFull](https://doc.rust-lang.org/std/ops/struct.RangeFull.html)，参见 切片。  |
| `x[a..]` | 组合的切片式索引，指定起始的范围 [RangeFrom](https://doc.rust-lang.org/std/ops/struct.RangeFrom.html)。 |
| `x[..b]` | 组合的切片式索引，指定终止的范围 [RangeTo](https://doc.rust-lang.org/std/ops/struct.RangeTo.html)。 |
| `x[a..b]` | 组合的切片式索引，指定始终的范围 [Range](https://doc.rust-lang.org/std/ops/struct.Range.html)。 |
| `a..b` | 左闭右开**区间**{{ ref(page="expressions/range-expr.html") }}，`..b` 同理。  |
| `a..=b` | 闭区间，`..=b` 同理。 |
| `s.x` | 命名**字段访问**{{ ref(page="expressions/field-expr.html") }}，如果 `x` 不是 `S` 的一部分的话则会尝试 [Deref](https://doc.rust-lang.org/std/ops/trait.Deref.html)。 |
| `s.0` | 数字字段访问，用于元组类型 `S` &#8203;`(T)`。 |

</div>

<div class="footnotes">

<sup>*</sup> 目前，可以参考 [该已知问题](https://github.com/rust-lang/rust/issues/48055) 和关联的 [RFC 1909](https://github.com/rust-lang/rfcs/pull/1909)。

</div>


### 引用和指针 {#references-pointers}

为非所有者内存赋予访问权限。参见 [泛型和约束](#generics-constraints)。


<div class="cheats">

| 示例 | 说明 |
|---------|-------------|
| `&S` | 共享 **引用** {{ book(page="ch04-02-references-and-borrowing.html") }} {{ std(page="std/primitive.reference.html") }} {{ nom(page="references.html")}} {{ ref(page="types.html#pointer-types")}} (用于存储 _任意_ `&s`)。 |
| {{ tab() }} `&[S]` | 特殊的切片引用，包含地址和长度 (`address`，`length`)。 |
| {{ tab() }} `&str` | 特殊的字符串引用，包含地址和长度 (`address`，`length`)。 |
| {{ tab() }} `&mut S` | 允许修改的独占引用 (参见 `&mut [S]`，`&mut dyn S`，...) |
| {{ tab() }} `&dyn T` | 特殊的 **trait 对象** {{ book(page="ch17-02-trait-objects.html#using-trait-objects-that-allow-for-values-of-different-types") }} 引用，包含地址和虚表 (`address`，`vtable`)。 |
| `*const S` | 不可变的**裸指针类型**{{ book(page="ch19-01-unsafe-rust.html#dereferencing-a-raw-pointer") }} {{ std(page="std/primitive.pointer.html") }} {{ ref(page="types.html#raw-pointers-const-and-mut") }}，内存不安全。 |
| `*mut S` | 可变的裸指针类型，内存不安全。 |
| `&s` | 共享**借用**{{ book(page="ch04-02-references-and-borrowing.html") }} {{ ex(page="scope/borrow.html") }} {{ std(page="std/borrow/trait.Borrow.html") }} (例如 _该_ `s` 的地址、长度、虚表等，比如 `0x1234`)。 |
| `&mut s` | 有**可变性**的独占借用。 {{ ex(page="scope/borrow/mut.html") }} |
| `ref s` | **引用绑定**。{{ ex(page="scope/borrow/ref.html") }} {{ deprecated() }}|
| `*r` | 对引用 `r` **解引用**{{ book(page="ch15-02-deref.html") }} {{ std(page="std/ops/trait.Deref.html") }} {{ nom(page="vec-deref.html") }}以访问其指向的事物。 |
| {{ tab() }} `*r = s;` | 如果 `r` 是一个可变引用，则将 `s` 移动或复制到目标内存。 |
| {{ tab() }} `s = *r;` | 如果 `r` 可 `Copy`，则将 `r` 引用的内容复制到 `s`。 |
| {{ tab() }} `s = *my_box;` | `Box` 有一个[特例](https://www.reddit.com/r/rust/comments/b4so6i/what_is_exactly/ej8xwg8/) ，即便它不可 `Copy`，也仍会从 Box 里面移动出来。 |
| `'a`  | **生命周期参数**，{{ book(page="ch10-00-generics.html") }} {{ ex(page="scope/lifetime.html")}} {{ nom(page="lifetimes.html") }} {{ ref(page="items/generics.html#type-and-lifetime-parameters")}}，为静态分析声明一块代码的持续时间。 |
| {{ tab() }}  `&'a S`  | 仅支持生存时间不短于 `'a` 的地址 `s` 。 |
| {{ tab() }}  `&'a mut S`  | 同上，但允许改变地址指向的内容。 |
| {{ tab() }}  `struct S<'a> {}`  | 表明 `S` 包含一个生命周期为 `'a` 的地址。由 `S` 的创建者决定 `'a`。 |
| {{ tab() }} `trait T<'a> {}` | 表明一个实现了 `impl T for S` 的 `S` 可能会包含地址。 |
| {{ tab() }}  `fn f<'a>(t: &'a T)`  | 同上，用于函数。调用者决定 `'a`。 |
| `'static`  | 特殊的生命周期，生存在程序的整个执行过程中。 |

</div>




###  函数和行为 {#functions-behavior}

定义代码单元及其抽象。

<div class="cheats">

| 示例 | 说明 |
|---------|-------------|
| `trait T {}`  | 定义 **trait** {{ book(page="ch10-02-traits.html") }} {{ ex(page="trait.html") }} {{ ref(page="items/traits.html") }}，它是一系列可被实现的通用行为. |
| `trait T : R {}` | `T` 是**父 trait** {{ ref(page="items/traits.html#supertraits") }} `R` 的子 trait。任何要 `impl T` 的 `S` 都必须先 `impl R`。 |
| `impl S {}`  | 类型 `S` 的函数**实现** {{ ref(page="items/implementations.html") }}，如方法。 |
| `impl T for S {}`  | 为类型 `S` 实现 trait `T`. |
| `impl !T for S {}` | 禁用自动推导的 **auto trait** {{ nom(page="send-and-sync.html") }} {{ ref(page="special-types-and-traits.html#auto-traits") }}。 |
| `fn f() {}`  | 定义一个**函数**{{ book(page="ch03-03-how-functions-work.html") }}  {{ ex(page="fn.html") }} {{ ref(page="items/functions.html") }}，或在 `impl` 里关联一个函数。 |
| {{ tab() }} `fn f() -> S {}`  | 同上，但会返回一个 `S` 类型的值。 |
| {{ tab() }} `fn f(&self) {}`  | 定义一个方法。例如，在 `impl S {}` 里面。 |
| `const fn f() {}`  | 编译器常量函数 `fn`，例如 `const X: u32 = f(Y)`。 {{ edition(ed="'18") }}|
| `async fn f() {}`  | **异步**{{ edition(ed="'18") }}函数转写。令 `f` 返回 `impl Future` {{ std(page="std/future/trait.Future.html") }}。 |
| {{ tab() }} `async fn f() -> S {}`  | 同上，但令 `f` 返回 `impl Future<Output=S>`。 |
| {{ tab() }} `async { x }`  | 用在函数内部，使 `{ x }` 变得 `impl Future<Output=X>`。 |
| `fn() -> S`  | **函数指针**{{ book(page="ch19-05-advanced-functions-and-closures.html#function-pointers") }} {{ std(page="std/primitive.fn.html") }} {{ ref(page="types.html#function-pointer-types") }}，内存存放的可调用地址。 |
| `Fn() -> S`  | **可调用 Trait**{{ book(page="ch19-05-advanced-functions-and-closures.html#returning-closures") }} {{ std(page="std/ops/trait.Fn.html") }}（又见 `FnMut` 和 `FnOnce`），可由闭包或函数等实现。 |
| <code>&vert;&vert; {} </code> | **闭包**{{ book(page="ch13-01-closures.html") }} {{ ex(page="fn/closures.html") }} {{ ref(page="expressions/closure-expr.html")}}，将会借用它所有的捕获。 |
| {{ tab() }} <code>&vert;x&vert; {}</code> | 有传入参数 `x` 的闭包。 |
| {{ tab() }} <code>&vert;x&vert; x + x</code> | 没有块表达式的闭包，仅可由单个表达式组成。 |
| {{ tab() }} <code>move &vert;x&vert; x + y </code> | 闭包，将会获取它所有捕获的所有权。 |
| {{ tab() }} <code> return &vert;&vert; true </code> | 闭包，起来像是逻辑或，但这里表示返回一个闭包。 |
| `unsafe {}` | **不安全代码**{{ book(page="ch19-01-unsafe-rust.html?highlight=unsafe#unsafe-superpowers") }} {{ ex(page="unsafe.html#unsafe-operations") }} {{ nom(page="meet-safe-and-unsafe.html") }} {{ ref(page="unsafe-blocks.html#unsafe-blocks") }}。如果你喜欢在周五晚上调试段错误的话~ |

</div>


### 控制流程 {#control-flow}

在函数中控制执行。

<div class="cheats">

| 示例 | 说明 |
|---------|-------------|
| `while x {}`  | **循环** {{ ref(page="expressions/loop-expr.html#predicate-loops") }}，当表达式 `x` 为真时运行。 |
| `loop {}`  | **无限循环** {{ ref(page="expressions/loop-expr.html#infinite-loops") }} 直到 `break`。可以用 `break x` 来 yield 一个值出来。 |
| `for x in iter {}` | 在 **迭代器** 上循环的语法糖。{{ book(page="ch13-02-iterators.html") }} {{ std(page="std/iter/index.html") }} {{ ref(page="expressions/loop-expr.html#iterator-loops") }} |
| `if x {} else {}`  | **条件分支** {{ ref(page="expressions/if-expr.html") }}。如果表达式为真则……否则…… |
| `'label: loop {}` | **循环标签** {{ ex(page="flow_control/loop/nested.html") }} {{ ref(page="expressions/loop-expr.html#loop-labels")}}，用于嵌套循环的流程控制。 |
| `break`  | **Break 表达式** {{ ref(page="expressions/loop-expr.html#break-expressions") }}，用于退出循环。 |
| {{ tab() }} `break x`  | 同上，但将 `x` 作为循环表达式的值（仅在 `loop` 中有效）。 |
| {{ tab() }} `break 'label`  | 不单单退出的是当前循环，而是最近一个标记有 `'label` 的循环。 |
| `continue `  | **Continue 表达式** {{ ref(page="expressions/loop-expr.html#continue-expressions") }}，用于继续该循环的下一次迭代。 |
| `continue 'label`  | 同上，但继续的是最近标记有 `'label` 的循环迭代。 |
| `x?` | 如果 `x` 是 [Err](https://doc.rust-lang.org/std/result/enum.Result.html#variant.Err) 或 [None](https://doc.rust-lang.org/std/option/enum.Option.html#variant.None)，**返回并向上传播**。{{ book(page="ch09-02-recoverable-errors-with-result.html#propagating-errors") }} {{ ex(page="error/result/enter_question_mark.html") }} {{ std(page="std/result/index.html#the-question-mark-operator-") }} {{ ref(page="expressions/operator-expr.html#the-question-mark-operator")}} |
| `x.await` | 仅在 `async` 中有效。将会 yield 当前流，直到 [Future](https://doc.rust-lang.org/std/future/trait.Future.html) 或 Stream {{ todo() }} `x` 就绪。{{ edition(ed="'18") }} |
| `return x`  | 从函数中提前返回。然而以表达式结束的方式更惯用。 |
| `f()` | 调用 `f`（如函数、闭包、函数指针或 `Fn` 等）。 |
| `x.f()` | 调用成员函数（方法），要求 `f` 以 `self`、`&self` 等作为第一个参数。 |
| {{ tab() }} `X::f(x)` | 同 `x.f()`。除非 `impl Copy for X {}`，否则 `f` 仅可调用一次。 |
| {{ tab() }} `X::f(&x)` | 同 `x.f()`。 |
| {{ tab() }} `X::f(&mut x)` | 同 `x.f()`。 |
| {{ tab() }} `S::f(&x)` | 同 `x.f()`，仅当 `X` 实现了对 `S` 的 [Deref](https://doc.rust-lang.org/std/ops/trait.Deref.html)。这里 `x.f()` 会去找 `S` 的方法。 |
| {{ tab() }} `T::f(&x)` | 同 `x.f()`，仅当 `X impl T`。这里 `x.f()` 会去找作用域内 `T` 的方法。 |
| `X::f()` | 调用关联函数。比如 `X::new()`。 |
| {{ tab() }} `<X as T>::f()` | 调用为 `X` 实现了的 trait 方法 `T::f()。 |
</div>



### 代码组织 {#organizing-code}

将项目分割成小的单元并最小化相关依赖。

<div class="cheats">

| 示例 | 说明 |
|---------|-------------|
| `mod m {}`  | 定义**模块** {{ book(page="ch07-02-defining-modules-to-control-scope-and-privacy.html") }} {{ ex(page="mod.html#modules") }} {{ ref(page="items/modules.html#modules") }}，其中的定义在 `{}` 内。 |
| `mod m;`  | 定义模块，其中的定义在 `m.rs` 或 `m/mod.rs` 内。 |
| `a::b` | 命名空间**路径**{{ ex(page="mod/use.html") }} {{ ref(page="paths.html")}}，表示 `a`（`mod` 或 `enum` 等） 里面的元素 `b`。 |
| {{ tab() }} `::b` | 相对于当前 crate 根下搜索 `b` 。{{ deprecated() }} |
| {{ tab() }} `crate::b` | 相对于当前 crate 根下搜索 `b`。{{ edition(ed="'18") }} |
| {{ tab() }} `self::b`  | 相对于当前模块下搜索 `b`。 |
| {{ tab() }} `super::b`  | 相对于父级模块下搜索 `b`。 |
| `use a::b;`  | **Use** {{ ex(page="mod/use.html#the-use-declaration") }} {{ ref(page="items/use-declarations.html") }} 声明，将 `b` 直接引入到当前作用域，以后就不需要再加 `a` 前缀了。 |
| `use a::{b, c};` | 同上，但同时将 `b` 和 `c` 都引入。 |
| `use a::b as x;`  | 将 `b` 引入作用域但命名为 `x`。比如 `use std::error::Error as E`。 |
| `use a::b as _;`  | 将 `b` 匿名的引入作用域，用于含有冲突名称的 trait。 |
| `use a::*;`  | 将 `a` 里面的所有元素都引入作用域。 |
| `pub use a::b;`  | 将 `a::b` 引入作用域，并再次从当前位置导出。 |
| `pub T`  | 控制 `T` 的**可见性** {{ book(page="ch07-02-defining-modules-to-control-scope-and-privacy.html") }}。“如果父级路径公开，我也公开”。 |
| {{ tab() }} `pub(crate) T` | 可见性仅在当前 crate 内。 |
| {{ tab() }} `pub(self) T`  | 可见性仅在当前模块内。 |
| {{ tab() }} `pub(super) T`  | 可见性仅在父级以下。 |
| {{ tab() }} `pub(in a::b) T`  | 可见性仅在 `a::b` 内。 |
| `extern crate a;` | 声明依赖一个外部 **crate** {{ book(page="ch02-00-guessing-game-tutorial.html#using-a-crate-to-get-more-functionality") }} {{ ex(page="crates/link.html#extern-crate") }} {{ ref(page="items/extern-crates.html#extern-crate-declarations") }} {{ deprecated() }}。换用 `use a::b` {{ edition(ed="'18") }}。  |
| `extern "C" {}`  | _声明_ **FFI** 的外部依赖和 ABI（如 `"C"`）。 {{ book(page="ch19-01-unsafe-rust.html#using-extern-functions-to-call-external-code") }} {{ ex(page="std_misc/ffi.html#foreign-function-interface") }} {{ nom(page="ffi.html#calling-foreign-functions") }} {{ ref(page="items/external-blocks.html#external-blocks") }} |
| `extern "C" fn f() {}`  | _定义_ FFI 导出成 ABI（如 `"C"`）的函数。 |
</div>



### 类型别名和转换 {#type-aliases-and-casts}

类型名称的简写，以及转为其他类型的方法。

<div class="cheats">

| 示例 | 说明 |
|---------|-------------|
| `type T = S;`  | 创建**类型别名**{{ book(page="ch19-04-advanced-types.html#creating-type-synonyms-with-type-aliases") }} {{ ref(page="items/type-aliases.html?highlight=alias#type-aliases") }}。这里表示 `S` 的另一个名字。 |
| `Self`  | **当前类型**{{ ref(page="types.html#self-types") }} 的类型别名。如 `fn new() -> Self`。 |
| `self`  | `fn f(self) {}` 的方法主体。同 `fn f(self: Self) {}`。 |
|  {{ tab() }}  `&self`  | 同上，但将借用指向自己的引用。同 `f(self: &Self)`。 |
|  {{ tab() }}  `&mut self`  | 同上，但是可变借用。同 `f(self: &mut Self)`。 |
|  {{ tab() }}  `self: Box<Self>`  | [任意自型](https://github.com/withoutboats/rfcs/blob/arbitray-receivers/text/0000-century-of-the-self-type.md)，为智能指针增加方法（`my_box.f_of_self()`）。 |
| `S as T`  | **消歧义**{{ book(page="ch19-03-advanced-traits.html#fully-qualified-syntax-for-disambiguation-calling-methods-with-the-same-name") }} {{ ref(page="expressions/call-expr.html#disambiguating-function-calls") }}，将类型 `S` 作为 trait `T` 看待。比如 `<X as T>::f()`。 |
| `S as R`  | 在 `use` 里，将 `S` 导入为 `R`。如 `use a::b as x`。 |
| `x as u32`  | 裸**转换**{{ ex(page="types/cast.html#casting") }} {{ ref(page="expressions/operator-expr.html#type-cast-expressions") }}，会发生截断和一些位上的意外。{{ nom(page="casts.html") }} |

</div>



### 宏和属性 {#macros-attributes}

实际编译前的代码预展开。

<div class="cheats">

| 示例 |  说明 |
|---------|---------|
| `m!()` |  **宏** {{book(page="ch19-06-macros.html")}} {{std(page="std/index.html#macros")}} {{ref(page="macros.html")}} 咒语。也作 `m!{}` 或 `m![]`（取决于宏本身）。 |
| `$x:ty`  | 宏捕获。如 `$x:expr`、`$x:ty`、`$x:path` 等，见下表。 |
| `$x` | **宏举例**中的宏代称。{{book(page="ch19-06-macros.html")}} {{ex(page="macros.html#macro_rules")}} {{ref(page="macros-by-example.html")}}
| `$(x),*` | 宏举例中的宏重复数。零或更多次。 |
| {{ tab() }} `$(x),?` | 同上，零或一次。 |
| {{ tab() }} `$(x),+` | 同上，一或更多次。 |
| {{ tab() }} `$(x)<<+` | 支持不是 `,` 的其他分隔符。这里是 `<<`。 |
| `$crate` | 特殊变量，指明宏定义在哪个 crate 里。{{ todo() }} |
| `#[attr]`  | 外部**属性**{{ex(page="attribute.html")}} {{ref(page="attributes.html")}}。注解接下来的内容。 |
| `#![attr]` | 内部属性。注解附近的内容。 |

</div>

{{ tablesep() }}

在 `macro_rules!` 实现里，会用到下面的宏捕获：

<div class="cheats">

| 宏捕获 |  说明 |
|---------|---------|
| `$x:item`    | 项目。如函数、结构体、模块等。 |
| `$x:block`   | 语句或表达式块 `{}`。如 `{ let x = 5; }` |
| `$x:stmt`    | 语句。如 `let x = 1 + 1;`、`String::new();` 或 `vec![];` |
| `$x:expr`    | 表达式。如 `x`、`1 + 1`、`String::new()` 或 `vec![]` |
| `$x:pat`     | 匹配。如 `Some(t)`、`(17、'a')` 或 `_` |
| `$x:ty`      | 类型。如 `String`、`usize` 或 `Vec<u8>` |
| `$x:ident`   | 标识符。如 `let x = 0;` 中的 `x`。 |
| `$x:path`    | 路径。如 `foo`、`::std::mem::replace`、`transmute::<_、int>` 等。 |
| `$x:literal` | 字面量。如 `3`、`"foo"`、`b"bar"` 等。 |
| `$x:lifetime` | 生命周期。如 `'a`、`'static` 等。 |
| `$x:meta`    | 元数据，即 `#[...]` 和 `#![...]` 中的属性值。 |
| `$x:vis`    | 可见性修饰符。如 `pub`、`pub(crate)` 等。 |
| `$x:tt`      | 单棵语法树。详细请参见[这里](https://stackoverflow.com/a/40303308)。 |
</div>



### 模式匹配 {#pattern-matching}

函数参数、`match` 或 `let` 表达式中的构造。


<div class="cheats">

| 示例 | 说明 |
|---------|-------------|
| `match m {}` | **模式匹配**{{ book(page="ch06-02-match.html") }} {{ ex(page="flow_control/match.html") }} {{ ref(page="expressions/match-expr.html") }}，下面跟匹配分支。参见 下表。 |
| `let S(x) = get();`  | 显然，`let` 也和下表的模式匹配类似。 |
|  {{ tab() }} `let S { x } = s;` | 仅将 `x` 绑定到值 `s.x`。 |
|  {{ tab() }} `let (_, b, _) = abc;` | 仅将 `b` 绑定到值 `abc.1`。 |
|  {{ tab() }} `let (a, ..) = abc;` | 也可以将“剩余的”都忽略掉。 |
|  {{ tab() }} `let Some(x) = get();` | **不可用** {{ bad() }}，因为模式可能会 **不匹配** {{ ref(page="expressions/if-expr.html#if-let-expressions") }}。换用 `if let`。 |
| `if let Some(x) = get() {}`  | 如果模式匹配则执行该分支（如某个 `enum` 变体）。语法糖<sup>*</sup>。 |
| `fn f(S { x }: S)`  | 类似于 `let`，模式匹配也可用在函数参数上。这里，`f(s)` 的 `x` 被绑定到 `s.x`。|

</div>


<div class="footnotes">

<sup>*</sup> 展开后是 `match get() { Some(x) => {}, _ => () }`。

</div>



{{ tablesep() }}

`match` 表达式的模式匹配分支。左列的分支也可用于 `let` 表达式。

<div class="cheats">

| 匹配分支 | 说明 |
|---------|-------------|
|  `E::A => {}` | 匹配枚举变体 `A`。参见**模式匹配**。{{ book(page="ch06-02-match.html") }} {{ ex(page="flow_control/match.html") }} {{ ref(page="expressions/match-expr.html") }} |
|  `E::B ( .. ) => {}` | 匹配枚举元组变体 `B`，通配所有下标。 |
|  `E::C { .. } => {}` | 匹配枚举结构变体 `C`，通配所有字段。 |
|  `S { x: 0, y: 1 } => {}` | 匹配含特定值的结构体（仅匹配 `s` 的 `s.x` 为 `0` 且 `s.y` 为 `1` 的情况）。 |
|  `S { x: a, y: b } => {}` | 匹配为**任意**(!)值的该类型结构体，并绑定 `s.x` 到 `a`，绑定 `s.y` 到 `b`。 |
|  {{ tab() }} `S { x, y } => {}` | 同上，但将 `s.x` 和 `s.y` 分别简写地绑定为 `x` 和 `y`。 |
|  `S { .. } => {}` | 匹配任意值的该类型结构体。 |
|  `D => {}` | 匹配枚举变体 `E::D`。仅当 `D` 已由 `use` 引入。 |
|  `D => {}` | 匹配任意事物并绑定到 `D`。如果 `D` 没被 `use` 进来，怕不是个 `E::D` 的假朋友。{{ bad() }} |
|  `_ => {}` | 通配所有，或者所有剩下的。 |
|  `(a, 0) => {}` | 匹配元组，绑定第一个值到 `a`，要求第二个是 `0`。 |
|  `[a, 0] => {}` | **切片模式**{{ ref(page="patterns.html?highlight=slice,pattern#slice-patterns") }} {{ link(url="https://doc.rust-lang.org/edition-guide/rust-2018/slice-patterns.html") }}。绑定第一个值到 `a`，要求第二个是 `0`。 |
|  {{ tab() }} `[1, ..] => {}` | 匹配以 `1` 开始的数组，剩下的不管。**子切片模式**。{{ todo() }} |
|  {{ tab() }} `[2, .., 5] => {}` | 匹配以 `1` 开始以 `5` 结束的数组。 |
|  {{ tab() }} `[2, x @ .., 5] => {}` | 同上，但将 `x` 绑定到中间部分的切片上（见下）。  |
| `x @ 1..=5 => {}` | 绑定匹配到 `x`，即**模式绑定**{{ book(page="ch18-03-pattern-syntax.html#-bindings") }} {{ ex(page="flow_control/match/binding.html#binding") }} {{ ref(page="patterns.html#identifier-patterns") }}。这里 `x` 可以是 `1`、`2` 直到 `5`。  |
| <code>0 &vert; 1 => {}</code> | 替代模式（或模式）。 |
| {{ tab() }}  <code>E::A &vert; E::Z </code> | 同上，但是枚举变体。 |
| {{ tab() }}  <code>E::C {x} &vert; E::D {x}</code> | 同上，但将 `x` 绑定到每个模式都有的 `x` 上面。 |
| `S { x } if x > 10 => {}`  | 模式**匹配条件**{{ book(page="ch18-03-pattern-syntax.html#extra-conditionals-with-match-guards")}} {{ ex(page="flow_control/match/guard.html#guards")}} {{ ref(page="expressions/match-expr.html#match-guards") }}。该匹配会要求这个条件也为真。 |

</div>



<!-- This is more relevant for let D = ... cases, https://www.reddit.com/r/rust/comments/a1846o/rust_quiz_26_medium_to_hard_rust_questions_with/eaop291/ -->
<!-- |  `D => {}` | Match struct if `D` unit `struct D;`| -->



### 泛型和约束 {#generics-constraints}

泛型有多种构造方式：`struct S<T>`、`fn f<T>()` 等等。

<div class="cheats">

| 示例 | 说明 |
|---------|-------------|
| `S<T>`  | **泛型**{{ book(page="ch10-01-syntax.html") }} {{ ex(page="generics.html") }}，类型参数 `T` 是占位符。 |
| `S<T: R>`  | 类型短 **trait 约束**{{ book(page="ch10-02-traits.html#using-trait-bounds-to-conditionally-implement-methods") }} {{ ex(page="generics/bounds.html") }}说明。（`R` **必须** 是个实际的 trait）。 |
| {{ tab() }} `T: R, P: S`  | **独立 trait 约束**（这里一个对 `T`，一个对 `P`）。 |
| {{ tab() }} `T: R, S`  | 编译错误{{ bad() }}。可以用下面的 `R + S` 代替。 |
| {{ tab() }} `T: R + S`  | **合并 trait 约束**{{ book(page="ch10-02-traits.html#specifying-multiple-trait-bounds-with-the--syntax") }} {{ ex(page="generics/multi_bounds.html") }}。`T` 必须同时满足 `R` 和 `S`。 |
| {{ tab() }} `T: R + 'a`  | 同上，但有生命周期。`T` 必须满足 `R`；如果 `T` 有生命周期，则必须长于 `'a`。 |
| {{ tab() }} `T: ?Sized` | 在前置定义 trait 约束之外的选项。`Sized`？{{ todo() }} |
| {{ tab() }} `T: 'a` | 类型**生命周期约束**{{ ex(page="scope/lifetime/lifetime_bounds.html") }}。`T` 应长于 `'a`。 |
| {{ tab() }} `'b: 'a` | 约束生命周期 `'b` 必须至少和 `'a` 一样长。 |
| `S<T> where T: R`  | 同 `S<T: R>`，但使得长的约束说明更易读。 |
| `S<T = R>` | 关联类型**默认类型参数**{{ book(page="ch19-03-advanced-traits.html#default-generic-type-parameters-and-operator-overloading") }}。 |
| `S<'_>` | 推测**匿名生命周期**。如果显然可见，让编译器“自己搞定”。 |
| `S<_>` | 推测**匿名类型**。如 `let x: Vec<_> = iter.collect()`。 |
| `S::<T>` | **Turbofish** {{ std(page="std/iter/trait.Iterator.html#method.collect")}} 消歧义类型调用。如 `f::<u32>()`。 |
| `trait T<X> {}`  | `X` 的 trait 泛型。可以有多个 `impl T for S`（每个 `X` 一个）。 |
| `trait T { type X; }`  | 定义**关联类型**{{ book(page="ch19-03-advanced-traits.html#specifying-placeholder-types-in-trait-definitions-with-associated-types") }} {{ ref(page="items/associated-items.html#associated-types") }} `X`。仅可有一个 `impl T for S` 。 |
| {{ tab() }} `type X = R;`  | 设置关联类型。仅在 `impl T for S { type X = R; }` 内。 |
| `impl<T> S<T> {}`  | 实现 `S<T>` 任意类型 `T` 的功能。 |
| `impl S<T> {}`  | 实现确定 `S<T>` 的功能。如 `S<u32>`。 |
| `fn f() -> impl T`  | **Existential 类型** {{ book(page="ch10-02-traits.html#returning-types-that-implement-traits") }}。返回一个对调用者未知的但 `impl T`的 `S`。 |
| `fn f(x: &impl T)`  | Trait 约束，“**impl trait**”{{ book(page="ch10-02-traits.html#trait-bound-syntax") }}。和 `fn f<S:T>(x: &S)` 有点类似。 |
| `fn f(x: &dyn T)`  | **动态分发**标记{{ book(page="ch17-02-trait-objects.html#using-trait-objects-that-allow-for-values-of-different-types") }} {{ ref(page="types.html#trait-objects") }}。`f` 不再单态。 |
| `fn f() where Self: R`  | 在 `trait T {}` 中标记 `f` 仅可由实现了 `impl R` 的类型访问。。 |
| `for<'a>` | **高阶 trait 约束**。{{ nom(page="hrtb.html")}} {{ ref(page="trait-bounds.html#higher-ranked-trait-bounds")}} |
| {{ tab() }} `trait T: for<'a> R<'a> {}` | 任何 `impl T` 的 `S` 在任意生命周期都需满足 `R`。 |

</div>



### 字符串和字符 {#strings-chars}

Rust 提供了若干种创建字符串和字符字面量的办法。


<div class="cheats">

| 示例 | 说明 |
|--------|-------------|
| `"..."` | UTF-8 **字符串字面量**{{ ref(page="tokens.html#string-literals")}}。会将 `\n` 等看作换行 `0xA` 等。 |
| `r"..."` | UTF-8 **裸字符串字面量**{{ ref(page="tokens.html#raw-string-literals")}}。不会处理 `\n` 等。 |
| `r#"..."#` 等 | UTF-8 裸字符串字面量。但可以包含 `"`。 |
| `b"..."` | **字节串字面量**{{ ref(page="tokens.html#byte-and-byte-string-literals")}}，由 ASCII `[u8]` 组成。不是字符串。 |
| `br"..."`、`br#"..."#` 等 | 裸字节串字面量，ASCII `[u8]`。说明见上。 |
| `'🦀'` | **字符字面量**{{ ref(page="tokens.html#character-and-string-literals")}}，固定的 4 字节 Unicode “**字符**”。{{ std(page="std/primitive.char.html") }} |
| `b'x'` | ASCII **字节字面量**。{{ ref(page="tokens.html#byte-literals")}} |

</div>


### 注释

无需解释。

<div class="cheats">

| 示例 | 说明 |
|--------|-------------|
| `//` | 行内注释。用于文档代码流内或_内部组件_。 |
| `//!` | 行内**文档注释**{{ book(page="ch14-02-publishing-to-crates-io.html#making-useful-documentation-comments") }} {{ ex(page="meta/doc.html#documentation") }} {{ ref(page="comments.html#doc-comments")}}。用于自动文档生成。 |
| `///` | 外部行内文档注释。在类型上面用。 |
| `/*...*/` | 块级注释。 |
| `/*!...*/` | 内部块级文档注释. |
| `/**...*/` | 外部块级文档注释. |
| ` ```rust ... ``` ` | 在文档注释中包含[文档测试](https://doc.rust-lang.org/rustdoc/documentation-tests.html)（文档代码可以用 `cargo test` 运行）。 |
| `#` | 隐藏文档测试中某行（` ```   # use x::hidden; ``` `）。 |

</div>


### 其他 {#miscellaneous}

这些小技巧不属于其他分类但最好了解一下。

<div class="cheats">

| 示例 | 说明 |
|---------|-------------|
| `!` | 永远为空的 **never 类型**。{{ experimental() }} {{ book(page="ch19-04-advanced-types.html#the-never-type-that-never-returns") }} {{ ex(page="fn/diverging.html#diverging-functions") }} {{ std(page="std/primitive.never.html") }} {{ ref(page="types.html?highlight=never#never-type") }} |
| `_` | 无名变量绑定。如 <code>&vert;x, _&vert; {}</code>。|
| `_x` | 变量绑定，明确标记该变量未使用。 |
| `1_234_567` | 为了易读加入的数字分隔符。 |
| `1_u8` | **数字字面量**的类型说明符。{{ ex(page="types/literals.html#literals") }} {{ ref(page="tokens.html#number-literals") }}（又见 `i8`、`u16`）。 |
| `0xBEEF`, `0o777`, `0b1001`  | 十六进制（`0x`）、八进制（`0o`）和二进制（`0b`) 整型字面量。 |
| `r#foo` | **裸标识符**{{ book(page="appendix-01-keywords.html?highlight=raw,iten#raw-identifiers") }} {{ ex(page="compatibility/raw_identifiers.html?highlight=raw,iden#raw-identifiers") }}。用于版本兼容。 |
| `x;` | **语句**{{ ref(page="statements.html")}}终止符。见**表达式**{{ ex(page="expression.html") }} {{ ref(page="expressions.html")}}。 |

</div>




### 通用操作符

Rust 支持大部分其他语言也有的通用操作符（`+`, `*`, `%`, `=`, `==`...）。因为这在 Rust 里没什么太大差别所以这里不列出来了。Rust 也支持**运算符重载**。{{ std(page="std/ops/index.html")}}

---

<div class="magic">

# 增强设施

## 语法糖 {#language-sugar}

如果有什么东西让你觉得，“不该能用的啊”，那可能就是这里的原因。


<div class="header-language-sugar">


| 名称 | 说明 |
|--------| -----------|
| **强制类型转换** {{ nom(page="coercions.html") }} | “弱”类型匹配签名。如 `&mut T` 到 `&T`。  |
| **解引用** {{ nom(page="vec-deref.html#deref") }} | [Deref](https://doc.rust-lang.org/std/ops/trait.Deref.html) `x: T` 将会一直解引用 `*x`、`**x`……直到满足目标 `S`。 |
| **Prelude** {{ std(page="std/prelude/index.html") }} | 自动导入基本类型。 |
| **重新借用** | 即便 `x: &mut T` 不能复制，也可以移动一个新的 `&mut *x` 代替。 |
| **生命周期省略** {{ book(page="ch10-03-lifetime-syntax.html#lifetime-elision") }} {{ nom(page="lifetime-elision.html#lifetime-elision") }} {{ ref(page="lifetime-elision.html?highlight=lifetime,el#lifetime-elision") }} | 自动注解 `f(x: &T)` 为 `f<'a>(x: &'a T)`。 |
| **方法解析** {{ ref(page="expressions/method-call-expr.html") }} | 解引用或借用 `x` 直到 `x.f()` 有效。 |

</div>

{{ tablesep() }}

> **编者记** <sup>💬</sup> ——尽管上面的特性将使简化了开发工作，但它们也会对理解当前发生了什么造成可能的妨碍。如果你对 Rust 还不太了解，想要搞明白到底发生了什么，你应该更详细地阅读相关资料。

<!-- End magic -->
</div>

---

<!-- This whole section doesn't look good on print -->
<div class="noprint">

# 数据类型

通用数据类型的内存表示。


## 基本类型 {#basic-types}

语言核心内建的必要类型。


#### 数字类型 {{ ref(page="types/numeric.html") }}

<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>u8</code>, <code>i8</code></name>
    <visual class="bytes">
        <byte><code></code></byte>
    </visual>
</datum>


<!-- NEW ENTRY -->
<datum class="spaced" >
    <name><code>u16</code>, <code>i16</code></name>
    <visual class="bytes">
        <byte><code></code></byte>
        <byte><code></code></byte>
    </visual>
</datum>


<!-- NEW ENTRY -->
<datum  class="spaced">
    <name><code>u32</code>, <code>i32</code></name>
    <visual class="bytes">
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
    </visual>
</datum>


<!-- NEW ENTRY -->
<datum  class="spaced">
    <name><code>u64</code>, <code>i64</code></name>
    <visual class="bytes">
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
    </visual>
</datum>


<!-- NEW ENTRY -->
<datum  class="spaced">
    <name><code>u128</code>, <code>i128</code></name>
    <visual class="bytes">
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
    </visual>
</datum>


<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>f32</code></name>
    <visual class="float">
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
    </visual>
</datum>


<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>f64</code></name>
    <visual class="float">
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
    </visual>
</datum>


<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>usize</code>, <code>isize</code></name>
    <visual class="sized">
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte style="border-color: lightslategrey;"><code></code></byte>
        <byte style="border-color: lightslategrey;"><code></code></byte>
        <byte style="border-color: lightslategrey;"><code></code></byte>
        <byte style="border-color: lightslategrey;"><code></code></byte>
    </visual>
    <zoom>
        与平台的 <code>ptr</code> 一致
    </zoom>
</datum>



<br/>


{{ tablesep() }}


<!-- Create a horizontal scrollable area on small displays to preserve layout-->
<div style="overflow:auto;">
<div style="min-width: 100%; width: 650px;">

<div class="tabs">

<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-numeric-1" name="tab-group-numeric" checked>
<label class="tab-label" for="tab-numeric-1"><b>整型</b></label>
<div class="tab-panel">
<div class="tab-content">



|整型*|最大值|
|---|---|
|`u8`| `255` |
|`u16` | `65_535` |
|`u32`| `4_294_967_295` |
|`u64`| `18_446_744_073_709_551_615` |
|`u128`| `340_282_366_920_938_463_463_374_607_431_768_211_455` |

<div class="footnotes">

<sup>*</sup> `i8` 和 `i16` 等类型的范围为 `-max/2` 到 `max/2`（向负无穷大四舍五入）。

</div>

</div></div></div>


<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-numeric-2" name="tab-group-numeric">
<label class="tab-label" for="tab-numeric-2"><b>浮点型</b></label>
<div class="tab-panel">
<div class="tab-content">


`f32` 的位表示<sup>*</sup>：

<!-- NEW ENTRY -->
<datum class="centered" style="opacity:0.7; margin-bottom:10px;">
    <visual class="float">
    <bitgroup>
        <bit><code>S</code></bit>
    </bitgroup>
    <bitgroup>
        <bit><code>E</code></bit>
        <bit><code>E</code></bit>
        <bit><code>E</code></bit>
        <bit><code>E</code></bit>
        <bit><code>E</code></bit>
        <bit><code>E</code></bit>
        <bit><code>E</code></bit>
        <bit><code>E</code></bit>
    </bitgroup>
    <bitgroup>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
        <bit><code>F</code></bit>
    </bitgroup>
    </visual>
</datum>

{{ tablesep() }}

说明：

| f32 | S (1) | E (8) | F (23) | 值 |
|------| ---------| ---------| ---------| ---------|
| 规格化数 | ± | 1 to 254 | 任意 | ±(1.F)<sub>2</sub> * 2<sup>E-127</sup>  |
| 非规格化数 | ± | 0 | 非零 | ±(0.F)<sub>2</sub> * 2<sup>-126</sup>  |
| 零 | ± | 0 | 0 | ±0  |
| 无穷大 | ± | 255 | 0 | ±∞  |
| NaN | ± | 255 | 非零 | NaN  |

{{ tablesep() }}

类似地，<code>f64</code> 如下：

| f64 | S (1) | E (11) | F (52) | 值 |
|------| ---------| ---------| ---------| ---------|
| 规格化数 | ± | 1 to 2046 | 任意 | ±(1.F)<sub>2</sub> * 2<sup>E-1023</sup>  |
| 非规格化数 | ± | 0 | 非零 | ±(0.F)<sub>2</sub> * 2<sup>-1022</sup>  |
| 零 | ± | 0 | 0 | ±0  |
| 无穷大 | ± | 2047 | 0 | ±∞  |
| NaN | ± | 2047 | 非零 | NaN  |

<div class="footnotes">
    <sup>*</sup> 浮点类型遵循 <a href="https://en.wikipedia.org/wiki/IEEE_754-2008_revision">IEEE 754-2008</a> 规范，并取决于平台大小端序。
</div>

</div></div></div>

<!-- End tabs -->
</div>

<!-- End overflow prevention -->
</div></div>


{{ tablesep() }}



#### 文字类型 {{ ref(page="types/textual.html") }}


<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>char</code></name>
    <visual class="char">
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
        <byte><code></code></byte>
    </visual>
    <description>任意 UTF-8 标量</description>
</datum>



<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>str</code></name>
    <visual>
        <note>...</note>
        <byte class="bytes"><code>U</code></byte>
        <byte class="bytes"><code>T</code></byte>
        <byte class="bytes"><code>F</code></byte>
        <byte class="bytes"><code>-</code></byte>
        <byte class="bytes"><code>8</code></byte>
        <note>... 未指明条目</note>
    </visual>
    <description>很少单独见到，常用 <code>&str</code> 代替</description>
</datum>

注意:

- `char` 总是为 4 字节，且仅包含一个 Unicode **标量值** (尽管会浪费空间)。
- `str` 是一个未知长度的字节数组，并保证存的都是 **UTF-8 代码点** (但难以索引)。

{{ tablesep() }}


## 自定义类型 {#custom-types}

用户定义的基本类型。它实际的<b>内存布局</b>{{ ref(page="type-layout.html") }}取决于<b>表示法</b>{{ ref(page="type-layout.html#representations") }}，还有对齐。


<!-- NEW ENTRY -->
<datum class="spaced">
    <name class="nogrow"><code>T: Sized</code></name>
    <name class="hidden">x</name>
    <visual>
       <framed class="any t"><code>T</code></framed>
    </visual>
    <!-- <description><code>T : Sized</code></description> -->
</datum>

<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>T: ?Sized</code></name>
    <visual>
       <framed class="any unsized"><code>T</code></framed>
    </visual>
    <description>动态大小类型 {{ ref(page="dynamically-sized-types.html") }}</description>
</datum>


<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>(A, B, C)</code></name>
    <visual>
       <framed class="any"><code>A</code></framed>
       <framed class="any" style="width: 100px;"><code>B</code></framed>
       <framed class="any" style="width: 50px;"><code>C</code></framed>
    </visual>
</datum>


<!-- NEW ENTRY -->
<datum style="margin-right:70px;">
    <name class="nogrow"><code>struct S; </code></name>
    <name class="hidden"><code>;</code></name>
    <visual style="width: 15px;" class="empty">
        <code></code>
    </visual>
    <!-- <description>Zero size.</description> -->
</datum>



<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>struct S { b: B, c: C } </code></name>
    <visual>
       <framed class="any" style="width: 100px;"><code>B</code></framed>
       <framed class="any" style="width: 50px;"><code>C</code></framed>
    </visual>
</datum>


<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>[T; n]</code></name>
    <visual>
       <framed class="any t"><code>T</code></framed>
       <framed class="any t"><code>T</code></framed>
       <framed class="any t"><code>T</code></framed>
       <note>... n 次</note>
    </visual>
</datum>


<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>[T]</code></name>
    <visual>
       <note>...</note>
       <framed class="any t"><code>T</code></framed>
       <framed class="any t"><code>T</code></framed>
       <framed class="any t"><code>T</code></framed>
       <note>... 未指明条目</note>
    </visual>
</datum>



{{ tablesep() }}

这些**合并类型**存有其一种子类型的值：

<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>enum E { A, B, C }</code></name>
    <visual class="enum" style="text-align: left;">
        <pad><code>标签</code></pad>
        <framed class="any">
            <code>A</code>
        </framed>
    </visual>
    <andor>排他性或</andor>
    <visual class="enum" style="text-align: left;">
        <pad><code>标签</code></pad>
        <framed class="any" style="width: 160px;">
            <code>B</code>
        </framed>
    </visual>
    <andor>排他性或</andor>
    <visual class="enum" style="text-align: left;">
        <pad><code>标签</code></pad>
        <framed class="any" style="width: 80px;">
            <code>C</code>
        </framed>
    </visual>
    <description>
        安全地保存 A、B 或 C。<br>又名“标签联合”，尽管编译器会忽略标签。
    </description>
</datum>


<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>union { ... }</code></name>
    <visual style="text-align: left;">
        <framed class="any">
            <code>A</code>
        </framed>
    </visual>
    <andor>不安全或</andor>
    <visual style="text-align: left;">
        <framed class="any" style="width: 160px;">
            <code>B</code>
        </framed>
    </visual>
    <andor>不安全或</andor>
    <visual style="text-align: left;">
        <framed class="any" style="width: 80px;">
            <code>C</code>
        </framed>
    </visual>
    <description>
        不安全地以多种方式解释同一块内存。<br>结果可能是未定义的。
    </description>
</datum>




## 引用和指针 {#references-pointers-ui}

引用授权了对其他内存空间的安全访问。裸指针则是不安全 `unsafe` 的访问。某些引用会有额外的合理负载 `payload`，见下。可变（`mut`）类型的表示一致。


<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>&'a T</code></name>
    <visual>
        <ptr>
           <code>ptr</code><sub>4/8</sub>
        </ptr>
        <payload>
            <code>payload</code><sub>4/8</sub>
        </payload>
    </visual>
    <memory-entry>
        <memory-link style="left:46%;">|</memory-link>
        <memory class="anymem">
            <framed class="any unsized"><code>T</code></framed>
        </memory>
    </memory-entry>
    <description>在 <code>'a</code> 期间，任意目标“内存”<br>都一定是有效的 <code>T</code>类型的 <code>t</code>。</description>
</datum>


<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>*const T</code></name>
    <visual class="unsafe">
        <ptr>
           <code>ptr</code><sub>4/8</sub>
        </ptr>
        <payload>
            <code>payload</code><sub>4/8</sub>
        </payload>
    </visual>
    <zoom>
        没有任何保证。
    </zoom>
</datum>

<br/>


{{ tablesep() }}


负载 `payload` 取决于引用的基类型。这对指针也适用。

<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>&'a T</code></name>
    <visual>
        <ptr>
           <code>ptr</code><sub>4/8</sub>
        </ptr>
    </visual>
    <memory-entry>
        <memory-link style="left:46%;">|</memory-link>
        <memory class="anymem">
            <framed class="any t"><code>T</code></framed>
        </memory>
    </memory-entry>
</datum>


<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>&'a T</code></name>
    <visual>
        <ptr>
           <code>ptr</code><sub>4/8</sub>
        </ptr>
        <sized>
            <code>len</code><sub>4/8</sub>
        </sized>
    </visual>
    <memory-entry>
        <memory-link style="left:46%;">|</memory-link>
        <memory class="anymem">
            <framed class="any unsized"><code>T</code></framed>
        </memory>
    </memory-entry>
    <description>如果 <code>T</code> 是未知大小的 <code>struct</code>，如<br><code>S { x: [u8] }</code>。
    字段 <code>len</code> 则是 <br> dyn 的长度，即内容的大小。</description>
</datum>



<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>&'a [T]</code></name>
    <visual>
        <ptr>
           <code>ptr</code><sub>4/8</sub>
        </ptr>
        <sized>
            <code>len</code><sub>4/8</sub>
        </sized>
    </visual>
    <memory-entry class="double">
        <memory-link style="left:24%;">|</memory-link>
        <memory class="anymem">
            ...
            <framed class="any" style="width: 30px;"><code>T</code></framed>
            <framed class="any" style="width: 30px;"><code>T</code></framed>
            ...
        </memory>
    </memory-entry>
</datum>


<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>&'a str</code></name>
    <visual>
        <ptr>
           <code>ptr</code><sub>4/8</sub>
        </ptr>
        <sized>
            <code>len</code><sub>4/8</sub>
        </sized>
    </visual>
    <memory-entry class="double">
        <memory-link style="left:24%;">|</memory-link>
        <memory class="anymem">
            ...
            <byte class="bytes"><code>U</code></byte>
            <byte class="bytes"><code>T</code></byte>
            <byte class="bytes"><code>F</code></byte>
            <byte class="bytes"><code>-</code></byte>
            <byte class="bytes"><code>8</code></byte>
            ...
        </memory>
    </memory-entry>
</datum>

<br>

<!-- NEW ENTRY -->
<datum class="spaced" style="padding-bottom: 165px;">
    <name><code>&'a dyn Trait</code></name>
    <visual>
        <ptr>
           <code>ptr</code><sub>4/8</sub>
        </ptr>
        <ptr>
            <code>ptr</code><sub>4/8</sub>
        </ptr>
    </visual>
    <memory-entry>
        <memory-link style="left:49%;">|</memory-link>
        <memory class="anymem">
            <framed class="any unsized"><code>T</code></framed>
        </memory>
    </memory-entry>
    <memory-entry style="width:220px; position: absolute;">
        <memory-link style="left:22%;">|</memory-link>
        <memory class="static-vtable" style="width: 210px;">
            <table>
                <tr class="vtable"><td><code>*Drop::drop(&mut T)</code></td></tr>
                <tr class="vtable"><td><code>size</code></td></tr>
                <tr class="vtable"><td><code>align</code></td></tr>
                <tr class="vtable"><td><code>*Trait::f(&T, ...)</code></td></tr>
                <tr class="vtable"><td><code>*Trait::g(&T, ...)</code></td></tr>
            </table>
        </memory>
        <description>其中 <code>*Drop::drop()</code>、<code>*Trait::f()</code> 等<br>都是各自对 <code>T</code> 实现 <code>impl</code> 的指针。</description>
    </memory-entry>

</datum>



## 闭包 {#closures-data}

闭包是一个临时函数，定义闭包时，它会自动管理数据**捕获**{{ ref(page="types/closure.html#capture-modes") }}环境中访问的内容。例如：

<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>move |x| x + y.f() + z</code></name>
    <visual>
       <framed class="any" style="width: 100px;"><code>Y</code></framed>
       <framed class="any" style="width: 50px;"><code>Z</code></framed>
    </visual>
    <zoom>匿名闭包类型 C1</zoom>
    <!-- <description>Also produces anonymous <br><code>f_c1 (c: C1, x: T)</code>. Details depend<br> which <code>FnOnce</code>, <code>FnMut</code>, <code>Fn</code> is allowed.</description> -->
</datum>



<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>|x| x + y.f() + z</code></name>
    <visual>
        <ptr>
           <code>ptr</code><sub>4/8</sub>
        </ptr>
        <ptr>
           <code>ptr</code><sub>4/8</sub>
        </ptr>
    </visual>
    <zoom>匿名闭包类型 C2</zoom>
    <memory-entry>
        <memory-link style="left:44%;">|</memory-link>
        <memory class="anymem">
            <framed class="any" style="width: 30px;"><code>Y</code></framed>
        </memory>
    </memory-entry>
    <memory-entry>
        <memory-link style="left:44%;">|</memory-link>
        <memory class="anymem">
            <framed class="any" style="width: 30px;"><code>Z</code></framed>
        </memory>
    </memory-entry>
    <!-- <description>Similar, but captured context by<br> reference. Details might differ <br> depending on types involved.</description> -->
</datum>

<!-- Little hack as description below was too cluttered. -->
<datum>
    <name>&nbsp;</name>
    <description>
    同样地，生成匿名函数 <code>fn</code> 比如 <code>f_c1 (C1, X)</code> 或 <br>
    <code>f_c2 (&C2, X)</code>。具体地 <code>FnOnce</code>、<code>FnMut</code>、<code>Fn</code> 等<br>也取决于属性和捕获类型。
    </description>
</datum>




## 标准库类型 {#standard-library-types}

Rust 标准库为上面提到的基本类型扩展了更多有用的类型，并定义了一些特殊的语义。一些通用类型如下：

<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>UnsafeCell&lt;T&gt;</code></name>
    <visual class="cell">
           <framed class="any unsized"><code>T</code></framed>
    </visual>
    <description>魔术类型，允许<br>别名可变性。</description>
</datum>


<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>Cell&lt;T&gt;</code></name>
    <visual>
           <framed class="any unsized celled"><code>T</code></framed>
    </visual>
    <description>允许 <code>T</code> 的<br>移动进出。</description>
</datum>


<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>RefCell&lt;T&gt;</code></name>
    <visual>
        <sized class="celled"><code>borrowed</code></sized>
        <framed class="any unsized celled"><code>T</code></framed>
    </visual>
    <description>也支持动态借用 <code>T</code>。<br>就像 <code>Cell</code> 是 <code>Send</code> 而不是 <code>Sync</code>。</description>
</datum>


<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>AtomicUsize</code></name>
    <visual class="atomic">
        <ptr class="atomic">
            <code>usize</code><sub>4/8</sub>
        </ptr>
    </visual>
    <description>其他原子类型类似。</description>
</datum>




<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>Option&lt;T&gt;</code></name>
    <visual class="enum" style="text-align: left;">
        <pad><code>标签</code></pad>
    </visual>
    <andor>或</andor>
    <visual class="enum">
        <pad><code>标签</code></pad>
        <framed class="any" style="width: 100px;">
            <code>T</code>
        </framed>
    </visual>
    <description>标签对特定的 T 不可见。</description>
</datum>


<!-- NEW ENTRY -->
<datum>
    <name><code>Result&lt;T, E&gt;</code></name>
    <visual class="enum">
        <pad><code>标签</code></pad>
        <framed class="any" style="width: 100px;">
            <code>E</code>
        </framed>
    </visual>
    <andor>或</andor>
    <visual class="enum">
        <pad><code>标签</code></pad>
        <framed class="any" style="width: 100px;">
            <code>T</code>
        </framed>
    </visual>
</datum>

{{ tablesep() }}


**通用堆存储器**


<!-- NEW ENTRY -->
<datum class="spaced">
    <name><code>Box&lt;T&gt;</code></name>
    <visual>
        <ptr>
           <code>ptr</code><sub>4/8</sub>
        </ptr>
        <payload>
            <code>payload</code><sub>4/8</sub>
        </payload>
    </visual>
    <memory-entry>
        <memory-link style="left:49%;">|</memory-link>
        <memory class="heap">
        <framed class="any unsized"><code>T</code></framed>
        </memory>
    </memory-entry>
</datum>

<spacer>
</spacer>

<!-- NEW ENTRY -->
<datum>
    <name><code>Vec&lt;T&gt;</code></name>
    <!-- For some reason we need the width for mobile not to line break -->
    <visual>
        <ptr>
           <code>ptr</code><sub>4/8</sub>
        </ptr>
        <sized>
            <code>capacity</code><sub>4/8</sub>
        </sized>
        <sized>
            <code>len</code><sub>4/8</sub>
        </sized>
    </visual>
    <memory-entry class="double">
        <memory-link style="left:25%;">|</memory-link>
        <memory class="heap capacity">
            <div>
                <framed class="any t"><code>T</code></framed>
                <framed class="any t"><code>T</code></framed>
                <note>... len</note>
            </div>
            <capacity>← <note>capacity</note> →</capacity>
        </memory>
    </memory-entry>
</datum>


{{ tablesep() }}


**所有字符串**


<!-- NEW ENTRY -->
<datum>
    <name><code>String</code></name>
    <!-- For some reason we need the width for mobile not to line break -->
    <visual>
        <ptr>
           <code>ptr</code><sub>4/8</sub>
        </ptr>
        <sized>
            <code>capacity</code><sub>4/8</sub>
        </sized>
        <sized>
            <code>len</code><sub>4/8</sub>
        </sized>
    </visual>
    <memory-entry class="double">
        <memory-link style="left:25%;">|</memory-link>
        <memory class="heap">
            <div>
                <byte class="bytes"><code>U</code></byte>
                <byte class="bytes"><code>T</code></byte>
                <byte class="bytes"><code>F</code></byte>
                <byte class="bytes"><code>-</code></byte>
                <byte class="bytes"><code>8</code></byte>
                <note>... len</note>
            </div>
            <capacity>← <note>capacity</note> →</capacity>
        </memory>
    </memory-entry>
    <description>观察 <code>String</code> 和 <code>&str</code> 与 <code>&[char]</code> 的差异。</description>
</datum>

<spacer>
</spacer>

<!-- NEW ENTRY -->
<datum>
    <name><code>CString</code></name>
    <!-- For some reason we need the width for mobile not to line break -->
    <visual>
        <ptr>
           <code>ptr</code><sub>4/8</sub>
        </ptr>
        <sized>
            <code>len</code><sub>4/8</sub>
        </sized>
    </visual>
    <memory-entry class="double">
        <memory-link style="left:25%;">|</memory-link>
        <memory class="heap">
            <div>
                <byte class="bytes"><code>A</code></byte>
                <byte class="bytes"><code>B</code></byte>
                <byte class="bytes"><code>C</code></byte>
                <note>... len ...</note>
                <byte class="bytes"><code>␀</code></byte>
            </div>
        </memory>
    </memory-entry>
    <description>以空字符结束，但中间没有空字符。</description>
</datum>


<spacer>
</spacer>

<!-- NEW ENTRY -->
<datum>
    <name><code>OsString</code> {{ todo() }}</name>
    <!-- For some reason we need the width for mobile not to line break -->
    <visual class="platformdefined">
        平台定义
    </visual>
    <memory-entry class="double">
        <memory-link style="left:25%;">|</memory-link>
        <memory class="heap">
            <div>
                <byte class="bytes"><code>?</code></byte>
                <byte class="bytes"><code>?</code></byte>
                /
                <word class="bytes"><code>?</code></word>
                <word class="bytes"><code>?</code></word>
            </div>
        </memory>
    </memory-entry>
    <description></description>
</datum>

<spacer>
</spacer>

<!-- NEW ENTRY -->
<datum>
    <name><code>PathBuf</code> {{ todo() }}</name>
    <!-- For some reason we need the width for mobile not to line break -->
    <visual class="platformdefined">
        <payload>
            <code>OsString</code>
        </payload>
    </visual>
    <memory-entry class="double">
        <memory-link style="left:40%;">|</memory-link>
        <memory class="heap">
            <div>
                <byte class="bytes"><code>?</code></byte>
                <byte class="bytes"><code>?</code></byte>
                /
                <word class="bytes"><code>?</code></word>
                <word class="bytes"><code>?</code></word>
            </div>
        </memory>
    </memory-entry>
</datum>


{{ tablesep() }}

**共享所有权**

如果类型 `T` 不包含 `Cell`，那它也会包含以下 `Cell` 类型的变体以允许共享实际可变性。

<!-- NEW ENTRY -->
<datum>
    <name><code>Rc&lt;T&gt;</code></name>
    <visual style="width: 180px;">
        <ptr>
           <code>ptr</code><sub>4/8</sub>
        </ptr>
        <payload>
            <code>payload</code><sub>4/8</sub>
        </payload>
    </visual>
    <div>
        <memory-entry class="quad">
            <memory-link style="left:15%;">|</memory-link>
            <memory class="heap">
                <sized class="celled"><code>strong</code><sub>4/8</sub></sized>
                <sized class="celled"><code>weak</code><sub>4/8</sub></sized>
                <framed class="any unsized"><code>T</code></framed>
            </memory>
        </memory-entry>
    </div>
    <description>在同一个线程上共享 <code>T</code> 的所有权。需要嵌套 <code>Cell</code>
    或 <br><code>RefCell</code> 以允许修改。它既不是 <code>Send</code> 也不是 <code>Sync</code> 的。</description>
</datum>


<!-- NEW ENTRY -->
<datum>
    <name><code>Arc&lt;T&gt;</code></name>
    <visual style="width: 180px;">
        <ptr>
           <code>ptr</code><sub>4/8</sub>
        </ptr>
        <payload>
            <code>payload</code><sub>4/8</sub>
        </payload>
    </visual>
    <div style="width: 0px;">
        <memory-entry class="quad">
            <memory-link style="left:15%;">|</memory-link>
            <memory class="heap">
                <sized class="atomicx"><code>strong</code><sub>4/8</sub></sized>
                <sized class="atomicx"><code>weak</code><sub>4/8</sub></sized>
                <framed class="any unsized"><code>T</code></framed>
            </memory>
        </memory-entry>
    </div>
    <description>同左。但如果 T 是  <code>Send</code> 和 <code>Sync</code> 的，则允许在线程间共享。</description>
</datum>

<br>

<!-- NEW ENTRY -->
<datum>
    <name><code>Mutex&lt;T&gt;</code> / <code>RwLock&lt;T&gt;</code></name>
    <visual style="width: 230px;">
        <ptr><code>ptr</code><sub>4/8</sub></ptr>
        <sized class="atomicx"><code>poisoned</code><sub>4/8</sub></sized>
        <framed class="any unsized celled"><code>T</code></framed>
    </visual>
    <memory-entry>
        <memory-link style="left:45%;">|</memory-link>
        <memory class="heap">
            <code>lock</code>
        </memory>
    </memory-entry>
    <description>需要包在 <code>Arc</code> 里以实现线程间共享。总是 <code>Send</code> 和 <code>Sync</code> 的。<br>可考虑用 <a href="https://crates.io/crates/parking_lot">parking_lot</a> 代替（更快且无堆分配）。
    </description>
</datum>


---

<!-- End NOPRINT for datatypes -->
</div>


# 标准库

<!-- <div class="wip"> -->

## Trait {#traits}

Trait 定义通用行为。如果 `S` 实现了 `trait T`，意味着 `S` 可以做 `T` 规定的行为。下面是一些常用但有些技巧性的 trait。

#### 🧵 线程安全

<!-- Shamelessly stolen from https://www.reddit.com/r/rust/comments/ctdkyr/understanding_sendsync/exk8grg/ -->
<table class="sendsync">
    <thead>
        <tr><th>例</th><th><code>Send</code><sup>*</sup></th><th><code>!Send</code></th></tr>
    </thead>
    <tbody>
        <tr><td><code>Sync</code><sup>*</sup></td><td><code>Mutex&lt;T&gt;</code>、<code>Arc&lt;T&gt;</code><sup>1,2</sup>、<i>大多数类型</i>…… </td><td><code>MutexGuard&lt;T&gt;</code><sup>1</sup>、<code>RwLockReadGuard&lt;T&gt;</code><sup>1</sup></td></tr>
        <tr><td><code>!Sync</code></td><td><code>Cell&lt;T&gt;</code><sup>2</sup>、<code>RefCell&lt;T&gt;</code><sup>2</sup></td><td><code>Rc&lt;T&gt;</code>、<code>Formatter</code>、<code>&dyn Trait</code></td></tr>
    </tbody>
</table>

<div class="footnotes">

<sup>*</sup> **`T: Send`** 表示实例 `t` 可以移动到另一个线程；**`T: Sync`** 表示 `&t` 可以移动到另一个线程。<br>
<sup>1</sup> 如果 `T` 为 `Sync`。 <br>
<sup>2</sup> 如果 `T` 为 `Send`。

</div>


#### 🚥 迭代器


<div class="tabs header-std-green">

<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-trait-iter-1" name="tab-group-trait-iter" checked>
<label class="tab-label" for="tab-trait-iter-1"><b>使用迭代器</b></label>
<div class="tab-panel">
<div class="tab-content">


**基本用法**

假设有一系列 `C` 类型的 `c`：

* **`c.into_iter()`** &mdash; 将序列 `c` 转为 **`Iterator`** {{ std(page="std/iter/trait.Iterator.html") }} `i`，并**消耗掉**<sup>*</sup> `c`。要求为 `C` 实现 **`IntoIterator`** {{ std(page="std/iter/trait.IntoIterator.html") }}。条目类型取决于 `C`。获取迭代器的“标准”做法。
* **`c.iter()`** &mdash; **某些**序列提供的更优方法，返回**借用的**迭代器，不消耗掉 `c`。
* **`c.iter_mut()`** &mdash; 同上，但返回**可变借用**迭代器来允许改变序列内容。


**迭代器**

对于迭代条目 `i`：

* **`i.next()`** &mdash; 如果 `c` 有下一个元素则返回 `Some(x)`，否则返回 `None`。


**循环**

* **`for x in c {}`** &mdash; 语法糖。调用 `c.into_iter()` 并循环 `i` 直到 `None`。



<div class="footnotes">

<sup>*</sup> 如果 `c` 看似并未被消耗掉，是因为类型实现了 `Copy`。例如，调用 `(&c).into_iter()` 将会在 `&c` 调用 `.into_iter()`（将会消耗掉这个引用并将其转为迭代器），但是剩下的 `c` 不受影响。

</div>

</div></div></div>


<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-trait-iter-2" name="tab-group-trait-iter">
<label class="tab-label" for="tab-trait-iter-2"><b>实现迭代器</b></label>
<div class="tab-panel">
<div class="tab-content">

**基本用法**

假设有一系列的 `struct C {}`。


* **`struct Iter {}`** &mdash; 创建用于保存不可变迭代器状态的结构体（比如索引）。
* **`impl Iterator for Iter {}`** &mdash; 实现 `Iterator::next()` 以产生元素。

此外，可以在 `impl C {}` 里提供一个 `fn iter(&self) -> Iter` 方法。

**可变迭代器**

* **`struct IterMut {}`** &mdash; 创建可变迭代器的结构体，它可以将保存的 `C` 视为 `&mut`。
* **`impl Iterator for IterMut {}`** &mdash; 这里 `Iterator::Item` 就是个 `&mut item` 了。

类似地，也可以实现一个 `fn iter_mut(&mut self) -> IterMut` 方法。


**实现循环**
* **`impl IntoIterator for C {}`** &mdash; 此时，`for` 循环可以如此使用了 `for x in c {}`。
* **`impl IntoIterator for &C {}`** &mdash; 为使用方便也可实现这个。
* **`impl IntoIterator for &mut C {}`** &mdash; 同理……


</div></div></div>


</div>

{{ tablesep() }}

<!--
#### 📦 Type Conversions


Conversions XXX

<div class="header-std-yellow">

| Trait ... | Implementing ... for `S` means | Requiring `<A: ...>` means |
|---------|-------------|----|
| `Borrow<T>` | `S` can produce `&T`, must match `Eq`, ... | Caller can pass `t`, `&t`, `box_t` , ...|
| `BorrowMut<T>` | `S` can produce `&mut T`, rest same. | Similar, `t`, `&mut t`, ...   |
| `AsRef<T>` | `S` can produce `&T`. | Caller can pass `Box<T>` or XXX. |
| `AsMut<T>` | `S` can produce `&mut T`. | Similar, but XXX  |


{{ tablesep() }} -->



## 字符串转换 {#string-conversions}


将字符串 `x` 转为**目标**类型……

<!-- Create a horizontal scrollable area on small displays to preserve layout-->
<div style="overflow:auto;">
<div style="min-width: 100%; width: 650px;">


<div class="tabs">

<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-str-1" name="tab-group-str" checked>
<label class="tab-label" for="tab-str-1"><code>String</code></label>
<div class="tab-panel">
<div class="tab-content stringconversion">

| `x` 的类型 | 转换方法 |
| --- | --- |
|`String`|`x`|
|`CString`|`x.into_string()?`|
|`OsString`|`x.to_str()?.into()`|
|`PathBuf`|`x.to_str()?.into()`|
|`Vec<u8>` <sup>1</sup> |`String::from_utf8(x)?`|
|`&str`|`x.into()`|
|`&CStr`|`x.to_str()?.into()`|
|`&OSStr`|`x.to_str()?.into()`|
|`&Path`|`x.to_str()?.into()`|
|`&[u8]` <sup>1</sup> |`String::from_utf8_lossy(x).into()`|

</div></div></div>

<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-str-2" name="tab-group-str" >
<label class="tab-label" for="tab-str-2"><code>CString</code></label>
<div class="tab-panel">
<div class="tab-content stringconversion">

| `x` 的类型 | 转换方法 |
| --- | --- |
|`String`|`CString::new(x)?`|
|`CString`|`x`|
|`OsString` <sup>2</sup>|`CString::new(x.to_str()?)?`|
|`PathBuf`|`CString::new(x.to_str()?)?`|
|`Vec<u8>` <sup>1</sup> |`CString::new(x)?`|
|`&str`|`CString::new(x)?`|
|`&CStr`|`x.into()`|
|`&OSStr` <sup>2</sup> |`CString::new(x.to_os_string().into_string()?)?`|
|`&Path`|`x.to_str()?.into()`|
|`&[u8]` <sup>1</sup> |`CString::new(Vec::from(x))?`|
|`*mut c_char` <sup>3</sup> |`unsafe { CString::from_raw(x) }`|

</div></div></div>

<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-str-3" name="tab-group-str" >
<label class="tab-label" for="tab-str-3"><code>OsString</code></label>
<div class="tab-panel">
<div class="tab-content stringconversion">

| `x` 的类型 | 转换方法 |
| --- | --- |
|`String`|`x.into()`|
|`CString`|`x.to_str()?.into()`|
|`OsString`|`x`|
|`PathBuf`|`x.into_os_string()`|
|`Vec<u8>` <sup>1</sup> | {{ todo() }} |
|`&str`|`x.into()`|
|`&CStr`|`x.to_str()?.into()`|
|`&OSStr`|`x.into()`|
|`&Path`|`x.as_os_str().into()`|
|`&[u8]` <sup>1</sup> | {{ todo() }} |

</div></div></div>

<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-str-35" name="tab-group-str" >
<label class="tab-label" for="tab-str-35"><code>PathBuf</code></label>
<div class="tab-panel">
<div class="tab-content stringconversion">

| `x` 的类型 | 转换方法 |
| --- | --- |
|`String`|`x.into()`|
|`CString`|`x.to_str()?.into()`|
|`OsString`|`x.into()`|
|`PathBuf`|`x`|
|`Vec<u8>` <sup>1</sup> | {{ todo() }} |
|`&str`|`x.into()`|
|`&CStr`|`x.to_str()?.into()`|
|`&OSStr`|`x.into()`|
|`&Path`|`x.into()`|
|`&[u8]` <sup>1</sup> | {{ todo() }} |

</div></div></div>

<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-str-4" name="tab-group-str" >
<label class="tab-label" for="tab-str-4"><code>Vec&lt;u8&gt;</code></label>
<div class="tab-panel">
<div class="tab-content stringconversion">

| `x` 的类型 | 转换方法 |
| --- | --- |
|`String`|`x.into_bytes()`|
|`CString`|`x.into_bytes()`|
|`OsString`| {{ todo() }} |
|`PathBuf`| {{ todo() }} |
|`Vec<u8>` <sup>1</sup> |`x`|
|`&str`|`x.as_bytes().into()`|
|`&CStr`|`x.to_bytes_with_nul().into()`|
|`&OSStr`| {{ todo() }} |
|`&Path`| {{ todo() }} |
|`&[u8]` <sup>1</sup> |`x.into()`|

</div></div></div>

<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-str-5" name="tab-group-str" >
<label class="tab-label" for="tab-str-5"><code>&str</code></label>
<div class="tab-panel">
<div class="tab-content stringconversion">

| `x` 的类型 | 转换方法 |
| --- | --- |
|`String`|`x.as_str()`|
|`CString`|`x.to_str()?`|
|`OsString`|`x.to_str()?`|
|`PathBuf`|`x.to_str()?`|
|`Vec<u8>` <sup>1</sup> |`std::str::from_utf8(&x)?`|
|`&str`|`x`|
|`&CStr`|`x.to_str()?`|
|`&OSStr`|`x.to_str()?`|
|`&Path`|`x.to_str()?`|
|`&[u8]` <sup>1</sup> |`std::str::from_utf8(x)?`|

</div></div></div>

<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-str-6" name="tab-group-str" >
<label class="tab-label" for="tab-str-6"><code>&CStr</code></label>
<div class="tab-panel">
<div class="tab-content stringconversion">

| `x` 的类型 | 转换方法 |
| --- | --- |
|`String`|`CString::new(x)?.as_c_str()`|
|`CString`|`x.as_c_str()`|
|`OsString` <sup>2</sup>|`x.to_str()?`|
|`PathBuf`|`CStr::from_bytes_with_nul(x.to_str()?.as_bytes())?`|
|`Vec<u8>` <sup>1</sup> |`CStr::from_bytes_with_nul(&x)?`|
|`&str`|`CStr::from_bytes_with_nul(x.as_bytes())?`|
|`&CStr`|`x`|
|`&OSStr` <sup>2</sup>| {{ todo() }} |
|`&Path`| {{ todo() }} |
|`&[u8]` <sup>1</sup> |`CStr::from_bytes_with_nul(x)?`|
|`*const c_char` <sup>1</sup> |`unsafe { CStr::from_ptr(x) }`|

</div></div></div>

<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-str-8" name="tab-group-str" >
<label class="tab-label" for="tab-str-8"><code>&OsStr</code></label>
<div class="tab-panel">
<div class="tab-content stringconversion">

| `x` 的类型 | 转换方法 |
| --- | --- |
|`String`|`OsStr::new(&x)`|
|`CString`| {{ todo() }} |
|`OsString`|`x.as_os_str()`|
|`PathBuf`|`x.as_os_str()`|
|`Vec<u8>` <sup>1</sup> | {{ todo() }} |
|`&str`|`OsStr::new(x)`|
|`&CStr`| {{ todo() }} |
|`&OSStr`|`x`|
|`&Path`|`x.as_os_str()`|
|`&[u8]` <sup>1</sup> | {{ todo() }} |

</div></div></div>

<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-str-85" name="tab-group-str" >
<label class="tab-label" for="tab-str-85"><code>&Path</code></label>
<div class="tab-panel">
<div class="tab-content stringconversion">

| `x` 的类型 | 转换方法 |
| --- | --- |
|`String`|`x.as_ref()`|
|`CString`|`x.to_str()?.as_ref()`|
|`OsString`|`x.as_ref()`|
|`PathBuf`|`x.as_ref()`|
|`Vec<u8>` <sup>1</sup> | {{ todo() }} |
|`&str`|`x.as_ref()`|
|`&CStr`|`x.to_str()?.as_ref()`|
|`&OSStr`|`x.as_ref()`|
|`&Path`|`x`|
|`&[u8]` <sup>1</sup> | {{ todo() }} |

</div></div></div>

<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-str-7" name="tab-group-str" >
<label class="tab-label" for="tab-str-7"><code>&[u8]</code></label>
<div class="tab-panel">
<div class="tab-content stringconversion">

| `x` 的类型 | 转换方法 |
| --- | --- |
|`String`|`x.as_bytes()`|
|`CString`|`x.as_bytes()`|
|`OsString`| {{ todo() }} |
|`PathBuf`| {{ todo() }} |
|`Vec<u8>` <sup>1</sup> |`&x`|
|`&str`|`x.as_bytes()`|
|`&CStr`|`x.to_bytes_with_nul()`|
|`&OSStr`| `x.as_bytes()` <sup>2</sup> |
|`&Path`| {{ todo() }} |
|`&[u8]` <sup>1</sup> |`x`|

</div></div></div>

<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-str-9" name="tab-group-str" >
<label class="tab-label" for="tab-str-9"><code>*const c_char</code></label>
<div class="tab-panel">
<div class="tab-content stringconversion-other">

| **目标**类型 | **源**类型 `x` | 转换方法 |
| --- | --- | --- |
|<b>`*const c_char`</b>|<b>`CString`</b>|`x.as_ptr()`|


</div></div></div>

<!-- End tabs -->
</div>

<!-- End overflow protection -->
</div></div>


<div class="footnotes">

<sup>1</sup> 你应当或必须（当调用了 `unsafe` 时）确保裸数据是有效的字符串表示（比如，`String` 是  UTF-8 编码数据）。

<sup>2</sup> 仅在某些平台上 `std::os::<your_os>::ffi::OsStrExt` 有辅助方法来访问 `OsStr` 的裸 `&[u8]` 表示。所以有时需要手动再转换一遍：

```
use std::os::unix::ffi::OsStrExt;
let bytes: &[u8] = my_os_str.as_bytes();
CString::new(bytes)?
```

<sup>3</sup> `c_char` **必须**由前一个 `CString` 转换而来。如果是从 FFI 来的，则用 `&CStr` 代替。

</div>

{{ tablesep() }}


## 字符串格式化 {#string-formatting}

`print!`、`eprint!`、`write!`（这些宏和对应的 -`ln` 宏，如 `println!`）都会格式化。格式化参数是 `{}` 或 `{argument}`，或遵循下面的基本[**语法**](https://doc.rust-lang.org/std/fmt/index.html#syntax)：


```
{ [argument] ':' [[fill] align] [sign] ['#'] [width [$]] ['.' precision [$]] [type] }
```

<div class="header-undefined-color-3">

| 元素 |  含义 |
|---------| ---------|
| `argument` |  数字（`0`、`1`……）或参数名。如 `print!("{x}", x = 3)`。 |
| `fill` | 当提供了 `width` 时，用于填充空白的字符（如 `0`）。 |
| `align` | 当提供了 `width` 时，表示左（`<`）、中（`^`）、右（`>`）。 |
| `sign` | 为 `+` 时表示总是显示正负号。 |
| `#` | [变体格式化](https://doc.rust-lang.org/std/fmt/index.html#sign0)。如调试信息 `?` 或十六进制 `0x`。 |
| `width` | 用 `fill` 填充（默认为空格）的最小宽度（&geq; 0）。如果以 `0` 开始则以零填充。 |
| `precision` | 数字位数（&geq; 0），或非数字的最大宽度。 |
| `$` | 将 `width` 或 `precision` 解释为参数标识符，以允许动态格式化。 |
| `type` | [**调试**](https://doc.rust-lang.org/std/fmt/trait.Debug.html)格式化(`?`) 、十六进制(`x`)、二进制(`b`)、八进制(`o`)、指针(`p`)、科学计数法(`e`)……[参见更多](https://doc.rust-lang.org/std/fmt/index.html#traits)。 |

</div>


{{ tablesep() }}


<div class="header-undefined-color-3">

| 示例 | 说明 |
|---------|-------------|
| `{:?}` | 打印参数调试信息。 |
| `{2:#?}` | 打印第三个参数，并格式化成更易读的调试信息。 |
| `{val:^2$}` | 将具名参数 `val` 居中格式化，宽度由第三个参数指定。 |
| `{:<10.3}` | 左对齐打印，宽度为 10，小数位 3。|
| `{val:#x}` | 将参数 `val` 格式化为十六进制，并有前导 `0x`（`x` 的变体格式）。 |

</div>


{{ tablesep() }}


<!-- END WIP section -->
<!-- </div> -->

---

# 工具


## 项目结构 {#project-anatomy}

项目结构布局，通用的文件和目录，这是 Rust [工具化](#tooling)的一部分。

<div class="header-red">

| 文件/目录 | 代码 |
|--------| ---- |
| 📁 `benches/` | crate 的性能测试，用 `cargo bench` 运行，需要 nightly。<sup>*</sup> {{ experimental() }} |
| 📁 `examples/` | 使用 crate 的例程，用 `cargo run --example my_example` 运行。 |
| 📁 `src/` | 项目实际源代码。 |
| {{ tab() }} `build.rs` |  [预编译脚本](https://doc.rust-lang.org/cargo/reference/build-scripts.html)。比如，当编译 C / FFI 时需要在 `Cargo.toml` 中指定的。 |
| {{ tab() }} `main.rs` | 应用程序默认入口点，即 `cargo run` 运行的。 |
| {{ tab() }} `lib.rs` | 库默认入口点。从这里开始找 `my_crate::f`。 |
| 📁 `tests/` | 集成测试，用 `cargo test` 运行。单元测试通常直接写在 `src/`里。 |
| `.rustfmt.toml` | [自定义](https://rust-lang.github.io/rustfmt/) `cargo fmt` 格式。 |
| `.clippy.toml` | 特定 [clippy lints](https://rust-lang.github.io/rust-clippy/master/index.html) 配置。 |
| `Cargo.toml` | 主项目配置。定义依赖、选项等…… |
| `Cargo.lock` | 可复现构建的依赖详情。建议为应用程序加入 `git` 管理，库则不要。 |
</div>

<div class="footnotes">

<sup>*</sup> stable 可以考虑 [Criterion](https://github.com/bheisler/criterion.rs)。

</div>


{{ tablesep() }}

<!-- Also not printing this table -->
<div class="noprint">

一个最小的包含各种入口点的例子如下：

<div class="tabs">

<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-anatomy-1" name="tab-group-anatomy" checked>
<label class="tab-label" for="tab-anatomy-1"><b>应用程序</b></label>
<div class="tab-panel">
<div class="tab-content">

<!-- Create a horizontal scrollable area on small displays to preserve layout-->
<div style="overflow:auto;">
<div style="min-width: 100%; width: 650px;">

```
// src/main.rs (默认应用程序入口点)

fn main() {
    println!("Hello, world!");
}
```

</div></div></div></div></div>

<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-anatomy-2" name="tab-group-anatomy" >
<label class="tab-label" for="tab-anatomy-2"><b>库</b></label>
<div class="tab-panel">
<div class="tab-content">

<!-- Create a horizontal scrollable area on small displays to preserve layout-->
<div style="overflow:auto;">
<div style="min-width: 100%; width: 650px;">

```
// src/lib.rs (默认库入口点)

pub fn f() {}      // 根下的公共条目，可被外部访问。

mod m {
    pub fn g() {}  // 根下非公开 (`m` 不公开)，
}                  // 所以 crate 外不可访问。
```
</div></div></div></div></div>


<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-anatomy-25" name="tab-group-anatomy" >
<label class="tab-label" for="tab-anatomy-25"><b>过程宏</b></label>
<div class="tab-panel">
<div class="tab-content">

<!-- Create a horizontal scrollable area on small displays to preserve layout-->
<div style="overflow:auto;">
<div style="min-width: 100%; width: 650px;">

```
// src/lib.rs (默认过程宏入口点)

extern crate proc_macro;  // 需要显式引入

use proc_macro::TokenStream;

#[proc_macro_attribute]   // 用法 `#[my_attribute]`
pub fn my_attribute(_attr: TokenStream, item: TokenStream) -> TokenStream {
    item
}
```


```
// Cargo.toml

[package]
name = "my_crate"
version = "0.1.0"

[lib]
crate_type = ["proc-macro"]
```


</div></div></div></div></div>



<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-anatomy-3" name="tab-group-anatomy" >
<label class="tab-label" for="tab-anatomy-3"><b>单元测试</b></label>
<div class="tab-panel">
<div class="tab-content">

<!-- Create a horizontal scrollable area on small displays to preserve layout-->
<div style="overflow:auto;">
<div style="min-width: 100%; width: 650px;">

```
// src/my_module.rs (项目中的任何文件)

fn f() -> u32 { 0 }

#[cfg(test)]
mod test {
    use super::f;           // 需要从父模块引入
                            // 可访问非公开成员
    #[test]
    fn ff() {
        assert_eq!(f(), 0);
    }
}
```
</div></div></div></div></div>


<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-anatomy-4" name="tab-group-anatomy" >
<label class="tab-label" for="tab-anatomy-4"><b>集成测试</b></label>
<div class="tab-panel">
<div class="tab-content">

<!-- Create a horizontal scrollable area on small displays to preserve layout-->
<div style="overflow:auto;">
<div style="min-width: 100%; width: 650px;">

```
// tests/sample.rs (集成测试样例)

#[test]
fn my_sample() {
    assert_eq!(my_crate::f(), 123); // 集成测试（和性能测试）
                                    // 取决于 crate 作为第三方提供了什么
                                    // 仅可访问公开条目
```
</div></div></div></div></div>



<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-anatomy-5" name="tab-group-anatomy" >
<label class="tab-label" for="tab-anatomy-5"><b>性能测试</b></label>
<div class="tab-panel">
<div class="tab-content">

<!-- Create a horizontal scrollable area on small displays to preserve layout-->
<div style="overflow:auto;">
<div style="min-width: 100%; width: 650px;">

```
// benches/sample.rs (性能测试样例)

#![feature(test)]   // #[bench] 仍是实验性功能

extern crate test;  // 由于某些原因在 '18 仍需要声明
                    // 虽然通常在 '18 里并不需要

use test::{black_box, Bencher};

#[bench]
fn my_algo(b: &mut Bencher) {
    b.iter(|| black_box(my_crate::f())); // `black_box` 避免 `f` 被优化
}
```
</div></div></div></div></div>
</div>

<!-- End noprint of code examples -->
</div>



{{ tablesep() }}



## Cargo

Cargo 的常用命令和工具。


<div class="header-tooling">

| 命令 | 说明 |
|--------| ---- |
| `cargo init` | 在最新的版本上创建新项目。 |
| <code>cargo <span class="cargo-prefix">b</span>uild</code> | 调试模式构建项目。（`--release` 开启所有优化）。 |
| <code>cargo <span class="cargo-prefix">c</span>heck</code> | 检查项目是否可以编译（更快）。 |
| <code>cargo <span class="cargo-prefix">t</span>est</code> | 运行项目测试。 |
| <code>cargo <span class="cargo-prefix">r</span>un</code> | 运行项目。仅当生成了二进制文件（main.rs）。 |
| <code>cargo doc --open</code> | 生成项目代码和依赖的本地文档。 |
| `cargo rustc -- -Zunpretty=X` | 显示预处理过后的 Rust 代码。特别地，当 X 为： |
| {{ tab() }} `expanded` |  将展开所有宏…… |
| <code>cargo +{nightly, stable} ...</code>  | 以给定的工具链运行命令。比如仅 “nightly only” 的工具。 |
| `rustup docs` | 打开离线 Rust 文档（包括《Rust 程序设计语言》）。在飞机上也可以编程！ |

</div>

<div class="footnotes">

命令如 <code>cargo <span class="cargo-prefix">b</span>uild</code> 表示 `cargo build` 或 `cargo b` 都有效。

</div>


{{ tablesep() }}


`rustup` 的可选组件。用 `rustup component add [tool]` 安装。


<div class="header-tooling">

| 工具 | 说明 |
|--------| ---- |
| `cargo clippy` | 额外([lints](https://rust-lang.github.io/rust-clippy/master/)) 检查通用 API 误用和非惯用代码。{{ link(url = "https://github.com/rust-lang/rust-clippy") }} |
| `cargo fmt` | 自动代码格式化。(`rustup component add rustfmt`) {{ link(url = "https://github.com/rust-lang/rustfmt") }} |

</div>

{{ tablesep() }}

更多 cargo 插件可以在[**这里**](https://crates.io/categories/development-tools::cargo-plugins?sort=downloads) 找到。


{{ tablesep() }}


## 交叉编译 {#cross-compilation}

<!-- <div class="steps"> -->

<!-- Create a horizontal scrollable area on small displays to preserve layout-->
<div style="overflow:auto;">
<div style="min-width: 100%; width: 650px;">

🔘 检查[目标是否支持](https://forge.rust-lang.org/release/platform-support.html)。

🔘 安装目标依赖：**`rustup target install X`**。

🔘 安装本地工具链（取决于目标可能需要**链接**）。

应从目标供应商（Google、Apple 等）获取这些资源。也可能不支持本地宿主环境（比如，Windows 不支持 iOS 工具链）。

**某些工具链需要额外的构建步骤**（比如 Android 的 `make-standalone-toolchain.sh`）。

🔘 修改 **`~/cargo/.config`** 如下：

```
[target.aarch64-linux-android]
linker = "[PATH_TO_TOOLCHAIN]/aarch64-linux-android/bin/aarch64-linux-android-clang"
```

   或

```
[target.aarch64-linux-android]
linker = "C:/[PATH_TO_TOOLCHAIN]/prebuilt/windows-x86_64/bin/aarch64-linux-android21-clang.cmd"
```

取决于编译器警告，有时需要设置环境变量。某些平台和配置可能对路径或引号**非常**敏感（比如 `\` 对 `/`）：

```
set CC=C:\[PATH_TO_TOOLCHAIN]\prebuilt\windows-x86_64\bin\aarch64-linux-android21-clang.cmd
```

✔️ 用 **`cargo build --target=X`** 编译。


<!-- End overflow area -->
</div>
</div>

<!-- End steps  -->
<!-- </div> -->

{{ tablesep() }}

---

# 编码指南


## Rust 惯用法 {#idiomatic-rust}

Java 或 C 的使用者需要转换下思维：

<div class="header-blue">

| 习语 | 代码 |
|--------| ---- |
| **用表达式思考** | `x = if x { a } else { b };` |
|  | `x = loop { break 5 };`  |
|  | `fn f() -> u32 { 0 }`  |
| **用迭代器思考** | `(1..10).map(f).collect()` |
|  | <code>names.iter().filter(&vert;x&vert; x.starts_with("A"))</code> |
| **用 `?` 捕获异常** | `x = try_something()?;` |
|  | `get_option()?.run()?` |
| **使用强类型** | `enum E { Invalid, Valid { ... } }` 之于 `ERROR_INVALID = -1` |
|  | `enum E { Visible, Hidden }` 之于 `visible: bool` |
|  | `struct Charge(f32)` 之于 `f32` |
| **提供生成器** | `Car::new("Model T").hp(20).run();` |
| **分离实现** | 泛型 `S<T>` 可以对每个 `T` 都有不同的实现。 |
|   | Rust 没有面向对象，但通过 `impl` 可以实现特化。 |
| **Unsafe** | 尽量避免 `unsafe {}`，因为总是会有更快更安全的解决方案的。除了 FFI。 |
| **实现 Trait** | `#[derive(Debug, Copy, ...)]`。根据需要实现 `impl`。|
| **工具化** | 利用 [**clippy**](https://github.com/rust-lang/rust-clippy) 可以提升代码质量。 |
|  | 用 [**rustfmt**](https://github.com/rust-lang/rustfmt) 格式化可以帮助别人看懂你的代码。 |
|  | 添加**单元测试** {{ book(page="ch11-01-writing-tests.html") }}（`#[test]`），确保代码正常运行。 |
|  | 添加**文档测试** {{ book(page="ch14-02-publishing-to-crates-io.html") }}（` ``` my_api::f() ``` `），确保文档匹配代码。 |
| **文档** | 以文档注解的 API 可显示在 [**docs.rs**](https://docs.rs) 上。 |
|  | 不要忘记在开始加上**总结句**和**例程**。 |
|  | 如果有这些也加上：**Panics**、**Errors**、**Safety**、**Abort** 和**未定义行为**。 |

</div>



{{ tablesep() }}

> 🔥 **强烈**建议对所有共享项目都遵循
> [**API 指南**](https://rust-lang.github.io/api-guidelines/)（[**检查列表**](https://rust-lang.github.io/api-guidelines/checklist.html)）
> ！ 🔥


{{ tablesep() }}



## Async-Await 101

类似于 C# 或 TypeScript 的 async / await，但又有所不同：

<div class="header-orange">

| 语法 | 说明 |
|---------|-------------|
| `async`  | 所有声明 `async` 的都会返回 `impl Future<Output=_>`。{{ std(page="std/future/trait.Future.html") }} |
| {{ tab() }} `async fn f() {}`  | 函数 `f` 返回 `impl Future<Output=()>`。 |
| {{ tab() }} `async fn f() -> S {}`  | 函数 `f` 返回 `impl Future<Output=S>`。 |
| {{ tab() }} `async { x }`  | 将 `{ x }` 转换为 `impl Future<Output=X>`。 |
| `let sm = f();   ` | 调用 `async f()` **不会**执行 `f`，但会产生状态机 `sm`。{{ note(note="1") }} |
| {{ tab() }} `sm = async { g() };`  | 同上，**不会**执行代码块 `{ g() }`，但会产生状态机。 |
| `runtime.block_on(sm);` {{ note(note="2") }}  | 在 `async {}` 外部，将 `sm` 置为实际运行态。会执行 `g()`。 |
| `sm.await` | 在 `async {}` 内部，运行 `sm` 直到完成。若 `sm` 未就绪，则 yield 到当前运行时。 |

</div>


<div class="footnotes">

{{ note(note="1") }} 技术上，`async` 会将接下来的代码转换为匿名编译器生成状态机类型，并由 `f()` 实例化该状态机。状态机总是 `impl Future` 的。取决于 `async` 内部使用的类型也可以是 `Send<` 的。状态机由运行时工作线程通过调用 `Future::poll()` 直接管理，或者父级的 `.await` 间接管理。<br>
{{ note(note="2") }} 目前 Rust 不自带运行时。可用第三方 crate，比如 [async-std](https://github.com/async-rs/async-std) 或 [tokio 0.2+](https://crates.io/crates/tokio)。另外，Future 在 Rust 中用法多种多样。参见这里的**许多** [futures crate](https://github.com/rust-lang-nursery/futures-rs) 工具库。

</div>

{{ tablesep() }}


<!-- Futures as seen by someone who holds an `impl Future<Output=X>` after calling `f()`:

- This `impl Future` is an anonymous, compiler-generated instance of a state machine.
- After one or more `Future::poll()` calls it will be in state `Ready` and the `Output` will be available.
- State progression initiated via `Runtime::block_on()` (and friends) if not in `async` context already.
- State progression initiated via `.await` if called from existing `async` context.

Futures as seen by someone who authors `async f() {}`:
- The written code will be transformed into state machine code that will later be instantiated.
- Derived traits of state machine depending on types you use inside. Always `Future`, maybe `Send`, ...
- Actual execution of this state machine happens in context of runtime via `poll()`, never directly.
- Runtime will execute from worker thread. Might or might not be thread that invoked runtime.
- When executing, worker thread runs until end, or until it encounters _another_ state machine `x`.
- If control passed to `x` via `x.await`, worker thread continues with that one instead.
- At some point a low-level state machine invoked via `.await` might not be ready. In that the case worker thread returns all the way up to runtime so it can drive another Future. -->

对每个 `x.await`，状态机将会通过控制转移到状态机 `x`。有时，由 `.await` 调用的低级状态机并未就绪，此时，工作线程直接返回到运行时，以使得它可以驱动另一个 Future。一段时间后，运行时：
- **可能**恢复执行。常见于此，除非 `sm` / `Future` 已析构。
- **可能**由前一个**或另一个**工作线程恢复执行（取决于运行时）。

`async` 代码块内部代码的简易流程图如下：


<!-- Create a horizontal scrollable area on small displays to preserve layout-->
<div style="overflow:auto;">
<div style="min-width: 100%; width: 650px;">

```
       consecutive_code();           consecutive_code();           consecutive_code();
START --------------------> x.await --------------------> y.await --------------------> READY
// ^                          ^     ^                               Future<Output=X> 就绪 -^
// 由运行时调用                 |     |
// 或由外部 .await 调用         |     会由另一个线程恢复（下一个最佳可用的），
//                            |     或者当 Future 已析构时根本不会执行。
//                            |
//                            执行 `x`。若已就绪，则继续执行。
//                            若未就绪，返回当前线程到运行时。
```

</div>
</div>

{{ tablesep() }}

这导致编写 `async` 时有以下注意事项：

<div class="header-orange">


| 语法 {{ note(note="1") }} | 说明 |
|---------|-------------|
| `sleep_or_block();` | 显然不对{{ bad() }}，当前线程永不终止，阻碍执行器。 |
| `set_TL(a); x.await; TL();` | 显然不对{{ bad() }}，`await` 会由其他线程返回，[thread local](https://doc.rust-lang.org/std/macro.thread_local.html) 无效。 |
| `s.no(); x.await; s.go();` | 可能不对{{ bad() }}，如果等待时 `Future` 被析构，则 `await` [不会返回](http://www.randomhacks.net/2019/03/09/in-nightly-rust-await-may-never-return/)。{{ note(note="2") }} |
| `Rc::new(); x.await; rc();` | 非 `Send` 类型拒绝实现 `impl Future`。兼容性差。 |

</div>

<div class="footnotes">

{{ note(note="1") }} 假设 `s` 是非局部可临时进入无效状态的任意变量。
`TL` 是局部保存的线程，`async {}` 包含未保证执行约束的代码。<br/>
{{ note(note="2") }} 因为当 `Future` 被析构后，[Drop](https://doc.rust-lang.org/std/ops/trait.Drop.html) 可能会在任何情况下执行到。如果必须使 `.await` 保持在这种状态的话，考虑用 drop guard 来清理或者修复应用程序状态。

</div>



{{ tablesep() }}


## 闭包 API {#closures-in-apis}

这些子 trait 的关系是 `Fn` : `FnMut` : `FnOnce`。即一个实现了 `Fn` 的闭包，也同时实现了 `FnMut` 和 `FnOnce`。同样地，实现了 `FnMut` 的闭包，也必然实现了 `FnOnce`。

从调用者的角度来看这意味着：

<div class="header-green">

| 签名 | 函数 `g` 可以调用什么 |  函数 `g` 可以接受什么参数 |
|--------| -----------| -----------|
| `g<F: FnOnce()>(f: F)`  | ... `f()` 一次 |  `Fn`, `FnMut`, `FnOnce`  |
| `g<F: FnMut()>(mut f: F)`  | ... `f()` 多次 | `Fn`, `FnMut` |
| `g<F: Fn()>(f: F)`  | ... `f()` 多次  | `Fn` |

</div>

<div class="footnotes">

注意，对调用者来说，如何**确定** `Fn` 闭包，是最为严格的。但是一个**包含** `Fn` 的闭包，对调用者来説，是对任意函数都最兼容的。

</div>



{{ tablesep() }}

站在定义闭包的角度来看：

<div class="header-green">

| 闭包 | 实现<sup>*</sup> | 说明 |
|--------| -----------| --- |
| <code> &vert;&vert; { moved_s; } </code> | `FnOnce` | 调用者必须放弃 `moved_s` 的所有权。 |
| <code> &vert;&vert; { &mut s; } </code> | `FnOnce`, `FnMut` | 允许 `g()` 改变调用者的局部状态 `s`。 |
| <code> &vert;&vert; { &s; } </code> | `FnOnce`, `FnMut`, `Fn` | 可能不会导致状态改变，但可能会共享和重用 `s`。 |

</div>

<div class="footnotes">

<sup>*</sup> Rust [偏向于以索引捕获](https://doc.rust-lang.org/stable/reference/expressions/closure-expr.html)（在调用者视角上最“兼容” `Fn` 的闭包），但也可以用 `move || {}` 语法通过复制或者移动捕获相关环境变量。。

</div>

{{ tablesep() }}

这会带来如下优势和劣势：

<div class="header-green">

| 要求 | 优势 | 劣势 |
|--------| -----------| -----------|
| `F: FnOnce`  | <span class="good">容易满足调用者。</span> | <span class="bad">仅用一次，`g()` 仅会调用 `f()` 一次。</span> |
| `F: FnMut`  | <span class="good">允许 `g()` 改变调用者状态。</span> | <span class="bad">调用者不能在 `g()` 期间重用捕获。</span> |
| `F: Fn`  | <span class="good">可同时存在多个。</span> | <span class="bad">最难由调用者生成。</span> |

</div>


{{ tablesep() }}


## 理解生命周期 {#reading-lifetimes}

生命周期有时难以理解。这里给出一个简易指南，指导 C 经验者如何阅读和翻译包含生命周期声明的代码。

<div class="header-magenta">

| 写法 | 念法 |
|--------| -----------|
| `let s: S = S(0)`  | 一块 `S` 大小的空间，叫做 `s`。包含一个 `S(0)` 的值。 |
|   | 如果用 `let` 声明，这块空间将生存在栈上。{{ note( note="1") }} |
|   | 通常，`s` 表示 **`s` 的位置** 和 **`s` 里面的值**。 |
|   | 作为位置，`s = S(1)` 表示分配值 `S(1)` 到位置 `s`。 |
|   | 作为值，`f(s)` 表示用 `s` 里面的值调用 `f`。 |
|   | 当明确表示其位置（地址）时使用 `&s`。 |
|   | 当明确表示用于保存这样一个位置的位置时，使用 `&S`。 |
| `&'a S`  | `&S` 是（至少）**可以保存一个地址的位置**，叫做引用。 |
|   | 存在这里的任意地址，都一定指向有效的 `S`。 |
|   | 存在这里的任意地址必须被证明其生命周期不短于(_outlive_) `'a`。 |
|   | 换言之，`&S` 部分限定了这里包含的任意地址的范围。 |
|   | `&'a` 部分限定了地址至少生存的时间。 |
|   | 包含的位置与生命周期并不相干，但总比它短。 |
|   | `'a` 仅在编译时期可见，由完全的静态分析得出。 |
| `&S`  | 有时 `'a` 会被省略（或者不能被指定），但它仍然存在。 |
|   | 在方法体中，生命周期可以自动确定。 |
|   | 在签名中，生命周期可以被“省略”（自动标注）。 |
|  `&s` | 会产生**位置 `s` 的实际地址**，叫做“借用”。 |
|   | 一旦 `&s` 产生，位置 `s` 就会进入**被借用状态**。 |
|   | 检查是否处于被借用状态取决于编译期分析。 |
|   | 分析取决于可能的地址传播路径。 |
|   | 只要**任意** `&s` 存在，那么 `s` 就不能被直接改变。 |
|   | 例如，`let a = &s; let b = a;` 中的 `b`。 |
|   | 借用 `s` 会持续到 `&s` 最后一次使用，并非 `&s` 析构时。 |
| `&mut s` | 同上，但产生可变借用。 |
|   | `&mut` 允许**借用的所有者**（地址）改变 `s` 的内容。 |
|   | 这里不是指 `s` 中的值，而是 `s` 的位置本身被借用了。 |

<div class="footnotes">

<sup>1</sup> 与上面的[数据结构](#data-structures)一节比较：对于同步代码显然如此。对于 `async` 来说，“栈帧”可能存储在堆上，并且取决于运行时的实现。

</div>



{{ tablesep() }}

阅读函数或类型签名也有套路：

| 写法 | 念法 |
|--------| -----------|
| `S<'a> {}` | 标明 `S` 会包含{{ note( note="*") }}至少一个地址（如引用）。 |
|  | `'a` 由该结构体的使用者自动确定。 |
|  | `'a` 会尽可能选择最小的范围。 |
| `f<'a>(x: &'a T)`  | 标明函数会接受一个地址（如引用）。 |
| {{ tab() }} {{ tab() }} {{ tab() }} {{ tab() }} `-> &'a S` | ……也会返回一个地址。 |
|   | `'a` 由调用者自动确定。 |
|   | `'a` 会尽可能选择最小的范围。 |
|   | `'a` 会由调用方选择同时**满足输入和输出**的。 |
|   | 最重要的是，生命周期名称会**传播借用状态**！ |
|   | 所以当 `'a` 的结果地址被使用了之后， `'a` 的输入地址会被锁定。 |
|   | 这里，当`let s = f(&x)` 的 `s` 存在，`x` 会被标记为“已借用”。 |
| `<'a, 'b: 'a>` | `S<>` 和 `f<>` 里面声明的生命周期也可以有范围。 |
|  | `<'a, 'b>` 部分表示类型至少持有两个地址。 |
|  | `'b: 'a` 部分表示**生命周期范围**，意为 `'b` 必须不短于（**outlive**）`'a`. |
|  | `&'b X` 中的任意地址的生存时间必须和 `&'a Y` 中的至少一样长。 |

<div class="footnotes">

<sup>*</sup> 技术上，结构体可能不持有任何数据（比如使用 `'a` 上的 [PhantomData](https://doc.rust-lang.org/std/marker/struct.PhantomData.html) 或者函数指针），但仍然保留 `'a` 用于交流和约束函数的引用确定生命周期。

</div>

</div>

 <!--
 TODO: ADVANCED GUIDE TO WORKING WIHT LIFETIMES

 Taking into account
 - slightly confusing cases like https://stackoverflow.com/questions/42637911/how-can-this-instance-seemingly-outlive-its-own-parameter-lifetime
 - interplay between lifetime-baseed subtyping and assignability
 - reading rules for when temporaries are created (note to self, compare 4abdb9f16b01c51562563b44f6593dc98f675210 in my playground)
- simplified version of https://doc.rust-lang.org/nomicon/subtyping.html

 -->


{{ tablesep() }}



## Unsafe, Unsound, Undefined

Unsafe 导致 unsound，unsound 导致 undefined，undefined 是一切原力的阴暗面。


<div class="tabs">

<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-unsafe-1" name="tab-unsafe" checked>
<label class="tab-label" for="tab-unsafe-1"><b>Unsafe 代码</b></label>
<div class="tab-panel">
<div class="tab-content">

**Unsafe 代码**

- 标记为 `unsafe` 的代码有特权。比如，解引用裸指针，或调用其他 `unsafe` 函数。
- 这是一份特殊的**作者必须给编译器的承诺**，编译器**会**相信你。
- `unsafe` 代码自身并非有害，但危险的是 FFI 使用方或者异常的数据结构。

<div style="overflow:auto;">
<div style="min-width: 100%; width: 650px;">

```rust
// `x` must always point to race-free, valid, aligned, initialized u8 memory.
unsafe fn unsafe_f(x: *mut u8) {
    my_native_lib(x);
}
```
</div></div></div></div></div>


<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-unsafe-2" name="tab-unsafe" >
<label class="tab-label" for="tab-unsafe-2"><b>Undefined 行为</b></label>
<div class="tab-panel">
<div class="tab-content">

**未定义行为 (UB)**
- 如前所述，`unsafe` 代码意味着对编译器的[特殊承诺](https://doc.rust-lang.org/stable/reference/behavior-considered-undefined.html)（否则它就不需要是 `unsafe` 的了）。
- 不遵守承诺会使编译器产生错误的代码，执行错误的代码会导致未定义行为。
- 在触发未定义行为之后，**任何**事情都可能发生。这种不知不觉的影响可能1)难以捉摸，2)明显远离事发现场，或3)只有在某些条件下才会被发现。
- 一个表面上**可以运行**的程序（包括任意数量的单元测试），并不能证明含有未定义行为的代码不会因为一些偶然原因而失败。
- 含有未定义行为的代码在客观上是危险的、无效的，根本不应该存在。

<div style="overflow:auto;">
<div style="min-width: 100%; width: 650px;">


```rust
if maybe_true() {
    let r: &u8 = unsafe { &*ptr::null() };   // 一旦运行，整个程序都会处于未定义状态。
} else {                                     // 尽管这一行看似什么都没干，程序可能两条路径
    println!("the spanish inquisition");     // 都运行了，然后破坏掉数据，或者发生别的。
}
```
</div></div></div></div></div>



<!-- NEW TAB -->
<div class="tab">
<input class="tab-radio" type="radio" id="tab-unsafe-3" name="tab-unsafe" >
<label class="tab-label" for="tab-unsafe-3"><b>Unsound 代码</b></label>
<div class="tab-panel">
<div class="tab-content">

**Unsound 代码**
- 任何会由于用户输入而导致 _safe_ Rust 产生未定义行为的都是 **unsound**（不健全）的（尽管仅仅可能是理论上的）。
- 比如 `unsafe` 代码可能违反上述承诺而产生未定义行为。
- Unsound 代码对稳定性和安全性造成风险，且违背了大部分 Rust 用户的基本假设。

<div style="overflow:auto;">
<div style="min-width: 100%; width: 650px;">

```rust
fn unsound_ref<T>(x: &T) -> &u128 {      // Signature looks safe to users. Happens to be
    unsafe { mem::transmute(x) }         // ok if invoked with an &u128, UB for practically
}                                        // everything else.
```

</div></div></div></div></div>

</div>

{{ tablesep() }}

>
> **负责任地使用 Unsafe**
>
> - 除非非用不可，不要使用 `unsafe`。
> - 遵循[《死灵书》](https://doc.rust-lang.org/nightly/nomicon/)、[《Unsafe 指南》](https://rust-lang.github.io/unsafe-code-guidelines/)，**永远**保证**所有**的安全不变性，**绝不**引发[未定义行为](https://doc.rust-lang.org/stable/reference/behavior-considered-undefined.html)。
> - 最小化 `unsafe` 用例，封装成易于评审的小的、优雅的模块。
> - 每个 `unsafe` 用例应当同时提供关于其安全性的纯文本理由提要。



{{ tablesep() }}



## API 稳定性 {#api-stability}

这些更改会破坏客户端代码，请比较 [**RFC 1105**](https://github.com/rust-lang/rfcs/blob/master/text/1105-api-evolution.md)。主要更改(🔴)**一定导致破坏**，一般更改(🟡)**可能导致破坏**：

<div class="header-api-stability">


{{ tablesep() }}

| Crate |
|---------|
| 🔴 编写一个 _stable_ 的 crate 但却依赖了 _nightly_。 |
| 🟡 修改了 Cargo 的功能（比如添加或移除功能） |

{{ tablesep() }}


| 模块 |
|---------|
| 🔴 重命名、移动、移除任何公开项。 |
| 🟡 添加新的公开项，因为 `use your_crate::*` 可能会破坏现有代码。 |

{{ tablesep() }}

| 结构体 |
|---------|
| 🔴 当所有字段都为公开时添加私有字段。 |
| 🔴 当没有私有字段时添加公开字段。 |
| 🟡 当至少有一个字段时添加或移除私有字段（在更改前或更改后）。 |
| 🟡 将有私有字段（至少有一个字段）的元组结构转换到普通结构，或反之。 |

{{ tablesep() }}

| 枚举 |
|---------|
| 🔴 添加新的枚举变体。 |
| 🔴 为枚举变体添加新字段。 |


{{ tablesep() }}

| Trait |
|---------|
| 🔴 添加非默认项，将会破坏已有的 `impl T for S {}`。 |
| 🔴 任何不必要的项签名修改，都会影响到使用者或者实现方。 |
| 🟡 添加一个默认项，可能会和另一个 trait 产生歧义。 |
| 🟡 添加默认类型参数。 |

{{ tablesep() }}

| Trait |
|---------|
| 🔴 实现任何“基本”trait。**不去**实现一个基本 trait 是一种最基本的承诺。 |
| 🟡 实现任何非基本的 trait，可能会导致歧义。 |

{{ tablesep() }}

| 固有实现 |
|---------|
| 🟡 添加内部项，可能会导致客户端倾向于调用这个 trait 的 fn 而导致编译错误。 |

{{ tablesep() }}

| 类型定义签名 |
|---------|
| 🔴 强约束（如 `<T>` 到 `<T: Clone>`）。 |
| 🟡 弱约束。 |
| 🟡 添加默认类型参数。 |
| 🟡 泛型归纳。 |

| 函数签名 |
|---------|
| 🔴 添加或移除参数。 |
| 🟡 引入新的类型参数。 |
| 🟡 泛型归纳。 |


{{ tablesep() }}

| 行为更改 |
|---------|
| 🔴 / 🟡 **改变语义可能不会导致编译器错误，但可能会使用户产生错误的逻辑。** |


</div>


{{ tablesep() }}



<!-- Don't render this section for printing, won't be helpful -->
<div class="noprint">

---

# 附录


## 外链和服务 {#links-services}

一些优秀的可视化图表：

{{ tool(src="link_containers.png", title="容器", url="https://docs.google.com/presentation/d/1q-c7UAyrUlM-eZyTo1pd8SZ0qwA_wYxmPZVOQkoDmH4/edit") }}
{{ tool(src="link_railroad.png", title="宏", url="https://lukaslueg.github.io/macro_railroad_wasm_demo/") }}
{{ tool(src="link_lifetimes.png", title="生命周期", url="https://rufflewind.com/2017-02-15/rust-move-copy-borrow") }}

{{ tablesep() }}


<div class="header-lavender">

| 备忘清单 | 说明 |
|--------| -----------|
| [Rust Learning⭐](https://github.com/ctjhoa/rust-learning) | 可能是学习 Rust 最好的链接合集。 |
| [Functional Jargon in Rust](https://github.com/JasonShin/functional-programming-jargon.rs) | Rust 的函数编程术语解释合集。 |
| [Periodic Table of Types](http://cosmic.mearie.org/2014/01/periodic-table-of-rust-types) | 解释各种类型和引用是如何联系在一起的。 |
| [Futures](https://rufflewind.com/img/rust-futures-cheatsheet.html) | 如何使用 Future。 |
| [Rust Iterator Cheat Sheet](https://danielkeep.github.io/itercheat_baked.html) | `std::iter` 和 `itertools` 的迭代器相关方法总结 |
| [Type-Based Rust Cheat Sheet](https://upsuper.github.io/rust-cheatsheet/) | 常见类型和转换方法。 |

</div>


{{ tablesep() }}


多数 Rust 资料都由社区开发。


<div class="header-lavender">


| 书籍&nbsp;️📚  | 说明 |
|--------| -----------|
| [The Rust Programming Language](https://doc.rust-lang.org/stable/book/) | Rust 标准指南，**新手请从这里开始**。 |
| {{ tab() }} [API Guidelines](https://rust-lang.github.io/api-guidelines/) | 如何编写惯用的且可重用的 Rust 库。 |
| {{ tab() }} [Asynchronous Programming in Rust](https://rust-lang.github.io/async-book/)  {{ experimental() }} | 讲解 `async` 和 `Futures` 等。 |
| {{ tab() }} [Edition Guide](https://doc.rust-lang.org/nightly/edition-guide/) | 和 Rust 2015、Rust 2018 以及更高版本打好交道。  |
| {{ tab() }} [Guide to Rustc Development](https://rustc-dev-guide.rust-lang.org/index.html) | 讲解编译器内部工作机制。 |
| {{ tab() }} [Little Book of Rust Macros](https://danielkeep.github.io/tlborm/book/index.html) {{ experimental() }}| 社区集体对 Rust 宏的经验积累。 |
| {{ tab() }} [Reference](https://doc.rust-lang.org/stable/reference/) {{ experimental() }}  | Rust 语言的参考手册。  |
| {{ tab() }} [RFC Book](https://rust-lang.github.io/rfcs/) | 查找已接受的 RFC，查询它们是如何对语言产生影响的。 |
| {{ tab() }} [Rust Cookbook](https://rust-lang-nursery.github.io/rust-cookbook/) | 一些简单的最佳实践示例代码。 |
| {{ tab() }} [Rustdoc Book](https://doc.rust-lang.org/stable/rustdoc/) | 如何自定义 `cargo doc` 和 `rustdoc`。 |
| {{ tab() }} [Rustonomicon](https://doc.rust-lang.org/nomicon/) | Rust 编程高级用法和 Unsafe 的暗黑艺术。 |
| {{ tab() }} [Unsafe Code Guidelines](https://rust-lang.github.io/unsafe-code-guidelines/)  {{ experimental() }} | 编写 `unsafe` 代码的简要指南。 |
| {{ tab() }} [Unstable Book](https://doc.rust-lang.org/unstable-book/index.html) | 不稳定项的相关信息。比如 `#![feature(...)]`。  |
| [The Cargo Book](https://doc.rust-lang.org/cargo/) | 如何使用 `cargo` 和编写 `Cargo.toml`。 |
| [The CLI Book](https://rust-lang-nursery.github.io/cli-wg/) | 如何创建命令行（CLI）工具。 |
| [The Embedded Book](https://docs.rust-embedded.org/book/intro/index.html) | 在 `#![no_std]` 设备上进行嵌入式开发。 |
| {{ tab() }} [The Embedonomicon](https://docs.rust-embedded.org/embedonomicon/) | 在  Cortex-M 上的 `#![no_std]` 开发。 |
| [The WebAssembly Book](https://rustwasm.github.io/docs/book/) | 编写用于 Web 的 `.wasm`。 |
| {{ tab() }} [The `wasm-bindgen` Guide](https://rustwasm.github.io/docs/wasm-bindgen/) | 如何将 Rust 同 JavaScript API 绑定。 |

</div>

<!-- Disabled for now as looks abandoned w/o content -->
<!-- | {{ tab() }} [SIMD Performance Guide](https://rust-lang.github.io/packed_simd/perf-guide/) {{ experimental() }} | Work with `u8x32` or `f32x8` to speed up your computations.  | -->


{{ tablesep() }}

通用组件的综合查找表。

<div class="header-lavender">

| 列表&nbsp;📋| 说明 |
|--------| -----------|
| [Rust Changelog](https://github.com/rust-lang/rust/blob/master/RELEASES.md) | 查看特定版本中更改的内容。 |
| [Rust Forge](https://forge.rust-lang.org/) | 与编译器打交道的发行进度和链接。 |
| {{ tab() }} [Rust Platform Support](https://forge.rust-lang.org/release/platform-support.html) | 支持的平台和优先级。 |
| {{ tab() }} [Rust Component History](https://rust-lang.github.io/rustup-components-history/) | 查询特定平台上各种 Rust 工具的 **nightly** 状态。 |
| [ALL the Clippy Lints](https://rust-lang.github.io/rust-clippy/master/) | 你可能会有兴趣的 [**clippy**](https://github.com/rust-lang/rust-clippy) lint 用法。 |
| [Configuring Rustfmt](https://rust-lang.github.io/rustfmt/) | 用于 `.rustfmt.toml` 的 [**rustfmt**](https://github.com/rust-lang/rustfmt) 选项。 |
| [Compiler Error Index](https://doc.rust-lang.org/error-index.html) | 想知道 `E0404`啥意思？ |
</div>

{{ tablesep() }}


提供信息或工具的在线服务。

<div class="header-lavender">

| 服务&nbsp;⚙️ | 说明 |
|--------| -----------|
| [crates.io](https://crates.io/) | 所有 Rust 第三方库。 |
| [std.rs](https://std.rs/) | `std` 文档的快捷方式。 |
| [docs.rs](https://docs.rs/) | 第三方库的文档，由源代码自动生成。 |
| [lib.rs](https://lib.rs/) | 非官方的 Rust 高质量库和应用程序列表。 |
| [Rust Playground](https://play.rust-lang.org/) | 试用或者分享 Rust 代码片段的地方。 |

</div>

{{ tablesep() }}


## 打印 PDF


> 想要下载 Rust 备忘清单的 PDF？<a href="javascript:window.print()"><b>生成 PDF</b></a>（或者选择 文件 > 打印，可能要 10 秒左右）然后“保存为 PDF”。Firefox 和 Chrome 的 PDF 导出都看起来不错。也可以下载该<a href="https://github.com/kingfree/cheats.rs/releases/download/2020-5-29/rust-cheet-sheet-cn.pdf"><b>已生成的 PDF</b></a>。

</div>

<footer>

Ralf Biedert, {{ year() }} – [cheats.rs](https://cheats.rs) 
<br/>
中文翻译 [Kingfree](https://github.com/kingfree)
<br/>
[Legal & Privacy](legal).

</footer>
