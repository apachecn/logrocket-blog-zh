# 比较颤振游戏引擎

> 原文：<https://blog.logrocket.com/comparing-flutter-game-engines/>

如果你是一个移动应用开发者，你可能在某个时候看到了一个非常流行的游戏，觉得你应该尝试一下游戏开发。

我记得对简单却极易上瘾的 *[Flappy Bird](https://en.wikipedia.org/wiki/Flappy_Bird)* 的流行感到惊讶，并认为这真的不是一个非常困难的游戏。它不像《T4》中的《部落冲突》和《纪念碑谷》中的《T5》、《T6》或类似的游戏那样复杂，这些游戏显然需要一个更大的开发团队。这是 Flutter 发布前的几年。当时我还在 Android 上试验 libGDX！

多年来，我喜欢在不同的平台和框架上用各种游戏引擎开发游戏。

在这篇文章中，我们将探索用 Flutter 创建游戏的不同方法。我们还将比较不同的颤振游戏引擎。本文不会涉及[添加 Unity app 到 Flutter](https://blog.logrocket.com/why-reinvent-the-wheel-these-5-mobile-game-engines-can-give-you-a-head-start/) 。在这种情况下，游戏实际上是在 Flutter 框架之外创建的。

本文的唯一先决条件是对颤振有一个基本的了解。

## 了解游戏引擎

就游戏引擎所提供的功能类型和质量而言，它们可以是非常多样化的。一些公司提供全套解决方案，而另一些公司提供的范围要窄得多。尽管如此，所有的游戏引擎都需要解决或补偿游戏的动态特性、大量对象的处理以及与底层 UI 组件的有限交互。

### 不断变化

游戏一般都是很动态的。他们可能需要考虑背景的滚动，物体的跳动，甚至是困惑或沮丧的用户的按键。为了获得良好的性能，游戏需要在不需要`setState(() {})`的情况下尽可能快地更新，比如回调以渲染新的一帧。

使这成为可能的关键代码是游戏循环。游戏循环反复运行，允许游戏更新对象位置、调整屏幕大小、改变摄像机位置或视角等等。

游戏循环出现在大多数游戏引擎中。这是与底层 Flutter 框架的一个关键区别，在 Flutter 框架中，新的帧是在事件发生后绘制的，而不是持续更新的画布。

### 涉及多个对象

大多数游戏都有丰富的视觉对象，有些甚至使用花哨的粒子效果。这些对象使用大量的内存，所以游戏引擎通常会提供在适当的位置加载和处理它们的方法。这些技术有点类似于正常颤振框架状态的`initState()`和`dispose()`调用。

### 独立于底层组件

游戏一般不会使用底层框架的 UI 组件。例如，你不能在大多数引擎中使用 Flutter 小部件来构建游戏对象。游戏引擎通常在画布上渲染。这允许大量的对象同时出现在屏幕上。它还使得游戏能够跨平台统一移植，因为几乎所有的操作系统都支持画布渲染。

视觉游戏对象通常被称为精灵。这些可以是静态的，也可以是动态的，可以用多种方式创建。一个例子是 sprite 表。这实际上是一个由多个精灵或精灵版本组成的大型图像文件。单个图像可以被编辑和重新排序，以产生运动的错觉。精灵可以是呈现在基础画布上的 PNG 图像。

与基本图形相比，其他功能(如音频、手势检测和摄像头)通常会因游戏而异。

## 创建没有引擎的游戏

你能创建一个没有引擎的游戏吗？是的。你应该吗？在大多数情况下，不会。下面是为什么你不应该这么做的简短答案:绝对的数学地狱。

为了进一步澄清，我相信每个人都应该尝试在没有任何引擎帮助的情况下创建一个完整的游戏(至少一次)。这是艰苦的工作。这基本上包括从头开始编写一个基本的引擎。

尝试这种规模的项目时保持谨慎是可以理解的，但是尝试自己的实现会很有启发性。深入游戏系统的元素可以提供宝贵的经验。

[Flutter 可以用来创建一个游戏的基本组件](https://blog.logrocket.com/building-2d-game-flutter/)。跑马灯和计时器可用于创建游戏循环。你可以创建一个定位系统，构建一个精灵系统，计算碰撞，并根据元素位置添加你自己的手势检测。当然，这些都不容易。但是，这可能是一个有趣的练习。

在构建复杂的元素或功能时，游戏引擎有着明显的优势。任何需要高级功能的游戏(如 hitboxes、物理、音频支持和摄像机移动)使用一个可靠的引擎来设计会比试图从头开始编码容易得多。

## 火焰

[Flame](https://flame-engine.org) 是一个复杂、成熟的游戏开发框架，是目前最流行的 Flutter 游戏引擎。它支持设计基本游戏所需的一切，包括游戏循环、精灵和精灵表、碰撞检测和音频。Flame 还为更复杂的功能提供了几个补充包，如增强的音频支持、物理、SVG 支持和 [Rive](https://rive.app) 集成。

Flame 使用`GameWidget`将游戏添加到应用程序中:

```
GameWidget(
 game: game,
 loadingBuilder: (context) => const Material(
   child: Center(
     child: CircularProgressIndicator(),
   ),
 ),
 errorBuilder: (context, ex) {
   return Material(
     child: Text('Error'),
   );
 },
 overlayBuilderMap: {
   'victory': // Build victory overlay,
   'defeat': // Build defeat overlay
 },
),
```

参数允许我们灵活地定义游戏过程中可能需要的任何覆盖，比如胜利、失败或暂停菜单。`loadingBuilder`和`errorBuilder`参数可以用来让用户知道游戏正在加载，或者根据需要提供错误消息。

我们可以通过扩展`FlameGame`类来定义游戏本身`DemoGame`:

```
class DemoGame extends FlameGame {

  @override
  Future<void> onLoad() async {
    // Load sprites, camera, etc.
    return super.onLoad();
  }

}
```

我们用 mixins 定义能力和功能:

```
class DemoGame extends FlameGame with
HasCollidables,
HasTappables,
HasDraggables {

  @override
  Future<void> onLoad() async {
    // Load sprites, camera, etc.
    return super.onLoad();
  }

}
```

为了创建游戏对象，我们子类化任何具有`position`和`size`的组件类型。在这个例子中，我们子类化`PositionComponent`:

```
class DemoComponent extends PositionComponent with Hitbox, Collidable {

 DemoComponent({
   required Vector2 position,
   required Vector2 size,
 }) : super(position: position, size: size);

 @override
 Future<void> onLoad() async {
   await super.onLoad();
   // Initialize sprites, hitboxes
 }

 @override
 void render(Canvas canvas) {
   super.render(canvas);
   // Render objects
 }

}
```

我们也可以使用 Flame 的 [Forge2D](https://github.com/flame-engine/flame/tree/main/packages/flame_forge2d) 包给引擎添加 [Box2D](https://box2d.org) 物理。这个软件包提供了构建更复杂的游戏的功能，以更真实的运动为特色。

### Flame 与其他发动机相比如何

个人比较喜欢火焰引擎组件的代码结构和整齐分离的游戏逻辑。Flame 提供了几种类型的游戏对象，以及提供不同功能的各种 mixins。Flame 在其[网站](https://docs.flame-engine.org/1.0.0/)和[自述文件](https://github.com/flame-engine/flame/blob/main/doc/README.md)中提供了完整的文档和多个教程。

## 刺

[Quill](https://pub.dev/packages/quill) 是一个轻量级游戏引擎，它使用简单的组件，巧妙地称为`Feather`和`Quill`，来渲染游戏对象。

为了用 Quill 设计一个游戏，我们首先初始化一个新的`QuillEngine`并提供一个包含游戏引擎循环的`Feather`用于初始化、更新和处理。

```
void main() async {
  QuillEngine(Demo())..start();
}
```

接下来，我们扩展`Feather`类来获得游戏循环:

```
class Demo extends Feather {

  @override
  void init() {
    // Initialize 
  }

  @override
  void input(Event event) {
    // Handle input
  }

  @override
  void update(Time time) {
    // Update objects on new frame
  }
}
```

我们可以在`Feather`子类中创建一个`Sprite()`:

```
  Sprite _demo;

    _demo = new Sprite()
      ..initWithColor(const Color(0xFFFFFFFF))
      ..setPosition(0.0, 0.0)
      ..setSize(100.0, 100.0);

```

### 鹅毛笔如何与其他引擎相比

羽茎远不如火焰完整。有几个缺失的功能，比如音频和图像缓存，在[引擎的文档](https://pub.dev/packages/quill)中列出，作为未来版本的预定。此外，与其他引擎相比，Quill 的游戏对象似乎具有较少的代码分离。

[SpriteWidget](https://pub.dev/packages/spritewidget) 是一个工具包，可以用来在 Flutter 中创建动画和游戏。与其他游戏引擎解决方案相比，这个包与小部件层次结构配合得很好，使它看起来更像颤动(或“颤动 y”)。

SpriteWidget 可以用来创建 sprite 节点和节点图，这带来了一些非常有趣的可能性。例如，工具包的文档[描述了从不同的精灵创建一辆汽车](https://pub.dev/packages/spritewidget)，并通过偏移将轮子链接到基础汽车节点。SpriteWidget 还包含全面的动画技术，包括多个动画的序列和分组。

SpriteWidget 提供了几种有用的技术，并为处理游戏开发的许多方面提供了独特的解决方案。然而，它没有提供全套的游戏开发工具，也没有得到很好的维护。本文发表时的 [pub 分数](https://pub.dev/packages/spritewidget/score)反映了工具包的退化。

## 照明

我最近创建了一个自己的小型演示游戏引擎: [Illume](https://pub.dev/packages/illume) 。Illume 使用 Flutter 小部件作为游戏对象，并添加了一个简单的游戏循环。

在为本文研究 Flutter 游戏开发引擎时，我注意到大多数解决方案依赖于向画布添加精灵的技术。对于一个游戏引擎来说，这可能是最合理和最持久的解决方案，但我想尝试利用 Flutter 的“一切都是小部件”哲学。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我想建立一个引擎，更好地与普通的 Flutter 应用程序相结合，而不是完全与主要的小部件 UI 代码分离。在某种程度上，SpriteWidget 实现了这一点，但在技术上它使用了包装器而不是小部件。

要构建一个带有 Illume 的游戏，我们只需使用带有`IllumeController`参数的`Illume`小部件，它控制游戏的不同方面:

```
IllumeController gameController = IllumeController();

// Inside build
Illume(
    illumeController: gameController,
),
```

为了定义游戏对象，我们扩展了`GameObject`类。例如，我们可以使用下面的代码来初始化墙:

```
class Wall extends GameObject {
  int initialDistance;

  Wall(
    this.initialDistance,
  );

  @override
  Widget build(BuildContext context) {
    return Container(
      color: Colors.green,
      child: const Text('Demo'),
    );
  }

  @override
  void init() {
   // Init size, alignment, position, etc
  }

  @override
  void onCollision(List<Collision> collisions) {
    // Called when collisions occur
    // E.g: illumeController.stopGame();
  }

  @override
  void onScreenSizeChange(Vector2 size) {
    // Transform object positions on screen changed
  }

  @override
  void update(Duration delta) {
    // Update on new frame
  }
}
```

`GameObject`为每个对象提供位置、对齐和基本的基于盒子的碰撞检测，当碰撞发生时触发回调。`build`方法允许我们直接在 Flutter 小部件中创建一个对象。我们甚至可以使用 Flutter 的默认手势检测小工具。

### 照明与其他引擎相比如何

Illume 并不是要取代成熟的游戏引擎。小工具比画在画布上的精灵更重，所以 Illume 需要更多的内存来运行，并且目前缺乏复杂游戏功能的功能。不过，Illume 确实提供了一个简单的解决方案，可以使用小部件快速构建一个简单的游戏。

## 结论

在本文中，我们探讨了游戏引擎与底层框架的不同之处。我们还讨论了从头开始编写一个 Flutter 游戏或使用以下游戏引擎之一的利弊:Flame、Quill、SpriteWidget 或 Illume。

Flame 是目前唯一一款开发完善、维护良好的在 Flutter 上创建游戏的解决方案。其他游戏引擎提供了创造性的技术，但是还没有发展到可以用于成熟游戏的程度。我认为本文中描述的任何解决方案都适用于较小的游戏，但我建议将 Flame 用于开发较大的制作游戏。目前，Flutter 中的游戏开发仍然是一场单打独斗的比赛。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)