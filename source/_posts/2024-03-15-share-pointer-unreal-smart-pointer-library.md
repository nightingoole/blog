---
title: SharedPointer - Unreal smart pointer library
date: 2024-03-15 14:34:03
description: This is a smart pointer library consisting of shared references (TSharedRef), shared pointers (TSharedPtr), weak pointers (TWeakPtr) as well as related helper functions and classes.  This implementation is modeled after the C++0x standard library's shared_ptr as well as Boost smart pointers.
tags: [ UnrealEngine ]
---

_  SharedPointer - Unreal smart pointer library
_ _
_ _ This is a smart pointer library consisting of shared references (TSharedRef), shared pointers (TSharedPtr),
_ _ weak pointers (TWeakPtr) as well as related helper functions and classes.  This implementation is modeled
_ _ after the C++0x standard library's shared_ptr as well as Boost smart pointers.
_ _
_ _ Benefits of using shared references and pointers:
_ _
_ _    Clean syntax.  You can copy, dereference and compare shared pointers just like regular C++ pointers.
_ _    Prevents memory leaks.  Resources are destroyed automatically when there are no more shared references.
_ _    Weak referencing.  Weak pointers allow you to safely check when an object has been destroyed.
_ _    Thread safety.  Includes "thread safe" version that can be safely accessed from multiple threads.
_ _    Ubiquitous.  You can create shared pointers to virtually any type of object.
_ _    Runtime safety.  Shared references are never null and can always be dereferenced.
_ _    No reference cycles.  Use weak pointers to break reference cycles.
_ _    Confers intent.  You can easily tell an object owner from an observer.
_ _    Performance.  Shared pointers have minimal overhead.  All operations are constant-time.
_ _    Robust features.  Supports 'const', forward declarations to incomplete types, type-casting, etc.
_ _    Memory.  Only twice the size of a C++ pointer in 64-bit (plus a shared 16-byte reference controller.)
_ _
_ _
_ _ This library contains the following smart pointers:
_ _
_ _    TSharedRef - Non-nullable, reference counted non-intrusive authoritative smart pointer
_ _    TSharedPtr - Reference counted non-intrusive authoritative smart pointer
_ _    TWeakPtr - Reference counted non-intrusive weak pointer reference
_ _
_ _
_ _ Additionally, the following helper classes and functions are defined:
_ _
_ _    MakeShareable() - Used to initialize shared pointers from C++ pointers (enables implicit conversion)
_ _    TSharedFromThis - You can derive your own class from this to acquire a TSharedRef from "this"
_ _    StaticCastSharedRef() - Static cast utility function, typically used to downcast to a derived type.
_ _    ConstCastSharedRef() - Converts a 'const' reference to 'mutable' smart reference
_ _    StaticCastSharedPtr() - Dynamic cast utility function, typically used to downcast to a derived type.
_ _    ConstCastSharedPtr() - Converts a 'const' smart pointer to 'mutable' smart pointer
_ _
_ _
_ _ Examples:
_ _    - Please see 'SharedPointerTesting.inl' for various examples of shared pointers and references!
_ _
_ _
_ _ Tips:
_ _    - Use TSharedRef instead of TSharedPtr whenever possible -- it can never be nullptr!
_ _    - You can call TSharedPtr::Reset() to release a reference to your object (and potentially deallocate)
_ _    - Use the MakeShareable() helper function to implicitly convert to TSharedRefs or TSharedPtrs
_ _    - You can never reset a TSharedRef or assign it to nullptr, but you can assign it a new object
_ _    - Shared pointers assume ownership of objects -- no need to call delete yourself!
_ _    - Usually you should "operator new" when passing a C++ pointer to a new shared pointer
_ _    - Use TSharedRef or TSharedPtr when passing smart pointers as function parameters, not TWeakPtr
_ _    - The "thread-safe" versions of smart pointers are a bit slower -- only use them when needed
_ _    - You can forward declare shared pointers to incomplete types, just how you'd expect to!
_ _    - Shared pointers of compatible types will be converted implicitly (e.g. upcasting)
_ _    - You can create a typedef to TSharedRef< MyClass > to make it easier to type
_ _    - For best performance, minimize calls to TWeakPtr::Pin (or conversions to TSharedRef/TSharedPtr)
_ _    - Your class can return itself as a shared reference if you derive from TSharedFromThis
_ _    - To downcast a pointer to a derived object class, to the StaticCastSharedPtr function
_ _    - 'const' objects are fully supported with shared pointers!
_ _    - You can make a 'const' shared pointer mutable using the ConstCastSharedPtr function
_ _
_ _
_ _ Limitations:
_ _
_ _    - Shared pointers are not compatible with Unreal objects (UObject classes)!
_ _    - Currently only types with that have regular destructors (no custom deleters)
_ _    - Dynamically-allocated arrays are not supported yet (e.g. MakeSharable( new int32[20] ))
_ _    - Implicit conversion of TSharedPtr/TSharedRef to bool is not supported yet
_ _
_ _
_ _ Differences from other implementations (e.g. boost:shared_ptr, std::shared_ptr):
_ _
_ _    - Type names and method names are more consistent with Unreal's codebase
_ _    - You must use Pin() to convert weak pointers to shared pointers (no explicit constructor)
_ _    - Thread-safety features are optional instead of forced
_ _    - TSharedFromThis returns a shared reference, not a shared pointer
_ _    - Some features were omitted (e.g. use_count(), unique(), etc.)
_ _    - No exceptions are allowed (all related features have been omitted)
_ _    - Custom allocators and custom delete functions are not supported yet
_ _    - Our implementation supports non-nullable smart pointers (TSharedRef)
_ _    - Several other new features added, such as MakeShareable and nullptr assignment
_ _
_ _
_ _ Why did we write our own Unreal shared pointer instead of using available alternatives?
_ _
_ _    - std::shared_ptr (and even tr1::shared_ptr) is not yet available on all platforms
_ _    - Allows for a more consistent implementation on all compilers and platforms
_ _    - Can work seamlessly with other Unreal containers and types
_ _    - Better control over platform specifics, including threading and optimizations
_ _    - We want thread-safety features to be optional (for performance)
_ _    - We've added our own improvements (MakeShareable, assign to nullptr, etc.)
_ _    - Exceptions were not needed nor desired in our implementation
_ _    - We wanted more control over performance (inlining, memory, use of virtuals, etc.)
_ _    - Potentially easier to debug (liberal code comments, etc.)
_ _    - Prefer not to introduce new third party dependencies when not needed_
