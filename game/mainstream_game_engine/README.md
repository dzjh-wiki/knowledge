# 主流游戏引擎简介
  * 游戏引擎为游戏设计者提供各种编写游戏所需的各种工具，其目的在于让游戏设计者能容易和快速地做出游戏程式而不用由零开始。

  * 游戏引擎包含以下系统：渲染引擎（即“渲染器”，含二维图像引擎和三维图像引擎）、物理引擎、碰撞检测系统、音效、脚本引擎、动画模块、人工智能、网络以及场景管理。

----

## 3A游戏
  * <strong>An AAA game (usually pronounced "triple A game")</strong> is an informal classification used for video games with the highest development budgets and levels of promotion. AAA game development is associated with high economic risk, with high levels of sales required to obtain profitability.

  * <strong>AAA（读作“3A”）</strong>在电子游戏产业中是指，高制作预算、高行销成本的游戏。其中行销成本也是巨大的开销，宣传费用甚至可能超过制作游戏本身的花费。因而，AAA级游戏的制作费用无疑是一笔相对更高的巨额投资。

## Unreal（虚幻引擎）
开发商：Epic Games  
开发语言：C++  
最新版本：虚幻引擎4
代表作：《绝地求生》、《战争机器》系列、《质量效应》系列、《无主之地》系列、《GTA 5》侠盗飞车 、《星际争霸Ⅱ》

## CryEngine（尖叫引擎）
开发商：Crytek
开发语言：C++, Lua, C#
最新版本：CRYENGINE 3
代表作：《孤岛危机》系列

## Frostbite Engine（寒霜引擎）
开发商：EA DICE
开发语言：C++, C#
最新版本：寒霜3
代表作：《战地》系列、 《极品飞车16/18/Online》

## IW Engine（无尽引擎）
开发商：Infinity Ward（简称IW）
开发语言：C, C++, Python
最新版本：IW 6.0
代表作：《使命召唤》系列

## Source Engine（起源引擎）
开发商：Valve Software
开发语言：C++
最新版本：Source 2
代表作：《半条命2》、《CS》系列、《求生之路》系列、《Dota 2》

## Anvil Engine
开发商：Ubisoft（育碧）
开发语言：C++
最新版本：AnvilNext 2.0
代表作：《刺客信条》、《波斯王子5》、《彩虹六号：围攻行动》

## RAGE(Rockstar Advanced Game Engine)
开发商：Rockstar San Diego
开发语言：C++
代表作：《荒野大镖客》、《GTA4/5》

## Gamebryo(NetImmerse的后续版本)
开发商：Numerical Design Limited
开发语言：C++
最新版本：Gamebryo 4.0
代表作：《辐射3》、《文明4》、《上古卷轴3/4》、《古剑奇谭》

## Creation（进化版版Gamebryo）
开发商：Bethesda Softworks
开发语言：C++
代表作：《辐射4》、《辐射76》、《上古卷轴5/6》

## Decima
开发商：Guerrilla Games
开发语言：C++
最新版本：未知
代表作：《地平线 零之曙光》、《死亡搁浅》

## Naughty Dog Game Engine（顽皮狗）
开发商：Naughty Dog
代表作：《神秘海域》系列、《最后的生还者》

## ID Tech引擎(由雷神之锤引擎发展而来)
开发商：id Software
开发语言：C++
最新版本：id Tech 5
代表作：《雷神之锤》系列、《毁灭战士》系列、《德军总部》系列、《狂怒》

## EGO引擎(Neon引擎的修改版本)
开发商：Codemasters
代表作：《尘埃》、《F1》系列、《闪点行动2：龙之崛起》

## The Dead Engine（死亡引擎）
开发商：Visceral Games
代表作：《死亡空间》

## Avalanche Engine（雪崩引擎）
开发商：Avalanche Studios
代表作：《正当防卫》系列

## GEO引擎
开发商：Volition Games
代表作：《红色派系》系列

## BigWorld
开发商：BigWorld
代表作：《战车世界》、《天下2/3》

## RE引擎
开发商：Capcom
代表作：《生化危机7》、《生化危机2/3 重置版》、《鬼泣5》

## Unity
开发商：Unity Technologies
开发语言：C#, Javascript
最新版本：2019.1.0
代表作：《炉石传说》、《神庙逃亡》、《纪念碑谷》、《王者荣耀》

## Cocos
开发商：雅基软件
开发语言：Objective-C、C++、JavaScript、C#
最新版本：0.6.4
代表作：《梦幻西游手游版》、《刀塔传奇》、《我叫MT》、《捕鱼达人》

## 自研引擎
如暴雪的《守望先锋》 、拳头的《英雄联盟》


## OpenGL和DirectX
既然讲到了游戏引擎，就不得不说与游戏息息相关的图形学。
当然这里不展开关于图形学的知识，只介绍跟游戏编程相关的计算机图形编程接口——OpenGL和DirectX。

<strong>OpenGL（全写Open Graphics Library）</strong>是指定义了一个跨编程语言、跨平台的编程接口规格的专业图形程序接口。是一个功能强大，调用方便的底层图形库。
OpenGL是个与硬件无关的软件接口，可以在不同的平台之间进行移植。
OpenGL被设计为只有输出的，所以它只提供渲染功能。其核心API没有窗口系统、音频、打印、键盘/鼠标或其它输入设备的概念。
虽然这一开始看起来像是一种限制，但它允许进行渲染的代码完全独立于他运行的操作系统，允许跨平台开发。

<strong>DirectX，（Direct eXtension，简称DX）</strong>是由微软公司创建的多媒体编程接口。据说在1995年至1996年，由于微软想将市场扩展到被任天堂和世嘉控制的游戏领域，同时又不想使用已在任天堂上使用的OpenGL技术，于是收购了一家制作3D API的公司，并进行整理，从而发布了新的3D API——Direct3D。
DirectX并不是一个单纯的图形API，它是由微软公司开发的用途广泛的API，按照性质分类，可以分为四大部分：显示部分、声音部分、输入部分和网络部分。
就以目前游戏开发来说，DirectX所占市场份额要大一些。