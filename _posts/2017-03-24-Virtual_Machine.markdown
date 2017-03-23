---
layout: post
title: Stack based vs Register based Virtual Machine
description: "Stack Register VM"
tags: [Virtual Machine VM]
---

----------------------

A virtual machine (VM) is a high level abstraction on top of the native operating system, that emulates a physical machine. Here, we are talking about process virtual machines and not system virtual machines. A virtual machine enables the same platform to run on multiple operating systems and hardware architectures. The Interpreters for Java and Python can be taken as examples, where the code is compiled into their VM specific bytecode. The same can be seen in the Microsoft .Net architecture, where code is compiled into intermediate language for the CLR (Common Language Runtime).

虚拟机是建立在本地OS上的一个高度抽象，它模拟里物理机。我们在这里就讨论一下进程虚拟机，而不是系统级别的虚拟机。虚拟机能让相同代码在不同的操作系统和硬件架构上运行。Java 和Python的解释器可作为例子，代码被编译为虚拟机客制化的字节码。同样的微软.Net架构被编译为了CLR。

What should a virtual machine generally implement? It should emulate the operations carried out by a physical CPU and thus should ideally encompass the following concepts:

虚拟机通常要实现什么呢？它通常模拟用物理CPU完成的运算和包含下面几个概念：

- Compilation of source language into VM specific bytecode

- Data structures to contains instructions and operands (the data the instructions process)

- A call stack for function call operations

- An ‘Instruction Pointer’ (IP) pointing to the next instruction to execute

- A virtual ‘CPU’ – the instruction dispatcher that

  - Fetches the next instruction (addressed by the instruction pointer)
  - Decodes the operands
  - Executes the instruction

- 编译源代码到虚拟机专用的字节码

- 数据结构包含指令和操作数（指令用的数据）

- 通过调用堆栈来完成函数调用操作指令

- 有一个指令指针指向下一个执行的指令

- 一个虚拟的CPU-指令被丢到这里来

  获取下一个指令（根据指令指针来获取地址）

  解码操作数

  执行指令

There are basically two main ways to implement a virtual machine: Stack based, and Register based. Examples of stack based VM’s are the Java Virtual Machine, the .Net CLR, and is the widely used method for implementing virtual machines. Examples of register based virtual machines are the Lua VM, and the Dalvik VM (which we will discuss shortly). The difference between the two approaches is in the mechanism used for storing and retrieving operands and their results.

通常来说，有两种方法来实现虚拟机：基于堆栈，基于寄存器。

基于堆栈的虚拟机的例子有：java虚拟机，.Net CLR，基于堆栈这是一个广泛使用的方法。基于寄存器的虚拟机有Lua VM和Dalvik虚拟机。这两种方法的区别是在存储、提取操作数和结果的机制不同。



