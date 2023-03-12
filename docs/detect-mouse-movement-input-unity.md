# 如何在 Unity 中检测鼠标移动作为输入

> 原文：<https://blog.logrocket.com/detect-mouse-movement-input-unity/>

## 介绍

理解鼠标输入是如何工作的是每个 Unity 开发人员最基本的技能之一。鼠标不仅用于与 2D UI 元素交互，如按钮、滑块和复选框，还经常用于与游戏场景中的 3D 游戏对象交互，或者，例如，在鼠标光标旁边显示信息性的 UI 工具提示。

我们还经常需要进行坐标系转换，将鼠标位置转换为世界空间位置——本质上，就是将 2D 屏幕坐标转换为游戏场景中某处的 3D 坐标。通过这样做，我们可以使用生成的 3D 坐标来选择或突出显示 3D 空间中的游戏对象，或者在玩家单击地形时实例化一些漂亮的尘埃粒子，以提供一些漂亮的反馈。

在本文中，我们将看看在 Unity 中使用鼠标移动作为输入的三种不同方式。我们将从传统的输入系统开始，然后熟悉新的输入系统和输入动作。我们将只关注 Unity 提供的解决方案，不会涉及任何第三方插件。

以下是我们将逐节介绍的内容:

现在让我们跳到 Unity，看看如何开始检测鼠标输入。

## 传统输入模块

当我们创建一个新的 Unity 项目时，默认情况下会启用遗留输入模块。我们可以从`UnityEngine.Input;`访问模块来读取当前鼠标位置，我们可以调用`Input.mousePosition`。

在 Unity 中读取鼠标位置时，我们处理的是像素坐标(通常指屏幕点或屏幕空间位置)。应用程序窗口左下角的像素坐标为`0,0`，右上角的坐标与窗口的像素尺寸相同。为了得到活动窗口的像素尺寸，我们可以使用`Screen.width`和`Screen.Height`。

```
private void Update()
{
Vector3 mousePosition = Input.mousePosition
}

```

调用`Input.mousePosition`将当前位置返回给我们作为`Vector3`。注意，返回值的`z`部分总是`0`。

如果应用程序在窗口模式下运行，如果位置的`x`或`y`小于零或大于活动窗口的尺寸，我们可以判断光标是否在屏幕边界之外。

为了检测鼠标按钮的状态，我们可以使用`Input.GetMouseButton`、`Input.GetMouseButtonDown`和`Input.GetMouseButtonUp`。我们必须提供一个鼠标按钮值作为该方法的参数。在传统输入模块中，鼠标左键的值是`0`，右键是`1`，中键是`2`。

```
private void Update()
{
// Check if the user is holding down the left mouse button on this frame.
if (Input.GetMouseButton(0))
{    
}

// Check if the user pressed the right mouse button down on this frame.
if (Input.GetMouseButtonDown(1))
{
}
​
// Check if the user released the middle mouse button on this frame.
if (Input.GetMouseButtonUp(2))
{
}
}

```

除了鼠标按键，我们还可以通过使用`Input.mouseScrollDelta`来读取鼠标滚轮的状态。这给了我们当前鼠标滚轮在当前帧上的状态。返回值是一种类型的`Vector2`，其中的`y`组件表示滚动方向。正面是价值上升，负面是价值下降；`x`组件始终是`0`。

```
private void Update()

{

// Check if the user is scrolling the mouse wheel up.

if (Input.mouseScrollDelta.y > 0)

{

}

}
```

你可以从 Unity 自己的文档中了解更多关于传统输入模块的信息。

## 新的输入系统

输入系统，通常被称为“新输入系统”，是 Unity 最近的输入处理方法。与传统输入模块相比，它为配置、读取和调试输入提供了更现代的方式。

