---
title: 虚幻引擎中的C++指针
date: 2024-03-15 14:30:33
description: 智能指针与常规指针最重要的区别是它能够自动释放所指向的对象。可以更加安全的使用动态内存，防止内存泄漏或者提前释放的情况。
tags: [ UnrealEngine ]
---

# 智能指针

## 基本概念

智能指针与常规指针最重要的区别是它能够自动释放所指向的对象。可以更加安全的使用动态内存，防止内存泄漏或者提前释放的情况。

智能指针的原理是通过引用计数来管理生命周期，当智能指针被对象使用的时候，引用计数 +1，使用完(作用域结束)后引用计数-1，当引用计数为 0 时，智能指针将被释放。

## 与原生的智能指针的区别

UE4 的智能指针是 C++ 智能指针的自定义实现，基本概念和使用方法都差不多，但是 UE4 做了很多拓展功能以更好的使用，以及更方便管理 UE4 项目。

- C++ 的原生智能指针并不是所有平台都支持，但是 UE4 为我们提供了支持跨平台使用。
- UE4 的智能指针更兼容 UE4 的数据类型和容器。
- UE4 的智能指针可选择线程安全，可跨线程管理引用计数，或者不选择，换取更好性能。
- UE4 智能指针还做了很多其他的优化，性能更好，更容易调试。

## 使用智能指针的注意事项

- UE4 对 UObject 对象提供了垃圾回收，智能指针不能直接指向 UObject 对象。
- UE4 对 C++ 原生对象不提供垃圾回收，需要你自己手动 free/delete，因此项目中如非必要最好不要将 UE4 智能指针和 C++ 原生智能指针混用，以免造成不必要的麻烦。
- 尽量用 UE4 的智能指针来管理非 UObject 类。

## TSharedPtr：共享指针

可以置为空(nullptr)，使用前需要判断有效性。

- 初始化&创建

```cpp
//默认ESPMode为Fast=0,即非线程安全

//快速创建
TSharedPtr<TestA> aPtr(new TestA());   

//MakeShared创建
TSharedPtr<TestA> bPtr = MakeShared<TestA>();  

//MakeShareable创建,可启用隐式转换   
TSharedPtr<TestA> cPtr = MakeShareable(new TestA()); 

//选择线程安全的共享指针       
TSharedPtr<TestA,ESPMode::ThreadSafe> dPtr = MakeShareable(new TestA());
```

- 查看引用计数与指针复制/转移

```cpp
//查看引用计数
aPtr.GetSharedReferenceCount();

//复制
TSharedPtr<TestA> aPtr_copy = aPtr; 

//转移指针
TSharedPtr<TestA> aPtr_moveTemp = MoveTemp(aPtr);

//转移指针
TSharedPtr<TestA> aPtr_moveTempIf = MoveTempIfPossible(aPtr);
```

- 解引用

```cpp
if (aPtr)
{
    //解引用
    aPtr->DoA(); 
}
if (aPtr.Get()!= nullptr)
{        
    //用原生指针解引用
    aPtr.Get()->DoA();
}
if (aPtr.IsValid())
{
    //用原生指针解引用
    (*aPtr).DoA(); //解引用
}
```

- 手动重置

```cpp
//重置
aPtr.Reset();

//直接赋值空指针重置
aPtr=nullptr;
```

## TSharedRef：共享引用

共享引用不可为空，不可用于 UObject 对象，没有 IsValid() 函数。

- 创建

```cpp
// 创建方式与共享指针类似
TSharedRef<TestA> aRef(new TestA());
TSharedRef<TestA> bRef = MakeShareable(new TestA());
TSharedRef<TestA> cRef = MakeShared<TestA>();
```

- TSharedPtr 与 TSharedRef 相互转换

```cpp
//隐式转换，共享引用->>共享指针
TSharedPtr<TestA> aPtr = aRef;

//共享指针->>共享引用
aRef= aPtr.ToSharedRef();
```

## TWeakPtr：弱指针

不参与引用计数，对象不存在共享指针时，TWeakPtr 将自动失效。

使用时需要判断有效性。

## TUniquePtr：唯一指针

强调对内存所有权的唯一性，TUniquePtr 在构造时必须显示的调用构造函数（除非是默认构造），并且不能有赋值/拷贝操作，其拷贝/赋值重载被关键字 =delete 标记，只能通过 MoveTemp() 转移内存所有权，类似 C++ 中的 std::move()，其指向的内存仅会被唯一的一个 TWeakPtr 所指向

## 智能指针转换

# 对象指针

## TObjectPtr

- 对于需要进行访问追踪的 UPROPERTY 成员变量，可以使用 TObjectPtr<T>替换裸指针；对于函数参数、局部指针变量等，则建议使用 UObject*裸指针。
- 在进行容器 Find 时、反射访问变量时，捕获类型应与变量声明的类型保持一致，不宜混用 TObjectPtr<T>*和 T**。
- 注册的 Hook 函数由于访问频次通常会很高，应保证在大多数情况下的低开销。

## TWeakObjectPtr

## FObjectPtr

> _FObjectPtr is the basic, minimally typed version of TObjectPtr_

## FWeakObjectPtr

# 引用

[SharedPointer - Unreal smart pointer library](https://lfl6qrvo2z.feishu.cn/wiki/wikcnc6LlK0DJnvvDITlwzwt2Vb)
