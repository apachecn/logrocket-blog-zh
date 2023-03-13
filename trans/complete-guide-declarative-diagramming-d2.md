# D2 声明性绘图完全指南

> 原文：<https://blog.logrocket.com/complete-guide-declarative-diagramming-d2/>

声明性图表使用声明性方法创建图表，从流程图到组织结构图。在声明性方法中，用户使用正式语言指定图表元素的关系和逻辑，而不是手动在画布上放置和连接形状。

结果图是根据用户的输入自动生成的。声明性图表对于创建难以手动创建的复杂图表或从存储在数据库或电子表格中的数据生成图表特别有用。

此外，声明性图表允许图表设计中的更大灵活性，以及在节省时间的同时根据需要容易地更新和改变图表的能力。

*向前跳跃*:

## 什么是 D2？

![D2 Header Image](img/788100f5173f285594dc49c01cd9907d.png)

[D2 (D2lang)](https://d2-lang.com/) 是一种特定领域的图表脚本语言，它将文本转换成图表。D2 采用声明式图表方法，并基于声明的代码语法(文本)生成图像。D2 不是面向设计师，而是面向工程师。使用 D2，您可以创建声明形状、连接、容器和特殊对象的图表。您还可以通过编程进行更多的定制，包括字符串、注释和重写。

D2 支持各种图表，包括流程图、状态图和实体关系图。D2 还提供了广泛的定制选项，允许用户改变图表的外观，以符合他们组织的品牌和需求，或者符合特定的标准。

您还可以使用 D2 创建自定义形状和元素，从而轻松创建准确反映项目特定需求的图表。

D2 是开源的，Terrastruct 项目支持它。Terrastruct 项目提供了一套工具，用于创建和维护基础设施代码。D2 语法简单易学，是 Terraform、Ansible 等其他工具的优秀补充。

## D2 入门

你需要在电脑上安装 D2 软件来将文本转换成图表。前往[安装文档页面](https://d2lang.com/tour/install)根据您计算机的资源和架构安装 D2。

如果你的电脑上安装了 Go 编程语言，下面是安装 D2 的方法。

```
go install oss.terrastruct.com/[email protected]

```

在安装目录中运行以下命令，验证 D2 是否正在运行:

```
# creates a D2 file and adds the text
echo 'x -> y' > input.d2

# runs the text as with D2 and outputs an SVG diagram
d2 -w input.d2 out.svg

```

该命令应该会启动服务器，打开默认的 web 浏览器，并显示下图:

![Displaying A D2 Diagram](img/7b0c40a4cd1345724752cd6c5acf1c3a.png)

如果你对这种方法有任何问题，你也可以选择用 CURL 脚本安装 D2。

或者，您可以使用提供常规 GUI 模式和 IDE 模式的 Terrastruct 控制台来编写和编译 D2 代码:

![The Terrastruct Console Provides A Mode To Write And Compile D2 Code](img/dc24aba0f096790c5fdc410d1255b433.png)

D2 还为流行的应用程序提供扩展和插件。还有 D2 [VSCode 扩展](https://d2lang.com/tour/vscode)和 [Vim](https://d2lang.com/tour/vim) 、[曜](https://d2lang.com/tour/obsidian)、 [Slack](https://d2lang.com/tour/slack) 和 [Discord](https://d2lang.com/tour/discord) 插件。D2 社区还为 Telegram、Postgres importer、Emacs 等应用程序中的 D2 支持构建了第三方插件和扩展。

## 用 D2 作图

D2 的声明性图表使用特定的语法规则，您必须遵循这些规则才能得到您想要的图表。

D2 支持大多数数据类型。在 D2，您可以通过在 diagram 指令后指定文本来编写 Hello World 或文本作为标签:

```
"Hello" -> "World!"

```

上面的语法声明了两个形状`Hello`和`World!`之间的连接。这些形状从`Hello`到`World!`用一条直线连接:

![The Syntax Declares A Connection Between Two Shapes](img/371f3cfcc10a1cc9d20e419fa2b9e07e.png)

### 在 D2 使用形状

您可以通过指定文本来声明 D2 中的形状。D2 的默认形状是正方形:

```
this

```

您可以使用分号在图表中声明多个形状:

```
this; that

```

正方形不是 D2 唯一的形状；您可以使用文本的形状字段创建一个[替代形状](https://d2lang.com/tour/shapes/):

```
Google Cloud.shape: cloud 
Myapp.shape: circle

```

您也可以用大括号将形状括在另一个形状中，并为父形状指定一个标识符:

```
big square: {
  Google Cloud.shape: cloud
  Myapp.shape: circle
}

```

![Working With Shapes In D2](img/f42d9a3ab6f4cfdd62990600a5f683c8.png)

### D2 的关系和标签

您可以在 D2 中使用连接运算符连接形状和文本，并通过在冒号(:)运算符后指定标签来标记连接。

D2 提供了四种连接运算符:

*   正向连接器`->`
*   直线连接器`--`
*   正向连接器`<-`
*   两侧连接器`<->`

以下是在 D2 使用上述运算符连接和标注形状的方法:

```
Google Cloud.shape: cloud
Microservice1.shape: circle

#  -> connects the google cloud shape to the Microservice1 shape with a "comment" label
Google Cloud -> Microservice1: Comments

Microservice2.shape: Parallelogram
AWS.shape: hexagon

# --  creates a straight line from the AWS shape to the Microservice2 shape

AWS -- Microservice2

Digital Ocean.shape: rectangle
Microservice3.shape: oval

#  -> connects the Microservice3 shape to the Digital ocean shape.

Digital Ocean <- Microservice3

Microservice4.shape: person
WebSocket.shape: callout

#  -> connects websocket and microservice4 shapes to each other with a "chat" label

WebSocket <-> Microservice4: Chat

```

![Connecting And Labeling Shapes In D2](img/967060171936d233e529943cebb6c806.png)

## 设计 D2 图表

样式和主题是图表的调味品。它们为所呈现的信息增加了额外的内容和多样性。D2 提供了向图中添加主题、样式和维度的功能。

### 向 D2 图添加主题

D2 支持许多主题，使您的图表看起来更专业。您可以将主题应用于特定的形状和表格。

您可以在 D2 CLI 上通过在命令中使用`-t`或`--theme`标志指定主题(主题编号)来设置主题。

您可以使用`d2`命令来指定主题，如下所示:

```
# specifies the usage of theme 101
d2 -t 101 input.d2 out.svg

```

下面是不同主题的安装图的输出:

![Using D2 To Specify Themes In Diagrams](img/d8189af2d3470676f36a8485cddfdab1.png)

或者，您可以使用`D2_THEME`环境变量来为图表指定主题:

```
D2_THEME=101 d2 input.d2 out.svg

```

### 自定义 D2 图

您可以使用`style`字段自定义形状和图表的样式。大多数样式使用 CSS 关键字并遵循 CSS 惯例。以下是 D2 目前提供的风格选项列表:

*   不透明
*   中风
*   填充(仅形状)
*   笔画宽度
*   划-划
*   阴影(仅形状)
*   字体大小
*   字体颜色
*   大胆的
*   意大利语族的
*   强调
*   划-划
*   边框半径(仅形状)
*   3D(仅矩形/正方形)
*   多个(仅形状)
*   动画(仅连接)

以下是如何使用样式字段来设置图表的样式:

```
x -> y: hi {
        # styles in the braces
  style: {
    opacity: 0.9
    stroke-dash: 3
    shadow: true
    font-size: 10
  }
}

```

![Styling Diagrams With Style Fields](img/d0bb464da850dd857fc912ef39038f62.png)

或者，使用`<element>.style.<style-option>`语法来设计图中特定元素的样式:

```
x -> y: hi
x.style.opacity: 0.9
x.style.stroke-dash: 3
x.style.shadow: true
x.style.font-size: 10
x.style.3d: true

```

![Styling Specific Elements Of Your Diagram](img/1528dcc00c2fe402dd2b86ea8e2cf78e.png)

### 向 D2 图添加维度

此外，您可以在您的图中使用关键字`width`和`height`来指定您的图的尺寸。您需要添加引号操作符`""`来指定维度:

```
print : "" {
  width: 50
  height: 50
}

```

以下是 dimensions 声明的结果:

![Result Of Dimensions Declaration](img/31ffdb6c9bdd829bf56562b5da5d7541.png)

### D2 的弦乐

D2 提供带引号和不带引号的字符串以增加灵活性；您可以在您的图中使用这两者。

使用字母数字值时，不带引号的字符串很方便。D2 修剪前导和尾随空白，所以你不必担心这一点:

```
My Name: Goodness
Name -> My Name

```

![Strings In D2](img/1d5d7477f42c9f65a8b955e7c3572892.png)

带引号的字符串对于使用符号很有用。单引号和双引号对 D2 有同样的效果:

```
"$$$$" -> '?/?/'

```

![Quoted Strings For Using Symbols](img/09896c3ac01876583fc0a82c51dfd550.png)

D2 使用 Bash 风格的注释语法。这意味着您可以用`#`符号对您的 D2 代码进行注释:

```
# top of line comment
name -> Goodness

name -> Goodness # end of line comment

```

在 D2，多行注释没有不同的语法。只需添加更多单行注释即可创建多行注释:

![Creating Multiline Comments In D2](img/80ad7cd72c4dc1895cf59cbfa93dd33a.png)

### D2 的覆盖

当你重新声明形状，D2 合并新的声明与前一个。最新的显式标签优先。

这里有一个在 D2 如何覆盖的例子:

```
name: Goodness {
  Language: English
}
name: Goodness {
  Book: Code
}

# is equivalent to this

name: Goodness {
  Language: English
  Book: Code
}

```

`name`用不同的数据声明了两次，D2 将它们合并成了形状:

![Overrides In D2](img/5a014205b8b7417b780cea88e80c27b4.png)

## 结论

在本文中，您了解了如何安装和开始使用声明性图表语言 D2。您学习了如何创建图表，以及如何设计它们的形状、主题和尺寸。

D2 在很多情况下都很有帮助，尤其是在自动生成随时间变化的图表方面。大多数 D2 用户在他们的文档和 ERD 图中使用这种语言。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)