# 宏

宏在Rust中是高级特性。通常你并不会自己去定义宏。
在C/C++中，宏只是简单的文本替换，它甚至不是卫生宏。

在Rust中，宏需要显式地调用，方法是在宏的名字上加一个`!`，例如

```rust
println!("println is a macro");
```

调用的不是函数，而是宏。Rust的函数没有variadic parameters（TODO:为什么？），但是宏有。对于print这种功能来说，就只能用宏了。

Rust的宏目前有三种类型，TODO:
