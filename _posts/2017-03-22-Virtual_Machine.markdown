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



## Stack Based Virtual Machines

A stack based virtual machine implements the general features described as needed by a virtual machine in the points above, but the memory structure where the operands are stored is a stack data structure. Operations are carried out by popping data from the stack, processing them and pushing in back the results in LIFO (Last in First Out) fashion. In a stack based virtual machine, the operation of adding two numbers would usually be carried out in the following manner (where 20, 7, and ‘result’ are the operands):

一个基于堆栈的虚拟机实现了上面说的那些通用要点，但是操作数的内存结构式以堆栈方式存储的。计算操作是通过pop获取数据，处理完后再把结锅push进堆栈，即LIFO。在堆栈虚拟机里，加法操作经常是按下面方式做的：

![Smaller icon](http://awalife.top/images/10/stackadd_thumb.png)

1. **POP 20**
2. **POP 7**
3. **ADD 20, 7, result**
4. **PUSH result**

Because of the PUSH and POP operations, four lines of instructions is needed to carry out an addition operation. An advantage of the stack based model is that the operands are addressed implicitly by the stack pointer (SP in above image). This means that the Virtual machine does not need to know the operand addresses explicitly, as calling the stack pointer will give (Pop) the next operand. In stack based VM’s, all the arithmetic and logic operations are carried out via Pushing and Popping the operands and results in the stack.

因为Push和Pop操作，需要4行指令才能完成加法操作。堆栈虚拟机的优势是操作数地址是根据堆栈指针推算得到。这意味着虚拟机不需要明确的指导操作数地址，因为堆栈指针可获得下一个操作数。基于堆栈的虚拟机，所有的算术和逻辑操作都可以通过Push和Pop运算和存储结果。

## Register Based Virtual Machines

In the register based implementation of a virtual machine, the data structure where the operands are stored is based on the registers of the CPU. There is no PUSH or POP operations here, but the instructions need to contain the addresses (the registers) of the operands. That is, the operands for the instructions are explicitly addressed in the instruction, unlike the stack based model where we had a stack pointer to point to the operand. For example, if an addition operation is to be carried out in a register based virtual machine, the instruction would more or less be as follows:

在基于寄存器实现的虚拟机，存储操作数的数据结构是基于CPU的寄存器结构的。没有Push和Pop的操作，但是指令集却都必须包含操作数的地址。这就是说，指令包含里操作数明确地址，不像基于堆栈虚拟机需要一个堆栈指针。举例来说，如果一个加法操作使用寄存器虚拟机，那么指令结构就会想下面这样：

![Smaller icon](http://awalife.top/images/10/registeradd_thumb.png)

1. **ADD R1, R2, R3** **;**        # Add contents of R1 and R2, store result in R3

As I mentioned earlier, there is no POP or PUSH operations, so the instruction for adding is just one line. But unlike the stack, we need to explicitly mention the addresses of the operands as R1, R2, and R3. The advantage here is that the overhead of pushing to and popping from a stack is non-existent, and instructions in a register based VM execute faster within the instruction dispatch loop.

正如我上面提到的那样，没有Pop和push的操作，所以指令只有一行。但是不像堆栈虚拟机，我们需要明确的指出R1，R2和R3的地址。优势是没有push和pop的负担，所以寄存器虚拟机要执行的更快。

Another advantage of the register based model is that it allows for some optimizations that cannot be done in the stack based approach. One such instance is when there are common sub expressions in the code, the register model can calculate it once and store the result in a register for future use when the sub expression comes up again, which reduces the cost of recalculating the expression.

另一个优势是能做一些不能在堆栈虚拟机上的优化。一个例子是减法操作，寄存器虚拟机能算一次然后为下一次计算提供寄存器缓存，减少了重复计算。

The problem with a register based model is that the average register instruction is larger than an average stack instruction, as we need to specify the operand addresses explicitly. Whereas the instructions for a stack machine is short due to the stack pointer, the respective register machine instructions need to contain operand locations, and results in larger register code compared to stack code.

但是问题是寄存器虚拟机的指令集平均来说要比堆栈虚拟机大的多，因为我们需要明确的给出具体的操作数地址。而堆栈寄存器的指令短是因为有堆栈指针。