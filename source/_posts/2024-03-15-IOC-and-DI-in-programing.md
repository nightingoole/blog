---
title: 控制反转与依赖注入
date: 2024-03-15 14:24:35
description: 控制反转是面向对象中一个重要的思想，它能指导我们如何设计出松耦合、更优良的程序。传统应用程序都是由我们在类内部主动创建依赖对象，从而导致类与类之间高耦合；
tags: [ Programing ]
---

# 控制反转(IOC, Inversion of Control)

控制反转是面向对象中一个重要的思想，它能指导我们如何设计出松耦合、更优良的程序。传统应用程序都是由我们在类内部主动创建依赖对象，从而导致类与类之间高耦合；有了 IoC 容器后，把创建和查找依赖对象的控制权交给了容器，由容器进行注入组合对象，所以对象与对象之间是松散耦合，这样也方便测试，利于功能复用，更重要的是使得程序的整个体系结构变得非常灵活。

- IOC 分为两部分，即：**控制**和**反转**

**控制：**代表对对象的控制权，包括对象的创建，对象的赋值，对象的生命周期等

**正转：**使用[new]关键字与构造函数手动创建和管理对象，对象从开始到销毁整个生命周期都由开发人员决定

```java
/**
* 
* 假设现在有一项登录业务"LoginService",
* 它依赖于数据层的"UserRepository"
* 
**/
public class LoginService {

    // 使用"new"关键字代表我们在管理对"UserRepository"的创建
    private UserRepository userRepository = new UserRepository();

    // 登录业务
    public void Login(String username, String password) {
        // 根据用户名在数据库中查找用户信息
        User user = userRepository.findUserByUsername(username);
        System.out.println(user.toString());
    }

}
```

**反转：**将开发人员管理对象的权利交由一个容器来管理，由容器管理对象的整个生命周期

```java
/**
* 
* 假设现在有一项登录业务"LoginService",
* 它依赖于数据层的"UserRepository"
* 
**/
public class LoginService {

    // 只需要声明依赖，使用IoC容器自行注入"userRepository"的值
    @Autowired
    private UserRepository userRepository;

    // 登录业务
    public void Login(String username, String password) {
        // 根据用户名在数据库中查找用户信息
        User user = userRepository.findUserByUsername(username);
        System.out.println(user.toString());
    }

}
```

# 依赖注入(DI, Dependency Injection)

组件之间依赖关系由容器在运行期决定，形象的说，即由容器动态的将某个依赖关系注入到组件之中。依赖注入的目的并非为软件系统带来更多功能，而是为了提升组件重用的频率，并为系统搭建一个灵活、可扩展的平台。通过依赖注入机制，我们只需要通过简单的配置，而无需任何代码就可指定目标需要的资源，完成自身的业务逻辑，而不需要关心具体的资源来自何处，由谁实现。

依赖注入是 IoC 的一种技术实现，常用的注入方式有三种：构造方法注入，Setter 注入，基于注解的注入

## 构造方法注入

```java
/**
* 
* 假设现在有一项登录业务"LoginService",
* 它依赖于数据层的"UserRepository"
* 
**/
public class LoginService {

    // 只需要声明依赖
    private final UserRepository userRepository;

    // IoC容器自动注入依赖
    public LoginService(UserReposiotry userRepository) {
        this.userRepository = userRepository;
    }

    // 登录业务
    public void Login(String username, String password) {
        // 根据用户名在数据库中查找用户信息
        User user = userRepository.findUserByUsername(username);
        System.out.println(user.toString());
    }

}
```

## Setter 注入

```java
/**
* 
* 假设现在有一项登录业务"LoginService",
* 它依赖于数据层的"UserRepository"
* 
**/
public class LoginService {

    // 只需要声明依赖
    private UserRepository userRepository;

    // IoC容器自动调用"SetUserRepository"对依赖进行注入
    public void SetUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    // 登录业务
    public void Login(String username, String password) {
        // 根据用户名在数据库中查找用户信息
        User user = userRepository.findUserByUsername(username);
        System.out.println(user.toString());
    }

}
```

## 基于注解的注入

这种方式常用于 Java 注解

```java
/**
* 
* 假设现在有一项登录业务"LoginService",
* 它依赖于数据层的"UserRepository"
* 
**/
public class LoginService {

    // 只需要声明依赖，通过Java注解特性自动注入依赖
    @Autowired
    private UserRepository userRepository;

    // 登录业务
    public void Login(String username, String password) {
        // 根据用户名在数据库中查找用户信息
        User user = userRepository.findUserByUsername(username);
        System.out.println(user.toString());
    }

}
```

