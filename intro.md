# Intro

这篇帖子主要介绍Rust和C++在细节上的异同。

这篇帖子是写给有C++背景同时又对Rust感兴趣的人的。主要参考是Rust官方的那本入门书。我尽量不说废话，让信息密度最大化。看完这篇帖子，你不一定会写Rust，但是对于它的基本概念应该会有比较全面的了解。

这篇帖子既是survey，也是review。不过我水平有限，提供的信息或者观点可能不正确，还请您甄别地读。

对于有C++背景的人，入门Rust并不难，学习曲线也不陡峭。总的来说，Rust比C++简单不少。C++的特性数不胜数，一致性也有些瑕疵，以至于成为现金世上最难精通的编程语言之一。

这篇帖子并不是灭C++威风而涨Rust士气的。我本身是C++工程师。只不过有不少地方Rust确实做得比C++好。比如说，Bjarne在八十年代就提出constraint的概念了，但是直到C++20才正式进入标准；而Rust则生来就有这个特性。

Rust虽然没提，但是很显然它就是在对标C++。当然，Rust的有些概念是别的语言来的，但是你会更多地感觉到它其实是在处处针对C++的。

把两门类似的语言放在一块对比，你会对这类语言有更深的理解。即使平时只用其中一门，对另一门的理解也可以帮助你把手上这门写得更好。

Rust和C++其实在设计理念上有一些根本的不同。并非C++历史包袱更重，有些设计是刻意为之的。

文中所提的C++，如果不特意说明版本，通常标准为C++11，不过有时可能也在捎带着说C。

有普遍使用的汉语词，用汉语；否则用英语

Rust和C++不同的一些点：

- `=`操作符默认是移动操作，lhs类型默认是常量引用。不止如此，对于非常量引用，Rust另有“borrow rules”，即对每个变量的存活的mutable引用的在一个时刻只能有一个。
- Rust没有class的概念，有的是`struct`，即数据结构。当然，你也可以给`struct`定义方法。
- Rust的traits大体上对应C++的模板，或者说C++20的concept。有一点不同，trait可以定义默认行为。
- Rust没有继承，如果重用函数，你可以用trait。如果想重用数据，恐怕就只能用“has a”了。
- Rust的宏和C++很不一样。

C++没有而Rust有的东西：

- 文档生成工具
- 测试框架
- 包管理框架
- Rust的宏

C++有而Rust没有的东西：

- 沉重的历史包袱
- variadic parameters of functions
- TODO:

二者相同的一些地方：

- 都是系统级编程语言。Rust甚至可以在STM32上运行
- 静态类型。Rust几乎强到极端
- 编译型
- 支持OOP。不过Rust没有继承
- 支持functional
- 都由非盈利组织管理。不过Rust的组织似乎更高效一些。
- 都会做mangling，但是也可以控制编译不mangle
- 都没有自动垃圾回收。通常都依赖destructor，这一概念实际上是Bjarne提出的
- 都可能写出runtime不安全的代码。Rust有很强的静态分析，让你在编译器就避免很多错误。但是你还是可以命令编译器跳过那些检查，最终写出可能不安全的代码。另外，即使Rust编译器运行了所有的检查，你的代码还是有可能在执行期出问题。
- 都有类型推断

一些参考：
[cpp vs rust cheatsheet](https://maulingmonkey.com/guide/cpp-vs-rust/)
[the rust book](https://doc.rust-lang.org/book/)
[C++ core guidlines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)
