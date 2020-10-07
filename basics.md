# 基本概念

## 链式调用

Rust里有大量的链式调用。例如

```rust
let mut x
io::stdin().read_line(&mut guess).expect("Failed to read line");
```

上面的`read_line()`返回的是一个`Result`类型。`Result`类似于C++的`optional`。二者最大的区别在于，`Result`的结果必须处理，否则编译器会报错；而`optional`最多只能在执行期抛异常。例如上面的代码，如果不调用`expect()`则编译器会直接报错。

`Result`的定义是这样的：

```rust
enum Result<T, E> {
  Ok(T),
  Err(E),
}
```

在Rust中`Result`是一种枚举类型。TODO:枚举类型以及C++ equivalents

`Result`或者`optional`所解决的一个问题是空指针。前者做得更彻底，使得Rust代码中不会有类似的问题。

Rust中大量应用了`Result`，这也是它的代码更安全的一个原因。

## 表达式

Rust的函数有两种返回形式，一种是像C++一样的`return`指令，另一种在最后一行写出要返回的变量，并且不带`;`，给人一种这个函数是一个表达式的感觉。我觉得这有点奇怪，TODO:他们为什么这么设计？本来就是指令式语言，何必搞这一套呢。

`if`是表达式，
`match`也是，但是不是每个arm都必须返回值。例如match到一个error时，逻辑上是没有值可返回的，编译器也允许你这么做：

```rust
TODO:补全代码
let guess: u32 = match guess.trim().parse() {
  Ok(num) => num,
  Err(_) => continue, // continue the loop
};
```

TODO:`for`和`loop`哪个是来着？

---

C++和Rust的initialization statement都不是expression，即`auto`和`let`都不回返回值。赋值操作都是expression。以下代码是类似的：

```rust
let mut x = 10;
let mut y = 20;
x = y = 30;
```

```cpp
auto x = 10;
auto y = 20;
x = y = 20;
```

不过二者有一点很不一样，就是由`{}`包裹的代码块。对Rust来说，这是一个expression，你可以这样写：

```rust
let y = {
  let x = 10;
  x + 1
};
```

注意，`x+1`后面没有分号，这一行是个表达式。作为block运行的最后一行代码，它也是整个`{}`块的值。当然，你也可以不返回什么：

```rust
let y = {
  let x = 10;
};
```

这等同于：

```rust
let y = {
  let x = 10;
  ()
}
```

`y`的类型`()`叫unit，或者叫，它只有一个值，也用`()`表示。大概有点像某些语言的`nil`。`()`是不占内存的。C++中的`NULL`或`nullptr`是占内存的。`()`又叫unit tuple，按这个思路，它在C++中的对应物大概是`std::tuple<>`。
