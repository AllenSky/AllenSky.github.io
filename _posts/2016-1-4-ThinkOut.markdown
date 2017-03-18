---
layout: post
title: Unity3D之C#反思
description: "Why is so bad about Singleton"
tags: [C# Singleton]
---
使用Unity3D也已经大概有4年左右，C#方面也在使用中不断的熟悉。但单例模式的使用一直骨鲠在喉。
下面是项目里的代码片段：


{% highlight css %}

fskChoose         	= ForthSkillChoose.Instance;
pskChoose          	= PublicSkillChoose.Instance;
sixthSkChoose      	= SixthSkillChoose.Instance;
sixthSkillAdjustor 	= SixthSkillConfigAdjustor.Instance;
awakenAdjustor     	= AwakenConfigAdjustor.Instance;
runeSkillAdjustor  	= RuneSkillConfigAdjustor.Instance;
suitAdjustor      	= SuitSkillConfigAdjustor.Instance;
talentSk           	= TalentSkill.Instance;
Statics			= StatictisInWar.instance;
StaticsHurt		= StatictisHurt.Instance;
StaticsBeHurt 	    	= StatictisBeHurt.Instance;
StaticReco         	= StatictisRecovery.Instance;
StaticsDmg         	= StaticsDmgReport.Instance;

{% endhighlight %}

大量的单例功能类出现在了控制器类中。Ok，先抛开项目管理的问题（确实项目是边做边改，需求不太明确），每次新增加的功能应该这么添加吗？
我们先来看看[StackOverFlow]上得票最多的讨论:

> Paraphrased from Brian Button:
> 
> 1. They are generally used as a global instance, why is that so bad? Because you hide the dependencies of your application in your code, instead of exposing them through the interfaces. Making something global to avoid passing it around is a code smell.
> 
> 2. They violate the single responsibility principle: by virtue of the fact that they control their own creation and lifecycle.
>
> 3. They inherently cause code to be tightly coupled. This makes faking them out under test rather difficult in many cases.
>
> 4. They carry state around for the lifetime of the application. Another hit to testing since you can end up with a situation where tests need to be ordered which is a big no no for unit tests. Why? Because each unit test should be independent from the other.


大概说的是什么呢？

1. 单例模式是全局实例，他会破坏代码中依赖关系，通常OO语言，我们应该使用接口暴露出依赖关系。
使用单例模式来避免参数的传递，代码就会走了味。

2. 违反了[单一职责]原则, 优点则是单例控制着自己的生命周期

3. 固有的内在逻辑导致非常严重的联系（即代码耦合）。使得测试相当的困难。

4. 他们在应用的生命周期外都能携带的状态。使得单元测试很难做，因为，每个单元测试都应该是相互独立的。


在得票第二的回复中也说道：

> When you think you need a global, you're probably making a terrible design mistake.

当你需要一个全局变量的时候，你很可能做了一个很糟糕的设计。

既然项目中的代码已然无法修改，那也脑补一番，省的以后再摔跤。说来也简单，提炼出interface。

同时，因为战斗模式的不断扩展，而初期也未考虑的情况下（感觉是代码写不好的借口），后悔扩展的时候没有直接重构代码，导致一步错，步步错，现在都没机会修改了（因为我也不知道有部分代码的作用，代码即文档，这个问题以后讨论）。其实重构起来核心也很简单就是针对借口编程，把实现放到具体的子类来决定，[简单工厂]的模式即可，定义好接口。


其实说了这么多，请**设计好，针对接口编程**

[StackOverFlow]:http://stackoverflow.com/questions/137975/what-is-so-bad-about-singletons
[单一职责]:https://en.wikipedia.org/wiki/Single_responsibility_principle
[简单工厂]:http://www.cnblogs.com/zhili/p/SimpleFactory.html
