---
layout: post
title: Devirtualization
description: "Optimazation Devirtualization"
tags: [IL2CPP Unity]
---

----------------------

Modern compilers are excellent at performing many optimizations to improve run time code performance. As developers, we can often help our compilers by making information we know about the code explicit to the compiler. Today we’ll explore one micro-optimization for IL2CPP in some detail, and see how it might improve the performance of your existing code.

现代编译器非常擅长于优化提高运行时的代码效率。作为一个开发者，我们通常能根据完整代码信息帮助编译器。今天我们说明一下：一个小的IL2CPP优化，了解它是怎么提高你先有代码的效率的。

### Devirtualization- 非虚化

There is no other way to say it, virtual method calls are always more expensive than direct method calls. The compiler cannot know which method will be called at run time – or can it?

可以肯定的说，虚方法调用总是比直接调用方法开销要大得多。编译器没法儿获得运行时哪个派生类的方法被调用，或者说能知道？

Devirtualization is a common compiler optimization tactic which changes a virtual method call into a direct method call. A compiler might apply this tactic when it can prove exactly which *actual* method will be called at compile time. Unfortunately, this fact can often be difficult to prove, as the compiler does not always see the entire code base. But when it is possible, it can make virtual method calls much faster.

通常，Devirtualization是编译器常见的优化策略，它会把虚函数调用变为直接调用函数。编译器在编译时期能确定具体是哪个函数被调用，则Devirtualization优化策略就能实施。不幸的是，编译器并不能总是能得到完整的代码（比如运行时加载C# library）所以采用此优化通常停困难的。（但是下面评论说：IL2CPP采用的是AOT，那么我们实际上是可以在IL2CPP里采用Devirtualization优化策略的。）

### The canonical example

As a young developer, I learned about virtual methods with a rather contrived animal example. This code might be familiar to you as well:

![Smaller icon](http://awalife.top/images/9/animal.png)

![Smaller icon](http://awalife.top/images/9/farm.png)

很常见的OO写法，对吧？我们来看看生成的C++代码怎么样。

下图是foreach(var animal in animals) Debug.LogFormat(……, animal.Speak())的C++代码

![Smaller icon](http://awalife.top/images/9/c_1.png)

It is going to lookup the proper virtual method in the vtable and then call it. This vtable lookup will be slower than a direct function call, but that is understandable. The Animal could be a Cow or a Pig, or some other derived type.

这里代码在虚函数表里去查找合适的方法并调用它。我们可以理解，查找虚函数表会比直接调用函数慢很多。因为Animal可能是Cow或是Pig或是其他派生类型。

下图是var cow = new Cow(); Debug.Logmat(…., cow.Speak());的C++代码

![Smaller icon](http://awalife.top/images/9/c2.png)

Even in this case we are still making the virtual method call! IL2CPP is pretty conservative with optimizations, preferring to ensure correctness in most cases. Since it does not do enough whole-program analysis to be sure that this can be a direct call, it opts for the safer (and slower) virtual method call.

Suppose we know that there are no other types of cows on our farm, so no type will ever derive from Cow. If we make this knowledge explicit to the compiler, we can get a better result. Let’s change the class to be defined like this:

即使在这个情况下，我们仍然采用的是虚函数调用。IL2CPP是采用相当保守的优化策略，倾向于保证大多数情况下正确执行。因为它没有对整个程序分析来确保这是一次函数直接调用，它选择更安全但更慢的虚函数调用。

假如我们知道不在会有其他的Cow类型，所以就不会有Cow的派生类型。如果我们能把这个信息明确的告诉编译器，那么我们就能有更好的结果，我们可以这么定义：

![Smaller icon](http://awalife.top/images/9/seal.png)

The sealed keyword tells the compiler that no one can derive from Cow (sealed could also be used directly on the Speak method). Now IL2CPP will have the confidence to make a direct method call:

sealed关键字编辑器没有任何派生类继承自Cow(sealed也可以在Speak方法上使用)。现在IL2CPP就能直接调用函数了。

![Smaller icon](http://awalife.top/images/9/seal_c.png)

The call to Speak here will not be unnecessarily slow, since we’ve been very explicit with the compiler and allowed it to optimize with confidence.

现在Speak函数就不会在慢了，因为我们已经明确的告诉编译器让它去优化了。

This kind of optimization won’t make your game incredibly faster, but it is a good practice to express any assumptions you have about the code *in the code*, both for future human readers of that code and for compilers.

这种优化不会让你的游戏有明显的变快，但这确实是一个好的实践指导，去给编译器说明你代码里的假定条件。


---------

评论里有价值的讨论：

class hierarchy is next:
Animal (base class) -> Cow -> FlyingCow

If I’m not mistaken, there are 2 possible cases, depending on the specifications of your **Flying cow**: does it make the same sound than the **Cow** or not?

1) If they make different sounds (“Moo” vs “Moooooooo”), it’s the case you state. **Flying cow** derives from **Cow**, so obviously the **Cow** class can’t be *sealed*. You can only *seal* the **Flying cow** class, so just as you stated this class will be the only one benefiting from the devirtualization.

2) Now, if they make the same sound (“Moo”) but the difference lies somewhere else in the class, what you can do is: *seal* the **Flying cow** class just like before so it still benefits from the entire devirtualization; but also *seal* the *method Speak()* on the **Cow** class (and of course don’t override it in the **Flying cow** class, because it’s the same) and then it should still be devirtualized.