# 在 Flutter 中构建可选容器

> 原文：<https://blog.logrocket.com/building-selectable-containers-flutter/>

有没有发现自己想要构建一个包含可选择容器的应用程序，并且想要通过给容器赋予不同的颜色来给它添加一些活力，这样它就可以在被点击之前显示为禁用状态？

那么，这就是您的教程，因为使用容器小部件无法实现这个特性。和我一起向你展示如何使用 Flutter 构建一个可选择的容器。

为了继续，我建议你对 Flutter 有很深的理解，因为我们将使用初学者可能不知道的小部件。

## 目标

在文章的最后，读者应该理解了`GestureDetector`小部件的主要用途以及如何在 Flutter 中构建一个可选择的容器。

我们开始吧！

## 入门指南

对于这个项目，我们将构建一个简单的应用程序，在这个应用程序中，当您选择一个容器时，背景会变成所选的颜色。使用`GestureDetector`小部件，我们将能够使用`onTap`方法打开和关闭这个特性。

为了避免混淆，我们将把代码分成三个不同的 dart 文件；“main.dart”、“gridcontainer.dart”和“gridview_itemview.dart”。

## 构建界面

### main.dart 文件

我们将从 main.dart 文件开始。这个文件中的默认代码是一个简单的计数器应用程序，通过单击 **Add** 按钮来增加。

我们不需要那个应用程序；因此，我们将删除该文件中除 material.dart 包和 main 函数之外的所有内容。我们将创建一个名为 **MyApp** 的无状态小部件，在其中，我们将覆盖构建函数，同时给它一个`BuildContext`类作为参数。

这个类的作用是处理部件树中每个部件的位置。我们还将返回一个`MaterialApp`,在这里我们将设置标题和 home(我们将在 gridcontainer.dart 文件中为它提供有状态小部件的值)。

main.dart 文件如下所示:

```
import 'package:flutter/material.dart';
import 'gridcontainer.dart';
void main() => runApp(const MyApp());
class MyApp extends StatelessWidget {
 const MyApp({Key? key}) : super(key: key);
 static const String _title = 'Flutter Code Sample';
 @override
 Widget build(BuildContext context) {
  return const MaterialApp(
   debugShowCheckedModeBanner: false,
   title: _title,
   home: GridContainer(),
  );
 }
}

```

### gridview_itemview.dart

在转到 gridcontainer.dart 文件之前，我们将向该文件添加一些代码。首先，我们将在代码中添加一个名为`GridViewItemView`的新的无状态小部件，后面是一些构建函数上面的构造函数变量。

```
 class GridViewItemView extends StatelessWidget {
 const GridViewItemView({ Key? key }) : super(key: key);
 @override
 Widget build(BuildContext context) {
  return Container(

  );
 }
}

```

构造函数将是一个名为 title(我们容器的标题)的字符串，名为 color(我们容器的颜色)的 color，以及设置为`onTap`的 VoidCallback。

```
 final String? title;
 final color? color;
 final VoidCallback? onTap;

```

添加完构造函数后，我们将把形参添加到我们的`GridViewItemView`中。现在，我们将把容器改为`GestureDetector`小部件(它允许我们添加`onTap`属性)。

接下来，我们将添加一个子属性，我们将赋予其值**中心**，在这个子属性中，我们将创建另一个子属性，并将其分配给**行**。在我们的**行**中，我们希望将`mainAxisAlignment`设置为中心，添加我们的文本，并设置文本样式。最后，我们将我们的`BuildContext`类定义为:

```
Widget build(BuildContext context) {
  return GestureDetector(
   onTap: onTap,
   child: Container(
    color: color,
    child: Center(
      child: Row(
     mainAxisAlignment: MainAxisAlignment.center,
     children: [
      Text(
       title.toString(),
       style: const TextStyle(color: Colors.white, fontSize: 20),
      ),
     ],
    )),
   ),
  );
 }

```

