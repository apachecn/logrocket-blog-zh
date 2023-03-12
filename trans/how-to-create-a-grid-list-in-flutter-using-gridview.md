# 如何使用 GridView - LogRocket Blog 在 Flutter 中创建网格列表

> 原文：<https://blog.logrocket.com/how-to-create-a-grid-list-in-flutter-using-gridview/>

在 Flutter 中开发应用程序时，您可能会遇到将项目列表显示为网格的需求。您可以使用 Flutter 中的 [GridView 类](https://api.flutter.dev/flutter/widgets/GridView-class.html)以网格格式显示数据——即，用行和列。

使用 GridView 是组合[行](https://api.flutter.dev/flutter/widgets/Row-class.html)和[列](https://api.flutter.dev/flutter/widgets/Column-class.html)类并创建可滚动网格列表的最佳方式。一个常见的用例是显示照片列表，比如在谷歌和苹果的原生照片应用程序中。

在本教程中，我们将演示如何在您的 Flutter 应用程序中实现 GridView。我们还将浏览一些实际的例子，以便您可以看到 GridView 的实际应用。

我们将详细介绍以下内容:

如果你是视觉学习者，看看这个快速视频教程:

 [https://www.youtube.com/embed/bLOtZDTm4H8?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/bLOtZDTm4H8?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

## 什么是 GridView？

在 Flutter 中，GridView 是一个小部件，它以 2D 数组的形式显示项目列表。简单地说，这些项目以表格的形式显示。

与普通列表不同，在普通列表中，项只在一个方向上呈现，GridView 同时在水平和垂直方向上呈现项。下图展示了 GridView 与 Flutter 应用程序中普通列表的不同之处:

![GridView in Flutter](img/150ccec69edc5385ab7cca710bffe168.png)

下面是启动和运行 GridView 的最少代码:

```
GridView(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 3,
  ),
  children: [
    Image.network('https://picsum.photos/250?image=1'),
    Image.network('https://picsum.photos/250?image=2'),
    Image.network('https://picsum.photos/250?image=3'),
    Image.network('https://picsum.photos/250?image=4'),
  ],
)

```

`gridDelegate`是控制列表中项目显示方式的属性。在我们的例子中，它被赋予一个`SliverGridDelegateWithFixedCrossAxisCount()`，而`crossAxisCount`被设置为`3`。这意味着如果滚动方向是垂直的，我们希望水平显示三个项目，如果滚动方向是水平的，我们希望垂直显示三个项目。任何列表的默认滚动方向都是垂直的，因此项目是水平显示的。

`children`指此处给出的项目列表。它接受任何小部件的列表，所以你可以显示任何你想在屏幕上出现的东西。

以下是输出:

![GridView Demo](img/2bf5165946da2d06e5701683c065146d.png)

下面是代码如何被翻译成用户界面:

![Code Translated to UI](img/b7de523ab4247bf3bcd5e18143485de6.png)

## GridView 属性

让我们来看看 GridView 的一些属性。

### `crossAxisSpacing`

设置该属性的值允许您在横轴上的项目之间放置一个空格。这意味着如果滚动方向是垂直的，空间将水平显示。

```
GridView(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
      crossAxisCount: 3, 
      crossAxisSpacing: 16),
  children: [
    Image.network('https://picsum.photos/250?image=1'),
    Image.network('https://picsum.photos/250?image=2'),
    Image.network('https://picsum.photos/250?image=3'),
)

```

![CrossAxisSpacing](img/71374fe6a9e38c872cf358d5512f9828.png)

### `mainAxisSpacing`

主轴是指列表滚动的轴。使用`mainAxisSpacing`属性给出滚动方向上项目之间的间距。

```
GridView(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
      crossAxisCount: 3, 
      mainAxisSpacing: 16),
  children: [
    Image.network('https://picsum.photos/250?image=1'),
    Image.network('https://picsum.photos/250?image=2'),
    Image.network('https://picsum.photos/250?image=3'),
)

```

![MainAxisSpacing](img/c6ef1b425523f200705042b338e0a2fc.png)

### `scrollDirection`

当 GridView 以横向模式显示时，您可能希望更改滚动方向。将`scrollDirection`设置为`Axis.horizontal`将会做到这一点。

```
GridView(
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 3,
  ),
  scrollDirection: Axis.horizontal,
  children: [
    ...
  ],
)

```

![GridView Demo Horizontal](img/a2a029d622f80f68a27b560f2a7a78a1.png)

### `physics`

此属性允许您设置列表的滚动行为。您可能根本不希望列表滚动。比方说，你正在展示一幅拼贴画。您可以通过将`physics`值设置为`NeverScrollableScrollPhysics()`来禁用滚动。默认情况下，Android 使用`ClampingScrollPhysics()`, iOS 使用`BouncingScrollPhysics()`,如下所示:

![Physics Comparison](img/892aef38a662811556cf350fb0683d77.png)

### `shrinkWrap`

将`shrinkWrap`值设置为`true`会导致 GridView 只占用所需的空间来填充滚动方向上的项目。默认为`false`，即使项目不在列表中，也会保留整个屏幕:

```
/////shrinkWrap: false,
Column(
  children: [
    Expanded(
      child: GridView(
        gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
          crossAxisCount: 3,
        ),
        shrinkWrap: false,
        children: [... ],
      ),
    ),
    ElevatedButton(onPressed: () {}, child: Text('Close'))
  ],
)
/////shrinkWrap: true,
Column(
  children: [
    GridView(
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 3,
      ),
      shrinkWrap: true,
      children: [...],
    ),
    ElevatedButton(onPressed: () {}, child: Text('Close'))
  ],
)

```

当`shrinkWrap`为`false`时，我们需要将 GridView 包装在扩展的小部件中，以便它占用所有可用空间。否则，它将抛出一个错误。

## 显示项目数量固定的列表

Flutter 有一个名为`GridView.count()`的构造函数，用于在 GridView 中只显示几个项目。此构造函数使您能够创建具有固定项数的 GridView。它还简化了在横轴上指定多个项目的方法。

示例代码如下所示:

```
GridView.count(
  crossAxisCount: 3,
  children: [
    ...
  ],
)

```

要在横轴中显示的项目数被分配给`crossAxisCount`属性。如果你仔细观察，你会发现我们不再需要`SliverGridDelegateWith FixedCrossAxisCount()`了。

`GridView.count()`可用于创建如下键盘用户界面:

![Keypad UI](img/dddffc666d64d31f7aa9fd6e33c835b1.png)

以下是上述设计的代码:

```
GridView.count(
  crossAxisCount: 3,
  shrinkWrap: true,
  padding: EdgeInsets.only(left: 24, right: 24),
  children: [
    DialKey(
      number: '1',
      letters: '',
    ),
...
  ],
)

```

`shrinkWrap`属性被设置为`true`，这导致 GridView 在屏幕上释放一些空间。

`DialKey()`是一个显示单个键的自定义小部件。看起来是这样的:

```
// DialKey widget
class DialKey extends StatelessWidget {
  final String number;
  final String letters;

  DialKey({this.number, this.letters});

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Container(
        width: 80,
        height: 80,
        child: FloatingActionButton(
          onPressed: () {},
          backgroundColor: Colors.grey.withOpacity(0.5),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              Text(
                '$number',
                style: TextStyle(
                    color: Colors.white,
                    fontSize: 24,
                    fontWeight: FontWeight.bold),
              ),
              Text(
                '$letters',
                style: TextStyle(
                    color: Colors.white,
                    fontSize: 16,
                    fontWeight: FontWeight.bold),
              )
            ],
          ),
        ),
      ),
    );
  }
}

```

## 在 GridView 中显示长列表

要显示可能来自数据库的长列表或无限数量的项目，您需要`GridView.builder()`构造函数。

下面是示例代码:

```
GridView.builder(
  itemCount: 100,
  itemBuilder: (context, index) => ItemTile(index),
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 2,
  ),
)

```

`itemCount`代表物品的数量。这有助于 GridView 估计最大滚动范围。

`itemBuilder`根据当前索引创建给定的小部件。

让我们尝试建立一个产品列表，如下所示:

![Product Listing](img/5a41ae83fbab276f1222cdc538edf3d1.png)

代码如下:

```
GridView.builder(
  itemCount: 100,
  itemBuilder: (context, index) => ItemTile(index),
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 2,
    childAspectRatio: 2,
  ),
)
class ItemTile extends StatelessWidget {
  final int itemNo;

  const ItemTile(
    this.itemNo,
  );

  @override
  Widget build(BuildContext context) {
    final Color color = Colors.primaries[itemNo % Colors.primaries.length];
    return Padding(
      padding: const EdgeInsets.all(8.0),
      child: ListTile(
        tileColor: color.withOpacity(0.3),
        onTap: () {},
        leading: Container(
          width: 50,
          height: 30,
          color: color.withOpacity(0.5),
          child: Placeholder(
            color: color,
          ),
        ),
        title: Text(
          'Product $itemNo',
          key: Key('text_$itemNo'),
        ),
      ),
    );
  }
}

```

在上面的代码中需要注意的一件重要事情是`childAspectRatio`属性。这可用于调整项目的高度，如下所示:

![ChildAspectRatio Comparison](img/d7c3179ecfb22aefd2274683ef914344.png)

## 构建响应式 GridView

随着 [Flutter 2.0](https://blog.logrocket.com/whats-new-in-flutter-2-0/) 的发布，除了移动应用之外，你现在还可以开发网络和桌面应用。当构建跨平台的应用程序时，你要确保通过创造最好的用户体验来迎合网络用户。在这种情况下，当网格显示在更大的屏幕上时，在网格上显示更多的项目可以大大提高 web 用户的 UX。

让我们修改前面的代码，以便在更大的屏幕上显示时，在横轴上显示更多的项目:

```
//Before
GridView.builder(
  itemCount: 100,
  itemBuilder: (context, index) => ItemTile(index),
  gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
    crossAxisCount: 2,
    childAspectRatio: 2,
  ),
)
//After
LayoutBuilder(builder: (context, constraints) {
  return GridView.builder(
    itemCount: 100,
    itemBuilder: (context, index) => ItemTile(index),
    gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
      crossAxisCount: constraints.maxWidth > 700 ? 4 : 1,
      childAspectRatio: 5,
    ),
  );
})

```

将 GridView 包装在`LayoutBuilder`中。`LayoutBuilder`提供了`constraints`，可以用来确定宽度和高度。使用这些约束，我们可以构建各种用户界面。

对于我们的例子，每当屏幕分辨率的宽度变为 700 或更大时，我们将在横轴上显示四个项目。

以下是输出:

![GridView Demo Output](img/f91d76038c8806453ff547df03bbe6aa.png)

## 结论

如果您已经做到了这一步，那么您应该已经掌握了使用 GridView 在 Flutter 中创建复杂且引人入胜的网格列表的所有必备技能和基础知识。

本例使用的完整代码可从 [GitHub](https://github.com/pinkeshdarji/flutter_gridview) 获得。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)