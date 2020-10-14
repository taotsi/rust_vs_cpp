# 控制流

## if

Rust的基本写法和C++类似：

```rust
if x < 0 {
  println!("negative");
} else if x > 0 {
  println!("positive");
} else {
  println!("zero");
}
```

> 插一句，就if语句来说，凡是以一对token（此处是花括号）来包裹代码块的语言，应该长得都差不多。与之相对的是以缩进来表示代码块的语言，是没法用`else if`这种写法的。你必须在`else`后面先换行缩进，才能再写`if`块。所以Python和Ruby之类的语言用的是`elif`和`elsif`这种方法。

但是Rust和C++的if语句也有很多不同的地方：

第一，Rust的if语句中的条件只能是`bool`。即使是整数类型，若非`bool`，Rust也不会帮你做隐式类型转换。

第二，Rust的`if`是表达式，你可以这样用：

```rust
let x = if y > 10 {20} else {30};
```

C++中if只能当statement用，既是compound statement，也是selection statement

## loop和while

Rust的`loop`大体类似于C++的`while true`，但是有差别。一个基本的`loop`块：

```rust
loop {
  print!("rose is a ");
}
```

`loop`会永远运行下去，直到遇见`break`。

`loop`是个表达式。它会返回`break`后面的表达式：

```rust
let mut counter = 0;
let x = loop {
  counter += 1;
  if counter == 5 {
    break counter;
  }
}
```

`loop`块一旦进入，想要正常退出，只能调用`break`。而`loop`又是一个表达式，所以就会有`break counter;`这种奇怪的写法。

Rust的`loop`和`while`有一点不同。`loop`语句是无条件进入的，所以必定会进入；而`while`语句如果条件一开始就不满足，压根就不会进去。所以`while`不能作为表达式：既然有可能就不进去，就可能没东西可返回。

`while`的用法是这样的：

```rust
let mut counter = 0;
while counter < 10 {
  counter += 1;
}
```

你也可以在`while`中用`break`，但是后面不能跟表达式。

## for

Rust的`for`语句只有类似于C++的range-based for loop的写法：

```rust
let a = [10, 20, 30, 40, 50];
for element in a.iter() {
  println!("{}", element);
}
```

如果你想要C++的for loop，只能用`while`实现。

C++的range-based for loop所迭代的对象需要实现一个迭代器类型，这个迭代器要实现`operator+()`。与之类似，Rust的迭代器需要实现`next()`。

当然，从C++20开始，你也可以用range library的那种迭代方式。

> C++的`for`有三个版本，最先是C风格那种，后来C++11出了range-based for loop，最近C++20又出了一个Ranges库。

TODO:迭代器


