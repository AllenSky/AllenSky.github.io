---
layout: post
title: Unity3D Light
description: "Unity3D Light"
tags: [Light Unity 3D]
---



Unity5较之于Unity4有非常大提升，包括有Physically Based Shading(也有叫做Physically Based Rendering), 引进了Precomputed Realtime Global illumination(简称PRGI). 本文关注PRGI，如果想了解PBS(PBR)，请看这里。

对于灯光，首先咱们要了解Lighting有哪些种类(Types of light)，这是最基础的部分：
1. **Point lights 点光源**
  A point light is located at a point in space and sends light out in all directions equally. The direction of light hitting a surface is the line from the point of contact back to the center of the light object. The intensity diminishes with distance from the light, reaching zero at a specified range. Light intensity is inversely proportional to the square of the distance from the source. This is known as ‘inverse square law’ and is similar to how light behaves in the real world.
  大致表达的意思是：点光源向周围各个方向均等的发射出光。电光源需要定义出一个照射范围（影响区域）光的强度遵循距离平方的倒数，且再Range距离上为0.
  ![image](http://amgoodlife.top/images/14/image1505550259_6334.png)

Point lights are useful for simulating lamps and other local sources of light in a scene. You can also use them to make a spark or explosion illuminate its surroundings in a convincing way.
电光源对模拟灯具或是局部光源非常有用。可以方便的生成一个闪烁或是爆炸去照亮周围。如下图：
![image](http://amgoodlife.top/images/14/image1505550843_26500.png)


2.**Spot lights 聚光灯**

Like a point light, a spot light has a specified location and range over which the light falls off. However, the spot light is constrained to an angle, resulting in a cone-shaped region of illumination. The center of the cone points in the forward (Z) direction of the light object. Light also diminishes at the edges of the spot light’s cone. Widening the angle increases the width of the cone and with it increases the size of this fade, known as the ‘penumbra’.

大致表达的意思是：聚光灯不仅有影响范围而且有角度。它影响的范围是一个椎体。
![image](http://amgoodlife.top/images/14/image1505551472_19169.png)
Spot lights are generally used for artificial light sources such as flashlights, car headlights and searchlights. With the direction controlled from a script or animation, a moving spot light will illuminate just a small area of the scene and create dramatic lighting effects.

聚光灯非常适合用于人造光源，比如手电筒，车灯，探照灯等。

![image](http://amgoodlife.top/images/14/image1505551568_15724.png)

**3.Directional lights 平行光（有向光）**
Directional lights are very useful for creating effects such as sunlight in your scenes. Behaving in many ways like the sun, directional lights can be thought of as distant light sources which exist infinitely far away. A directional light does not have any identifiable source position and so the light object can be placed anywhere in the scene. All objects in the scene are illuminated as if the light is always from the same direction. The distance of the light from the target object is not defined and so the light does not diminish.

它非常适合用来创造太阳光的效果。平行光可被认为是一个无穷远的光源，所以在Unity里平行光的Transform的Position没有实际的作用，你怎么改变都是一样的效果，因为理论上平行光是无穷远处。同时所有场景里的物体都会被照射，而且光的强度也不会减弱。

![image](http://amgoodlife.top/images/14/image1505552050_29358.png)

Directional lights represent large, distant sources that come from a position outside the range of the game world. In a realistic scene, they can be used to simulate the sun or moon. In an abstract game world, they can be a useful way to add convincing shading to objects without exactly specifying where the light is coming from. 

平行光是代表这超远距离的光源。在一个写实风格的场景里，可以被用来模拟太阳或是月亮。或是方便的添加物体的影子。

![image](http://amgoodlife.top/images/14/image1505554018_26962.png)

By default, every new Unity scene contains a Directional Light. In Unity 5, this is linked to the procedural sky system defined in the Environment Lighting section of the Lighting Panel (Lighting>Scene>Skybox). You can change this behaviour by deleting the default Directional Light and creating a new light or simply by specifying a different GameObject from the ‘Sun’ parameter (Lighting>Scene>Sun). Rotating the default Directional Light (or ‘Sun’) causes the ‘Skybox’ to update. With the light angled to the side, parallel to the ground, sunset effects can be achieved. Additionally, pointing the light upwards causes the sky to turn black, as if it were nighttime. With the light angled from above, the sky will resemble daylight. If the Skybox is selected as the ambient source, Ambient Lighting will change in relation to these colors.
在Unity5的新场景里会包含一个平行光，它被连接到procedural sky system（姑且叫做预设的天空系统）.你也可以自己重新建立这个连接关系。日落，夜晚，这些模式都能通过旋转平行光来达到。

**4.Area lights 区域光**

An Area Light is defined by a rectangle in space. Light is emitted in all directions uniformly across their surface area, but only from one side of the rectangle. There is no manual control for the range of an Area Light, however intensity will diminish at inverse square of the distance as it travels away from the source. Since the lighting calculation is quite processor-intensive, area lights are not available at runtime and can only be baked into lightmaps. 
区域光是定义一块区域内向各个方向发射，但是仅限于一面。强度是根据距离平方的倒数减少。因为光照计算是想到耗费处理器的，所以arealight是非运行时生效的，仅能烘培到光照贴图。
![image](http://amgoodlife.top/images/14/image1505558523_5705.png)

Since an area light illuminates an object from several different directions at once, the shading tends to be more soft and subtle than the other light types. You might use it to create a realistic street light or a bank of lights close to the player. A small area light can simulate smaller sources of light (such as interior house lighting) but with a more realistic effect than a point light.
因为区域光会同时从多个角度照射一个物体，所以阴影会显得更加的柔和。当距离玩家近处的街灯，可以创建区域光来产生更加真实的效果。
![image](http://amgoodlife.top/images/14/image1505558845_28145.png)

**5.Emissive materials 自发光物质**
![image](http://amgoodlife.top/images/14/image1505558896_23281.png)

Like area lights, emissive materials emit light across their surface area. They contribute to bounced light in your scene and associated properties such as color and intensity can be changed during gameplay. Whilst area lights are not supported by Precomputed Realtime GI, similar soft lighting effects in realtime are still possible using emissive materials.

‘Emission’ is a property of the Standard Shader which allows static objects in our scene to emit light. By default the value of ‘Emission’ is set to zero. This means no light will be emitted by objects assigned materials using the Standard Shader.

There is no range value for emissive materials but light emitted will again falloff at a quadratic rate. Emission will only be received by objects marked as ‘Static’ or “Lightmap Static’ from the Inspector. Similarly, emissive materials applied to non-static, or dynamic geometry such as characters will not contribute to scene lighting.

However, materials with an emission above zero will still appear to glow brightly on-screen even if they are not contributing to scene lighting. This effect can also be produced by selecting ‘None’ from the Standard Shader’s ‘Global Illumination’ Inspector property. Self-illuminating materials like these are a useful way to create effects such as neons or other visible light sources.

Emissive materials only directly affect static geometry in your scene. If you need dynamic, or non-static geometry - such as characters, to pick up light from emissive materials, Light Probes must be used.
就像区域光那样，自发射物质也是通过表面发射光线。他们会在场景里贡献反射光的颜色和强度。同时和区域光一样也是不被PRGI支持。"Emission"是Standard Shader里的一个属性，只不过初始参数为0，表示没有光线会被发射出来。自发光物质没有Range参数，光照衰减同样遵循距离平方的倒数的规则。只有被标记为“Static”和“Lightmap Static”的才能接受自发光。同样的，非静态的或者动态的物体上包含自发光材料，也不会对场景的光照产生贡献。换句话说，必须是“Static”和“Lightmap Static”的自发光和“Static”和“Lightmap Static”的接收物体才能看出效果。

**6.Ambient light 环境光**

Ambient light is light that is present all around the scene and doesn’t come from any specific source object. It can be an important contributor to the overall look and brightness of a scene.
环境光在场景里无处不在，它不是从特定的物体里发出来，它是场景里非常重要的整体观感和明亮度。



----------------------------------------------- Rendering Path ---------------------------------------------------

## **Rendering Path**

在进一步介绍Unity光照之前，还是有必要来了解一下：*Rendering Path*。所谓Rendering Path就是指在渲染场景中光照的渲染方式，目前Unity支持的有*Legacy Vertext Lit*(No Support for RealtimeShadows) ,*Forward Rendering*,*Deferred Rendering*(Not Supported by mobile)

目前这三种Rendering Path，需要的系统以及硬件的支持都不同。下图是具体的（请大家自动忽略**Legacy Deferred**，Unity5不支持这种方式）

![image](http://amgoodlife.top/images/14/RenderPathComparison.png)



### **Vertex lit**

Vertex Lit即顶点光照，顾名思义， 就是所有的光照计算都是在顶点进行的，因此所有的像素运算效果都不支持，如阴影，法线贴图，light cookies等。一个物体一般只有一个pass。效果最差，运行最快。适合老设备或者一般的移动设备。这里也需要注意下面：

> ## Vertex Lit Rendering path
>
> **Since vertex lighting is most often used on platforms that do not support programmable shaders, Unity can't create multiple shader permutations internally to handle lightmapped vs. non-lightmapped cases. So to handle lightmapped and non-lightmapped objects, multiple passes have to be written explicitly.**
>
> - *Vertex* pass is used for non-lightmapped objects. All lights are rendered at once, using a fixed function OpenGL/Direct3D lighting model ([Blinn-Phong](http://en.wikipedia.org/wiki/Blinn-Phong_shading))
> - *VertexLMRGBM* pass is used for lightmapped objects, when lightmaps are RGBM encoded (this happens on most desktops and consoles). No realtime lighting is applied; pass is expected to combine textures with a lightmap.
> - *VertexLMM* pass is used for lightmapped objects, when lightmaps are double-LDR encoded (this happens on mobiles and old desktops). No realtime lighting is applied; pass is expected to combine textures with a lightmap.

Vertex Lit多数被用于固定管线Shader，所以不能通过代码逻辑来处理带烘培贴图和不带烘培贴图的情况。Unity 在使用 Vertex lit 模式时无法在内部自动分别处理使用了光照图的对象和未使用的，所以需要作者自己显式的针对 Vertex, VertexLMRGBM, VertexLMM 这三个 LightMode 的 PassTag 分别写一个 Pass，以便适应没有光照图，以及使用了光照图但编码不同的情况。



--------------------- 插入Fixed Pipline和programmable Pipeline的介绍 --------------------------

另外这里还要进一步介绍一下固定管线和可编程管线的区别到底如何。

首先Fixed Pipline和programmable Pipeline都是可以通过写代码来控制的。只不过固定渲染管线是指：可配置（configurable）的管线，实现不同效果就好像在电路中打开不同的开关。这个时候的API比较典型的都是这样的:

![image](http://amgoodlife.top/images/14/fixedaip.jpg)

可以看出来，这就是让大家花式扳开关的节奏.

但是大家的欲望是无限的，这么有限的扳开关的模式，渲染的效果实在是不理想，后来大家就把模式换成了流水线上坐着几个工人（Shader），然后给工人下命令（可编程）的方式了，这样的话只要工人忙得过来，那我就可以自己定义各种光照模型，贴图方式了，程序员的可发挥空间就大得多了。

固定管线时就像下面这样花式设参数:

![image](http://amgoodlife.top/images/14/FixedPiplineParameter.jpg)

到了可编程的时代就可以自己写代码来设置各种光照模型了：

![image](http://amgoodlife.top/images/14/ProgrammablePipline.jpg)

------------------结束Fixed Pipline和programmable Pipeline的介绍 --------------------------





### **Forward Rendering**

是绝大数引擎都含有的一种渲染方式。要使用Forward Rendering，一般在Vertex Shader或Fragment Shader阶段对每个顶点或每个像素进行光照计算，并且是对每个光源进行计算产生最终结果。下面是Forward Rendering的核心伪代码。

``` 
	For each light: 
		For each object affected by the light: 
			framebuffer += object * light
```

Forward Rendering渲染的复杂度大约可用O(num_geometry_fragments * num_lights)来表示，可以看出复杂度和集合体的面数还有光源的数量正相关。

具体在Unity中，有它自己的实现规则：

In Forward Rendering, some number of brightest lights that affect each object are rendered in fully per-pixel lit mode. Then, up to 4 point lights are calculated per-vertex. The other lights are computed as Spherical Harmonics (SH), which is much faster but is only an approximation. Whether a light will be a per-pixel light or not is dependent on this:

- Lights that have their Render Mode set to **Not Important** are always per-vertex or SH.
- Brightest directional light is always per-pixel.
- Lights that have their Render Mode set to **Important** are always per-pixel.
- If the above results in less lights than current **Pixel Light Count** [Quality Setting](https://docs.unity3d.com/Manual/class-QualitySettings.html), then more lights are rendered per-pixel, in order of decreasing brightness.

Rendering of each object happens as follows:

- Base Pass applies one per-pixel directional light and all per-vertex/SH lights.
- Other per-pixel lights are rendered in additional passes, one pass for each light. 

 Unity会挑选出影响物体最亮的光（对于不同物体，最亮的光也许不同），可能是4个(A是在Base Pass，BCD在 Additional Pass)，会逐像素渲染。然后是接着较亮的4个光用于逐顶点渲染，剩下的光则被用于球面调和渲染（近似处理），下图圆圈（表示一个Geometry），进行Forward Rendering处理。

![img](http://amgoodlife.top/images/14/ForwardLightsExample.png)

将得到下面的处理结果：

![img](http://amgoodlife.top/images/14/ForwardLightsClassify.png) 

对于ABCD四个光源我们在Fragment Shader中我们对每个pixel处理光照，对于DEFG光源我们在Vertex Shader中对每个vertex处理光照，而对于GH光源，我们采用球谐光照（SH）函数进行处理。注意这些灯光组会重叠，这是为了减少“Light Popping”（应该是减少光的跳跃）

**Forward Rendering优缺点**

很明显，对于Forward Rendering，光源数量对计算复杂度影响巨大，所以比较适合户外这种光源较少的场景（一般只有太阳光）。

但是对于多光源，我们使用Forward Rendering的效率会极其低下。因为如果在vertex shader中计算光照，其复杂度将是 ，而如果在fragment shader中计算光照，其复杂度为 。可见光源数目和复杂度是成线性增长的。

对此，我们需要进行必要的优化。比如

- 1.多在vertex shader中进行光照处理，因为有一个几何体有10000个顶点，那么对于n个光源，至少要在vertex shader中计算10000n次。而对于在fragment shader中进行处理，这种消耗会更多，因为对于一个普通的1024x768屏幕，将近有8百万的像素要处理。所以如果顶点数小于像素个数的话，尽量在vertex shader中进行光照。
- 2.如果要在fragment shader中处理光照，我们大可不必对每个光源进行计算时，把所有像素都对该光源进行处理一次。因为每个光源都有其自己的作用区域。比如点光源的作用区域是一个球体，而平行光的作用区域就是整个空间了。对于不在此光照作用区域的像素就不进行处理。但是这样做的话，CPU端的负担将加重，因为要计算作用区域。
- 3.对于某个几何体，光源对其作用的程度是不同，所以有些作用程度特别小的光源可以不进行考虑。典型的例子就是Unity中只考虑重要程度最大的4个光源。

下面介绍一下Forward Rendering的Pass

**Base Pass**

Base pass renders object with one per-pixel directional light and all SH/vertex lights. This pass also adds any lightmaps, ambient and emissive lighting from the shader. Directional light rendered in this pass can have Shadows. Note that Lightmapped objects do not get illumination from SH lights.

Note that when “OnlyDirectional” [pass flag](https://docs.unity3d.com/Manual/SL-PassTags.html) is used in the shader, then the forward base pass only renders main directional light, ambient/lightprobe and lightmaps (SH and vertex lights are not included into pass data).

Base Pass使用一个逐像素的平行光和其他逐顶点或球调和的光渲染物体。在这个Pass里会加载lightmap,环境光和自发光。平行光在此能有阴影。注意使用了Lightmap的物体在不会受到SH（球谐光照）光的影响。注意如果标注了“OnlyDirectional”标记的则只会使用平行光，环境光，探照灯和光照贴图（SH（球谐光照）光和逐顶点的光不会被计算）。

**Additional passes**

Additional passes are rendered for each additional per-pixel light that affect this object. Lights in these passes by default do not have shadows (so in result, Forward Rendering supports one directional light with shadows), unless *multi_compile_fwdadd_fullshadows* variant shortcut is used.

Additional passes会逐步把影响这个物体的逐光照的灯渲染物体。默认情况下不会产生影子，除非开启*multi_compile_fwdadd_fullshadows*。



### Deferred Rendering

不支持移动设备! 它需要：Multiple Render Targets(MRT), Shader Model 3.0(or later) and Depth render textures.

When using deferred shading, there is no limit on the number of lights that can affect a GameObject. All lights are evaluated per-pixel, which means that they all interact correctly with normal maps, etc. Additionally, all lights can have cookies and shadows.

使用*Deferred Rendering*就不会有照射物体光源数量的限制。对所有的光源都是逐像素渲染的，意味着都能使用normal maps.而且所有光源都能有cookies和阴影。

**Note:** Deferred rendering is not supported when using Orthographic projection. If the Camera’s projection mode is set to Orthographic, the Camera falls back to Forward rendering.

另外，需要注意的是：unity不支持在正交视角时设置Deferred rendering.如果在正交视角设置为延迟渲染，会被默认退化为Forward rendering.



----------------------------------------- 插入 MRT的介绍 --------------------------------------------

在介绍***MRT***之前，我们先谈一下**Frame Buffer Object**(***FBO***)，使用FBO的目的是：**将场景渲染到一个贴图上，而非显示屏**。

具体来说在3D图形学中，不管我们在前期进行如何的处理，最终实际上都是要显示在屏幕上的。而在屏幕上显示的东西是一个一个的像素，也就是说必然存在一块内存，这块内存容纳了所有的屏幕上的像素点。在OpenGL中，默认情况下它最终输出到的地方是一个由系统自行创建管理的一块内存，我们称之为帧缓存（Frame Buffer）。而对于我们现在的要求，我们并不需要将最终处理完毕的数据输出到这个帧缓存中去，而是需要输入到一块由我们自己指定的缓存中去。这就是FBO需要使用的地方。

​       当然，FBO的结构十分的复杂，它是各种缓存如Color Buffer, Depth Buffer, Stencil Buffer等等的集合。当我们不想要向系统默认的缓存中输出这些值的时候，我们就可以通过指定我们自己创建FBO，然后输出数据到指定的地方。

​       同时需要注意的一点就是，FBO是各个缓存的集合，但并不是说我们指定了一个FBO，系统就会将各个Buffer的值输出到这个FBO中去。由于类似的的buffer有很多，但是在实际使用的过程中往往不需要全部都使用到，也就是说我们可以有选择的指定哪些buffer是我们想要的，然后通过FBO将这些buffer输出到指定的地方。所以FBO更像是一个过滤器，它会将用户指定的缓存发送到用户指定的地方去，如Texture或者Render Buffer。

​       而指定到底要输出那些缓存以及要输出到什么地方的操作被称为Attachment（附加）。在FBO中存在这很多的附加点，我们只要将我们创建的用于容纳缓存的对象附加到FBO的指定附加点，那么就能够从那里接受到对应的缓存数据。

有了这些知识我们就能实现**Render To Texture**(***RTT***)渲染到贴图上：

​       第一步，我们要创建一个Frame Buffer Object

​       第二步，创建一个Texture Object

​       第三步，将Texture Object附加到Frame Buffer Object的Color Buffer附加点上

​       第四步，绑定激活Frame Buffer Object作为输出目的地，正常绘制你要绘制的场景

​       第五步，解除绑定Frame Buffer Object，绑定默认系统帧缓存，即Frame Buffer Object的ID为0的buffer，让系统buffer作为输出目的地

​       第六步，绑定激活上面保存了场景渲染图的纹理，传递到Shader中去

​       第七步，绘制一个和屏幕一样大小的四边形，并且在Shader中处理该纹理，然后输出到系统buffer中，从而显示出来。

那么**Multiple Render Texture**(***MRT***)是直接使用***FBO***实现***RTT***的进化版本。MRT技术出现之前，我们要把实时渲染的内容输出到多个目标缓冲区时，必须针对每个缓冲输入draw命令，进行顶点变化之类的操作，这样会有很多冗余的操作，比如相同的顶点被传入多次。使用MRT技术，通过pixel shader，我们能够把图像输出到不同渲染缓冲区中，而使用的draw命令只有一次。***注意：缓冲一般用纹理texture表示***

参考图展示了这一个过程：

![img](http://amgoodlife.top/images/14/color_g_buffer.png)

![img](http://amgoodlife.top/images/14/z-buffer.png)

![img](http://amgoodlife.top/images/14/normal_g_buffer.png)

![img](http://amgoodlife.top/images/14/finalcompositing.png)

----------------------------------------- 完成 MRT的介绍 --------------------------------------------



Deferred Rendering*（延迟渲染）顾名思义，就是将光照处理这一步骤延迟一段时间再处理。具体做法就是将光照处理这一步放在已经三维物体生成二维图片之后进行处理。也就是说将物空间的光照处理放到了像空间进行处理。要做到这一步，需要一个重要的辅助工具——**G-Buffer**。G-Buffer主要是用来存储每个像素对应的Position，Normal，Diffuse Color和其他Material parameters。根据这些信息，我们就可以在像空间中对每个像素进行光照处理。下面是Deferred Rendering的核心伪代码。

```
For each object:
	Render to multiple targets 
For each light:
	Apply light as a 2D postprocess
```

Deferred Rendering的最大的优势就是将光源的数目和场景中物体的数目在复杂度层面上完全分开。The rendering overhead of realtime lights in deferred shading is proportional to the number of pixels illuminated by the light and *not* dependent on Scene complexity. 

大约时间复杂度O(screen_resolution * num_lights)



**\*G Buffer **- 指Geometry Buffer，亦即“物体缓冲”。区别于普通的仅将颜色渲染到纹理中，G-Buffer指包含颜色、法线、世界空间坐标的缓冲区，亦即指包含颜色、法线、世界空间坐标的纹理。由于G-Buffer需要的向量长度超出通常纹理能包含的向量的长度，通常在游戏开发中，使用多渲染目标技术来生成G-Buffer，即在一次绘制中将颜色、法线、世界空间坐标分别渲染到三张浮点纹理中。

常见的做法是将颜色，深度和法线分别渲染到不同的buffer里面，在最后计算光照的时候的通过这三个buffer和光源的信息计算出最终pixel的颜色