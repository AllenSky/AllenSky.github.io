IL2CPP Optimizations:Avoid Boxing

### **Heap allocations are slow**(申请堆内存很慢)

Like many programming languages, C# allows the memory for objects to be allocated on the stack (a small, “fast”, scope-specific, block of memory) or the heap (a large, “slow”, global block of memory). Usually allocating space for an object on the heap is much more expensive than allocating space on the stack. It also involves tracking that allocated memory in the garbage collector, which has an additional cost. So we try to avoid heap allocations where possible.

就像很多编程语言一样，C#允许在栈（小而快，特定范围的一段内存）或者堆（大而慢，全局的内存）。通常来说，在堆上申请内存要比在栈上代价要昂贵的多。在堆上申请还会引发垃圾回收器对内存申请的追踪，这又是额外的开销。我们应该尽可能的避免在堆上申请内粗。

C# lets us do this by separating types into *value types* (which can be allocated on the stack), and *reference types*(which must be allocated on the heap). Types like int and float are value types, string and object are reference types. User-defined value types use the struct keyword. User-defined reference types use the class keyword. Note that a value type can never hold a the value null. In C#, the null value can only be assigned to reference types. Keep this distinction in mind as we continue.

C#允许我们使用值类型（在栈上申请）和引用类型（在堆上申请）。int和float类型是值类型，string和对象是引用类型。用户定义的值类型使用struct关键字，用户定义的引用类型用class关键字。注意值类型不可能是null。在C#中，null只能设置给引用类型。在我们继续之前请记住这个差别。

Being good performance citizens, we try to avoid heap allocations unless they are necessary. But sometimes we need to convert a value type on the stack into a reference type on the heap. This process is called *boxing*. 

我们尽量避免在堆上申请内存。但是有时候我们也需要把栈上的值类型转换为堆上的引用类型。这个过程被叫做“boxing”。

Boxing:

1. Allocates space on the heap 在堆上申请内存
2. Informs the garbage collector about the new object 通知垃圾回收器有新对象
3. Copies the data from the value type object into the new reference type object 从值类型拷贝数据到引用类型。

Ugh, let’s add boxing to our list of things to avoid!

### **That pesky compiler**

Suppose we are happily writing code, avoiding unnecessary heap allocations and boxing. Maybe we have some trees for our world, and each has a size which scales with its age:

我们正开心的写代码呢，避免不必要的堆内存申请和装箱。

![Smaller icon](http://awalife.top/images/12/hassize.png)

Elsewhere in our code, we have this convenient method to sum up the size of many things (including possibly Treeobjects):

![Smaller icon](http://awalife.top/images/12/totalsize.png)

This looks safe enough, but let’s peer into a little bit of the Intermediate Language (IL) code that the C# compiler generates:

然后我们看一下C#编译器生成的IL代码。

![Smaller icon](http://awalife.top/images/12/il.png)

The C# compiler has implemented the if (things[i] != null) check using boxing! If the type T is already a reference type, then the box opcode is pretty cheap – it just returns the existing pointer to the array element. But if type T is a value type (like our Tree type), then that box opcode is *very* costly. Of course, value types can never be null, so why do we need to implement the check in the first place? And what if we need to compute the size of one hundred Tree objects, or maybe one thousand Tree objects? That unnecessary boxing will quickly become *very*important.

C#编译器实现了 if (things[i] != null) 时的检查会去装箱。如果类型T是一个引用类型，那么装箱操作是很廉价的，它只是返回已有的对象指针。但如果类型T是一个值类型（就像我们的Tree类型）那么装箱的操作则非常耗费。当然，值类型不可能是null，所以为什么我们需要去实现检查这个操作呢？如果我们要及时100个Tree对象，或者1000个呢？这个不必要的装箱操作就会非常值得考虑来。

### **The fastest code is anything you don’t execute**

The C# compiler needs to provide a general implementation that works for any possible type T, so it is stuck with this slower code. But a compiler like IL2CPP can be a bit more aggressive when it generates code that will be executed and when it doesn’t generate the code that won’t!

IL2CPP will create an implementation of The TotalSize<T> method specifically for the case where T is a Tree. the IL code above looks like this in generated C++ code:

C#编译器需要提供一个为各种类型T都通用的实现，所以才陷于着缓慢的代码。但是像IL2CPP可以更加主动的生成需要的代码，不生成不需要的代码。

当T是Tree类型时，IL2CPP会TotalSize<T> 函数对生成一个实现。下面就是生成的C++代码：

![Smaller icon](http://awalife.top/images/12/newil.png)

IL2CPP recognized that the box opcode is unnecessary for a value type, because we can prove ahead of time that a value type object will never be null. In a tight loop, this removal of an unnecessary allocation and copy of data can have a significant positive impact on performance.

IL2CPP能识别出装箱操作对值类型是不必要的，因为我们能事先知道值类型是不可能为null的。在一个[tight loop]里，移除这个不必要的堆内存申请和数据拷贝能显著的改善性能。

### **Wrapping up**

As with the other micro-optimizations discussed in this series, this one is a common optimization for .NET code generators. All of the scripting backends used by Unity currently perform this optimization for you, so you can get back to writing your code.

正如其他的这个系列文章里讨论的微小优化，这个也是一个常见的.NET代码生成优化。所有脚本后段都享受到了这个优化，你可以安心愉快的继续写你的代码了。

-----------

什么是tight loop，From wiki:

1. (computing) In assembly languages, a loop which contains few instructions and iterates many times.
2. (computing) Such a loop which heavily uses I/O or processing resources, failing to adequately share them with other programs running in the operating system.

For case 1 it is probably like

```
for (unsigned int i = 0; i < 0xffffffff; ++ i) {}
```

[tight loop]:https://en.wiktionary.org/wiki/tight_loop