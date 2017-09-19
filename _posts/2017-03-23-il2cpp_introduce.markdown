---
layout: post
title: AN INTRODUCTION TO IL2CPP INTERNALS
description: "AN INTRODUCTION TO IL2CPP INTERNALS"
tags: [IL2CPP Unity]
---

----------------------

**What is IL2CPP?**

The technology that we refer to as IL2CPP has two distinct parts.

- An ahead-of-time ([AOT]) compiler
- A runtime library to support the virtual machine

什么是IL2CPP？

此技术主要由下面两点来决定

- Ahead-of-time([AOT]) 编译器
- 运行时环境

**The AOT compiler**

The IL2CPP AOT compiler is named il2cpp.exe. The il2cpp.exe utility is a managed executable, written entirely in C#. We compile it with both .NET and Mono compilers during our development of IL2CPP.

AOT 编译器叫il2cpp.exe，它是完全用C#编写的。

The il2cpp.exe utility accepts managed assemblies compiled with the Mono compiler that ships with Unity and generates C++ code which we pass on to a platform-specific C++ compiler.

il2cpp.exe能把托管程序集生成平台相关的C++代码。

You can think about the IL2CPP toolchain like this:

![Smaller icon](http://amgoodlife.top/images/9/il2cpp-toolchain-smaller.png)

**The runtime library**

The other part of the IL2CPP technology is a runtime library to support the virtual machine. We have implemented this library using almost entirely C++ code (it has a little bit of platform-specific assembly code, but let’s keep that between the two of us). We call the runtime library libil2cpp, and it is shipped as a static library linked into the player executable. One of the key benefits of the IL2CPP technology is this simple and portable runtime library.

IL2CPP技术的另一方面是运行时的库去支持vm。基本上是用C++代码实现了这个运行时库（还有些平台相关的汇编代码），此库叫做libil2cpp，是一个静态库，可被链接进可执行程序。此技术的一个关键优势是：简单可移植。

One key part of the runtime is the garbage collector. We’re shipping Unity 5 with [libgc](https://github.com/ivmai/bdwgc/), the Boehm-Demers-Weiser garbage collector. However, libil2cpp has been designed to allow us to use other garbage collectors.

runtime library的一个核心功能是：垃圾回收器。在Unity5里叫做libgc，一个*[Boehm-Demers-Weiser]*垃圾回收器。但libil2cpp是将垃圾回收设计为可替换部件的。

**What does IL2CPP not do?**

I’d like to point out one of the challenges that we did not take on with IL2CPP, and we could not be happier that we ignored it. We did not attempt to re-write the C# standard library with IL2CPP. When you build a Unity project which uses the IL2CPP scripting backend, all of the C# standard library code in mscorlib.dll, System.dll, etc. is the exact same code used for the Mono scripting backend.

We rely on C# standard library code that is already well-known by users and well-tested in Unity projects. So when we investigate a bug related to IL2CPP, we can be fairly confident that the bug is in either the AOT compiler or the runtime library, and nowhere else.

我们没有为IL2CPP重写C#的标准库，原因是Mono后端依然可用，大家都熟悉，而且C#标准库已经被验证是没问题的。

-------------

简单说明一下[AOT]和[JIT]。

AOT是编译生成System-dependent的可执行包，编译时期已经完成了优化。它有两个点：

- Reduced runtime overhead 减少了运行时的负担
- Performance trade-off 性能权衡，不能像JIT一样在运行时动态的优化hot code

JIT（Just-in-time）是动态编译，在运行时完成最终的编译。

- Startup delay and optimization 初次运行会有明显的延迟，due to taken time to load and compile the bytecode to machine code

Boehm-Demers-Weiser是为C／C++设计的保守垃圾回收器。它采用的是mark-sweep，标记-清除算法。

[AOT]:https://en.wikipedia.org/wiki/Ahead-of-time_compilation
[JIT]:https://en.wikipedia.org/wiki/Just-in-time_compilation
[Boehm-Demers-Weiser]:https://en.wikipedia.org/wiki/Boehm_garbage_collector

