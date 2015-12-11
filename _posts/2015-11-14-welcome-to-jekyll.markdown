---
layout: post
title:  "Blog 开张"
date:   2015-11-14 16:52:07
categories: 个人
tags: 博客 个人
image: /images/1/up.jpg
---
### 开张
这是我在Github上的第一篇博客,也是我将近5年前搁笔的回归.5年时间,改变了很多.有了媳妇儿,孩子也即将降生.
5年时间大部分在技术上,1年的Android,4年的Unity.能勉强算是个游戏开发者.
	
这次,先来说说为什么要开启独立博客？开博客的好处有很多,比如[这里].最认同的一个看法便是：**书写是为了更好的思考**.那为啥又是独立博客呢？因为对csdn博客,163博客等都不太让我满意.所以就踏上了自己搭建博客之路.

自己做网站，有如下几个问题要解决：

1. 域名
2. 服务器（主机或者云）
3. 博客系统

域名可在[万网], [godaddy]挑选花钱购买便可.我用的是[万网], 听说[godaddy]网上有各种优惠码,但是我没尝试过.

有了域名，那就要域名解析.域名解析在购买域名的服务商都有现成的服务，问题在于需要一个固定公网IP地址，才能把域名绑定上.有固定IP的人来说，可以让一台电脑24小时跑来当服务器，但各种突发问题，比如停电，网络线路维修...也挺麻烦的. 况且对于一般老百姓在家里上网是没有固定公网IP的，找一个云服务商是个不错的选择. 我简单看了一下阿里云,一年最少230,太贵了.目前应该也不需要这么多的流量和硬件. 所以在网上找了一圈，免费的[GitHub Pages]作为博客是个不错的选择,而且GitHub作为码农的我是最合适不过的.
image: /images/1/aliyun.png

[GitHub]是软件仓库,其中还有非常多的著名开源项目.如果想在[GitHub Pages]上愉快的玩耍,熟悉[Git]命令是少不了的, 但是[GitHub]有一个可视化的[Git]前端.
image: /images/1/github_client.png

虽然web不是我工作方向,但好歹我是计算机科班出身.折腾这些应该比不少文科生要容易的多.比如[1] 和 [2]. 英语没问题的话，最好还是直接按照[官方]指导来的最准确.我最后是按照[官方]文档搭建起来的.需要说明的是，[GitHub Pages]有两种建站方式.

1. 项目site
2. 个人site

博客当然选择个人site啦.个人site的branch必须在master上.幸运的是默认就在master branch上,无需特意改动.

博客系统种类非常多,比如有[WordPress],[FarBox], [Ghost]等, [FarBox]是国内团队做的，效果不错，也简单易上手. 但是后台可设置的东西太少,文档不足,资料偏少，模版设置的规则不清楚,所以最后放弃了.其实一旦我们选择了[GitHub Pages]就默认使用[Jekyll]. 它有如下优点：

1. 简单. 不需要数据库
2. 静态页面
3. 专注博客

对于一个不是做web前端的码农来说, Markdown语法真是简单易上手.同时我也隆重推荐[Mou],所见即所得的好工具,毫不犹豫的Preorder支持一把。

![Mou icon](http://25.io/mou/Mou_128.png)

对刚起步的我(工作特别繁忙),[Jekyll]也有非常好的[themes],直接拿来用.

So, 你看到这篇博客就是：[GitHub Pages] + [Jekyll] + [Mou] 完成的.

[这里]:http://mindhacks.cn/2009/02/15/why-you-should-start-blogging-now/
[1]:http://www.zhihu.com/question/20463581
[2]:http://cnfeat.com/blog/2014/05/10/how-to-build-a-blog/
[官方]:https://pages.github.com
[万网]:http://www.net.cn/
[godaddy]:https://www.godaddy.com
[GitHub]:https://github.com
[GitHub Pages]:https://pages.github.com
[Git]:http://git-scm.com/download/
[WordPress]:https://cn.wordpress.org
[FarBox]:https://www.farbox.com
[Ghost]:https://blog.ghost.org
[Jekyll]:http://jekyllrb.com
[themes]:http://jekyllthemes.org
[Mou]:http://25.io/mou/