---
title: 关键字-auto
date: 2024-03-04 16:13:49
description: 给没耐心的读者：初始化 `auto`，请使用不带花括号的拷贝赋值操作符 `=`。我知道当你一眼看见 `auto` 的时候可能内心会蹦出来好几种可能的用法，但是只有一种是正确的。
tags: [c++, keyword]
---
## 怎么用对 `auto`

给没耐心的读者：初始化 `auto`，请使用不带花括号的拷贝赋值操作符 `=`。

我知道当你一眼看见 `auto` 的时候可能内心会蹦出来好几种可能的用法，但是只有一种是正确的。

- `auto x(5)` 是对的，但是如果你有，比如说一个类叫 `SomeClass`，然后想要用 `auto x(SomeClass())` 来得到一个 `SomeClass` 类型的 `x`，那么不好意思，你最后得到的其实是一个函数的声明——这个函数的返回类型是自动推演的——即 `auto` 在这里的作用。关于 `auto` 在函数中的作用我会在之后的一篇文章中总结。
- `auto x{somgthing}`，能这么写说明你对 C++11 的 `initializer_list` 有了一定了解了；但是在 `auto` 中这么写，你会得到一个错误的类型推演——它会把 `x` 的类型推演成 `initializer_list<SomeType>`，其中 `SomeType` 就是你写的 `something`。同样的，如果你写成 `auto x = {something};` 它也会把类型自动推演成 `initializer_list<SomeType>`。

所以，只有 `=` 会最保险地完成初始化 `auto` 这个任务。直接写成 `auto x = something`。

在使用 `auto` 的时候，给变量和函数起一个好名字就显得极为重要了，因为读你代码的人——很有可能是未来的你——只能通过这个变量名来推断它的类型和作用了，除非你要写很多不必要的注释或者指望读者往上翻好多上下文。比如：`auto x = someFunc();` 只告诉了我们 `someFunc` 函数的返回类型和 `x` 的类型是一样的，是不是很让人恼火？我们即使靠猜都对 `x` 的类型毫无头绪。另一方面呢，`auto points = calculateScore();` 是不是好很多？我们会觉得这是一个计算分数的函数，返回的结果也八成是和分数有关的数或类型。

## 什么时候用 `auto`

这个问题的答案到此就非常明显了：

如果那个地方的非关键中间变量完全依赖于上下文中其他变量的类型，那么就用 `auto` 吧。

## 你会如何初始化固定类型的变量？

如果什么时候我们想把一个变量的类型显式的固定下来呢？下边有两种方法做这件事：要么显式地声明这个变量的类型，要么显式地使用这个变量的构造器：

```cpp
std::size_t size{2};  _//2是int类型，但是我们想要size_t_
 auto size = std::size_t{2}; _//同上_
```

## 在函数中使用 auto

基本上，在函数中使用 `auto` 的情形大致可分为两类，在 C++11 中，`auto` 被引入放到函数声明中返回类型的位置，用间接的方式来定义函数的返回类型，如下：

```cpp
_//等价于 std::string someFunc(int i, double j);_
 auto someFunc(int i, double j) -> std::string;
```

在 C++14 中，编译器可以直接推断出函数的返回类型了，所以可以写成下边的样子：

```cpp
auto someFunc(int i, double j) {_//自动推断返回类型为std::string_
   return std::to_string(i + j);}
```

## 尾置返回类型

上边 C++11 的例子并没有给我们带来很多直观的感受——`auto` 在其中到底有什么用？既然我们还是要显示声明函数的返回类型，而且和更传统的返回类型表述相比，我们为了实现尾置还要再多写一个 `auto` 和 `->`，更重要的是，这种声明看起来真的太丑了，我们为什么要用这种表述方式呢？

在很多返回类型要取决于参数类型的时候，比如在函数模板中，上边这种写法就会有很大作用，因为你很有可能并不知道进行某种操作后自己会得到什么类型，请看下例：

```cpp
template<typename T, typename Vauto addWithTwoTypes(T t, V v) -> decltype(t + v) {return t + v;}
```

上边这个函数会返回 T 类型变量和 V 类型变量的和，如果 `T` 和 `V` 分别是 `short` 和 `int`，那么返回类型就会自动推断为 `int`，但是如果一个是 `double` 一个是 `int`，那么返回类型就会是 `double`。因此，返回值类型和两个模板类型都相关。

如果将上述例子写成如下形式，不使用 `auto`，可不可行呢？

```cpp
template<typename T, typename Vdecltype(t + v) addWithTwoTypes(T t, V v) {return t + v;}
```

答案是否定的，因为在推导 `decltype(t + v)` 时 `t` 和 `v` 还没定义，因此会有类似“模板未实例化”的报错。

再举一个例子，如果有如下定义：

```cpp
class JackRoseCreator {public:Jack giveMeJack();Rose giveMeRose();};Baby operator+(Jack const& jack, Rose const& rose);template<typename Tauto giveMeSomething(T const& t) -> decytype(t.giveMeJack() + t.giveMeRose()) {return t.giveMeJack() + t.giveMeRose();}
```

上边这个例子就是第一个例子的复杂版，在我们的 `auto` 写法中，最后 `auto` 会被编译器推导为一个 `Baby` 类型，但是其他写法可能就没这么简单了。

另外一个不常用的地方在于，使用尾置返回类型也可简化一些函数的写法，比如：

```cpp
int (*generatorArr(int i))[10086];  _//返回一个指向大小为10086的int类型的数组的指针_
```

上边函数可以用 `auto` 来简化成：

```cpp
auto generatorArr(int i) -> int (*)[10086];
```

是不是看起来更方便一些？
