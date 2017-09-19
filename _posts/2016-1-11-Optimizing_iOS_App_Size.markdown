---
layout: post
title: Unity3D系列之iOS App Size优化(1)
description: "OPTIMIZING IOS APP SIZE WITH RESOURCE SLICING"
tags: [Unity3D iOS Optimize]
---
随着开发完成度的不断提高，终于能腾出一些时间。今年的愿望之一：翻译Unity Blog上的文档，学习Unity的同时也造福一下英语不太好的同学。这次打算翻译的是：[OPTIMIZING IOS APP SIZE WITH RESOURCE SLICING].

参考姐妹篇[Unity3D系列之iOS App Size优化(2)]

开始之前来一个我们游戏的图

![Smaller icon](http://amgoodlife.top/images/4/bg.jpg)



--------------
### OPTIMIZING IOS APP SIZE WITH RESOURCE SLICING


###通过资源切割方式优化IOS应用安装包体积

App slicing is a new iOS feature available since iOS and tvOS version 9.0. It’s purpose is to reduce the size of the main application bundle by allowing developers to upload several device-dependent versions of resources to the App Store. Only one out of these is included in the App bundle for the device of the particular user.

>App slicing(应用切割，注意字母大小写。大写的特指苹果公司的技术)是自iOS和tvOS 9.0开始的一项新技术。它的目的是介绍减少应用包的体积，它允许开发者提交不同版本的资源到App Store来适配不同设备。只有其中一个资源会被下载到特定的用户设备上。

App slicing is very useful on iOS platform, because it helps the developers to pack more assets into the initial app bundle and still stay within the 100MB over-the-air download limit. To achieve this objective, the developer may also use the counterpart of app slicing, on-demand resources, which we have covered previously. However, in order to use on-demand resources, the app needs to be adapted to download needed resources dynamically, which is not always convenient or optimal. It’s also more complex in all cases. App slicing does not have these drawbacks.

>**App slicing**技术在iOS平台上非常有用，因为它帮助开发者把尽可能多的资源放入初始的应用包内，而且依然保持在100MB的无线下载限制内。为了达到这个目标，开发者也许使用作用相当的其他app slicing的技术，之前Unity博客提到的**随需应变资源**。但是，为了使用随需应变资源，应用需要根据机器手动动态的下载相对应的资源，这并不总是最佳的和方便的。而且，通常总是更复杂的方式。App slicing则没有这些缺点。

There are two flavors of app slicing: app executable slicing and app resource slicing. The former refers to removal of unneeded executable code from the application bundle and is employed automatically on the App Store for all apps that target iOS/tvOS version 9.0 and higher. The latter refers to removal of unneeded assets and needs more developer effort in order to work. This is what we will cover in this blog post.

>有两种方式来给app瘦身：app可执行部分切割和app资源切割。前者指的是从应用中去除不需要的可执行代码，而且这些工作都是在苹果商店里的所有的apps自动的完成的，当然是针对iOS/tvOS 9.0以后版本的。后者指的是去除不需要的资源，它需要开发者付出努力才能完成这个工作。这是本片博客要说的东西。

The primary objective of app resource slicing is to reclaim wasted bandwidth and space in cases when there are several variants of the same asset. Usage of different asset variants is frequently needed because there is large difference of hardware capabilities between the most recent and older, but still widely used iOS devices. Most of the time developers need to use assets of different quality to fully utilize the most performant devices without penalizing the rest. Prior to iOS 9.0, all asset variants had to be included into the main app bundle. That wasted bandwidth and storage space on devices, as all but one asset variants were unused. By employing app resource slicing, it’s possible to exclude any unneeded assets from the application bundle.

>app资源切割的主要目标是：如果一个应用里有很多相同的资源变体，就能收回被浪费的带宽和空间。使用不同资源变体是很常见的，因为最新的硬件和老硬件通常有着巨大的兼容性问题。大部分时候，开发者需要使用不同质量的资源去完全利用设备的性能而不损害其他设备。在iOS9.0以前，所有的资源变体都需要都需要被加入进app包内。这样就浪费了带宽和设备的存储空间，因为只有其中之一资源才被使用。通过使用app资源瘦身，就有可能把不用资源踢出app包。

The most convenient way of taking advantage of app resource slicing is via asset bundles. They are the easiest option for adapting the layout of the application assets for slicing. Asset bundles are integrated within Unity and the performance is effectively equivalent to loading from regular data files. Asset bundles are also used as the backend for on-demand resources, thus it’s convenient to add app thinning support to an app that already uses on-demand resources or vice-versa. Once asset bundles are configured, you need very few additional changes to use app resource slicing, as shown in the code examples below. In this blog post, we won’t cover asset bundles. If this is the first time you hear about them, this resource will help you.

>最方便的拥有app资源切割优点的方式是通过asset bundles。它是最简单的app资源切割的适配选项。Asset bundles已经被集成入Unity，而且性能和加载普通资源文件一样。Asset bundles也经常被用来满足**随需应变资源**，如果已经是这样使用AssetBundle的话，就很容易添加app瘦身的技术。反之，要使用app瘦身，就要使用asset bundles。一旦asset bundles被配置好后，你只需额外做很少的改变就能使用app资源切割技术，代码如下所示。这篇博客，我们不会讨论asset bundles的技术。

In order to use app resource slicing, the developer needs to specify which devices are eligible for all variants of assets that need to participate in resource slicing and then load the assets during application startup. In vanilla iOS app development, this is done by creating data set or image set items within an asset catalog, setting up device variations and then assigning resources to the placeholders in the UI. In Unity, the developer needs to set up asset bundle variants, that is, several asset bundles containing the same set of assets assets themselves potentially being different. All asset bundle variants that need to participate in app resource slicing need to be registered in code using UnityEditor.iOS.BuildPipeline.collectResources callback API. Later, the exact device requirements are specified in the player settings UI. Finally, the developer needs to manually load asset bundle via AssetBundle.CreateFromFile API on application startup.

The following two code examples demonstrate the essence of using app resource slicing:

>为了使用app资源瘦身，开发者需要确定哪些设备是适合使用资源瘦身的，然后在建立应用的时候上传资源。在普通的iOS应用开发时，在asset catalog里创建data set和image set，setting up device variations and then assigning resources to the placeholders in the UI。在Unity方面，开发者需要设置 asset bundles 变体，也就是，多个asset bundles包含相同的资源。所有的需要参加资源瘦身的asset bundle变体需要在UnityEditor里使用代码（UnityEditor.iOS.BuildPipeline.collectResources API）注册。之后，在Unity Player的设置UI里就会出现具体的设备需求。最后，开发者需在应用启动时，使用AssetBundle.CreateFromFile API手动加载asset bundle。

下面两段代码示例展示了使用app资源瘦身的核心实质。

{% highlight css %}

using UnityEditor.iOS;
#if ENABLE_IOS_APP_SLICING
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
    new Resource("asset-bundle-name").BindVariant("path/to/asset-bundle.hd"), "hd")
                                     .BindVariant("path/to/asset-bundle.md"), "md")
                                     .BindVariant("path/to/asset-bundle.sd"), "sd"),
  };
 }

Loading of asset bundles during startup:


< ...>
var bundle = AssetBundle.LoadFromFile("res://path/to/asset-bundle");

// now use AssetBundle APIs to load assets
// or Application.LoadLevel to load scenes

var asset = bundle.LoadAsset("Asset");
< ...>


{% endhighlight %}


The easiest way to start exploring asset bundles and app resource slicing is to use our Asset Bundle Manager demo project, which is available on BitBucket. The landing page offers a comprehensible description of how to use and tweak the demo.

>最简单的方法开始使用asset bundles和app资源瘦身就是使用Asset Bundle Manager demo工程，它现在在BitBucket上。登陆页上有详尽的描述如何使用和修改demo。

貌似现在Unity官方文档还没有更多的描述，具体的也只能参考BitBucket demo。:(


[Unity3D系列之iOS App Size优化(2)]:http://amgoodlife.top/On_Demand_iOS/
[OPTIMIZING IOS APP SIZE WITH RESOURCE SLICING]:http://blogs.unity3d.com/cn/2015/12/28/optimizing-ios-app-size-with-resource-slicing/

