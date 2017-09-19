---
layout: post
title: Unity3D系列之自动化打包
description: "Unity3D自动化打包"
tags: [Unity3D iOS Android Compile AutoBuild]
---

按照惯例先来一个我们游戏的宣传图

![Smaller icon](http://amgoodlife.top/images/7/yilidan.jpg) 

虽然Unity提供里Editor，用可视化的方式编译出Apk（Eclipse工程）或是Xcode工程，但是对于一个复杂的项目而言，还是不太方便的，尤其是面对中国不计其数的第三方市场，配置编译选项和不同渠道插件是一件闹心的事情。所以，用脚本去配置好一次，以后每次打包（尤其是出大量的渠道包）就不会每次恶心。

因为iOS的缘故，只能用Mac机器，所以脚本环境都是基于Mac OSX。脚本打包的核心便是：[Unity CommandLine]，同时再配合一些简单的Shell脚本知识。


###Android脚本编译
脚本代码如下：

{% highlight css %}

#!/bin/sh
 
#参数判断  
if [ $# != 3 ];then  
    echo "需要三个参数。 第一个参数是游戏平台的名字，第二个是Debug还是Release模式, 第三个是工程包(projection)还是APK(apk)包"  
    exit     
fi  
 
#UNITY程序的路径#
UNITY_PATH=/Applications/Unity4/Unity.app/Contents/MacOS/Unity
 
#游戏程序路径#
PROJECT_PATH=/Users/Allen/Desktop/AllMyUnityProject/ShadowDota

#在Unity中构建apk#
$UNITY_PATH -projectPath $PROJECT_PATH -executeMethod ProjectBuilder.BuildForAndroid project-$1 mode-$2 apkorproject-$3 -quit -batchmode -logFile ~/Desktop/And.log
 
echo "生成完毕"

{% endhighlight css %}

>$UNITY_PATH -projectPath $PROJECT_PATH -executeMethod ProjectBuilder.BuildForAndroid project-$1 mode-$2 apkorproject-$3 -quit -batchmode -logFile ~/Desktop/And.log

重点说明最后一句代码：就是我们会采用静默模式-batchmode，通过**ProjectBuilder.BuildForAndroid**此函数编译出APK，日志文件输出到桌面的And.log

一旦进入BuildForAndroid函数，那就是Unity C#的API调用，其核心是：
BuildPipeline.BuildPlayer函数，此函数就会编译出APK或是Eclipse Android工程文件。

简单来说，脚本打包就完成了。但是仍然有些细节要说明。
1. 在调用BuildPipeline.BuildPlayer前，根据需要准备好资源或是代码或是插件。通常来说会动态的修改C#代码，拷贝Jar包到Plugins/Android目录下，配置androidmanifest.xml文件等等。
2. 在调用BuildPipeline.BuildPlayer之后，打包完成后，Unity会调用OnPostProcessBuild函数，我们可以将编译前准备的资源删除掉。

------------------------------------------
###iOS脚本编译

iOS和Android脚本编译，大同小异。但有两个问题。

1. 接入第三方时，iOS都是需要修改XCode工程配置文件或是ObjC代码。这里推荐使用[XUPorter],我也是使用这个开源工具，有幸这个插件的作者还和我一起工作一段时间。

2. 由于只能生成Xcode工程，不能直接生成ipa文件。所以，我们仍需要通过Shell脚本调用Xcode Commond Line去完成Xcode编译。核心代码如下：


{% highlight css %}

#制作Archive
xcodebuild -scheme Unity-iPhone archive -archivePath build/Unity-iPhone -configuration Release
#打包IPA
xcodebuild -exportArchive -exportFormat ipa -archivePath build/Unity-iPhone.xcarchive -exportPath build/${ipa_name}.ipa -exportProvisioningProfile "wjry_adhoc"

{% endhighlight css %}



#总结
也许上面说的有点混乱，其核心却非常简单
通过Shell脚本运行Unity，运行起来的Unity调用一个我们编写好的C#函数，然后生成apk或是Xcode工程。如果是Xcode工程，再接着调用Xcode编译出ipa文件。

在这个流程中，我们需要在BuildPipeline.BuildPlayer函数之前准备好打包资源，在OnPostProcessBuild函数里删除之前准备好的资源，恢复原状。

-------------------------------------

说起具体项目的问题：我们依然要解决接入各种第三方平台的问题。
所以我们要定义好接口interface，然后根据具体的平台实现不同的子类。
同时我们定义好所有第三方回掉到同一个GameObject物体上的函数。
所有接口，仅以登陆为例.

{% highlight css %}
    /// <summary>
	/// Call Third Party API
	/// </summary>
	public interface ICallThirdParty {

		//*********** 登陆相关 *************
		//实例化sdk，调用sdk登录方法
		void Login(Action<string, string> onThirdPartyNotify);
	}

	
	/// <summary>
	/// Third Party CallBack API
	/// </summary>
	public interface IFromThirdParty {

		//*********** 登陆相关 *************
		//登录第三方成功,得到第三方认证码
		void LoginSuc(string code);

		//第三方取消登录
		void LoginCacel(string code);

	}

{% endhighlight css %}

调用第三方API都继承ICallThirdParty，回调都继承IFromThirdParty

[Unity CommandLine]:http://docs.unity3d.com/Manual/CommandLineArguments.html
[XUPorter]:https://github.com/onevcat/XUPorter