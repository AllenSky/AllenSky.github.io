---
layout: post
title: Unity3D Light
description: "Unity3D Light"
tags: [Light Unity 3D]
---

#关于Unity3D Light的介绍

Unity5较之于Unity4有非常大提升，包括有Physically Based Shading(也有叫做Physically Based Rendering), 引进了Precomputed Realtime Global illumination(简称PRGI). 本文关注PRGI，如果想了解PBS(PBR)，请看这里。

首先咱们要了解Lighting有哪些种类(Types of light)：
##1. Point lights 点光源
A point light is located at a point in space and sends light out in all directions equally. The direction of light hitting a surface is the line from the point of contact back to the center of the light object. The intensity diminishes with distance from the light, reaching zero at a specified range. Light intensity is inversely proportional to the square of the distance from the source. This is known as ‘inverse square law’ and is similar to how light behaves in the real world.
大致表达的意思是：点光源向周围各个方向均等的发射出光。电光源需要定义出一个照射范围（影响区域）光的强度遵循距离平方的倒数，且再Range距离上为0.
![image](http://awalife.top/images/_v_images/_image_1505550259_6334.png)

Point lights are useful for simulating lamps and other local sources of light in a scene. You can also use them to make a spark or explosion illuminate its surroundings in a convincing way.
电光源对模拟灯具或是局部光源非常有用。可以方便的生成一个闪烁或是爆炸去照亮周围。如下图：
![image](http://awalife.top/images/_v_images/_image_1505550843_26500.png)


##2.Spot lights 聚光灯

Like a point light, a spot light has a specified location and range over which the light falls off. However, the spot light is constrained to an angle, resulting in a cone-shaped region of illumination. The center of the cone points in the forward (Z) direction of the light object. Light also diminishes at the edges of the spot light’s cone. Widening the angle increases the width of the cone and with it increases the size of this fade, known as the ‘penumbra’.

大致表达的意思是：聚光灯不仅有影响范围而且有角度。它影响的范围是一个椎体。
![image](http://awalife.top/images/_v_images/_image_1505551472_19169.png)
Spot lights are generally used for artificial light sources such as flashlights, car headlights and searchlights. With the direction controlled from a script or animation, a moving spot light will illuminate just a small area of the scene and create dramatic lighting effects.

聚光灯非常适合用于人造光源，比如手电筒，车灯，探照灯等。

![image](http://awalife.top/images/_v_images/_image_1505551568_15724.png)

##3.Directional lights 平行光（有向光）
Directional lights are very useful for creating effects such as sunlight in your scenes. Behaving in many ways like the sun, directional lights can be thought of as distant light sources which exist infinitely far away. A directional light does not have any identifiable source position and so the light object can be placed anywhere in the scene. All objects in the scene are illuminated as if the light is always from the same direction. The distance of the light from the target object is not defined and so the light does not diminish.

它非常适合用来创造太阳光的效果。平行光可被认为是一个无穷远的光源，所以在Unity里平行光的Transform的Position没有实际的作用，你怎么改变都是一样的效果，因为理论上平行光是无穷远处。同时所有场景里的物体都会被照射，而且光的强度也不会减弱。

![image](http://awalife.top/images/_v_images/_image_1505552050_29358.svg)

Directional lights represent large, distant sources that come from a position outside the range of the game world. In a realistic scene, they can be used to simulate the sun or moon. In an abstract game world, they can be a useful way to add convincing shading to objects without exactly specifying where the light is coming from. 

平行光是代表这超远距离的光源。在一个写实风格的场景里，可以被用来模拟太阳或是月亮。或是方便的添加物体的影子。

![image](http://awalife.top/images/_v_images/_image_1505554018_26962.png)

By default, every new Unity scene contains a Directional Light. In Unity 5, this is linked to the procedural sky system defined in the Environment Lighting section of the Lighting Panel (Lighting>Scene>Skybox). You can change this behaviour by deleting the default Directional Light and creating a new light or simply by specifying a different GameObject from the ‘Sun’ parameter (Lighting>Scene>Sun). Rotating the default Directional Light (or ‘Sun’) causes the ‘Skybox’ to update. With the light angled to the side, parallel to the ground, sunset effects can be achieved. Additionally, pointing the light upwards causes the sky to turn black, as if it were nighttime. With the light angled from above, the sky will resemble daylight. If the Skybox is selected as the ambient source, Ambient Lighting will change in relation to these colors.
在Unity5的新场景里会包含一个平行光，它被连接到procedural sky system（姑且叫做预设的天空系统）.你也可以自己重新建立这个连接关系。日落，夜晚，这些模式都能通过旋转平行光来达到。

##4.Area lights 区域光

An Area Light is defined by a rectangle in space. Light is emitted in all directions uniformly across their surface area, but only from one side of the rectangle. There is no manual control for the range of an Area Light, however intensity will diminish at inverse square of the distance as it travels away from the source. Since the lighting calculation is quite processor-intensive, area lights are not available at runtime and can only be baked into lightmaps. 
区域光是定义一块区域内向各个方向发射，但是仅限于一面。强度是根据距离平方的倒数减少。因为光照计算是想到耗费处理器的，所以arealight是非运行时生效的，仅能烘培到光照贴图。
![image](http://awalife.top/images/_v_images/_image_1505558523_5705.svg)

Since an area light illuminates an object from several different directions at once, the shading tends to be more soft and subtle than the other light types. You might use it to create a realistic street light or a bank of lights close to the player. A small area light can simulate smaller sources of light (such as interior house lighting) but with a more realistic effect than a point light.
因为区域光会同时从多个角度照射一个物体，所以阴影会显得更加的柔和。当距离玩家近处的街灯，可以创建区域光来产生更加真实的效果。
![image](http://awalife.top/images/_v_images/_image_1505558845_28145.png)

##5.Emissive materials 自发光物质
![image](http://awalife.top/images/_v_images/_image_1505558896_23281.png)
Like area lights, emissive materials emit light across their surface area. They contribute to bounced light in your scene and associated properties such as color and intensity can be changed during gameplay. Whilst area lights are not supported by Precomputed Realtime GI, similar soft lighting effects in realtime are still possible using emissive materials.

‘Emission’ is a property of the Standard Shader which allows static objects in our scene to emit light. By default the value of ‘Emission’ is set to zero. This means no light will be emitted by objects assigned materials using the Standard Shader.

There is no range value for emissive materials but light emitted will again falloff at a quadratic rate. Emission will only be received by objects marked as ‘Static’ or “Lightmap Static’ from the Inspector. Similarly, emissive materials applied to non-static, or dynamic geometry such as characters will not contribute to scene lighting.

However, materials with an emission above zero will still appear to glow brightly on-screen even if they are not contributing to scene lighting. This effect can also be produced by selecting ‘None’ from the Standard Shader’s ‘Global Illumination’ Inspector property. Self-illuminating materials like these are a useful way to create effects such as neons or other visible light sources.

Emissive materials only directly affect static geometry in your scene. If you need dynamic, or non-static geometry - such as characters, to pick up light from emissive materials, Light Probes must be used.
就像区域光那样，自发射物质也是通过表面发射光线。他们会在场景里贡献反射光的颜色和强度。同时和区域光一样也是不被PRGI支持。"Emission"是Standard Shader里的一个属性，只不过初始参数为0，表示没有光线会被发射出来。自发光物质没有Range参数，光照衰减同样遵循距离平方的倒数的规则。只有被标记为“Static”和“Lightmap Static”的才能接受自发光。同样的，非静态的或者动态的物体上包含自发光材料，也不会对场景的光照产生贡献。换句话说，必须是“Static”和“Lightmap Static”的自发光和“Static”和“Lightmap Static”的接收物体才能看出效果。

##6.Ambient light 环境光

Ambient light is light that is present all around the scene and doesn’t come from any specific source object. It can be an important contributor to the overall look and brightness of a scene.
环境光在场景里无处不在，它不是从特定的物体里发出来，它是场景里非常重要的整体观感和明亮度。
------------------------------------------ 未完待续 ---------------------------------------
