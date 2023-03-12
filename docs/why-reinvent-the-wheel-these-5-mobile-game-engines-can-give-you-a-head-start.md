# 为什么要重新发明轮子？这 5 个手机游戏引擎可以给你一个良好的开端

> 原文：<https://blog.logrocket.com/why-reinvent-the-wheel-these-5-mobile-game-engines-can-give-you-a-head-start/>

游戏开发行业是软件开发中发展最快的领域之一。围绕它的工具生态系统也是如此。

从头开始构建一个游戏需要图形、动画和编程方面的深厚知识和技术专长。为了帮助节省时间和资源，游戏开发者社区已经构建并利用了无数的工具来帮助他们以越来越高的效率创建令人惊叹的游戏。今天，大多数开发者转向游戏引擎来简化整个过程。

游戏引擎帮助开发人员添加输入、物理、渲染、脚本、碰撞检测、人工智能等内容，而无需显式编程。基本上，它们旨在帮助您构建游戏，而无需重新发明轮子。

更好的是，这些工具中有许多是跨平台的，因此它们支持其他系统类型和版本。“造一个，用在任何地方”这句话适用于此。

有很多游戏引擎可供选择，最好的选择并不总是显而易见的，尤其是如果你是这个领域的新手。在本指南中，我们将重点关注移动游戏开发中一些最好和最受欢迎的游戏引擎，分析每种引擎的优缺点和常见用例。

## 1.一致

Unity Technologies 于 2005 年推出的 Unity 是最受欢迎的游戏开发引擎之一。如果你是一个经验丰富的游戏玩家，一些由 Unity 构建的大牌，包括*英雄联盟*和*钢铁侠*。

Unity 是一个跨平台的游戏引擎，包括大量的教程来帮助初学者入门。基于这些原因，Unity 是我最喜欢的手机游戏开发引擎。

看看下面的优点和缺点。

### 赞成的意见

*   适合 2D 和 3D 游戏开发
*   支持开发人员之间的协作以及多人游戏开发。开发者可以开发和销售游戏资产来获得额外的收入来源
*   包括帮助新手掌握手机游戏开发的教程
*   支持在其他建模和动画平台(如 Blender 和 Maya)上开发的资源
*   支持各种平台的游戏开发(例如，桌面、移动、控制台、虚拟现实)

### 骗局

*   需要高处理速度才能在 PC 上有效运行
*   陡峭的学习曲线

### 用例

Unity 支持 C#、Unity script(也叫 JavaScript)和 boo(编写时还没那么流行)。

下面是一个在简单的 unity 游戏中选择胜负的 C#脚本。

```
void CheckPlayersDeath() 
{
  // 1
  if (deadPlayers == 1) 
  { 
    // 2
    if (deadPlayerNumber == 1) 
    { 
      Debug.Log("Player 2 is the winner!");
    // 3
    } 
    else 
    { 
      Debug.Log("Player 1 is the winner!");
    }
     // 4
  } 
  else 
  { 
    Debug.Log("The game ended in a draw!");
  }
}  

```

## 2.虚幻引擎

