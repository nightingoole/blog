---
title: TTypeContainer
date: 2024-03-15 14:41:37
description: Type containers allow for configuring objects and their dependencies without actually creating these objects. They fulfill a role similar to class factories, but without being locked to a particular type of class. When passed into class constructors or methods, type containers can facilitate the Inversion of Control (IoC) pattern.

tags: [ UnrealEngine ]
---

# _Template for type containers._

_Type containers allow for configuring objects and their dependencies without actually_
_creating these objects. They fulfill a role similar to class factories, but without_
_being locked to a particular type of class. When passed into class constructors or_
_methods, type containers can facilitate the Inversion of Control (IoC) pattern._
_ _
_Since UE neither uses run-time type information nor pre-processes plain old C++ classes,_
_type names need to be exposed using the Expose_TNameOf macro in order to make them_
_registrable with type containers, i.e. Expose_TNameOf(FMyClass)._
_ _
_Once a type is registered with a container, instances of that type can be retrieved from it._
_There are currently three life time scopes available for instance creation:_
_ _
_  1. Unique instance per process (aka. singleton),_
_     using RegisterClass(ETypeContainerScope::Process) or RegisterInstance()_
_  2. Unique instance per thread (aka. thread singleton),_
_     using RegisterClass(ETypeContainerScope::Thread)_
_  3. Unique instance per call (aka. class factory),_
_     using RegisterClass(ETypeContainerScope::Instance) or RegisterFactory()_
_ _
_See the file TypeContainerTest.cpp for detailed examples on how to use each of these_
_type registration methods._
_ _
_In the interest of fast performance, the object pointers returned by type containers are_
_not thread-safe by default. If you intend to use the same type container and share its_
_objects from different threads, use TTypeContainer[ESPMode::ThreadSafe](ESPMode::ThreadSafe) instead, which_
_will then manage and return thread-safe versions of all object pointers._
_ _
_Note: Type containers depend on variadic templates and are therefore not available_
_on XboxOne at this time, which means they should only be used for desktop applications._
_ _

# _类型容器的模板._

_ 类型容器允许配置对象及其依赖项，而无需实际操作_
_ 创建这些对象。它们扮演着类似于一流工厂的角色，但没有_
_ 被锁定到特定类型的类。当传递到类构造函数或_
_ 方法、类型容器可以促进控制反转（IoC）模式_

_ 由于 UE 既不使用运行时类型信息，也不预处理普通的旧 C++ 类，_
_ 类型名称需要使用 Expose_TNameOf 宏来公开，以使它们_
_ 可使用类型容器注册，即 Expose_TNameOf(FMyClass)_

_ 一旦向容器注册了类型，就可以从中检索该类型的实例_
_ 当前有三个生命期范围可用于实例创建：_

_1.每个进程的唯一实例（aka.singleton），_
_使用 RegisterClass（etypContainerScope::Process）或 RegisterInstance()_
_2.每个线程的唯一实例（也称为线程单例），_
_使用 RegisterClass（etypContainerScope::Thread）_
_3.每次调用的唯一实例（也称为类工厂），_
_使用 RegisterClass（etypContainerScope::Instance）或 RegisterFactory()_

_ 请参阅文件 TypeContainerTest.cpp，以获取有关如何使用其中每一项的详细示例_
_ 类型注册方法。_

_ 为了提高性能，类型容器返回的对象指针是_
_ 默认情况下不是线程安全的。如果您打算使用同一类型的容器并共享其_
_ 对于来自不同线程的对象，请改用 TTypeContainer[ESPMode::ThreadSafe](ESPMode::ThreadSafe)，这将_
_ 然后将管理并返回所有对象指针的线程安全版本_

_ 注：类型容器依赖于可变模板，因此不可用_
_ 这意味着它们只能用于桌面应用程序_

