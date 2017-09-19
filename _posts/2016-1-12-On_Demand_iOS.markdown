---
layout: post
title: Unity3D系列之iOS App Size优化(2)
description: "MASTERING ON-DEMAND RESOURCES FOR APPLE PLATFORMS"
tags: [Unity3D iOS Optimize]
---

Unity Blog翻译篇之 MASTERING ON-DEMAND RESOURCES FOR APPLE PLATFORMS


参考姐妹篇[Unity3D系列之iOS App Size优化(1)]

开始之前来一个我们游戏的图

![Smaller icon](http://amgoodlife.top/images/5/bg.jpg)


--------------


On-demand resources is a new iOS feature available since iOS and tvOS version 9.0. Its purpose is to reduce the size of the main application bundle, so that developers can separate certain resources from the main application bundle, host them on App Store infrastructure and download them on-demand.

> **随需应变资源**是一项在iOS和tvOS 9.0以后的新功能。他的目的是减少主应用的体积，所以开发者可在主应用分别采用特定的资源，保存在App store商店里，按需下载。


This is very important on the new AppleTV platform where the main application bundle is limited to 200MB. Therefore, most developers will need to use dynamically loaded resources one way or another. To make developer lives easier, Unity provides an on-demand resources API wrapper which has been shipped in Unity 5.2.0 patch 1.

> 在新的AppleTV 平台中这非常重要，因为在主应用的尺寸被限制在了200MB以内。因此，大部分的开发者可能需要使用动态加载资源的方式。为了方便开发者，Unity 5.2.0 patch 1 中提供了**随需应变资源**的API。

You can use on-demand resources to both reduce initial application download sizes and reduce the device storage usage by removing no longer needed assets. Generally, any resource that is not strictly needed to launch an app is a candidate for being loaded or unloaded on-demand. For example, consider a level-based game: the application does not need level 10 when the user is still playing level 3. On the other hand, the first levels may be safely unloaded when the user plays level 16.

>你可使用**随需应变资源**来减少初始应用包的下载大小，同时减少不再被需要的资源占用设备存储空间的使用率。通常来说，启动app时，任何不被确定需要的资源都可能被加载，也可能随需卸载。举例来说，考虑一个关卡游戏，app在玩关卡3的时候不需要关卡10。而另一边，第一关也可能可以安全的卸载当玩家已经在玩关卡16的时候。

The most convenient way of taking advantage of on-demand resources is via asset bundles. Asset bundles solve many remaining problems in dynamic asset loading, such as memory and CPU efficiency, dependency tracking and optimization for target platform. Once asset bundles are configured, you need very few additional changes to use on-demand resources, as shown in the code examples below. In this blog post we won’t cover asset bundles. If this is the first time you hear about them, this resource will help you.

>最方便的方法来使用**随需应变资源**是通过asset bundles。Asset bundles解决了很多动态加载资源的问题，比如内存，CPU使用率，资源依赖和特定平台优化。一旦asset bundles被配置好，你可能需要非常少的额外修改就能使用**随需应变资源**，如下面的代码所示。在这篇博客里面，我们不讨论asset bundles。如果是第一次了解asset bundles，可以参考[Asset Bundles]。


In order to use on-demand resources, the developer needs to perform two actions: assign some identifier, called tag, to each resource during build process and request the resources using the assigned tag during app runtime when needed. In vanilla iOS app development, the first step is done by assigning tags to resources in Xcode, whereas resources are requested using NSBundleResourceRequest API. In Unity, both tag assignment and resource retrieval are performed via code: the former via UnityEditor.iOS.BuildPipeline.collectResources event API, and the latter via UnityEngine.iOS.OnDemandResources.PreloadAsync API.

> 为了使用**随需应变资源**，开发者需要做两件事情：对每一个资源在编译时确定identifier，通常被叫做tag，在运行时按需使用tag来请求资源。在日常的iOS应用开发时，第一步是在Xcode里分派好tag给资源，而在资源请求时时通过NSBundleResourceRequest API来完成。在Unity，在tag分派使用：UnityEditor.iOS.BuildPipeline.collectResources event API，在检索时通过这些代码：UnityEngine.iOS.OnDemandResources.PreloadAsync API。

While the current on-demand resources API does not constrain tag names in any way, there are several guidelines that simplify development. It’s best to assign each asset bundle a unique tag which is derived from asset bundle name. This offers most flexibility and it’s also the simplest approach. In this case, the developer will be able to set priority of each individual download and also retrieve progress of each of them. Also, remember that Apple recommends that all the resources which are assigned the same tag have a cumulative size no larger than 64MB for a good balance between download speed and storage space availability.

The following two code examples demonstrate the essence of using on demand resources:

>目前，**随需应变资源**API没有限制任何tag名字，但在开发期间依然有一些指导。最好的分派每个资源一个唯一tag，而这个tag可以使asset bundle的名字。它提供了最大的扩展性和方便使用性。在这样的情况下，开发者可根据优先级下载每个独立资源，而且获取每个瞎子啊的进度。同时，也请记住Apple推荐所有的分配为同一个tag的资源不要超过64MB，因为过大或过小对下载速度和存储空间的使用率上都不会更好。

下面的两段代码展示了使用**随需应变资源**的精髓。


Editor script to assign identifiers to resources:

>编辑器代码给资源分配tag

{% highlight css %}

using UnityEditor.iOS;
#if ENABLE_IOS_ON_DEMAND_RESOURCES
public class BuildResources
{
 [InitializeOnLoadMethod]
 static void SetupResourcesBuild()
 {
   UnityEditor.iOS.BuildPipeline.collectResources += CollectResources;
 }
 static UnityEditor.iOS.Resource[] CollectResources()
 {
  return new Resource[] {
    new Resource("asset-bundle-name", "path/to/asset-bundle").AddOnDemandResourceTags("asset-bundle-name-tag"),
  };
 }
}

{% endhighlight %}


Resource retrieval during runtime:

>运行时请求资源

{% highlight css %}
using UnityEngine.iOS;
 
// We can execute the following function as a coroutine, like this:
// StartCoroutine(LoadAsset("asset-bundle-name", "asset-bundle-name-tag"));
public static IEnumerator LoadAsset(string resourceName, string odrTag)
{
 // Create the request
 var request = OnDemandResources.PreloadAsync(new string[] { odrTag } );
 // Wait until request is completed
 yield return request;
 // Check for errors
 if (request.error != null)
  throw new Exception("ODR request failed: " + request.error);
 // Now we can use the resource, for example, by loading an asset bundle like this:
 // var bundle = AssetBundle.CreateFromFile("res://" + resourceName);
 // ...
 // We need to call Dispose() when resource is no longer needed.
 // request.Dispose();
}

{% endhighlight %}

The easiest way to start exploring asset bundles and on-demand resources is to use our Asset Bundle Manager demo project, which is available on BitBucket. The landing page offers a comprehensible description of how to use and tweak the demo.

>最简单开始使用**随需应变资源**和asset bundles就是参考Asset Bundle Manager demo项目，现在[BitBucket]上。登陆页上有详尽的描述如何使用和修改demo。

Several Unity games utilizing on-demand resources have been already shipped on Apple TV. These include Breakneck and Bruce Lee: Enter the Game – Unchained Edition.

>已经有几个登录Apple TV的Unity游戏使用了**随需应变资源**技术。他们在[这里]和[这里2].

[Unity3D系列之iOS App Size优化(1)]:http://amgoodlife.top/Optimizing_iOS_App_Size/
[MASTERING ON-DEMAND RESOURCES FOR APPLE PLATFORMS]:http://blogs.unity3d.com/cn/2015/11/26/mastering-on-demand-resources-for-apple-platforms/
[Asset Bundles]:https://unity3d.com/learn/tutorials/topics/scripting/assetbundles-and-assetbundle-manager
[BitBucket]:https://bitbucket.org/Unity-Technologies/assetbundledemo
[这里]:http://pikpok.com/games/breakneck/
[这里2]:http://madewith.unity.com/games/bruce-lee-enter-game-unchained-edition