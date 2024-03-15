---
title: ECS 面向数据编程
date: 2024-03-15 14:45:32
description: ECS 由 Entities（实体），Components（组件），Systems（系统）三部分组成的一个面向数据编程（DOP）架构， 也是游戏工业界比较新的架构。要理解 ECS 架构，可以分别从这三部分的概念了解。
tags: [ Programing ]
---

# ECS 架构

ECS 由 Entities（实体），Components（组件），Systems（系统）三部分组成的一个面向数据编程（DOP）架构， 也是游戏工业界比较新的架构。要理解 ECS 架构，可以分别从这三部分的概念了解。

> ECS 架构的普及可以归功于 GDC 2017 上的演讲 "Overwatch Gameplay Architecture and Netcode" ，这个演讲便是讲述了《守望先锋》所使用的 ECS 架构； Unity 也有插件所支持的 Entitas 框架，再后来 Unity 2018 也推出了 ECS 框架的 preview 版本（至今仍然 preview ）；至于 Epic 则在 UE5 中推出了 MASS 框架，其实也是属于一种 ECS 架构

- **Entity（实体）：**代表了一个装有若干个 Component 的容器，可以添加或删除 Component；Entity 本身没有属性和逻辑，而是通过的 Components 的组合来体现 Entity 的属性组成
- **Component（组件**）**：**代表了一份特定格式的数据；例如：Translation，它可以包含一个 vec4 变量来表示位移数据
- **System（系统）：**代表一个每帧都会执行的行为逻辑，其接口一般是输入若干个 Component，输出（或者更准确说是修改）若干个 Component；例如：TransformUpdate，它可以输入 Rotation、Scale、Translation，然后修改 LocalToWorld

![](boxcngjhsLX5Um7aPIFKMrkQkUf.png)

ECS 架构的优势在于：

- 组件模式设计，解耦合，易扩展
- 数据布局对 CPU Cache 友好，减少 memory-bound
- 可以搭配 Job System 来充分利用多核，从而获得更好的并行加速，减少 CPU-bound

缺点在于：

- 编写 ECS 代码实际上就是在某种规范约束下写逻辑（DOP 是比较反直觉的，需要优先考虑数据布局），很难像面向对象编程（OOP）那样随心所欲地抽象出各种逻辑（例如父子关系就难以在 ECS 框架中实现 ），在某种意义上是牺牲了代码开发效率
- 在某些方面（例如游戏 UI），ECS 框架的优化效果并不明显；只有在比较大规模相同物体的情况下才能获得明显的优化效果

# 架构细节

## Entity

- Entity 的具体实现往往只包含一个身份 ID（索引），通过向数据结构（一般是动态数组）进行索引查询可以知道这个 Entity 对应的那些 Components

## Component

- Component 的属性应当是强联系的，相关性不强的属性应该切分成多种 Components，这主要是为了 Cache 友好。例如一些 System 行为中，只对 Component 的部分数据感兴趣，但由于空间局部性，另一部分不感兴趣的数据也加载进 Cache（避免无效数据夹杂在连续内存），如果切分 Component 的粒度设计的好，那么就可以只加载感兴趣的 Component 进 Cache
- 删除 Entity 时，需要删除其对应的 Components。为此不推荐使用 lazy 标记的删除方法，而是将 buffer 中最后一个 Entity 的 Components 覆盖到被删除的位置，并将 buffer 中的 Components 数量减去 1

## System

- 如果确定各个 System 行为的依赖关系（无依赖关系则是最理想的情况）并分好顺序，那么就可以使用多线程来并行跑 Systems（Job System 就是为此而用的），减少 CPU-bound
- 还可以引入 LOD 机制，通过计算与玩家的距离来决定 Systems 的 update 频率

# Unity ECS 的实现

## Archetype

原型，一种 Components 的组合方式，可以把 Archetype 理解成一个类，而 Entity 则是类的实例化对象。通过 Archetype 可以生成并管理若干个含有相同 Components 组合的 Entities

为什么需要 Archetype ？

- 使用 Archetype 可以将相同种类的 Entities 聚拢，设计 System 的行为时就可以跑在指定种类的 Entities 上，也就是跑在一个或多个 Archetype 上

![](boxcnSikLbhzgsa4Iuh4w7yk5Vd.png)

## ArchetypeChunk

是存放 Entity 对应 Components 的 buffer，而每个 Archetype 各自管理着自己的 Chunks；每个 Chunk 只能容纳一定数量的 Components ，若超出数量，Archetype 会创建新的 Chunk 来容纳多出的 Entity Components

为什么需要 ArchetypeChunk ？

- 假如 Archetype 直接管理着一个无限大的 Chunk ，然后把所有自己实例化出来的 Entity Components 通通塞进去，那么在多线程环境下的删除等操作很容易造成冲突
- 分成若干个 Chunk（但不宜让 Chunk 容量太小，不然会丧失 ECS 的优化效果），并让每个 Chunk 最多受到一个线程的处理。那么对某个 Entity 的删除操作则只会影响当前 Chunk 的线程，不会干扰到其它线程

![](boxcn7WITnYeAIGjTfKuGOsox2g.png)

# 参考

[游戏架构设计--面向数据编程(DOP) - KillerAery - 博客园](https://www.cnblogs.com/KillerAery/p/11746639.html)
