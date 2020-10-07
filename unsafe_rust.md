# 不安全性

Rust其实有safe和unsafe两个版本，后者的检查更宽松。unsafe代码需要特别声明。不过即使是在没有任何unsafe代码的情况下，Rust也不是完全安全的。

## 非unsafe代码的不安全性

- 整数溢出
- utf字符串迭代错位
- 数组访问溢出

## unsafe Rust
