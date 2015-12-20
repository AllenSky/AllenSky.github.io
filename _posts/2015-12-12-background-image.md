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


-------------------------
今天就让我们来考虑一下这种ARPG（或是Moba）的游戏优化方式。下图是我们公司正在封测的游戏.


![Smaller icon](http://awalife.top/images/2/war.jpg)

核心的战斗需求是支持多人联网相互PK（目前这个玩法没开放）。基于这个要求，决定采用[ZeroMQ]
作为底层通讯层。[ZeroMQ]有诸多好处，抽象出多种网络数据传输方式，比如Publish Subscribe等...不用特别在意网络时序，比如服务器先启动，客户端再去链接这样的顺序，我这里就不一一列举了。同时，[ZGuide]作为Zero的文档，但也推荐每个开发人员都仔细研读，关于Socket的知识大都囊括。 

但在Unity中使用[ZeroMQ]，必须编译出Android的SO库，IOS的.a库，Mac的.a库给UnityEditor。觉得较为麻烦，而且我觉得应该有C#的版本，果然google之后，我使用了[NetMQ]。[NetMQ]本身的质量还不错，也一直有人维护，但是PortToUnity之后还是有不会少问题，我也是在大量测试后基本上都修正了。（其实作为GitHub的收益者，没有把我修改的不少Bug， pull request到这些开源项目上，一直是我的遗憾）。如果同学想要这部分东西，可以单独邮件联系[我]。

再谈优化之前，还是要再说说战斗的模式。
这个游戏的设计模式用的是不太标准的C/S模式（为啥不标准后面会提到，主要是为了提高非联网战斗的性能）。C端不负责任何逻辑，只负责战斗的表现，比如渲染场景，人物，动作，特效等。S端负责整个战斗的逻辑，比如AI，技能释放，场景事件等。C端一定是在手机设备上，S端可在手机上也可以在服务器上。

###优化之C端

	C端的优化是资源上的优化

1. 根据目标机器的性能，设定定点数面数的要求，动画的骨骼数量。这个在项目开始的时候就要有一个目标机器的范围。

2. 设置适当的特效。目前大部分的手机设备对于渲染Alpha都有负担，而且Alpha的范围越大（通常是特效范围大）机器的性能下降越是严重。检测机器的性能，根据性能开启高中低三档的效果。我发现大部分的android机器耗电都十分明显，尤其是显示的特效多了之后，感觉不仅耗电，性能下降还很严重。关于显示方面的profile，不仅有Unity的Profile，还有高通的[AdrenoProfiler]。[AdrenoProfiler]值得仔细研究。很多很多优化的知识都能在这里学习到。

3. 开启遮挡剔除。Unity本身就内建的功能，除此还可将不在显示范围内的特效全部关闭。不在显示区域，可以优化事情有很多，主要都放到服务端优化来说明。

4. 战斗中同步加载资源会出现掉帧的想象。这个在Unity里面目前视乎没有好的解决方法，那怕你是用了LoadAsync，也有绕不过去的Instanciate这个同步方法。所以只能提前预加载，在进入战斗之前就准备好所有要展示的模型，而不是需要的时候再动态载入。这个原理简单，但是很多技能或者触发器或动态的创建某些东西，这个要预处理起来还是要费一番功夫的。Unity对Resources加载有很多黑盒子，如果你创建的东西，不再Camera显示区域，似乎Unity也并未加载资源，而是延迟等到Camera看见的时候才真正的加载，所以还得要特别的缩小预加载的资源（使肉眼不易察觉），让资源们在Camera下露一次眼。这点讨论的东西，和具体的Unity版本有关系，不排除将来不是这样了。

4. 贴图。贴图能小勿大，光照贴图就更是了。贴图格式不要使用RGBA32，Android尽可能的使用ETC或RGBA16，IOS尽可能的使用PVRTC ARGB 4Bit或是PVRTC RGB 4Bit。告诉美术同学，能不要Alpha就绝不要Alpha。需要特别说明一点的是：Android opengl2.0 的ETC是不支持Alpha的，3.0支持，但目前市面上还有很多2.0的设备，那怎么办呢？可以用RGBA16，但RGBA16压缩质量不高，贴图的文件体积较大。所以我们可以自己写一个Shader，使用两个ETC格式的贴图，其中一个是原图，一个是Alpha图（Alpha图还能把尺寸再价低一点）合并为一个带有alpha的图。
这里我是修改的NGUI的Shader为例，我们项目的UI使用的是NGUI搭建的。

附上代码：

{% highlight css %}

Shader "ETC_Alpha/NGUI/Unlit/Transparent Colored"
{
	Properties
	{
		_MainTex ("Base (RGB)", 2D) = "black" {}
		_AlphaTex ("Trans (A)", 2D) = "white" {}
	}
	
	SubShader
	{
		LOD 200

		Tags
		{
			"Queue" = "Transparent"
			"IgnoreProjector" = "True"
			"RenderType" = "Transparent"
		}
		
		Cull Off
		Lighting Off
		ZWrite Off
		Fog { Mode Off }
		Offset -1, -1
		Blend SrcAlpha OneMinusSrcAlpha

		Pass
		{
			CGPROGRAM
				#pragma vertex vert
				#pragma fragment frag
				#pragma multi_compile R_CHANNEL G_CHANNEL B_CHANNEL
				#include "UnityCG.cginc"
	
				struct appdata_t
				{
					float4 vertex : POSITION;
					float2 texcoord : TEXCOORD0;
					fixed4 color : COLOR;
				};
	
				struct v2f
				{
					float4 vertex : SV_POSITION;
					half2 texcoord : TEXCOORD0;
					fixed4 color : COLOR;
					fixed gray;
				};
	
				sampler2D _MainTex;
				sampler2D _AlphaTex;
				float4 _MainTex_ST;

				v2f vert (appdata_t v)
				{
					v2f o;
					o.vertex = mul(UNITY_MATRIX_MVP, v.vertex);
					o.texcoord = TRANSFORM_TEX(v.texcoord, _MainTex);
					o.color = v.color;
					o.gray = dot(v.color, fixed4(1,1,1,0));
					return o;
				}
				
				fixed4 frag (v2f i) : COLOR
				{
					fixed4 col;

					col = tex2D(_MainTex, i.texcoord);

					#if R_CHANNEL
						col.a=tex2D(_AlphaTex, i.texcoord).r;
					#elif G_CHANNEL
						col.a=tex2D(_AlphaTex, i.texcoord).g;
					#else
						col.a=tex2D(_AlphaTex, i.texcoord).b;
					#endif

					if (i.gray == 0)  
					{  
			       		float _gray = dot(col.rgb, float3(0.299, 0.587, 0.114));
			       		col.rgb = float3(_gray, _gray, _gray);
					}  
					else 
					{
						col = col * i.color;
					}

					return col;
				}
			ENDCG
		}
	}

	SubShader
	{
		LOD 100

		Tags
		{
			"Queue" = "Transparent"
			"IgnoreProjector" = "True"
			"RenderType" = "Transparent"
		}
		
		Pass
		{
			Cull Off
			Lighting Off
			ZWrite Off
			Fog { Mode Off }
			Offset -1, -1
			ColorMask RGB
			Blend SrcAlpha OneMinusSrcAlpha
			ColorMaterial AmbientAndDiffuse
			
			SetTexture [_MainTex]
			{
				Combine Texture * Primary
			}
		}
	}
}

{% endhighlight %}


###优化之S端


[怎样花两年时间去面试一个人]:http://mindhacks.cn/2011/11/04/how-to-interview-a-person-for-two-years/
[KISS]:https://en.wikipedia.org/wiki/KISS_principle
[Unity3D]:https://unity3d.com/
[文件夹]:http://docs.unity3d.com/Manual/SpecialFolders.html
[Resources.Load]:http://docs.unity3d.com/ScriptReference/Resources.Load.html
[OO]:https://en.wikipedia.org/wiki/Object-oriented_programming
[设计模式]:http://book.douban.com/subject/1052241/
[Head First设计模式]:http://book.douban.com/subject/2243615/
[reimport ui Assemblies]:http://issuetracker.unity3d.com/issues/unityengine-dot-ui-dot-dll-is-in-timestamps-but-is-not-known-in-guidmapper-dot-dot-dot-1
[ZeroMQ]:http://zeromq.org/
[NetMQ]:https://github.com/somdoron/netmq
[我]:wuwei.allenming@gmail.com
[AdrenoProfiler]:https://developer.qualcomm.com/mobile-development/mobile-technologies/gaming-graphics-optimization-adreno/tools-and-resources