---
title: template & concept
date: 2024-03-04 15:58:07
description: C++20新特性：约束和概念。
tags: [c++, template, keyword]
---
# 基本用法

```csharp
// 定义概念：使用concept关键字定义概念
template <typename T>
concept check_is_actor = std::derived_from<T, AActor>;

// 使用概念：require关键字后接自定义的概念
template <typename T>
    requires check_is_actor<T>
FORCEINLINE void GetItem(T& OutActor)
{
    //......
}
```

# Useage

```csharp
_// Fill out your copyright notice in the Description page of Project Settings._

#pragma once

#include "CoreMinimal.h"
#include "Subsystems/GameInstanceSubsystem.h"
#include "NightingoleObjectPoolSubsystem.generated.h"


template <typename T>
concept check_is_actor = std::derived_from<T, AActor>;

_/**_
_ * _
_ */_
UCLASS(Category="Nightingole|Toolkit")
class NIGHTINGOLESTOOLKIT_API UNightingoleObjectPoolSubsystem : public UGameInstanceSubsystem
{
    GENERATED_BODY()

    TMap<FString, TArray<AActor*>> ObjectPool;

public:

    template <typename T>
       requires check_is_actor<T>
    FORCEINLINE T* GetItem()
    {
       const UClass* Class = T::StaticClass();
       const FString ClassName = Class->GetClassPathName().ToString();
       if (ObjectPool.Contains(ClassName))
       {
          auto Pool = ObjectPool.FindChecked(ClassName);
          auto Actor = Pool.Pop();
          Actor->SetActorEnableCollision(true);
          Actor->SetActorTickEnabled(true);
          Actor->SetActorHiddenInGame(false);
          return static_cast<T*>(Actor);
       }
       return nullptr;
    }

    template <typename T>
       requires check_is_actor<T>
    FORCEINLINE void ReturnItem(T* InActor)
    {
       if (InActor)
       {
          const UClass* Class = T::StaticClass();
          const FString ClassName = Class->GetClassPathName().ToString();

          InActor->SetActorHiddenInGame(true);
          InActor->SetActorTickEnabled(false);
          InActor->SetActorEnableCollision(false);

          if (ObjectPool.Contains(ClassName))
          {
             auto Pool = ObjectPool.FindChecked(ClassName);
             Pool.Push(InActor);
          }
          else
          {
             TArray<AActor*> Pool;
             Pool.Push(InActor);
             ObjectPool.Add(ClassName, Pool);
          }
       }
    }
};
```

# 标准预定义

**Standard library concepts**

