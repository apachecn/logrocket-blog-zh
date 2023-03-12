# Hucssley 实用程序类如何改进前端开发

> 原文：<https://blog.logrocket.com/hucssley-utility-classes/>

虽然功能是 web 开发中最重要的方面，但是创造一个美观的用户体验也是非常重要的。然而，造型可能是一项既费力又耗时的任务。在某些情况下，人们可能会在花费大量时间处理某个组件的外观之前快速实现所需的功能。这就是 CSS 框架非常有用的地方。

大多数 CSS 框架，比如 Bootstrap 和 Semantic UI，都带有预构建的组件，开发人员可以快速使用这些组件来加快开发速度。

然而，哈克斯利做的事情有点不同。由于没有预构建的组件，Hucssley 是一个实用程序类库，而是遵循其他类似库(如 [Tailwind](https://tailwindcss.com/) 和 [Tachyons](https://tachyons.io/) )的路线，提供了各种实用程序类，开发人员可以使用它们和基本 HTML 元素一起快速创建组件。

## 为什么要用哈克斯利？

Hucssley 基于 [SASS](https://sass-lang.com/) ，提供原子实用程序类来帮助快速创建一致且高性能的 UI 组件。虽然这可能意味着我们必须自己创建所有的组件，但 Hucssley 比传统的 UI 组件库拥有更大的优势，因为它是完全可定制的，不需要第三方的支持。

Hucssley 团队围绕五个关键目标构建了它，正如他们在[文档](https://github.com/stowball/hucssley/blob/master/README.md)中所定义的:

1.  通过提供一个基本上与现有 CSS 属性 1:1 映射的原子类系统，非常容易学习和使用
2.  允许任何技能的人快速构建 web，而不会不知不觉地导致 CSS 膨胀或违背 CSS 的一些核心原则，但有时很难理解
3.  提供为任何断点、用户交互或 UI 状态构建 UI 所需的工具
4.  完全与平台无关，可在前端堆栈之间移植，Sass 是唯一的依赖
5.  高度灵活地满足您的需求，能够轻松定制现有的类和创建新的类

hucsley 背后的核心思想是使它尽可能地简单和可采用，因为它的语法尽可能地接近 CSS，这使得 hucsley 比其他 CSS 实用程序库更容易学习，或者如文档中的[所述:](https://github.com/stowball/hucssley/blob/master/README.md)

> "如果你知道 CSS 属性，你就知道 Hucssley "

Hucssley 的目标是简化样式化过程，而不考虑 CSS 方面的专业知识，同时避免膨胀并简化组件和状态的命名过程。Hucssley 还处理任何可能因使用某些 CSS 选择器而引起的特异性战争，这使得在调整某些属性时更容易避免不必要的副作用。

Hucssley 也是平台不可知的，可以很容易地插入任何 web 开发框架，如 React、Vue 或 Angular，甚至可以用于 HTML 电子邮件。

在构建设计系统组件时使用像 Hucssley 这样的实用类库，可以允许跨组件的一致用户体验的快速开发。

如果你想了解更多关于 Hucssley 如何试图解决与 CSS 相关的问题，他们的文档中有一部分是关于他们如何试图[重新思考 CSS](https://github.com/stowball/hucssley/blob/master/rethinking-css.md) 的。

## 安装 Hucssley

目前，Hucssley 仍然处于早期开发阶段，因此，它还没有在 npm 上发表。因此，您需要通过运行以下命令直接从 GitHub 安装它:

```
npm install github:stowball/hucssley#master
```

或者用[纱](https://yarnpkg.com/):

```
yarn add github:stowball/hucssley#master
```

您可以像这样将 Hucssley 导入到您的 SASS 文件中，从而在您的文件中不做任何定制地使用它:

```
@import "path_to_node_modules/hucssley/src/index";
```

但是，如果您希望对其进行自定义，您将需要导入 Hucssley 文件，然后导入您自己的自定义文件，如下所示，这将用您的新文件覆盖默认配置:

```
@import "path_to_node_modules/hucssley/src/helpers";

@import "path_to_node_modules/hucssley/src/variables/global/index";
// @import "custom/variables/global/index";

@import "path_to_node_modules/hucssley/src/variables/classes/index";
// @import "custom/variables/classes/index";
// set class overrides before if you don’t need access to the defaults & want changes to flow through referenced vars

@import "path_to_node_modules/hucssley/src/variables/reset/index";
// @import "custom/variables/reset/index";

@import "path_to_node_modules/hucssley/src/styles";
// @import "custom/classes/index";
```

在使用 webpack 的项目中，可以用 [Sass loader](https://github.com/webpack-contrib/sass-loader#imports) 加载 Hucssley。

如果您使用 npm，可以通过重新安装软件包将 Hucssley 升级到最新版本。对于 yarn，情况稍微复杂一些，因为运行安装命令不会自动更新任何文件，相反，您应该运行以下命令:

```
  yarn upgrade hucssley
```

就这样，你就可以在你的项目中使用 Hucssley 了！

## 用途和功能

通过将 Hucssley 的实用程序类作为常规类添加到 HTML 元素中来使用它们，如使用以下代码的文档中的这个[现场演示](https://codepen.io/stowball/full/WBoxOj)所示:

```
<div class="
  background-color:blue-0
  padding:500
">
  <div class="
    align-items:center
    background-color:neutral-0
    border-color:neutral-200
    border-radius:500
    border-style:solid
    border-width:100
    display:flex
    flex-direction:column
    padding:500
    text-align:center
    @mq-768--flex-direction:row
    @mq-768--text-align:left
  ">
   <img
      alt=""
      class="
        background-color:blue-600
        border-color:neutral-100
        border-radius:1000
        border-style:solid
        border-width:200
        margin-bottom:400
        width:50%
        @mq-600--width:30%
        @mq-768--margin-bottom:0
        @mq-768--margin-right:500
        @mq-768--width:20%
      "
      src="https://hireup.cdn.prismic.io/hireup/89e15301c28e6396927d85e38e9c5d5833ebab09_kyle_357-bonnie.png"
    />
    <div>
      <p class="
        font-size:600
        font-weight:700
        line-height:200
        margin-bottom:400
      ">
        Disability support workers who love what you love
      </p>
      <a
        class="
          background-color:blue-900
          border-color:neutral-700
          color:neutral-0
          display:inline-block
          padding-horizontal:400
          padding-vertical:300
          transition-duration:200
          transition-easing:ease
          transition-property:all
          &:hocus--background-color:blue-600
          &:hocus--scale:105
          @mq-768--padding-horizontal:500
          @mq-768--padding-vertical:400
        "
        href="#"
      >
        Find your connection
      </a>
    </div>
  </div>
</div>
```

仅仅通过一个普通的 HTML 文件，我们就可以看到建立一个响应性强、可访问的交互式组件是多么容易，这个组件可以用来在卡片上显示用户的个人资料。这些都是用普通的 HTML 和 Hucssley 完成的。

这个演示展示了 Hucssley 开箱即用的一些优秀特性，让我们看看这些关键特性以及它们如何帮助您的开发过程。

## 模块

模块允许开发人员生成伪类和自定义父类。Hucssley 课程都有这些核心模块:

*   Hucssley 中的基类大致等同于 CSS 属性和值的标准版本。例如`align-items: flex-end`可通过基类`.align-items:flex-end`获得
    *   属性是类名，其值跟在`:`之后
    *   有些类别与默认类别略有不同，它们是:

```
animation-count -> animation-iteration-count
animation-easing -> animation-timing-function
animation-mode -> animation-fill-mode
animation-state -> animation-play-state
content -> ::[pseudo]-content
momentum-scrolling -> -webkit-overflow-scrolling
pos-[bottom,left,right,top] -> bottom,left,right,top
rotate -> transform: rotate
scale -> transform: scale
size -> height & width
svg-fill-color -> fill
svg-fill-rule -> fill-rule
svg-stroke-color -> stroke
transition-easing -> transition-timing-function
translate-x -> transform: translateX
translate-y -> transform: translateY
```

然而，上面所有的别名——以及所有其他的类别名——都可以根据您的喜好进行更改。请阅读[文档](https://github.com/stowball/hucssley/blob/master/hucssley-classes.md#aliases)了解更多详情。

*   `hocus`—`hover`和`focus`伪类的快捷模块
*   `focus`、`hover`、`active`和`visited`——代表它们对应的伪类
*   `reduced-motion`–为喜欢较少动作页面的用户提供辅助功能(a11y)
*   `responsive`–帮助构建适应各种显示器的移动优先体验

## 状态模块

这些允许你按照这个模式`[state-name]--[base-class]`根据特定的状态改变 UI。对于要激活的状态类，需要将原始状态名作为一个类添加，如下所示:

```
<div class="
  display:none
  //add the state class
  is-open--display:block
  // add the state name to activate the class
  is-open
">
  //add some UI
</div>
```

## 分组模块

使用组，当与通用父元素(`:focu`和/或`:hover`)交互时，或者当它处于特定的 UI 状态时，您可以设置子元素的样式。他们的语法是`group[group-type]__[base-class]`:

```
.group:focus__scale:110
.group:hover__scale:110
.group:hocus__scale:110 // both :focus and :hover
.group-is-selected__background-color:blue-300
```

就像状态模块一样，原始状态名需要与被赋予了一个`.group`类的父元素一起应用:

```
<html>
  …
  <div class="
    group
+   is-selected
  ">
    <ul class="
      group:hover__scale:110
      group-is-selected__background-color:blue-300
    "></ul>
  </div>
</html>
```

模块可以组合起来，创造各种各样的体验。

## 天平

哈克斯利倾向于用千年(0-1000)来代表价值观，而不是名字。这使得声明某些属性的值的方法更加统一。但是，您可以根据自己的喜好自由定制，并且可以根据自己的喜好将刻度值指定给一个名称。

默认情况下，以下类使用千年标度:

*   `background-color`
*   `border-color`
*   `border-radius`
*   `border-width`
*   `color`
*   `font-size`
*   `letter-spacing`
*   `line-height`
*   `margin`
*   `padding`
*   `transition-delay`
*   `transition-duration`

而其他的，像`opacity`和`scale`使用与你的概念模型更相关的值。

## 配置

Hucssley 很容易定制，但是，你需要熟悉 Sass 的基本语法([变量](https://sass-lang.com/documentation/variables)、[列表](https://sass-lang.com/documentation/values/lists)和[地图](https://sass-lang.com/documentation/values/maps))。按照文档中的定义，配置分为三个部分:

*   **重置**配置使用普通变量定制“通用”样式，比如是否默认使用`box-sizing: border-box`
*   全局配置主要使用地图来处理默认的媒体查询、颜色、间距、用户界面状态和主题
*   **类**提供列表和映射变量来调整模块，以及每个类单独的值。一些类(比如那些处理颜色的类)默认继承自同一个基本变量，所以只需要一个改变就可以影响所有的`border-color`、`background-color`和`color`类。所有的类都可以在上述的各个模块中生成

Hucssley 提供了几个助手函数来帮助定制，您可以在文档的[这一节中了解更多信息。](https://github.com/stowball/hucssley#configuration-helpers)

这些自定义的帮助函数也可以用来创建你自己的主题，如本演示中所示。这还不是全部，您还可以使用这些助手来[创建定制类](https://github.com/stowball/hucssley#creating-custom-classes)以实现您想要的 UI。

Hucssley 类有意具有较低的特异性计数，但是当将其集成到现有项目中时，这可能会导致冲突。为了帮助解决这个问题，Hucssley 为开发人员提供了两种方法来提高特异性:

*   在导入重置和类样式之前，使用`$hu-use-important: true;`强制`!important`:

```
$hu-use-important: true;

@import "path_to_node_modules/hucssley/src/styles";
// @import "custom/classes/index";
```

*   用派生选择器分隔类样式。Hucssley 是用 Sass 编写的，这意味着您可以轻松地将您的导入包装在一个新的选择器中，以将每个类转换为使用派生选择器:

```
.hucssley {
  @import "path_to_node_modules/hucssley/src/styles";
  // @import "custom/classes/index";
}
```

这将产生:

```
.hucssley .align-content:baseline {
  align-content: baseline;
}

.hucssley .align-content:center {
  align-content: center;
}
```

## 结论

Huccsley 非常注重允许开发人员利用他们以前的 CSS 知识，它非常容易掌握，有助于解决与普通 CSS 相关的问题。这些只是 Hucssley 内置的许多功能中的一部分。这是一项令人兴奋的技术，一旦它离开测试版，将会在 web 开发中掀起波澜，看到它将如何帮助开发人员使用其广泛的工具套件创建出色的体验将是令人兴奋的。你可以在文档中阅读更多关于 Hucssley 还能提供什么的细节。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。