我们希望将`GridViewItemView`添加到 gridcontainer.dart 文件中。

### gridcontainer.dart 文件

我们将开始初始化我们的有状态小部件，在代码中使用名称`GridContainer`。

```
import 'package:flutter/material.dart';

class GridContainer extends StatefulWidget {
 const GridContainer({ Key? key }) : super(key: key);
 @override
 State<GridContainer> createState() => _GridContainerState();
}
class _GridContainerState extends State<GridContainer> {
 @override
 Widget build(BuildContext context) {
  return Scaffold(

  );
 }
}

```

我们将使用支架小部件代替容器小部件。接下来，我们将把`appBar`属性和`GridView`小部件(GridView.count()允许 GridView 指定行数和列数)添加到我们的 scaffold 主体中。

接下来，我们将指定以下 **GridView.count** 属性:`crossAxisCount` *、* `mainAxisSpacing`、`crossAxisSpacing`。

```
Widget build(BuildContext context) {
 return Scaffold(
  appBar: AppBar(
   title: const Text('Selectable Container'),
  ),
  body: GridView.count(
   crossAxisCount: 3,
   padding: const EdgeInsets.only(top: 16, left: 16, right: 16),
   mainAxisSpacing: 16,
   crossAxisSpacing: 16,
 );
}

```

现在，我们想给我们创建的容器赋予一种颜色。为此，我们将创建四个变量；红色、紫色、橙色(用于容器)和背景(我们将设置其不透明度以区分网格和背景)。

```
const red = Color.fromARGB(255, 255, 17, 0);
const purple = Colors.purple;
const orange = Colors.deepOrange;
const background = Colors.white;

```

回到我们的支架小部件，我们将添加我们的孩子 ***、*** ，后面跟着三个`GestureDetector`小部件，每个小部件包含一个子小部件`GridViewItemView`。这些子部件还将包含一个标题和一个颜色属性(我们之前创建的名称和常量)。

```
children: <Widget>[
 GestureDetector(
  child: const GridViewItemView(
   title: 'red',
   color: red,
  )),
 GestureDetector(
  child: GridViewItemView(
   title: 'Orange',
   color: orange,
  )),
 GestureDetector(
  child: GridViewItemView(
   title: 'purple',
   color: purple,
  )),
],

```

下面是目前应用程序的界面:

![Application interface displays three selectable containers in red orange and purple](img/27bdaf698d6ca5a8b6810ce2b6fe2fa7.png)

container

我们的应用程序目前只显示容器。在下一节中，我们将实现一些功能，这些功能允许我们改变背景颜色并一次选择一个容器。

## 实现功能

在本节中，我们将实现两个功能:

1.  非活动容器(当选择另一个容器时)
2.  背景颜色变化(`onTap`属性)

### 非活动容器

前面，我们创建了四个变量，其中三个属于容器，最后一个属于背景。

类似地，我们将为我们的容器创建三个新变量，并命名为；`inactivered`、`inactivepurple`和`inactiveorange`。

然后，我们将为它们分配颜色(相似的颜色，但更暗的阴影)，一旦它们对应的容器处于非活动状态，这些颜色就会生效。

```
const inactivered = Color.fromARGB(255, 160, 13, 3);
const inactivepurple = Color.fromARGB(255, 74, 18, 83);
const inactiveorange = Color.fromARGB(255, 165, 56, 22);

```

为了完全实现这个特性，我们将创建一些`if`语句，但是在继续之前，我们将使用 Dart 枚举来定义我们的命名常量。

为此，我们将使用 enum 关键字创建枚举，并将其命名为 **Colour。**然后，我们将赋值；红色、橙色和紫色。这是它的样子:

```
enum Colour {
 red,
 orange,
 purple,
}

```

(注意:我们将在稍后创建的方法中调用这些值)

现在，我们希望所有容器都以非活动颜色开始，当我们点击容器时，它应该会变为活动颜色。