| Defined in namespace std                                                                                                       |                                                                                                                                                                                                                                |
| ------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Core language concepts**                                                                                                     |                                                                                                                                                                                                                                |
| Defined in header [<concepts>](https://en.cppreference.com/w/cpp/header/concepts)                                              |                                                                                                                                                                                                                                |
| [same_as](https://en.cppreference.com/w/cpp/concepts/same_as)(C++20)                                                           | specifies that a type is the same as another type(concept)                                                                                                                                                                     |
| [derived_from](https://en.cppreference.com/w/cpp/concepts/derived_from)(C++20)                                                 | specifies that a type is derived from another type(concept)                                                                                                                                                                    |
| [convertible_to](https://en.cppreference.com/w/cpp/concepts/convertible_to)(C++20)                                             | specifies that a type is implicitly convertible to another type(concept)                                                                                                                                                       |
| [common_reference_with](https://en.cppreference.com/w/cpp/concepts/common_reference_with)(C++20)                               | specifies that two types share a common reference type(concept)                                                                                                                                                                |
| [common_with](https://en.cppreference.com/w/cpp/concepts/common_with)(C++20)                                                   | specifies that two types share a common type(concept)                                                                                                                                                                          |
| [integral](https://en.cppreference.com/w/cpp/concepts/integral)(C++20)                                                         | specifies that a type is an integral type(concept)                                                                                                                                                                             |
| [signed_integral](https://en.cppreference.com/w/cpp/concepts/signed_integral)(C++20)                                           | specifies that a type is an integral type that is signed(concept)                                                                                                                                                              |
| [unsigned_integral](https://en.cppreference.com/w/cpp/concepts/unsigned_integral)(C++20)                                       | specifies that a type is an integral type that is unsigned(concept)                                                                                                                                                            |
| [floating_point](https://en.cppreference.com/w/cpp/concepts/floating_point)(C++20)                                             | specifies that a type is a floating-point type(concept)                                                                                                                                                                        |
| [assignable_from](https://en.cppreference.com/w/cpp/concepts/assignable_from)(C++20)                                           | specifies that a type is assignable from another type(concept)                                                                                                                                                                 |
| [swappableswappable_with](https://en.cppreference.com/w/cpp/concepts/swappable)(C++20)                                         | specifies that a type can be swapped or that two types can be swapped with each other(concept)                                                                                                                                 |
| [destructible](https://en.cppreference.com/w/cpp/concepts/destructible)(C++20)                                                 | specifies that an object of the type can be destroyed(concept)                                                                                                                                                                 |
| [constructible_from](https://en.cppreference.com/w/cpp/concepts/constructible_from)(C++20)                                     | specifies that a variable of the type can be constructed from or bound to a set of argument types(concept)                                                                                                                     |
| [default_initializable](https://en.cppreference.com/w/cpp/concepts/default_initializable)(C++20)                               | specifies that an object of a type can be default constructed(concept)                                                                                                                                                         |
| [move_constructible](https://en.cppreference.com/w/cpp/concepts/move_constructible)(C++20)                                     | specifies that an object of a type can be move constructed(concept)                                                                                                                                                            |
| [copy_constructible](https://en.cppreference.com/w/cpp/concepts/copy_constructible)(C++20)                                     | specifies that an object of a type can be copy constructed and move constructed(concept)                                                                                                                                       |
| **Comparison concepts**                                                                                                        |                                                                                                                                                                                                                                |
| Defined in header [<concepts>](https://en.cppreference.com/w/cpp/header/concepts)                                              |                                                                                                                                                                                                                                |
| [boolean-testable](https://en.cppreference.com/w/cpp/concepts/boolean-testable)(C++20)                                         | specifies that a type can be used in Boolean contexts(exposition-only concept*)                                                                                                                                                |
| [equality_comparableequality_comparable_with](https://en.cppreference.com/w/cpp/concepts/equality_comparable)(C++20)           | specifies that operator == is an equivalence relation(concept)                                                                                                                                                                 |
| [totally_orderedtotally_ordered_with](https://en.cppreference.com/w/cpp/concepts/totally_ordered)(C++20)                       | specifies that the comparison operators on the type yield a total order(concept)                                                                                                                                               |
| Defined in header [<compare>](https://en.cppreference.com/w/cpp/header/compare)                                                |                                                                                                                                                                                                                                |
| [three_way_comparablethree_way_comparable_with](https://en.cppreference.com/w/cpp/utility/compare/three_way_comparable)(C++20) | specifies that operator <=> produces consistent result on given types(concept)                                                                                                                                                 |
| **Object concepts**                                                                                                            |                                                                                                                                                                                                                                |
| Defined in header [<concepts>](https://en.cppreference.com/w/cpp/header/concepts)                                              |                                                                                                                                                                                                                                |
| [movable](https://en.cppreference.com/w/cpp/concepts/movable)(C++20)                                                           | specifies that an object of a type can be moved and swapped(concept)                                                                                                                                                           |
| [copyable](https://en.cppreference.com/w/cpp/concepts/copyable)(C++20)                                                         | specifies that an object of a type can be copied, moved, and swapped(concept)                                                                                                                                                  |
| [semiregular](https://en.cppreference.com/w/cpp/concepts/semiregular)(C++20)                                                   | specifies that an object of a type can be copied, moved, swapped, and default constructed(concept)                                                                                                                             |
| [regular](https://en.cppreference.com/w/cpp/concepts/regular)(C++20)                                                           | specifies that a type is regular, that is, it is both [semiregular](https://en.cppreference.com/w/cpp/concepts/semiregular) and [equality_comparable](https://en.cppreference.com/w/cpp/concepts/equality_comparable)(concept) |
| **Callable concepts**                                                                                                          |                                                                                                                                                                                                                                |
| Defined in header [<concepts>](https://en.cppreference.com/w/cpp/header/concepts)                                              |                                                                                                                                                                                                                                |
| [invocableregular_invocable](https://en.cppreference.com/w/cpp/concepts/invocable)(C++20)                                      | specifies that a callable type can be invoked with a given set of argument types(concept)                                                                                                                                      |
| [predicate](https://en.cppreference.com/w/cpp/concepts/predicate)(C++20)                                                       | specifies that a callable type is a Boolean predicate(concept)                                                                                                                                                                 |
| [relation](https://en.cppreference.com/w/cpp/concepts/relation)(C++20)                                                         | specifies that a callable type is a binary relation(concept)                                                                                                                                                                   |
| [equivalence_relation](https://en.cppreference.com/w/cpp/concepts/equivalence_relation)(C++20)                                 | specifies that a [relation](https://en.cppreference.com/w/cpp/concepts/relation) imposes an equivalence relation(concept)                                                                                                      |
| [strict_weak_order](https://en.cppreference.com/w/cpp/concepts/strict_weak_order)(C++20)                                       | specifies that a [relation](https://en.cppreference.com/w/cpp/concepts/relation) imposes a strict weak ordering(concept)                                                                                                       |

Additional concepts can be found in [the iterators library](https://en.cppreference.com/w/cpp/iterator#C.2B.2B20_iterator_concepts), [the algorithms library](https://en.cppreference.com/w/cpp/iterator#Algorithm_concepts_and_utilities), and [the ranges library](https://en.cppreference.com/w/cpp/ranges#Range_concepts).

详见：[Concepts library (since C++20) - cppreference.com](https://en.cppreference.com/w/cpp/concepts)