[虚幻引擎](https://www.unrealengine.com/en-US/)最早出现在 1998 年出版的第一人称射击游戏*虚幻*中。最初由 Epic Games 开发，于 2005 年开源。你可以[在 GitHub 上免费下载虚幻引擎](https://docs.unrealengine.com/en-US/GettingStarted/DownloadingUnrealEngine/index.html)。

用虚幻开发的热门游戏有*蝙蝠侠:阿卡姆疯人院&城市*、*出路*和*生物突变人*。

虚幻也支持 windows，iOS，Linux，Oculus Rift，PlayStation，Xbox 和许多其他平台。我强烈推荐游戏开发团队使用它，但是虚幻引擎对于一个程序员来说可能太难了。

### 赞成的意见

*   支持在平台内测试你的游戏
*   使用 C++编程语言
*   对于很少或没有编码经验的开发人员来说，这是一条简单的学习曲线
*   可以在 Unreal 中编译 C++代码，而不必关闭编译器(热重装)

### 骗局

*   与 Unity 相比，包含的插件很少
*   定价模型；如果你在做一个大规模的项目，支付 5%的总收入会很困难
*   理解最佳实践的陡峭学习曲线

### 用例

让我们来看一个类定义，一旦你创建了一个 C++类，稍后 Blueprint 将扩展它，Unreal 将自动生成这个类定义。

```
#include "GameFramework/Actor.h"
#include "MyActor.generated.h"

UCLASS()
class AMyActor : public AActor
{
    GENERATED_BODY()

public:
    // Sets default values for this actor's properties
    AMyActor();

    // Called every frame
    virtual void Tick( float DeltaSeconds ) override;

protected:
    // Called when the game starts or when spawned
    virtual void BeginPlay() override;
};

```

## 3.Solar2D

Solar2D 是 2009 年发布的 2D 游戏开发引擎。它旨在允许游戏开发者为 iOS、Android 和桌面开发 2D 游戏。它是免费和开源的。

使用 Solar2D，您可以直接从 Solar2D 模拟器进行构建，或者将您的项目与 Android studio 集成，以添加原生功能。Solar2D 支持 iOS、Android 和 Window。

一些用 Solar2D 开发的游戏包括*设计师城市*、 *Zip Zap* 和*枪手 Taco Truck。*

### 赞成的意见

*   离线构建
*   非常快速和简单
*   使用 Lua 语言，非常易学。
*   提供资源和帮助的有用且引人入胜的论坛
*   可以扩展到使用 OOP 特性

### 骗局

*   没有 GUI 可视化编辑器，这对于快速开发非常重要
*   仅支持 2D 游戏(和一点 2.5D)
*   不适合创建商业应用

### 用例

如果你想用一个免费的开源游戏引擎开始你的游戏开发之旅，你可能会考虑转而使用 Solar2D。如果你已经熟悉 Lua 编程语言，你会发现开始使用 Solar2D 特别容易。

下面的代码片段将使用 Lua 在 Solar2D 游戏引擎中创建一些对象。

```
local obj1 = display.newRect (groupA, x1, y1, 100, 100)
obj1:setFillColor (1, 0, 0)

local obj2 = display.newRect (groupA, x2, y2, 200, 100)
obj2:setFillColor (1, 1, 0)

local obj3 = display.newRect (groupA, x3, y3, 100, 200)
obj3:setFillColor (0, 1, 1)

```

要在屏幕旋转时触发已创建对象的旋转:

```
local function onOrientationChange (event)
   local currentOrientation = event.type

   if currentOrientation == "portrait" then
      transition.to (groupA, {rotation = -90, time = 1000, yScale = -1})

   elseif currentOrientation == "portraitUpsideDown" then
      transition.to (groupA, {rotation = -90, time = 1000, xScale = -1})

   elseif currentOrientation == "landscapeLeft" then
       transition.to (groupA, {rotation = 180, time = 1000, yScale = -1})

    elseif currentOrientation == "landscapeRight" then
       transition.to (groupA, {rotation = 0}) 
-- Or how to set the default orientation again?

 end
end
Runtime:addEventListener ("orientation", onOrientationChange)

```

如果你热爱探索新技术，又有时间学习，Solar2D 当然值得一试。

## 4.喝点什么

[SpriteKit](https://developer.apple.com/spritekit/) 是苹果公司在 2013 年发布的游戏开发引擎。因此，它被广泛认为是开发苹果游戏的最佳选择。

SpriteKit 支持 iOS、macIS、tvOS 和 watchOS，并与 GameplayKit 和 SceneKit 很好地集成。它也支持 Swift。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

这个引擎包括几乎所有必要的资源，包括物理和照明动画，使游戏开发过程愉快，没有麻烦。

用 SpriteKit 开发的著名游戏包括*太空探索*、*诡异墙*和*快速魔方*。

### 赞成的意见

*   内置物理引擎，类似于 Box2D，简单易学
*   动作系统(一个从 Cocos2D 借用的概念)让你可以创建复杂的动画链，甚至反转它们
*   直观的 API 游戏对象以树形结构组织，每个对象继承 SKNode

### 骗局

*   更多地关注操作系统而不是内容
*   不跨平台(仅限 iOS)
*   相对较小的开发者社区

### 用例

SpriteKit 使用 Swift 编程语言。下面的代码片段将一个精灵添加到游戏中。

```
/ 1
let player = SKSpriteNode(imageNamed: "player")

override func didMove(to view: SKView) {
  // 2
  backgroundColor = SKColor.white
  // 3
  player.position = CGPoint(x: size.width * 0.1, y: size.height * 0.5)
  // 4
  addChild(player)
}

```

如果你想为 iOS 操作系统开发 2D 游戏，SpriteKit 是个不错的选择。

## 5.果酱 SDK

[Marmalade SDK](https://www.marmaladegamestudio.com/) 原名 Ideaworks3D Limited，是由 Marmalade Technologies Limited 创建的游戏开发引擎。这个跨平台引擎支持 Windows、iOS 和 Android 设备。

在 Marmalade 上开发的游戏资产可以导出到其他 3D 建模和动画工具，如 Maya 或 Autodesk。该引擎还支持 2D 游戏开发。

一些使用橘子酱打造的游戏包括*斩断绳索*、*背叛者*、*使命召唤:* *僵尸*。

### 赞成的意见

*   跨平台
*   使用 C++
*   与第三方代码库轻松集成
*   本机 CPU 指令减少了编译的压力和时间
*   无需重新编译即可将应用程序重新部署到其他平台

### 骗局

*   昂贵的许可证价格通常高于个人开发者和自由职业者

### 用例

下面的代码示例是一个`.mkb`文件，它是 Marmalade 项目的文件扩展名。`.mkb`文件指定了所有必需的源文件、库和构建选项，必须实现它们才能完成 Marmalade 中的项目。

```
#
# Main MKB file for Hello project
#

# Modules used by this project
subprojects
{
  iwgx
}

# The files that make up the project (source, data etc.)
files
{
  [SourceCode]
  (source)

  Main.cpp
}

# Settings to configure the deployment process 
deployments
{
  name="Hello"
  caption="Hello World"
}

```

我们指定了`main.cpp`，它是一个 C++文件，包含下面的 C++代码片段。

```
//---------------------------------------------------------
// Learning Mobile Game Development with Marmalade
// Chapter 1 - Hello
//---------------------------------------------------------

// Marmalade SDK includes
#include "IwGx.h"
#include "s3eConfig.h"
#include "s3eDevice.h"

//---------------------------------------------------------
// Main entry point
//---------------------------------------------------------
int main()
{
  // Initialise Marmalade modules
  IwGxInit();

  // Set a default message, then check the ICF file to see if
  // a proper message has been specified
  char lMessage[S3E_CONFIG_STRING_MAX] = "Hello!";
  s3eConfigGetString("APP", "Message", lMessage);

  // Set screen clear colour to black
  IwGxSetColClear(0, 0, 0, 255);

  // Draw text at double default size
  IwGxPrintSetScale(2);

  // Loop until we receive a quit message
  while (!s3eDeviceCheckQuitRequest())
  {
    // Allow device to process its internal events
    s3eDeviceYield(0);

    // Clear the screen
    IwGxClear();

    // Display our message on screen
    IwGxPrintString(10, 10, lMessage);

    // Flush IwGx draw buffers to screen
    IwGxFlush();

    // Swap screen double buffer
    IwGxSwapBuffers();
  }

  // Terminate Marmalade modules
  IwGxTerminate();

  return 0;
}

```

剩下的工作由构建和编译来完成。有关更多详细信息，请查看官方文档。

## 摘要

这只是移动游戏开发者社区所能提供的一个快速快照，但是我们在本指南中讨论的五个游戏引擎代表了广泛的使用案例，应该可以帮助你确定你的下一个项目需要什么样的工具。

现在，您已经看到了一些最流行的移动游戏引擎的基本优缺点，现在该由您来选择一个，并开始做您最擅长的事情:创建不可思议的移动体验。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)