要开始使用新的输入系统，我们首先需要使用 Unity 的软件包管理器安装它。你可以从 [Unity 注册表](https://openupm.com/)中“输入系统”这个名字下找到。

在安装过程中，Unity 会询问您是否要禁用传统输入模块 API，并启用新输入系统的后端。启用新后端后，Unity 将重启。

通过进入项目设置，将活动输入处理设置为 **Both** ，可以同时激活传统输入模块和新输入系统。这在转换现有项目以使用新的输入系统时特别有用。

### 直接从设备读取输入

使用新的输入系统读取鼠标位置的语法略有不同，但仍然和使用传统输入模块一样简单。最大的区别是返回值是一个`Vector2`。我们可以这样理解当前的情况:

```
private void Update()

{

Vector2 mousePosition = Mouse.current.position.ReadValue();

}

The syntax for reading the mouse button states has changed as well.

private void Update()

{

// Check if the user is holding down the left mouse button on this frame.

if (Mouse.current.leftButton.isPressed)

{

}

​

// Check if the user pressed the right mouse button down on this frame.

if (Mouse.current.rightButton.wasPressedThisFrame)

{

}

​

// Check if the user released the middle mouse button on this frame.

if (Mouse.current.middleButton.wasReleasedThisFrame)

{

}

}

private void Update()

{

// Check if the user is scrolling the mouse wheel up.

if (Mouse.current.scroll.ReadValue().y > 0)

{

}

}
```

### 使用输入动作

直接从设备读取输入既快速又方便，但这种方法可能会令人头疼，特别是如果我们想要添加对多种输入设备的支持，例如触摸、笔或手写笔，或者任何可以充当指针并有可能取代鼠标的设备。

因此，我们可以使用输入动作，而不是直接访问设备。通过输入动作，我们可以将多个输入设备绑定到一个动作上，例如，鼠标点击和游戏手柄按钮按压都可以调用“点击”动作。

输入动作定义包含在动作映射中，动作映射位于输入动作资源中。让我们看看如何创建输入动作资产，然后使用输入动作读取鼠标输入。

首先，让我们创建一个新的游戏对象，并向其添加一个`PlayerInput`组件。现在，选择游戏对象，点击`PlayerInput`组件上的**创建动作…** 按钮。

![Adding PlayerInput component to game object, then clicking the create actions button](img/d183093713d77f53a6156e7dd4ce097b.png)

这样做将创建一个新的带有一些有用预置的输入动作资源。预置包含两个动作地图，**玩家**和 **UI** ，这些地图包含一些基本的输入动作。鼠标输入可以通过 UI 动作图来访问。对于下面的例子，我们将使用`Point`输入动作来读取鼠标位置。

您还可以通过右键单击项目文件夹并选择**创建/输入动作**来创建一个空的输入动作资产。在本文的剩余部分，我们将坚持使用预置，因为它们为我们提供了一些非常有用的例子。

![Action properties window](img/92562ff1cd7a7383b5b83142e0174a95.png)

在我们进入代码之前，让我们在 Unity 的项目视图中选择新创建的输入动作资产，并选中 **Generate C# Class** 复选框。点击**应用**。这样做将生成一个 C#类，它包含我们的输入动作资产的内容(动作映射和我们所有的输入动作)。

代码生成为我们提供了一种快速的方法，可以轻松地直接从我们的 IDE 中访问输入操作，并具有完全有效的代码完成功能。您可以根据自己的喜好更改生成的类名、其名称空间和文件路径。对于下面的例子，我将我的类命名为`Inputs`。

![Generating C# class and hanging class name](img/b798e4b969ed40552416e3c926ed9830.png)

现在，让我们来看一个例子，如何使用输入动作来读取鼠标位置。创建一个名为`MouseInput`的类，并将其附加到一个新的游戏对象上。然后添加以下内容:

```
public class MouseInput : MonoBehavior
{
private Inputs _inputs;
​
private void Start()
{
    _inputs = new Inputs();
    _inputs.UI.Enable();
}
​
private void Update()
{
        var mousePosition = _inputs.UI.Point.ReadValue<Vector2>();
}
}

```

让我们回顾一下我们班上发生的事情。在`Start`方法中，我们创建了一个`Inputs`类的新实例。接下来，为了能够从`Point`输入动作中读取鼠标位置值，我们需要启用它所属的动作映射。

`Point`输入动作属于`UI`动作图，所以我们将调用`UI.Enable()`来启用它。现在我们可以使用`_inputs.UI.Point.ReadValue<Vector2>()`从更新循环中读取鼠标位置。

预设还提供了用于检测鼠标按钮状态的用户输入动作。

```
private void Update()

{

// Check if the user is holding down the left mouse button on this frame.

if (_inputs.UI.Click.IsPressed())

{

}

​

// Check if the user pressed the right mouse button down on this frame.

if (_inputs.UI.RightClick.WasPressedThisFrame())

{

}

​

// Check if the user released the middle mouse button on this frame.

if (_inputs.UI.MiddleClick.WasReleasedThisFrame())

{

}

}

private void Update()

{

// Check if the user is scrolling the mouse wheel up.

if (_inputs.UI.ScrollWheel.ReadValue<Vector2>().y > 0)

{

}

}
```

要了解更多关于新输入系统的信息，你可以[参考 Unity 自己的文档](https://docs.unity3d.com/Packages/com.unity.inputsystem@1.3/manual/index.html)。

## 决定正确的方法

无论是输入系统、渲染管道还是网络解决方案，Unity 生态系统的新来者可能会被众多可用的解决方案所淹没。为您的项目选择正确的方法并不总是简单明了的，并且在开发的后期更改核心系统对您的团队来说是非常昂贵的。

说到输入，遗留输入模块对于许多项目来说仍然是有效的方法，但是新输入系统的当前版本 v1.3.0 提供了更好的 API 和调试工具，更不用说输入动作系统了。它仍然缺乏的是 Unity 社区成员创建的大量内容，如教程和有用的论坛帖子，但可用的信息一直在增长。

如果您的目标是包括对许多不同输入设备的支持，那么使用新的输入系统会更有意义，因为它的输入动作支持开箱即用。对于不太复杂的项目，特别是那些只针对使用鼠标和触摸输入的移动和 PC 的项目，坚持使用传统的输入模块是非常好的。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)