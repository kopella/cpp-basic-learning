# 模板 (Templates)

## 概述

模板本质上是一个说明，描述如何用给定的恰当模板实参来生成某些东西。

通过使用模板生产的类与函数和普通的类与函数并没有什么区别，使用模板也不会因此多出一些额外的运行时机制。

在目前标准的 C++20 中，一共可以声明五种不同的模板，分别是类模板（class template）、函数模板（function template）、变量模板（variable template）*（c++14）*、别名模板（alias template）*（c++11）*、和概念（concept）*（c++20）*。

### 模板定义

定义模板往往是从一个特定的具体实例开始的，然后再将其转化为一个具体的模板。这样，程序员可以将大多数常规的代码与设计错误和由泛化所引起的问题分开处理。同样的，若要理解一个其他程序员所编写的模板时，一个比较好的方法也是对模板设想出一个特定的类型实参，以此理解它的行为。

> 泛型设计的一个哲学是：一个通用组件应该从一个或多个具体实例泛化而得，而不是简单地从第一原理直接设计。

### 模板实例化

从一个模板和一个模板实参列表生成一个类或一个函数地过程通常被称为模板实例化（template instantiation）。一个模板针对某个特定模板实参列表地版本被称为特例化（specialization）。

## 类模板（class template）

```cpp
template<typename C>
class String {
public:
    String();
    explicit String(const C∗);
    String(const String&);
    String operator=(const String&);
    // ...
    C& operator[](int n) { return ptr[n]; } // unchecked element access
    String& operator+=(C c); // add c at end
    // ...
private:
    static const int short_max = 15; // for the short str ing optimization
    int sz;
    C∗ ptr; // ptr points to sz Cs
};
```

这是来自《The C++ Programming Language》的一个字符串模板实例。前缀 `template<typename C>` 指出将要声明一个模板，而在声明中将要用到类型参数 `C`。

前缀 `template<class C>` 的含义是等价的，不过需要注意的是，即使使用这样的类型，`C` 也依然只是一个类型名，而不是一个类名。