为此，我们将进入状态小部件，创建四个名为“color”的变量；`redColour`、`orangeColour`、`purpleColour`、`mainbackground`。

接下来，我们将变量分别设置为`inactivered`、`inactiveorange`、`inactivepurple`和`background`。

然后，我们将前三个变量设置为我们的子小部件中容器颜色的初始值(改变之前分配给它的颜色)。

```
Color redColour = inactivered;
Color orangeColour = inactiveorange;
Color purpleColour = inactivepurple;
Color mainbackground = background;

```

![Containers are shown in inactive colors](img/58b589f03802a469d5f2d4faf5e84b22.png)

inactive container

(注意:我们的状态小部件是可变的，也就是说，它的属性可以改变和变异，这意味着这个类的属性不一定是最终的)

最后，为了让我们的容器随时改变，我们将创建一个带有输入**颜色** `selectedcolour`的`updatecolour`方法，它将指定所选择的颜色。

接下来，我们将使用一个`if`语句来检查`selectedcolour`是否为红色。在此之后，我们将使用 if 语句来检查`redColour`是否等于`inactivered`。如果为真，我们会将其更改为活动颜色(红色)，并将其他颜色(`orangeColour` *、*、`purpleColour`)设置为非活动颜色(`inactivepurple`、inactiveorange)。

否则，如果它是活动卡的颜色，我们会将其更改为非活动卡的颜色。对其他容器遵循相同的步骤，我们可以进入下一阶段。

```
void updatecolour(Colour selectedcolour) {
 if (selectedcolour == Colour.red) {
  if (redColour == inactivered) {
   redColour = red;
   orangeColour = inactiveorange;
   purpleColour = inactivepurple;
  } else {
   redColour = inactivered;
  }
 }
 if (selectedcolour == Colour.orange) {
  if (orangeColour == inactiveorange) {
   orangeColour = orange;
   redColour = inactivered;
   purpleColour = inactivepurple;
  } else {
   orangeColour = inactiveorange;
  }
 }
 if (selectedcolour == Colour.purple) {
  if (purpleColour == inactivepurple) {
   redColour = inactivered;
   orangeColour = inactiveorange;
   purpleColour = purple;
  } else {
   purpleColour = inactivepurple;
  }
 }
}

```

在创建了`if`语句之后，我们将前往我们的`GestureDetector`小部件并创建一个`onTap`属性。在这个属性中，我们将使用一个`setState`来更新我们的颜色属性的状态。现在，我们将调用上面的方法并设置其对应的`selectedcolour`。

```
// red
onTap: () {
 setState(() {
  updatecolour(Colour.red);
 });
}
// orange
onTap: () {
 setState(() {
  updatecolour(Colour.orange);
 });
}
// purple
onTap: () {
 setState(() {
  updatecolour(Colour.purple);
 });
}

```

下面是应用程序目前的样子:

![Red container is selected and displays brighter color while the other two containers are more muted](img/fc36334587cd202b29e7d192cfd3b3b6.png)

selected container(red)

### 背景颜色变化

这一部分包含了应用程序的最后一部分，实现这里的特性相当容易。在我们的`setState`中，我们将添加条件 if 语句来检查`redColour`是否等于活动颜色(红色)。

如果为真，`mainbackground`将变为活动颜色，如果为假，将变为背景颜色。对其他两个容器实现相同的逻辑，我们应该得到如下结果:

```
if (redColour == red) {
 mainbackground = red;
} else {
 mainbackground = background;
}

```

![Main background is red while red container is selected](img/f51167f4e65455c9982ffd29627bfbab.png)

selected background(red)

以下是 gridview_itemview.dart 文件的完整代码:

