# 使用名称空间组织 TypeScript 代码

> 原文：<https://blog.logrocket.com/organizing-typescript-code-using-namespaces/>

## 介绍

随着第三方库在企业软件中的使用越来越多，我们经常会遇到全局名称空间被污染的问题，导致全局名称空间中的组件之间的名称冲突。因此，我们需要使用名称空间来组织代码块，以便唯一地标识变量、对象和类。

在本文中，我们将讨论名称空间，何时需要它们，以及如何使用它们来增强 TypeScript 代码的组织。

### 先决条件

*   打字知识
*   熟悉 JavaScript

## 什么是名称空间？

名称空间是组织代码的范例，因此变量、函数、接口或类在局部范围内被组合在一起，以避免全局范围内组件之间的命名冲突。这是减少全球范围污染的最常见策略之一。

虽然[模块](https://blog.logrocket.com/organize-code-in-typescript-using-modules/)也用于代码组织，但是名称空间很容易用于简单的实现。模块提供了一些额外的好处，比如强大的代码隔离、对捆绑的强大支持、组件的重新导出以及组件的重命名，这些都是名称空间所没有的。

## 为什么我们需要名称空间？

名称空间有以下优点:

*   代码可重用性——名称空间对于代码可重用性的重要性不能低估
*   膨胀的全局范围—名称空间减少了全局范围内的代码量，使其不那么膨胀
*   第三方库—随着越来越多的网站依赖于第三方库，使用命名空间保护您的代码以防止您的代码和第三方库之间的同名冲突非常重要
*   分布式开发——随着分布式开发变得流行，污染几乎是不可避免的，因为开发人员使用公共变量或类名要容易得多。这导致了全球范围的域名冲突和污染

## 使用名称空间的设计考虑

### 隐式依赖顺序

在使用一些外部库时使用命名空间将需要您的代码和这些库之间的依赖关系的隐式实现。这导致了您自己管理依赖项以便正确加载它们的压力，因为依赖项可能容易出错。

如果你发现自己处于这种情况，使用模块将会减轻你的压力。

### Node.js 应用程序

对于 Node.js 应用程序，推荐使用模块而不是名称空间，因为模块是 Node 中封装和代码组织的事实标准。

### 非 JavaScript 内容导入

在处理非 JavaScript 内容时，推荐使用模块而不是名称空间，因为有些模块加载器(如 SystemJS 和 AMD)允许导入非 JavaScript 内容。

### 传统模式

当使用不再是工程化的而是不断修补的代码库时，建议使用命名空间而不是模块。

此外，在移植旧的 JavaScript 代码时，名称空间也很方便。

## 在 TypeScript 中探索命名空间

既然我们对什么是 TypeScript 命名空间以及我们为什么需要它们有了共同的理解，我们可以更深入地研究如何使用它们。

鉴于 TypeScript 是 JavaScript 的超集，它的名称空间概念来自 JavaScript。

默认情况下，JavaScript 没有提供名称空间，因为我们必须使用 IIFE(立即调用的函数表达式)来实现名称空间:

```
var Vehicle;
(function (Vehicle) {
    let name = "car";
})(Vehicle || (Vehicle = {}));

```

定义名称空间的代码太多了。同时，TypeScript 以不同的方式做事。

### 单文件命名空间

在 TypeScript 中，使用关键字`namespace`后跟一个选择的名称来定义命名空间。

单个 TypeScript 文件可以根据需要拥有任意多个命名空间:

```
namespace Vehicle {}
namespace Animal {}

```

正如我们所看到的，与使用 IIFE 的名称空间的 JavaScript 实现相比，TypeScript 名称空间是一块语法蛋糕。

函数、变量和类可以在命名空间中定义，如下所示:

```
namespace Vehicle {
  const name = "Toyota"
  function getName () {
      return `${name}`
  }
}
namespace Animal {
  const name = "Panda"
  function getName () {
      return `${name}`
  }
}

```

上面的代码允许我们使用相同的变量和函数名，而不会发生冲突。

### 在命名空间之外访问函数、变量、对象和类

为了访问名称空间之外的函数或类，必须在函数或类名前添加关键字`export`,如下所示:

```
namespace Vehicle {
  const name = "Toyota"
  export function getName () {
      return `${name}`
  }
}

```

注意，我们必须省略变量的关键字`export`,因为它不应该在名称空间之外被访问。

现在，我们可以如下访问`getName`功能:

```
Vehicle.getName() //Toyota

```

### 使用嵌套命名空间组织代码

TypeScript 允许我们使用嵌套的名称空间来组织代码。

我们可以创建嵌套的名称空间，如下所示:

```
namespace TransportMeans {
  export namespace Vehicle {
    const name = "Toyota"
    export function getName () {
        return `${name}`
    }
  }
}

```

注意在`Vehicle`名称空间之前的`export`关键字。这允许在`TransportMeans`名称空间之外访问该名称空间。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们还可以执行名称空间的深度嵌套。

我们的嵌套名称空间可以按如下方式访问:

```
TransporMeans.Vehicle.getName() // Toyota

```

### 命名空间别名

对于深度嵌套的名称空间，名称空间别名可以方便地保持整洁。

使用 import 关键字定义命名空间别名，如下所示:

```
import carName= TransporMeans.Vehicle;
carName.getName(); //Toyota

```

### 多文件命名空间

命名空间可以在多个 TypeScript 文件之间共享。这是通过`reference`标签实现的。

考虑以下因素:

```
//constant.ts
export const name = "Toyota"

//vehicle.ts
<reference path = "constant.ts" />
export namespace Vehicle {
  export function getName () {
      return `${name}`
  }
}

```

这里，为了访问`name`，我们必须引用`constant.ts`文件:

```
//index.ts
<reference path = "constant.ts" />
<reference path = "vehicle.ts" />
Vehicle.getName() // Toyota

```

注意我们是如何从最高级别的名称空间开始引用的。这就是如何处理多文件接口中的引用。TypeScript 将在编译文件时使用此顺序。

我们可以使用以下命令指示编译器将我们的多文件类型脚本代码编译成一个 JavaScript 文件:

```
 tsc --outFile index.js index.ts

```

使用这个命令，TypeScript 编译器将生成一个名为`index.js`的 JavaScript 文件。

## 结论

为了构建可伸缩和可重用的 TypeScript 应用程序，TypeScript 命名空间非常方便，因为它们改进了应用程序的组织和结构。

在本文中，我们已经能够探索名称空间，何时需要它们，以及如何实现它们。查看[TypeScript Handbook:Namespaces](https://www.typescriptlang.org/docs/handbook/namespaces.html)了解更多关于名称空间的信息。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.