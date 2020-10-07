# 变量

## 类型推断

虽然是静态语言，Rust也有类型推断：

```rust
let x_rs = 10;
```

`x_rs`默认是immutable的，如果你试图对它再次赋值，编译器就会报错。这类似于C++的

```cpp
const auto x_cc = 10;
```

但是**二者不完全一样**。TODO:Rust的右值默认会被move，而C++则是复制。

mutable变量需要显式声明:

```rust
let mut x_rs = 10;
```

这类似于C++的

```cpp
auto x_cc = 10;
```

注意，Rust的`mut`不同于C++的`mutable`哦。话说`mutable`这玩意有时候挺烦的，人要懂得克制自己。

除了`let`，Rust还有个`const`：

```rust
const ANSWER: u32 = 42;
```

Rust的`const`变量的类型必须显式声明。TODO:为什么？

这其实类似于C++的constexpr：

```cpp
constexpr int ANSWER = 42;
```

## 类型声明

当然可以显式声明变量类型：

```rust
let x_rs: u32 = 42;
```

大概类似于C++的

```cpp
int x_cc = 42;
```

## shadowing

Rust有一个shadow的概念，例如：

```rust
let x = 42;
let x = "answer";
```

你可以用`let`定义一个重名的变量，甚至改变它的类型。

有时这样还不错，对于语义相同而类型或值不同的变量，可以只用一个名字，以增加代码可读性。比如你有个`config`变量，它先是一个表明路径的字符串，继而变成一个读取文件的`Result`，继而变成文件里的内容，最后变成一个json类型。

不过也有坏处，毕竟同一个变量名字被多次定义之后，可能会造成误解。当你看到`config`的时候，可能会纳闷它到底是个什么东西。

## 类型

### 标量

Rust的整数是`i32`和`u32`这样的关键字来声明的。`u32`类似于C++的`unsgined long int`或者`uint32_t`（`<cstdint>`）。

Literal整数可以有分隔符，例如`let x = 1_234_567;`。C++14也有分隔符，是单引号`auto x = 1'234'567;`。单引号在Rust中另有他用（用于声明变量的生存期）。

Rust的整数也是有溢出风险的。debug模式下，执行期会有溢出检查，发现则抛异常；release模式则没有。编译器不会检查溢出。当然，如果你有意在溢出时wrap，可以用`Wrapping`类型来避免执行期异常。

Rust的浮点数类型是`f32`和`f64`，其中后者是默认类型。C++的浮点数默认是`double`，通常也是64位。

二者的算术运算是一样的。

二者的boolean也是一样的。

Rust的`char`是4字节的，支持unicode字符集，类似于C++的`wchar_t`。而C++的`char`就一个字节，实际上就是个8位整数，只支持ASCII。Rust这样设计其实也有风险，在迭代字符串的时候可能会错位或者只迭代一个字的一部分，这在语义上有问题，但是程序并不理解。

### 复合类型

#### tuple

Rust的tuple类似于C++的`std::tuple`，长度固定，类型固定，直接用括号声明：

```rust
let tup: (i32, f64, u8) = (500, 6.4, 1);
let (x, y, z) = tup;
```

第二行分解了tuple。这是一种模式匹配。类似于C++的

```cpp
auto tup = std::tuple<int, double, uint8_t>(500, 6.4, 1);
auto const [x, y, z] = tup;
```

C++17之后是可以省略模板参数的，即`auto tup = std::tuple(500, 6.4, 1);`（TODO:验证）。只不过第三个数1会推导为`int`。TODO:Rust也可以吗？

说到访问tuple的元素的方法，二者都挺怪的。C++里得这样：

```cpp
const auto x = std::get<0>(tup);
```

而Rust就要简单一些：

```rust
let x = tup.0;
```

Rust虽然看起来更简单，但是似乎跟其他特性没什么一致性。TODO:数字不是变量名吧？一致性？

#### 数组

和C++一样，Rust的数组是定长且同构的：

```rust
let a = [1, 2, 3];
```

在C++中，目前更推荐用`std::array`代替裸数组，C++17开始则可以省略模板类型，看起来更像上面的Rust代码：

```cpp
const auto a = std::array{1, 2, 3};
```

在C++17之前，模板类型需要显式给出：

```cpp
const auto a = std::array<int, 3>{1, 2, 3};
```

Rust中当然也可以显式声明数组类型：

```rust
let a: [i32; 3] = [1, 2, 3];
```

这个类型声明看起来也有点奇怪，和其他概念没什么一致性；不过倒是挺简洁。

Rust中，`let a = [42; 3];`会创建一个有3个42的数组，类似于C++的`const auto a = std::vector(3, 42);`。C++的`std::array`没有这个功能。

访问数组用`[]`操作符：

```rust
let a0 = a[0];
```

执行期会有边界检查，和C++的`at()`函数一样。而C++的`[]`操作符是没有边界检查的。其实这里能看出二者的设计上的差异，Rust宁可牺牲一点性能也要在执行期做边界检查。编译器检查是不可能的，哪个语言都做不到。
