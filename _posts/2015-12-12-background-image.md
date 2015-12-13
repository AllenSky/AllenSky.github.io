---
layout: post
title: Unity3D 优化
description: "Unity3D Optimization"
tags: [Unity3D C# Optimize]
---
在经历过或大或小，或简单或复杂的[Unity3D]项目（后面我都简称为U3D）之后，这几乎是每一个U3D项目组会遇见的问题。而且，越是复杂大型的项目，对与优化的要求就会更加强烈。在此，我也谈谈U3D优化的一些方法。

	
* ##招聘优秀的员工	
	人才是最重要的资源，IT行业尤其如此。
	
	招到不合适的人轻则费钱重则把公司搞挂。
	
	没有人才，谈什么优化？
	
	请看[怎样花两年时间去面试一个人]
	
* ##[KISS]原则
  KISS是最最重要的原则，没有之一。
  
  1. 把一切不需要的东西，都从项目中拿走。
  
	  U3D对于工程目录Assets下的*[文件夹]*是有规则的。这里我只说一下比较重要的几个文件夹。**Resources** 通过*[Resources.Load]*可在Runtime时加载资源，所以所有在这个目录下的资源都会被编译进入安装包。还有，**StreamingAssets** 目录下通常存放这一些原始资源(原始的数据格式)，比如CG视频，这也会被放入安装包。这两个资源位置很方便使用，但也需要注意，注意或是不小心或是项目变更，就把不使用的资源放入其中，从而不小心把安装包体积变大。
	
	2. 优化不只是程序员的工作，而是项目组相互讨论，甚至是相互妥协才能最终完成。
	
		优化似乎被认为是程序员才需要做的事情。不，而是整个项目组要做的事情。复杂的UI交互设计，复杂的UI界面，消耗的不仅仅是开发时间，消耗的也是玩家手中设备的性能和电池。简单的设计，易懂的交互才是根本。在此之上，程序做一些合理的建议，来保证游戏的性能。即使如此，经常被问UI这么这么做有没有性能问题的我，其实，都不太好回答。因为哪怕是我们目标设备已设定是500￥以上的android手机，同价位的不同设备有时差别真的不小。
	
	3. 编写简单的代码
	
		编写小的简单的代码。如无必要绝不写大函数。我看过很多写的很长的，重复的函数，要么我删掉重写，要么绝不敢碰一下。**复制黏贴代码**，是导致函数很长的原因之一，而且这绝对是一个坏习惯。代码应该自己手动输入，这样才会思考。
		
		接手别人工作的时候，尽量多看看项目已经积累的代码，而不是从头再造轮子。
		
		生活在C#环境里的U3D开发人员。U3D的C#是大约.NET 3.5的环境，在此环境下，C#什么是能用的什么是不适合用的，什么是绝对不能用的，这算是基础要求。良好的[OO]思想。常用的设计模式熟练掌握，才能编写合适的代码（推荐[设计模式]和[Head First设计模式]）
		
	4. 构建Daily Build
		
		稍微大些的项目，使用U3D编译时间都不短。所以编译工作就应该交由自动化工具去完成。每日构建不能减少bug，但是却能让bug及时出现，尽早解决。同时，还能缓解开发人员的工作效率。不过，虽然我都编写好了打包的自动化脚本工具，但是每一次正式的编译打包，我都是亲力亲为，主要是我们开发人员bug实在难以避免，同时Unity偶尔会出现[reimport ui Assemblies]Bug。即便如此，我依然推荐每日构建的工具[Jenkins](https://jenkins-ci.org)。

这里我谈到的是大概念，后续会补充具体优化的方式和细节。

[怎样花两年时间去面试一个人]:http://mindhacks.cn/2011/11/04/how-to-interview-a-person-for-two-years/
[KISS]:https://en.wikipedia.org/wiki/KISS_principle
[Unity3D]:https://unity3d.com/
[文件夹]:http://docs.unity3d.com/Manual/SpecialFolders.html
[Resources.Load]:http://docs.unity3d.com/ScriptReference/Resources.Load.html
[OO]:https://en.wikipedia.org/wiki/Object-oriented_programming
[设计模式]:http://book.douban.com/subject/1052241/
[Head First设计模式]:http://book.douban.com/subject/2243615/
[reimport ui Assemblies]:http://issuetracker.unity3d.com/issues/unityengine-dot-ui-dot-dll-is-in-timestamps-but-is-not-known-in-guidmapper-dot-dot-dot-1