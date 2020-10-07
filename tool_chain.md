# 工具链

工具链由官方的`cargo`提供，负责一个包的整个生命周期。这是一整套流程，包括工程的创建，依赖管理以及包的上传等。

对于流程的每一步，C++都有对应的工具。例如，工程管理可以用cmake，包的管理则由每个distro自行维护。C++的包管理之混乱，历来饱受诟病。目前流行的工具链，尚没有一个是可以做好一个包的整个生命周期的。C++的委员会似乎对此也没什么意向。目前有一些尚不成熟或不流行的工具可用，例如build2，旨在模仿Rust的crate机制。不过要注意，这些工具都来自第三方。而`cargo`和crate则是由Rust官方维护的。再进一步讲，C++的委员会实际上只负责标准制定，编译器的实现也来自第三方。而Rust的编译器则来自rust官方。

Rust的包有一个统一的源，由Rust官方维护。任何人都可以上传其开发的包。而C++的依赖管理则更加倚重系统的发行版。于是，同一个项目组乃至同一个公司的开发人员，须用同一种系统。

总而言之，C++的工具链是不统一的和第三方的，而Rust则是统一且官方的。这一点Rust比C++好一些。

## 工程管理

在Rust中，一个工程称作一个“crate”。当然，你在一个工程内也可以有多个crates，看你怎么定义工程二字。

Cargo.toml用于管理包的信息，包括其依赖。你可以指定依赖的版本。Rust一般会要求其软件包向下兼容。不过鉴于Rust仍然在发展期，依赖breaking的情况未必少见。

## 编译

通常我们用`cargo`命令来做编译。`cargo build`用来编译代码。`cargo run`用来。你当然也可以直接用编译器`rustc`来编译。

`cargo build`默认是debug模式。你需要显式调用`--release`选项。debug模式的优化等级是最低的，编译是最快的，生成的二进制运行是最慢的。你可以在Cargo.toml中控制debug的编译选项。

Rust没有头文件和源文件之分。你也不用操心链接的问题。只要告诉Rust你的依赖就好。

Rust有着强大的静态分析，你可以`cargo check`你的代码，它只会做静态检查，不会编译。

## 依赖

对于标准库来说，在Rust中，想要在一个代码文件中使用某个包，可以这样：

```rust
use std::io;
```

在C++中，则要`#include <iostream>`。

不过在C++20中，你可以直接这样用：

```cpp
import <iostream>;
```

C++20有了module功能。module不会像include一样传染，也不会有循环引用的问题。据Bjarne说，module的编译速度可以快十倍。clang目前已经支持module，gcc还不支持。build2也支持，cmake不支持。

如果不是标准库，在Rust中，你需要在Cargo.toml中指定依赖

```toml
[dependencies]
your_dependency = "1.2.3"
```

初次`cargo build`时这些依赖会自动下载。

Rust另外有一个Cargo.lock文件，用于锁定你的依赖版本，以避免升级带来的影响。

在C++中，除了`#include`之外，如果你的依赖不在系统目录中，你还得指定include路径。另外，你可能还需要指定链接选项。例如在cmake中：

```cmake
target_include_directories(your_target PUBLIC path_to_include)
target_link_libraries(your_target PUBLIC dependent_target)
```

## 文档生成器

见另一个文件
