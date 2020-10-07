# IO

## 标准输出

Rust通常用`println!`来打印至标准输出，例如

```rust
println!("the answer is {}", 42);
```

对应C++的：

```cpp
std::cout << "the answer is " << 42 << '\n';
```

TODO:我不确定最后是`'\n'`还是`std::endl`

format功能也不是什么难事，在C++20之前也有第三方库可以做到，例如[fmt](https://github.com/fmtlib/fmt)。在C++20中，你可以这样：

```cpp
std::cout << std::format("the answer is {}\n", 42);
```

其实`std::format`的实现就是fmt库。