```
import 'package:flutter/material.dart';
class GridViewItemView extends StatelessWidget {
 const GridViewItemView(
   {Key? key,
   this.title,
   this.color,
   this.onTap,})
   : super(key: key);
 final String? title;
 final Color? color;
 final VoidCallback? onTap;
 @override
 Widget build(BuildContext context) {
  return GestureDetector(
   onTap: onTap,
   child: Container(
    color: color,
    child: Center(
      child: Row(
     mainAxisAlignment: MainAxisAlignment.center,
     children: [
      Text(
       title.toString(),
       style: const TextStyle(color: Colors.white, fontSize: 20),
      ),
     ],
    )),
   ),
  );
 }
}

```

以下是 gridcontainer.dart 文件的完整代码:

```
import 'package:flutter/material.dart';
import 'package:testproject/gridview_itemview.dart';
const red = Color.fromARGB(255, 255, 17, 0);
const inactivered = Color.fromARGB(255, 160, 13, 3);
const purple = Colors.purple;
const inactivepurple = Color.fromARGB(255, 74, 18, 83);
const orange = Colors.deepOrange;
const inactiveorange = Color.fromARGB(255, 165, 56, 22);
const background = Colors.white;
enum Colour {
 red,
 orange,
 purple,
}
class GridContainer extends StatefulWidget {
 const GridContainer({Key? key}) : super(key: key);
 @override
 State<GridContainer> createState() => _GridContainerState();
}
class _GridContainerState extends State<GridContainer> {
 Color redColour = inactivered;
 Color orangeColour = inactiveorange;
 Color purpleColour = inactivepurple;
 Color mainbackground = background;
 void updatecolour(Colour selectedcolour) {
  if (selectedcolour == Colour.red) {
   if (redColour == inactivered) {
    redColour = red;
    orangeColour = inactiveorange;
    purpleColour = inactivepurple;
   } else {
    redColour = inactivered;
   }
  }
  if (selectedcolour == Colour.orange) {
   if (orangeColour == inactiveorange) {
    orangeColour = orange;
    redColour = inactivered;
    purpleColour = inactivepurple;
   } else {
    orangeColour = inactiveorange;
   }
  }
  if (selectedcolour == Colour.purple) {
   if (purpleColour == inactivepurple) {
    redColour = inactivered;
    orangeColour = inactiveorange;
    purpleColour = purple;
   } else {
    purpleColour = inactivepurple;
   }
  }
 }
 @override
 Widget build(BuildContext context) {
  return Scaffold(
   backgroundColor: mainbackground.withOpacity(0.7),
   appBar: AppBar(
    title: const Text('Selectable Container'),
   ),
   body: GridView.count(
    crossAxisCount: 3,
    padding: const EdgeInsets.only(top: 16, left: 16, right: 16),
    mainAxisSpacing: 16,
    crossAxisSpacing: 16,
    children: <Widget>[
     GestureDetector(
       onTap: () {
        setState(() {
         updatecolour(Colour.red);
         if (redColour == red) {
          mainbackground = red;
         } else {
          mainbackground = background;
         }
        });
       },
       child: GridViewItemView(
        title: 'red',
        color: redColour,
       )),
     GestureDetector(
      onTap: () {
       setState(() {
        updatecolour(Colour.orange);
        if (orangeColour == orange) {
         mainbackground = orange;
        } else {
         mainbackground = background;
        }
       });
      },
      child: GridViewItemView(
       title: 'Orange',
       color: orangeColour,
      ),
     ),
     GestureDetector(
      onTap: () {
       setState(() {
        updatecolour(Colour.purple);
        if (purpleColour == purple) {
         mainbackground = purple;
        } else {
         mainbackground = background;
        }
       });
      },
      child: GridViewItemView(
       title: 'purple',
       color: purpleColour,
      ),
     ),
    ],
   ),
  );
 }
}

```

## 结论

我们已经结束了教程，我希望你现在已经学会了如何在 Flutter 中使用`GestureDetector`构建可选容器。感谢您的关注，祝您编码愉快！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)