# 所有权（Ownership）

在C++中，提到ownership，一般想到的是智能指针，主要是`std::shared_ptr`和`std::unique_ptr`。这和Rust不太一样。Rust中的ownership则是一个更普遍的概念。

Rust和C++的scope的概念是一样的。这里一般指的是一对`{}`所括起来的域。一个变量在退出其所在的scope时自动销毁。这得益于RAII。RAII是Bjarne发明的，使得在没有GC的情况下更安全地管理资源。换句话说，如果一个语言有GC，那它就不需要RAII。事实也是如此，有RAII的语言有C++、Rust和D等，都是C++这一派的。

Rust中构造函数和销毁函数分别叫`new()`和`drop()`，对应C++的`Widget()`和`~Widet()`。当然，Rust没有继承一说，所以它的销毁比C++简单一些。TODO:

在Rust中，`=`操作符默认行为是移动操作。在下面的代码中：

```rust
let s1 = String::from("hello");
let s2 = s1;
```

`s1`对内存的所有权被移入了`s2`。这类似（但不同）于C++中的：

```cpp
std::string s1{"hello"};
auto s2 = std::move(s1);
```

在C++中，一个变量被移动之后，它有可能被销毁，也有可能只是被置空。这取决于具体的情况。实际上，C++ core guidelines（C.64）中建议道，一个对象即使被移动，也应当保持有效状态；理想情况下该对象应当有着其类型的默认值。不过，C++标准比这宽松：只要这个对象可以被销毁就行。也就是说，在C++标准的眼中，一个变量即使在被移动之后，在它剩余的scope中依然是存活的。如果你真的在move操作里干了什么不该干的事情，那就只能跟bug在执行期见了。为此，C++ core guidelines还有另一条建议（E.5）：保持scope短小。

说了这么一大堆，Rust却有更粗暴的方法：一旦变量被移动，该变量立即失效，即使仍处于scope中，也不论其资源是否依然有效，编译器都不再允许你访问它。这样说也不全对，你还是可以对已经移动的变量重新赋值的：

```rust
let mut s1 = String::from("hello");
let s2 = s1;
s1 = String::from("world");
println!("{}", s1);
```

这也容易理解，`s1`在移动之后其实仍然处于有效状态，只是编译器阻止你访问它而已。如果你是对它重新赋值，它就获得了新的资源的所有权，当然也就可以被再次访问了。这个问题可能比表面上看起来的复杂TODO:。

在上面的`String`例子中，你可以用`clone()`方法来浅拷贝：

```rust
let s1 = String::from("hello");
let s2 = s1.clone();
```

其实`let s2 = s1.clone();`才更像是`auto s2 = std::move(s1);`。因为`std::move()`对`std::string`做的事情也是浅拷贝。当然，这话你不能全信。因为C++的移动语义到底是什么行为，取决于它的实现。`std::string`是浅拷贝，不代表其他的类也是。一般来说，移动过的变量还是不要再碰为好。

在Rust中，也不是所有类型的`=`操作符都是移动操作，比如那些标量类型（例如整数和浮点数）。在Rust中，有一个trait叫`Copy`，凡是实现它的对象，在把它赋值给其他变量之后也仍然可以访问。由`Copy`组成的tuple也是`Copy`。在C++中，POD（plain old data）的赋值操作也是复制，即使是移动你也只能复制。当然，C++的标量类型也没法移动。C++的POD和Rust的`Copy`有些不同：前者不可以有任何用户定制行为，包括构造和销毁函数，本质上等同于C的`struct`，而Rust的`Copy`只要求不允许实现`Drop`(否则可能会有多次释放的风险)。

Rust函数的形参类型若不标明为引用，在实例化时也会执行移动操作。下面的代码是不能编译的：

```rust
fn main() {
  let s1 = String::from("hello");
  func(s1);
  println!("{}", s1);
}

fn func(s: String) {
  println!("take ownership of {}", s);
}
```

原因是`s1`的所有权已被`func`的形参`s`取走。

同理，Rust函数的返回值类型若不标明为引用，同样也会执行移动操作。例如返回一个局部变量：

```rust
fn func() {
  let s = String::from("hello");
  s
}
```

`s`在返回时会被移动，这类似于C++的：

```cpp
void func() {
  std::string s{"hello"};
  return std::move(s);
}
```

当然，在C++里，你即使不加`std::move()`，由于[复制省略](https://en.cppreference.com/w/cpp/language/copy_elision)的存在，`s`可能也会被移动；或者更好，`s`的移动操作都可以被直接优化掉。一般来说，在C++里，对返回值`std::move()`是多此一举的：它有可能屏蔽复制省略，以致降低性能。

顺便插一句，在两种语言里，返回局部变量的引用都是不行的。

要想Rust函数形参不夺取变量所有权，把形参声明为引用类型即可：

```rust
fn func(s: &String) {
  // ...
}
```

这类似于C++的：

```cpp
void func(const std::string &s) {
  // ...
}
```

引用在Rust中又叫借用：它不夺取变量所有权，只是借用。引用可更改与否(mutability)取决于它所借用的变量可更改与否。上面的代码中，`s`是immutable的引用，只能读不能写。想要能写，得这样声明：

```rust
fn func(s: &mut String) {
  // ...
}
```

这就类似于C++的：

```cpp
void func(std::string &s) {
  // ...
}
```

一如你不能给C++的`func()`传入`const`参数，在Rust中你也不能给`func()`传入immutable的参数。

关于借用，有这样几条规则：
1.在同一个scope内，一个变量一次只能有一个mutable引用。这就避免了数据竞争问题。
