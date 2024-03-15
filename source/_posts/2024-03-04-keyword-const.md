---
title: 关键字-const
date: 2024-03-04 16:15:58
description: 关于 `const` 正确性，有时写出 `const` 正确的代码可不仅仅通过编译器的考验就够了。在 C++ 中，`const` 一般包含两个部分：语法部分和语义部分，我们分别来看一下。
tags: [c++, keyword]
---
关于 `const` 正确性，有时写出 `const` 正确的代码可不仅仅通过编译器的考验就够了。在 C++ 中，`const` 一般包含两个部分：语法部分和语义部分，我们分别来看一下。

# 语法 `const`

## const 常量

`const` 语法部分指的是编译器需要检查的部分，即我们从语法层面有没有违反了 `const` 规则。如果我们声明了一个变量是简单的 `const` 类型，比如一个 `const` 类型的 `int`，那么编译器就不会让我们更改他。

```cpp
int const shouldNotAlter = 42;shouldNotAlter = 38;  _//Error_
```

如果编译器是 GCC，那么错误信息就会告诉我们正在试图给一个"read-only variable"赋值，相对应的 Clang 会弹出一个"variable with const-qualified type"。当然，如果我们试图改变一个 `const` 的 class 或 struct 的成员变量，我们会得到同样的报错信息。

```cpp
struct Data{int i;double d;};Data const data{1, 2.0};data.i = 2; _//Error_
```

## const 函数

那么对于函数而言是怎样呢？

如果在一个类里有一个成员函数，编译器此时会默认我们假定这个方法有可能会改变这个对象的成员，所以对于一个 `const` 对象而言，我们无法调用那些非 `const` 方法（我在[上一篇文章](https://zhuanlan.zhihu.com/p/161560391)中从 `this` 指针和顶/底层 `const` 的角度详细分析了原因）。我们必须手动声明这些方法是 `const` 类型的，才能够被 `const` 成员所调用。

```cpp
class MyClass {public:void couldModify();void dontModify() const;};MyClass const myClassP{};myClass.dontModify(); _//OK_
 myClass.couldModify();  _//Error_
```

报错信息可能会不太一样，但总体意思还是 `this` 指针具有的是 `const SomeClass` 类型，但是函数并没有被标记为 `const`。
