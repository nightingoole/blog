---
title: 实现 MVC 架构
date: 2024-03-15 14:38:23
description: 在虚幻引擎中如何利用TTypeContainer实现一个MVC框架。
tags: [ UnrealEngine, Programing ]
---

# 前置阅读

[虚幻引擎之智能指针](https://lfl6qrvo2z.feishu.cn/wiki/wikcnyMNQQRvtogFoDhrsyup9Gd)

[MVC 架构的演变与解析](https://lfl6qrvo2z.feishu.cn/wiki/wikcnrDhHKjMpNgGGISCjR3Yete)

[TTypeContainer](https://lfl6qrvo2z.feishu.cn/wiki/wikcnxhiK7lmRZ1GTOPW1NFAz9e)

# 选择容器[TTypeContainer]

[TTypeContainer]是 UnrealEngine 内置的一个类型容器模板类，它允许我们配置对象和依赖而不需要实际手动去创建对象。它扮演了类似于类工厂的角色，但没有锁定指定的类才能使用。当传入类构造函数或方法时，类型容器可以促进 IoC 模式

# 整体架构

![](boxcnQuOmPsK0MTr78fRrUVIDpd.png)

整体设计参考经典 MVC 架构，但更加明确的各层职责，并进一步划分 Model 层。

- View：视图层

包含了组件蓝图和用户组件(C++)等用户 UI 部分

- Controller：控制层

只做为业务层与视图层的胶水代码，有路由的作用

Model 层按照传统客户端程序的三层架构，进一步划分为：

- Data Model：数据模型层

用于表示抽象出来的数据模型

- Service：服务层

用于编写与具体业务相关的服务

- Data Repository：数据仓库层

用于编写与数据操作相关的代码，包括网络数据、本地数据、IoT 数据等，还可以将数据持久化操作也放入此层

# 实现过程

## 新建项目

![](boxcn7TSfwYjchdKJTJLE5B1eVg.png)

新建完成后如图

## 创建 IOC 容器

- IOCContainer.h

```cpp
#pragma once

#include "..."

UCLASS()
class ARCHVIZEXPLORER_API UIOCContainer : public UObject
{
   GENERATED_BODY()

public:

   // 全局静态容器
   static TTypeContainer<> Container;
};
```

- IOCContainer.cpp

```cpp
#include "Containers/IOCContainer.h"

// 初始化IOC容器
TTypeContainer<> UIOCContainer::Container;
```

## 创建数据模型

- UserRepository.h

```cpp
// 简单的数据结构
struct FUser
{
   FString Username;
   FString Password;
};
```

## 创建控制层

- LoginController.h

```cpp
#pragma once

#include "..."

_/**_
_ * 控制层，与常规桌面应用程序不同的是_
 * UE中的控制层需要继承AActor以达到
 * 连接View与Service的作用
_ */_
UCLASS(BlueprintType)
class ARCHVIZEXPLORER_API ALoginController : public AActor
{
   GENERATED_BODY()

   TSharedPtr<IUserService> UserService;

public:
   ALoginController();

   UFUNCTION(BlueprintCallable)
   void PostLogin(FString Username, FString Password);
};
```

控制层，与常规桌面应用程序不同的是，UE 中的控制层需要继承 AActor 并放入场景中以达到连接 View 与 Service 的作用

- LoginController.cpp

```cpp
#include "Controllers/LoginController.h"
#include "Containers/IOCContainer.h"

ALoginController::ALoginController()
{
   _// 必须加上判断依赖是否已注册，否则会导致编辑器启动崩溃_
   if (UIOCContainer::Container.IsRegistered<IUserService>())
   {
      UserService = UIOCContainer::Container.GetInstance<IUserService>();
   }
}

void ALoginController::PostLogin(FString Username, FString Password)
{
   UserService->Login(Username, Password);
}
```

[LoginController]控制器依赖于[UserService]用户服务，于是先申明为成员变量，之后在构造函数中初始化值

## 创建服务层

- UserService.h

```cpp
#pragma once
#include "Repositories/UserRepository.h"

_/**_
_ * 创建用户服务层接口_
_ * 定义登录接口_
_ **/_
struct IUserService
{
   virtual ~IUserService() = default;
   virtual void Login(FString Username, FString Password) = 0;
};

_// 使用宏注册接口名称_
Expose_TNameOf(IUserService)

_/**_
_ * 实现[IUserService]接口_
_ * 实现[Login]接口_
_ **/_
class FUserService : public IUserService
{
   TSharedPtr<IUserRepository> UserRepository;

public:

   FUserService();

   virtual void Login(FString Username, FString Password) override;
};
```

要注册到[IOCContainer]中的类需要以接口与具体实现类的方式来进行注册。首先我们需要在头部定义接口[IUserService]，并在接口中定义[Login]的纯虚函数。而后，由[FUserService]来具体实现接口。同时也体现了 IOC 模式中的面向接口编程的思想

[UserService]依赖于[UserRepository]数据访问层，先申明变量，之后在构造函数中初始化

- UserService.cpp

```cpp
#include "Services/UserService.h"
#include "Containers/IOCContainer.h"

FUserService::FUserService()
{
   // 从IOC容器中初始化依赖
   _// 必须加上判断依赖是否已注册，否则会导致编辑器启动崩溃_
   if (UIOCContainer::Container.IsRegistered<IUserRepository>())
   {
      UserRepository = UIOCContainer::Container.GetInstance<IUserRepository>();
   }
}

_/**_
_ * 调用[UserRepository]中的数据访问接口_
_ **/_
void FUserService::Login(FString Username, FString Password)
{
   UE_LOG(LogTemp, Warning, TEXT("Received user info: %s, %s"), *Username, *Password)
   const FUser User = UserRepository->FindUserByUsername(Username);
   UE_LOG(LogTemp, Warning, TEXT("Found user info: %s, %s"), *User.Username, *User.Password)
}
```

## 创建数据访问层

- UserRepository.h

```cpp
#pragma once

struct FUser
{
   FString Username;
   FString Password;
};

struct IUserRepository
{
   virtual ~IUserRepository() = default;
   virtual FUser FindUserByUsername(FString Username) = 0;
};

Expose_TNameOf(IUserRepository)

class FUserRepository : public IUserRepository
{
   virtual FUser FindUserByUsername(FString Username) override;
};
```

- UserRepository.cpp

```cpp
#include "Repositories/UserRepository.h"

FUser FUserRepository::FindUserByUsername(FString Username)
{

   // Access data from web service or database with params
   // ... //

   // Return data
   return FUser{Username, "199798"};
}
```

# 源码浅析

## [IOCContainer]本质

```cpp
// other code... 

private:

   _/** Critical section for synchronizing access. */_
_   _mutable FCriticalSection CriticalSection;

   _/** Maps class names to instance providers. */_
_   _TMap<FString, TSharedPtr<IInstanceProvider>> Providers;

 // other code...
```

## 注册过程

```cpp
_/**_
_ * Registers a class for instances of the specified class._
_ *_
_ * @param R The type of class to register an instance class for._
_ * @param T Tye type of class to register (must be the same as or derived from R)._
_ * @param P The constructor parameters that the class requires._
_ * @param Scope The scope at which instances must be unique (default = Process)._
_ * @see RegisterDelegate, RegisterInstance, Unregister_
_ */_
template<class R, class T, typename... P>
void RegisterClass(ETypeContainerScope Scope = ETypeContainerScope::Process)
{
   static_assert(TPointerIsConvertibleFromTo<T, R>::Value, "T must implement R");

   TSharedPtr<IInstanceProvider> Provider;

   switch(Scope)
   {
   case ETypeContainerScope::Instance:
      Provider = MakeShareable(
         new TFunctionInstanceProvider<T>(
            [this]() -> TSharedPtr<void, Mode> {
               return MakeShareable(new T(GetInstance<P>()...));
            }
         )
      );
      break;

   case ETypeContainerScope::Thread:
      Provider = MakeShareable(
         new TThreadInstanceProvider<T>(
            [this]() -> TSharedPtr<void, Mode> {
               return MakeShareable(new T(GetInstance<P>()...));
            }
         )
      );
      break;

   default:
      Provider = MakeShareable(
         new TSharedInstanceProvider<T>(
            MakeShareable(new T(GetInstance<P>()...))
         )
      );
      break;
   }

   AddProvider(TNameOf<R>::GetName(), Provider);
}

_/**_
_ * Add an instance provider to the container._
_ *_
_ * @param Name The name of the type to add the provider for._
_ * @param Provider The instance provider._
_ */_
void AddProvider(const TCHAR* Name, const TSharedPtr<IInstanceProvider>& Provider)
{
   FScopeLock Lock(&CriticalSection);
   {
      Providers.Add(Name, Provider);
   }
}
```

## 初始化过程

```cpp
_/**_
_ * Gets a shared pointer to an instance of the specified class._
_ *_
_ * @param R The type of class to get an instance for._
_ * @param A shared reference to the instance._
_ * @see RegisterClass, RegisterDelegate, RegisterFactory, RegisterInstance_
_ */_
template<class R>
TSharedRef<R, Mode> GetInstance() const
{
   FScopeLock Lock(&CriticalSection);
   {
      const TSharedPtr<IInstanceProvider>& Provider = Providers.FindRef(TNameOf<R>::GetName());
      check(Provider.IsValid());

      return StaticCastSharedPtr<R>(Provider->GetInstance()).ToSharedRef();
   }
}
```

# 思考：如何在蓝图中实现 IOC？

# 参考

[UE-Testing - Github](https://github.com/jimmyt1988/UE-Testing)

[TTypeContainer - Unreal Engine](https://docs.unrealengine.com/4.27/en-US/API/Runtime/Core/Misc/TTypeContainer/)

[Where do i declare my IOC Container? - Unreal Engine Forum](https://forums.unrealengine.com/t/where-do-i-declare-my-ioc-container/405121)

