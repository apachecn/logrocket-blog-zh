# JavaScript 参考指南:JS 模块生态系统

> 原文：<https://blog.logrocket.com/javascript-reference-guide-js-modules/>

## 介绍

JavaScript 模块是 JavaScript 程序的独立部分，可以独立测试并在其他项目中重用。它们用于改进代码的组织。

在本文中，您将学习如何使用 JavaScript 生态系统中可用的模块系统在 JavaScript 应用程序中创建和使用模块。

## 什么是模块系统？

早期，JavaScript 主要用于增加网页的交互性。然而，JavaScript 不断发展，并被开发人员用来创建运行在服务器端和客户端的复杂 web 应用程序(感谢像 [Helma](https://web.archive.org/web/20090210011535/http://dev.helma.org/) 和 [Jaxer](https://web.archive.org/web/20090121063903/http://www.aptana.com/jaxer) 这样的项目)。

正如我们中许多从事服务器端代码工作的人所知道的，如果没有良好的结构，这些代码尤其难以维护、测试和调试，因为代码本身之间会发生大量的交互。

随着应用程序的增长，这对于 JavaScript 来说是一个问题，因为它没有一种标准的方法来将代码组织成可以单独测试和维护的不同的功能部分，也没有在项目中包含其他 JavaScript 代码。

当然，直到模块出现。

然而，即使模块分离和结构化代码，仍然需要一个生态系统来共享这些模块。

Python、Java 和 Ruby 等编程语言都有共享软件包的生态系统，从小规模到成熟的应用程序都有，这一事实进一步激发了创建这样一个生态系统的灵感。

为此， [Kevin Dangoor](https://www.kevindangoor.com/) 创建了 ServerJS 组，希望让公开可用的 JavaScript 代码集合更有价值。今天，ServerJS 被称为 [CommonJS](http://www.commonjs.org/) 。

## 为什么您的应用程序中需要模块

当您的应用程序包含几行代码时，您可能不需要模块，但是如果代码行(LOC)开始读取跨越多个文件的数百行代码，那么最好将您的代码库分成模块。好处是巨大的，其中包括:

*   复用性
*   可读性
*   更容易维护

### 复用性

当您的应用程序被组织成模块时，可以方便地在其他项目中重用这些模块，消除了完全重写的需要，从而加快了您的开发工作流。

### 可读性

您的应用程序肯定会有多种功能。如果你在一个文件中设计了所有的应用程序功能，很有可能你将来会觉得难以阅读。

当您使用模块设计应用程序时，每个功能都可以驻留在一个模块中。因此，当出现错误时，您将知道问题的根本原因，并且可以在与其余代码库完全隔离的情况下跟踪和修复它。

### 更容易维护

软件的魅力之一是进化。从长远来看，您的应用程序将需要您认为必要的某些特性*或*您的用户要求的特性，当您的应用程序的架构被构造成模块时，您可以轻松地添加或删除特性。

此外，我们人类并不完美，我们的应用程序会有[个 bug](https://en.wikipedia.org/wiki/Software_bug)。修复 bug 也是软件维护的一部分。有了模块，你就知道在你的代码库中去哪里找了。

## JavaScript 中的模块系统

以下是撰写本文时 JavaScript 中可用的模块系统:

*   CommonJS(更受欢迎)
*   异步模块定义(AMD)(不太流行)
*   通用模块定义(UMD)(不太流行)
*   [ES 模块](https://blog.logrocket.com/es-modules-in-node-today/)(更受欢迎)

### CommonJS

CommonJS 是一个模块系统，旨在提供编写 JavaScript 应用程序的标准方法，尤其是服务器端应用程序。是 [Node.js](https://nodejs.org/en/) 中使用的模块格式；一个(主要)用 C++编写的 JavaScript 运行时环境。

在 CommonJS 中，由于两个关键字`exports`和`require`，您可以创建和使用模块。当你想公开模块的特定代码时使用`exports`关键字，然后`require`被使用这种模块的文件使用。

此外，Node.js 有一个`module.exports`属性，您可以为它分配一个新值，比如函数或对象(稍后讨论)。

既然您已经知道 CommonJS 在管理模块时使用了`export`和`require`，那么最好看看这些关键字在应用程序中如何发挥作用的实际方法。为此，我们将模拟一个原型购物车，我们将在 Node.js 上测试它，幸运的是，它实现了 CommonJS 模块格式。

#### 示例项目:模拟购物车

您将在大多数电子商务网站上找到购物车，我们无法在本文中实现购物车的全部功能，这超出了本文的范围，但是相反，我们将演示与购物车相关的一个流行的事情:向购物车添加商品。

在我们继续之前，您需要以下内容:

*   节点. js
*   您最喜欢的代码编辑器

如果您在安装 Node.js 时需要帮助，请根据您的操作系统查看以下资源:

此外，如果你在 Windows 上，在安装 Node.js 之后，你可以[安装 GitBash](https://gitforwindows.org/) ，与 Windows 命令提示符相比，git bash 在其命令行上提供颜色高亮..

解决了这个问题，让我们继续。打开代码编辑器，键入以下代码:

```
// Save as cart.js

/**
 * Define an array of items.
 * Mind you this variable is private to this
 * module.
 */
var items = [];
// The function to add item to the cart
function addItem (name, price) 
    item.push({
    name: name,
    price: price
  });
}
// This ensures that the function is usable
// outside this module
exports.total = function () {
    return items.reduce(function (a, b) {
    return a + b.price;
  }, 0);
};
// This also expose the addItem function
// outside this module 
exports.addItem = addItem;
```

看看上面的代码，第一个表明它是一个模块的是`exports`关键字。如代码注释中所述，`exports`关键字允许您在模块外部公开部分代码。

购物车模块设置得很好。如果您需要测试它，您将需要另一个 JavaScript 文件，或者您可以使用 Node.js 命令行。现在，我们将使用后者。

现在，执行以下操作:

*   导航到保存`cart.js`的目录。
*   打开您的命令行(或终端)

通过键入以下命令启动节点控制台:

```
node
```

之后，键入以下内容:

```
let cart = require('./cart');
```

这将需要`cart.js`文件。一会儿，让我们解释它是如何工作的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

当您阅读前面的代码块时，您会观察到两件事:

*   我们在文件名前加了`./`。
*   当需要文件时，我们没有写文件扩展名。

为什么？如果你可以问的话。嗯，`cart.js`是一个你在本地创建的模块，可以在你系统的某个地方找到，你必须传递一个相对路径，这样 Node.js 知道它不是在一个名为`node_modules`的文件夹中寻找模块，那是`./`的工作。

其次，去掉文件扩展名，`require()` 将查找名为`cart.js`的文件，这是我们文件的名称。

现在，回到命令行，让我们检查 cart 对象:

```
cart
#Expected output: { total: [Function], addItem: [Function: addItem] }
```

接下来，添加一些项目:

```
cart.addItem('Intro to TypeScript', 50);
#Expected output: undefined
```

您可以查看总数:

```
cart.total()
#Expected output: 50
```

继续添加另一个项目:

```
cart.addItem('Intro to CSS', 45);
```

总数现已达到 95:

```
cart.total();
#Expected output: 95
```

这是创建一个基本模块的方法:你可以创建你的功能，并且你只能公开你希望其他文件(需要这个文件)使用的那部分代码。该部分被称为 API(应用程序编程接口)。

这很好，但是我们有一个问题:只有一个购物车。

这是因为您不能再次请求 cart 来获得第二个 cart，因为当您稍后需要一个已经需要的文件时，`exports`对象被缓存并重用，这意味着只有一个模块实例，因此，您只能获得一个副本。

让我告诉你我的意思。切换回您的终端并键入以下内容:

```
second_cart = require('./cart');
```

现在您可能认为这会创建一个新的购物车，但事实并非如此。当你检查总数时，仍然是 95:

```
second_cart.total();
#Expected output: 95
```

当您需要购物车的多个实例时，您需要在模块中创建一个构造函数。为什么是构造函数？

这是因为它是在 JavaScript 中使用关键字`new`调用函数时创建的，这意味着我们每次使用`new`创建购物车时都会获得一个新的购物车实例。

创建另一个文件或重写`cart.js`文件以匹配以下内容:

```
// cart.js

// Create the constructor function
function Cart () {
    this.items = [];
}
// Add the functions to its property
Cart.prototype.addItem = function (name, price) {
    this.items.push({
        name: name,
        price: price
    });
}

Cart.prototype.total = function () {
    return this.items.reduce(function(a, b) {
        return a + b.price;
    }, 0);
};
// Export the function
module.export = Cart;
```

现在，当您需要这个模块时，返回的是 cart 函数，而不是以 cart 函数为属性的对象。厉害！

切换回您的控制台，然后键入以下内容:

```
Cart = require('./second_cart');
#Expected output: [Function: Cart]
```

接下来，通过依次输入命令创建两个购物车:

```
new_shopping_cart = new Cart();
#Expected output: { items: [] }

new_shopping_cart_2 = new Cart();
#Expected output: { items: [] }
```

然后向`new_shopping_cart`添加一个项目，如下所示:

```
new_shopping_cart.addItem('', 50);
new_shopping_cart.total(); #Expected output: 50
```

当您检查另一个购物车时，总数仍然为零。

```
new_shopping_cart_2.total();
#Expected output: 0
```

概括一下:

*   模块的局部变量是私有的
*   在 CommonJS 中，您使用`exports`从一个模块中公开必要的功能，该模块基本上是一个 JavaScript 文件
*   很明显 Node.js 实现了 CommonJS 模块格式，但是它也有`modules.export`，也用于导出模块功能
*   任何想要使用 CommonJS 模块的文件都将使用`require()`函数来读取文件
*   用`exports`创建的模块只能被使用一次

#### 常见的优点

*   使用起来很简单
*   您将遇到的大多数工具系统和包都是围绕 CommonJS 构建的
*   在 Node.js 中实现；一个流行的 JavaScript 运行时环境

#### 常见的缺点

*   一个 JavaScript 文件中可以有一个模块
*   如果您想在 web 浏览器中使用它，则需要额外的工具
*   本质上是同步的，在某些情况下不适合在 web 浏览器中使用

### 异步模块定义(AMD)

CommonJS 的一个缺点是它是同步的。AMD 的目标是通过规范中定义的 API 异步加载模块及其依赖关系来解决这个问题。

AMD API 定义如下:

```
define(id?, dependencies?, factory);
```

其中:

*   `id`:可选参数，指定正在定义的模块的 id。这是模块的路径
*   `dependencies`:这是模块的数组，这些模块是被定义的模块所需要的依赖关系
*   `factory`:被执行来实例化模块或对象的函数。它创建一个 JavaScript 闭包，每个模块调用一次

此外，当您想要使用这个定义的模块时，您将需要规范中定义的`require`函数，如下所示:

```
require(dependencies?, callback);
```

其中:

*   `dependencies`:依赖关系数组
*   `callback`:模块加载时执行的回调函数

关于 AMD API 更详细的解释，[请查看 GitHub](https://github.com/amdjs/amdjs-api/blob/master/AMD.md) 上的 AMD API 规范。

现在，这都是理论(包括规格)，但我希望你能很好地理解这个理论如何在代码中工作*。做到这一点的最好方法是探索 AMD 的实际实现，其中有各种各样的实现，讨论所有的实现是不切实际的。*

而是解释其中比较流行的一个:[require js](https://requirejs.org/)；它将自己描述为“JavaScript 文件和模块加载程序”。

因此，我们将设置 RequireJS，我将(用代码)向您展示如何将 AMD 与 RequireJS 一起使用。

以下是您将在后续章节中学习的内容的简要概述:

*   如何设置要求
*   AMD `require()`和 CommonJS `require()`的区别。
*   `require()`在 AMD 如何工作？
*   如何定义 AMD 模块
*   如何用直接路径名定义 AMD 模块
*   如何用常用函数定义 AMD 模块
*   如何用 CommonJS 代码定义 AMD 模块
*   工厂函数的其他用途
*   在 amd 中定义模块时要小心
*   AMD 的优势
*   AMD 的缺点

#### 如何设置要求

RequireJS 的设置非常简单，您只需导航到 RequireJS 网站并[下载它](https://requirejs.org/docs/download.html)，然后执行以下步骤:

*   创建一个项目文件夹(使用您选择的名称)。
*   创建一个名为`js`的子文件夹。
*   将下载的 RequireJS 文件移动到`js`。文件夹。
*   创建一个`index.html`。文件在*根目录下*。

然后在`index.html`中键入以下代码:

```
<!DOCTYPE html>
<html lang="en">
    <head>
     <meta charset="utf-8" />
     <title>Introduction to RequireJS</title>
    </head>
    <body>
        <!-- Your HTML code for the page -->

     <!-- include config and RequireJS file from the js folder-->
     <script data-main="js/config" src="js/require.js"></script>
    </body>
</html>
```

其中:

*   `data-main="js/config"`:这是 RequireJS 的切入点。这也是我们配置它的地方，在这个例子中,`config`文件驻留在一个名为`js`的文件夹中
*   `src="js/require.js"`:你在这里加载一个脚本的正常方式，它会加载`require.js`

此时，我们还没有创建`config.js`文件，因此，执行以下步骤来创建`config`文件:

*   用编辑器创建一个空白文件
*   将文件另存为`js`文件夹中的`config.js`

在前面的代码块中，你会注意到我们没有包含文件的扩展名`config`,这是因为在 RequireJS 中我们没有包含 JavaScript 文件的扩展名，因为它是为 JavaScript 文件设计的。

因此，当它看到`config`(和其他 JavaScript 文件)时，它知道这是一个 JavaScript 文件。

最后，我们通过在包含`require.js`的`<script>`标签下写下面的*来初始化 RequireJS(检查前面的代码块):*

```
<!-- index.html -->

<!-- Previous code remains the same -->
<script>
    require(['config'], function() {
        // Your code here.
    })
</script>
```

在您的页面加载配置文件后，`require()`中的代码将会运行。

当您阅读上面的代码时，我想您可能会问以下问题:

*   我在这里看到`require()`；和 CommonJS `require()`有什么不同？
*   `require()`如何处理`config`文件？

让我们来回答这些问题。

#### AMD `require()`和 CommonJS `require()`的区别

差异如下所示:

*   AMD `require()`接受一个依赖数组和一个回调函数，CommonJS `require()`接受一个模块 ID。
*   AMD `require()`是异步的，而 CommonJS `require()`是同步的

我们和好了吗？我们继续吧。

#### `require()`在 AMD 如何工作？

在此之前，最好解释一下加载 RequireJS 的标签，这里又是它:

```
 <script data-main="js/config" src="js/require.js"></script>
```

这个脚本标签是一个异步调用，这意味着当 RequireJS 通过`src="js/require.js`加载时，它将异步加载在`data-main`属性中指定的`config`文件，因此，当 RequireJS 获取`config`文件时，该标签下的任何 JavaScript 代码都可以执行。

要看到这一点，切换回您的代码编辑器，打开`index.html`，并执行以下操作:

*   注释`require()`功能
*   在加载 RequireJS 的脚本标记下添加一个脚本标记
*   在脚本标签内添加`alert();`
*   保存您的文件

现在，您的`index.html`应该如下所示

```
<!-- index.html -->
<!-- Other code remains the same -->
<script data-main="js/config" src="js/require.js"></script>
<script>
    alert();
</script>
```

当您在浏览器中加载文件时，在第二个`<script>`标签中声明的`alert()`函数将在初始页面加载时执行，这是在加载`config`文件之前。

现在，在您单击`OK`之前，请启动浏览器开发工具并切换到网络选项卡，确认`config`文件尚未加载。你会发现`config`文件还没有加载，当你点击`OK`时，`config`文件被加载。

所有这些都在下面的 GIF 图片中有所描述:

![Config File Loading](img/c27aacc13a6bc26f3e0759013e7acd0e.png)

这样一来，下面是`require()`如何在后台处理配置文件(和其他 JavaScript 代码)*:*

*   它将创建一个`<script>`块，将`src`设置为`data-main`属性中定义的`config.js`的文件路径
*   将`<script>`块放入 HTML `<head>`部分
*   等待它加载。当它这样做时，*它触发回调函数*

您可以通过保存文件来确认这一点；将其加载到您的浏览器中；然后检查浏览器开发工具:

![Browser Developer Tools](img/1986e0ceb5bec3a09e8ccd86c191bfba.png)

您可以通过修改我们的代码来验证`require()`中的函数是回调函数，如下所示:

```
<!-- index.html -->
<!-- Remaining code remains the same -->

<script data-main="js/config" src="js/require.js"></script>

<!-- Add this script tag-->
<script>
    alert("Before config loads");
</script>

<!-- Add this script tag-->
<script>
    require(["config"], function () {
     alert("After config loads")
    });
</script>
```

保存文件，并将其加载到浏览器中。您将观察到以下情况:

*   在`require()`之前的`alert();`函数将首先执行。
*   `require()`中的`alert();`函数将在配置文件加载后执行。

这显示在下面的 GIF 图像中:

![Alert Function Showing Before Config File Loads](img/bda7d4c0e1525a291b02e452d510101f.png)

#### 如何定义 AMD 模块

以下是 AMD 中的基本模块定义:

```
define(['dependency1', 'dependency2'], function() {
    // Your module code goes here
});
```

这个模块定义清楚地显示了两个依赖项和一个函数。

这些依赖项是用`define()`函数创建的模块本身，它们本身也可以有依赖项。

而函数是工厂函数，如果它返回任何东西，那将是模块的输出值。请注意，您稍后将会了解到，它不需要返回任何内容。

我知道这可能会令人困惑，所以，我们会这样做:

*   创建一个没有依赖关系的模块
*   将创建的模块用作另一个模块的依赖项

在此之前，你需要做一些家务。请执行以下操作:

*   在你的`js`文件夹中创建两个文件夹，分别名为`customScripts`和`lib`
*   将`require.js`移动到`lib`文件夹
*   切换到您的`index.html`文件，并更新脚本标签`src`属性值以指向`require.js`的新位置。现在应该是:`src="js/lib/require.js"`

切换到您的代码编辑器，键入以下代码并保存在您的`customScripts`目录中:

```
// js/customScripts/addition.js
/**
 * The module returns a function which
 * adds two numbers.
 */
define(function() {
    return function(a, b) {
        alert(a+b);
    }
});
```

接下来，在您的`customScripts`目录中创建另一个文件，并键入下面的代码，将其保存为`calculator.js`。

```
// js/customScripts/calculator.js
/**
 * This module depends on the addition.js
 * module.
 */
define(['addition'], function(addition) {
    addition(7, 9);
});
```

当 RequireJS 看到前面的代码块时，它会去寻找依赖项，并通过将它们作为参数传递给函数来自动将其注入到您的模块中。

在后台，它为`addition.js`和`calculator.js`文件创建一个`<script>`标签，并将其放在 HTML `<head>`元素中，等待它们加载，然后它将运行该函数，这类似于`require()`的行为。如果您想确认，请查看浏览器开发工具。

![Browser Developer Tools Script Tag](img/a4b057bcdce364d31c18d7f98fbbd2b5.png)

现在，保存您的文件，导航到`index.html`文件，并更新`require()`函数以匹配以下内容:

```
// index.html
require(['config'], function() {
    require(['calculator']);
});
```

现在，在浏览器中加载您的`index.html`文件，没有任何反应。怎么会这样事情是这样的:

*   RequireJS 试图找到`calculator.js`模块
*   它没有找到它，因为它不知道去哪里找它

我们如何解决这个问题？我们必须通过`config`文件告诉 RequireJS 在哪里可以找到`calculator.js`(和其他模块),因为它是应用程序的入口点。

在我们编写使我们的应用程序工作的代码之前，我们最好解释一下`config`文件的基本结构，如下面的代码块所示:

```
requirejs.config({
    baseURL: "string",
    paths: {},
    shim: {},
});
```

其中:

*   这告诉 RequireJS 在哪里可以找到你的模块
*   `paths`:这些是将与`define()`一起使用的模块的名称。在`paths`中，你也可以使用文件的 CDN 版本，当你这样做的时候，需要在本地可用的 CDN 版本之前加载你的模块的 CDN 版本
*   这允许你加载不是作为 AMD 模块编写的库，并让你以正确的顺序加载它们

我们将在应用程序中使用`baseURL`和`paths`，而不使用`shim`。

掌握了在`config`文件中写什么的知识后，切换到您的`config`文件并更新它:

```
// js/config.js
requirejs.config({
    // The folder which contains your js scripts and modules.
    baseURL: "js",
    paths: {
        /**
        * Location of your modules, In this case,
        * these modules are in the customScripts folder. Note,
        * there is no need for the "js" extension.
        */
        addition: "customScripts/addition",
        calculator: "customScripts/calculator",
        /**
        * If the "addition" module (or any module) is being
        * retrieved from a CDN (Content Delivery Network,
        * and also, you have a local copy, you'll write
        * it as such. Don't forget to leave off the "js"
        * extension
        addition: [
            "https://your-cdn-domain/addition",
            "customScripts/addition"
            ],
        */
    },
});
```

就是这样。现在，重新加载你的浏览器，你会得到一个结果提示。

![Page Says 16 Alert](img/5208b00f561ce5ac33b8f28197ebd06c.png)

嗯，这就是你在 AMD 用 RequireJS 定义和处理模块的方式，但是这是在 AMD 用 RequireJS 定义模块的方式之一，还有其他的。请继续阅读。

#### 用直接路径名定义模块

还可以通过指定模块的路径名来定义模块，路径名是模块文件在项目目录中的位置。下面给出一个例子:

```
/**
 * The "path/to/module" is the direct location
 * that tells RequireJS where to find the module.
 */
define("path/to/module", function() {
    // Your module code here.
})
```

RequireJS 不鼓励这种方法，因为当您将模块移动到项目中的另一个位置时，您需要返回并手动更改模块中的*路径名*。

#### 定义具有类似普通 JS 所需功能的 AMD 模块

在 AMD 中，您可以使用类似于 CommonJS 的语法编写您的模块，使用`require()`函数作为依赖项。因此，我们可以将前面的`calculator.js`重写为:

```
/**
 * This is known as dynamic loading, which
 * means the code executes at runtime.
 */
define(function (require) {
    const additionModule = require("addition");

    additionModule(7, 9);
});
```

#### 用 CommonJS 代码定义 AMD 模块

在前面的段落中，您已经了解了 CommonJS 模块如何通过`exports`对象公开它们的代码。您可能会惊讶地发现，可以使用这种语法来定义您的 AMD 模块。RequireJS 将这种技术描述为“[简化的 CommonJS 包装器](https://requirejs.org/docs/api.html#cjsmodule)”。

包装器的语法是:

```
define(function(require, exports, module) {
    // CommonJS code.
})
```

RequireJS 文档规定`exports`和`module`如果不用可以省略，但是，如果要用的话，也要按照这个顺序写，否则“[麻烦就来了](https://requirejs.org/docs/commonjs.html#manualconversion)”。另一方面，`require`也不能省略。

作为一个例子，让我们以本文前面实现的购物车为例，我们将把它重写为一个 AMD 模块。

```
// customScripts/AMDShoppingCart.js

// Define AMD module with CommonJS code.
define(function (require, exports, module) {
    // CommonJS code.
    var items = [];

    function addItem(name, price) {
            items.push({
              name: name,
              price: price,
            });
    }

    function total() {
            return items.reduce(function (a, b) {
             return a + b.price;
          }, 0);
    }

    exports.addItem = addItem;
    exports.total = total;

    /**
    * In RequireJS, you can also use the following
    * to export the module functionality. It works the
    * same.

    return {
            addItem,
            total,
    }; */

});
```

然后，您可以这样使用`AMDShoppingCart`:

```
// customScripts/UseShoppingCart.js

// Use AMD module with CommonJS code.
define(function(require) {
    // CommonJS code.
    const shoppingCart = require("AMDShoppingCart");

    // Add items to the shopping cart
    shoppingCart.addItem("first", 20);
    shoppingCart.addItem("second", 90);
    shoppingCart.addItem("third", 5);

    alert(shoppingCart.total()); // 115
});
```

在本文的这个阶段，我相信您已经知道如何设置 RequireJS 来使用和处理这些文件，如果还不知道，请参考如何定义一个模块一节。

继续，保存您的文件并启动您的浏览器，您将得到一个关于购物车中商品总数的提示`115`。

你应该知道`shoppingCart`只有一个实例。如果您想看看实际效果，请修改`UseShoppingCart.js`:

```
// customScripts/UseShoppingCart.js

// Use AMD module with CommonJS code.
define(function(require) {
    // Add this below shoppingCart
    const shoppingCart2 = require("AMDShoppingCart");

    // Code truncated, check previous code block

    // check its total
    alert(shoppingCart2.total()); // 115
});
```

如前所述，如果需要购物车的多个实例，可以使用构造函数。

修改`AMDShoppingCart.js`以使用构造函数。

```
// customScripts/AMDShoppingCart.js

// Define AMD module with CommonJS code using
// constructor functions.
define(function (require, exports, module) {
   function Cart() {
        this.items = [];
    }

    Cart.prototype.addItem = function (name, price) {
        this.items.push({
            name: name,
            price: price,
        });
    };

    Cart.prototype.total = function () {
        return this.items.reduce(function (a, b) {
            return a + b.price;
        }, 0);
    };

    module.exports = Cart;
});
```

然后修改`UseShoppingCart.js`:

```
// customScripts/UseShoppingCart.js

// Use AMD module with CommonJS code.
define(function(require) {
    // CommonJS code.
    const shoppingCart = require("AMDShoppingCart");

    let a = new shoppingCart();
    let b = new shoppingCart();

      a.addItem("first", 20);
      a.addItem("second", 90);

    // Check the carts total
      alert(a.total()); // 110
    alert(b.total()); // 0
});
```

#### 工厂函数的其他用途

在上两个小节中，您了解了如何利用工厂函数来模拟和创建 CommonJS 代码，但这并没有结束。

以下是您可以使用工厂函数做的其他事情:

*   返回一个对象
*   返回一个函数(如我们的例子所示)
*   返回构造函数
*   创建私有变量和函数，因为工厂对象基本上创建了一个闭包

#### 在 AMD 中定义模块时的注意事项

人类并不完美，但我们仍然努力做到完美，这实际上并不能阻止我们犯错。当您在 AMD 中定义模块时，请注意以下几点:

*   无论您在依赖项数组中列出什么，都必须与工厂函数中的赋值相匹配
*   尽量不要将异步代码与同步代码混合使用。在`index.html`上编写其他 JavaScript 代码时就是这种情况

人类创造的一切都有它的好和坏，让我们看看一些 amd。

#### AMD 的优势

*   异步加载缩短了启动时间
*   能够将模块分成多个文件
*   支持构造函数
*   无需额外工具即可在浏览器中工作

#### AMD 的缺点

*   语法很复杂
*   你需要一个像 RequireJS 这样的加载器库来使用 AMD

我们没有用 RequireJS 涵盖与 AMD 相关的所有内容，但是我们所涵盖的内容足以为您提供一个坚实的基础。如果你想了解更多关于 RequireJS 的知识，我只给你指出一个地方: [RequireJS 官方文档](https://requirejs.org/docs/api.html)。

### 通用模块定义(UMD)

CommonJS 是为服务器端 JavaScript 设计的，因此，如果没有一些工具或捆绑，它的代码在 Web 浏览器中不会很好地运行。另一方面，AMD 被设计为在客户端(Web 浏览器)工作，不涉及任何工具。此外，AMD 的一些实现特别要求 JS 允许您使用类似于 [r.js](https://requirejs.org/docs/optimization.html) 的工具将 CommonJS 代码转换为 AMD 代码。

因此，需要一个“通用”的方法让每个人都参与进来，于是 UMD 诞生了。UMD 代表通用模块定义。

UMD 是一组既支持 AMD 又支持 CommonJS 的模式，它被设计成使你的代码工作，而不管执行代码的环境如何。这可以在客户端使用 RequireJS，也可以在服务器端使用 Node.js。

乍一看，你可能会发现 UMD 的语法让人不知所措，或者有人称之为“丑陋”，但是，正如你稍后将了解到的，你可以在浏览器、RequireJS 和 Node.js 中成功运行 UMD 模块(无需工具或捆绑)。

UMD 通过在部署模块功能之前检查实现(或调用)模块的环境来实现这一壮举，在 Web 浏览器中，它使模块功能作为一个全局变量可用。

正如我前面提到的，阅读 UMD 模块可能会让人不知所措，因此，我不会扔给你一个完整的 UMD 模块，这样你就可以自己去弄明白它，相反，我会把它分解开来，告诉你是什么让它起作用，以及如何使用它。

#### 定义 UMD 模块

UMD 模块由两部分组成:

1.  **一个立即被调用的函数表达式(life)**:检查想要使用该模块的环境。它需要两个参数:`root`和`factory`。`root`是对全局范围的`this`引用，`factory`是我们定义你的模块的函数
2.  **一个匿名函数**:这创建了我们的模块，它作为第二个参数传递给 1 中讨论的 IIFE。此外，向这个匿名函数传递任意数量的参数，以指定模块的依赖关系

在代码中:

```
// The Immediately Invoked Function Expression
(function(root, factory) {
    // Here, you check the environment that want's
    // to use the module, and you'll write
    // the necessary code that will make the code
    // work in that environment.

    // In this case the environment could be Node.js
    // RequireJS (AMD) or a Web browser.
}(this, function() { // The anonymous function, It'll take arguments which are dependencies of the module being defined.
    // Your module code will reside here
}));
```

好了，这就是 UMD 模块的结构，但是它还是什么都不做。让我们解决这个问题。

下面的代码块定义了一个简单返回短语`A UMD module`的 UMD 模块。

```
// The IIFE
(function(root, factory) {
    // The module made available as a global variable.
    // Here, the module name is myUMDModule
    root.myUMDModule = factory();
}(this, function() { // The anonymous function.
    // Our module code
    return {
            name: 'A UMD module'
    }
}));
```

请，该模块没有考虑环境，因此，在这个阶段，它将只在网络浏览器中工作。

您可能面临的下一个挑战是:我如何使用这个模块？请执行以下操作:

1.  复制上面的代码
2.  启动您的网络浏览器
3.  打开浏览器开发工具(Windows 上的`Ctrl + Shift + I`，Mac 上的`Cmd + Shift + I`，导航到`Console`
4.  粘贴代码，然后按键盘上的 enter 键

接下来，开始在浏览器控制台中键入模块名，即`myUMDModule`，您会看到它是一个全局变量，如下图所示:

![myUMDmodule Showing as a Global Variable](img/9c8607a2e51fe72f90e977b8c017b72b.png)

下一个问题:我如何在支持 AMD 的环境中使用它，例如 RequireJS？

您可以通过添加几行代码来为 AMD 环境执行一些检查。

```
// Save as UMDwithAMD.js
(function (root, factory) {

    // Add the following
    if (typeof define === "function" && define.amd) { // checks for an AMD environment

            // If true, create the module using the define
            // function, and use the function passed as an
            // argument to this IIFE as the factory function.
            define(factory);

    } else { // We are in the browser
            // Create the module as a global object
            // which is useful for web browsers
            // without the need for RequireJS.
            root.myUMDModule = factory();
    }

})(this, function () { // The anonymous function.
    // Our module code
    return {
            name: "A UMD module",
    };
});
```

如果您想在 RequireJS 中测试这段代码，可以采取两条路线。途径 1:创建一个使用这个模块作为依赖项的文件。途径二:通过`require()`在`index.html`页面使用该模块。让我们看看第一条路线之前的第二条路线。

**再一次，如果你已经阅读了这篇文章，我相信你可以在 RequireJS** 中设置你的 AMD 模块。出于修订的目的，以下是您将如何设置`UMDwithAMD.js`:

1.  将其保存在之前创建的`customScripts`目录中
2.  更新`config`文件并指定`UMDwithAMD.js`的位置

现在，在`index.html`上，修改`require()`函数以匹配以下内容:

```
// Part of index.html

// The script tag to use RequireJS should come
// before this code.
require(["config"], function () {
    require(['UMDwithAMD'], function(UMDwithAMD) {
            alert(UMDwithAMD.name); // A UMD module
    });
});
```

保存您的文件，启动(或刷新)您的浏览器，输出应该是:`A UMD module`。

第二条路线:创建一个依赖于`UMDwithAMD`的 UMD 模块。切换到代码编辑器，将下一个代码块保存为`UseAMDwithAMD.js`。

```
// Save as UseUMDwithAMD.js
(function (root, factory) {
    if (typeof define === "function" && define.amd) { // checks for an AMD environment
        // Note: The define function now uses
        // the UMDwithAMD as a dependency
     define(["UMDwithAMD"], factory);
    } else { // we are in the browser (root is window)
     root.myUMDModule = factory();
    }
})(this, function (UMDwithAMD) {
    // Use UMDwithAMD
    alert(UMDwithAMD.name);
});
```

接下来，修改`index.html`上的`require()`功能，以匹配以下内容:

```
require(["config"], function () {
    require(['UseUMDwithAMD']);
});
```

保存文件并刷新浏览器。记下输出。尽管如此，它应该是:`A UMD module`。

现在，你可能会问:`UseUMDwithAMD.js`和它的依赖关系`UMDwithAMD.js`在没有 RequireJS 的情况下能在 web 浏览器中工作吗？是啊！会的。执行以下操作:

1.  创建一个新的项目文件夹，并将其命名为`learningUMD`
2.  用 HTML5 文档的结构创建一个`index.html`文件，并保存在这个文件夹中
3.  将`UseAMDwithAMD.js`和`UMDwithAMD.js`复制到这个文件夹中。(现在，您的项目文件夹中有 3 个文件)
4.  在`index.html`的`head`部分创建 2 个脚本标签
5.  第一个`script`标签**的`src`属性必须**指向`UMDwithAMD.js`因为`UseUMDwithAMD.js`依赖于它，所以需要先加载
6.  第二个`script`标签**的`src`属性必须**指向`UseUMDwithAMD.js`，因为它是依赖项
7.  在`UseUMDwithAMD.js`的匿名函数中，你需要将`alert(UMDwithAMD.name)`改为`alert(myUMDModule.name)`，因为我们想在网络浏览器环境中测试相关模块，因此`name`属性只能在全局变量`myUMDModule`中使用。我们不能使用`alert(UMDwithAMD.name)`，因为我们不是在 AMD 环境中测试。如果你这样做，你会得到`undefined`

最后，您的新 HTML 文件应该是这样的:

```
<!DOCTYPE html>
<html lang="en">
    <head>
     <meta charset="utf-8" />
     <title>Introduction to UMD</title>
     <script src="UMDwithAMD.js"></script>
     <script src="UseAMDwithAMD.js"></script>
    </head>
    <body></body>
</html>
```

并且`UseUMDwithAMD.js`将看起来像:

```
/**
 * Save as UseUMDwithAMD.js (modified to display an
 * alert in a Web browser environment)
 */
(function (root, factory) {
    // Code truncated
})(this, function (UMDwithAMD) { // You can leave the argument, it does no harm.
    // Use UMDwithAMD
    alert(myUMDModule.name); // Note, we are using myUMDModule and not UMDwithAMD
});
```

现在，启动您的浏览器，观察警报显示的短语:`A UMD module`。

到目前为止，我们已经展示了单个模块:`UMDwithAMD.js`如何在浏览器中工作，以及如何在 AMD 中工作，而不需要做任何修改。下一步是看看它是否能在 Node.js 中不加修改地工作。你准备好了吗？让我们找出答案。

在此之前，我们需要向`UMDwithAMD.js`添加一个检查，看看我们是否在 Node.js 环境中。更新`UMDwithAMD.js`以匹配以下内容:

```
// Save as UMDwithAMD.js
(function (root, factory) {
    // Add the following
    if (typeof define === "function" && define.amd) {
            define([], factory);
    } else if (typeof module === "object" && module.exports) { // Check for Node.js environment
        // Does not work with strict CommonJS, but
        // only CommonJS-like environments that support
        // module.exports, like Node.
            module.exports = factory();
    } else {// We are in the browser
            // Create the module as a global object (root is window)
            root.myUMDModule = factory();
    }
})(this, function () {
    // The anonymous function.
    // Our module code
    return {
            name: "A UMD module",
    };
});
```

现在，切换到命令行并执行以下步骤:

1.  导航到保存`UMDwithAMD.js`的目录
2.  键入`node`启动 Node.js 控制台
3.  类型`myModule = require('./UMDWithAMD.js')`
4.  最后，检查`name`属性

前面的步骤(和结果)如下图所示:

![Name Property in Node.js](img/8e9938b74612dd9d87d10d905a8da4c8.png)

就这样，一个 UMD 模块成功地在 Node.js、AMD 和 RequireJS 中工作，web 浏览器巩固了模式名中的单词“universal”。

你应该知道，我并没有在模块代码中编造我们检查多个环境的方法。 [UMD GitHub repo](https://github.com/umdjs/umd) 有一些 [UMD 模板](https://github.com/umdjs/umd/tree/master/templates)你可以根据环境和你想要达到的目标来使用。

下面是模板的简要介绍和代码注释中提到的推荐用法:

| **UMD 模板** | **推荐用法** |
| [amdWeb.js](https://github.com/umdjs/umd/blob/master/templates/amdWeb.js) | 有用的时候，你想创建一个 UMD 模块与 AMD 公司或浏览器全局 |
| [amdWebGlobal.js](https://github.com/umdjs/umd/blob/master/templates/amdWebGlobal.js) | 如果你有一些由 AMD 加载器加载的脚本，但是它们仍然想要访问全局，这是很有用的 |
| [commonjsAdapter.js](https://github.com/umdjs/umd/blob/master/templates/commonjsAdapter.js) | 这可以用作一个库模块的通用样板文件，您只需要将它公开给 CommonJS 和 AMD 加载器。它不能很好地定义浏览器全局 |
| [commonjsStrict.js](https://github.com/umdjs/umd/blob/master/templates/commonjsStrict.js) | 用于创建带有 CommonJS、AMD 或浏览器全局变量的模块 |
| [commonjsStrictGlobal.js](https://github.com/umdjs/umd/blob/master/templates/commonjsStrictGlobal.js) | 如果你有一些由 AMD 加载器加载的脚本，但是它们仍然想要访问全局，这是很有用的 |
| [jqueryPlugin.js](https://github.com/umdjs/umd/blob/master/templates/jqueryPlugin.js) | 创建一些 jQuery 插件 |
| [nodadapter . js](https://github.com/umdjs/umd/blob/master/templates/nodeAdapter.js) | 这可以用作一个库模块的通用样板文件，您只需要向节点和 AMD 加载器公开它 |
| [returnExports.js](https://github.com/umdjs/umd/blob/master/templates/returnExports.js) | 用节点、AMD 或浏览器全局变量创建一个模块 |
| [returnExportsGlobal.js](https://github.com/umdjs/umd/blob/master/templates/returnExportsGlobal.js) | 使用节点、AMD 或浏览器全局变量创建模块。这个例子创建了一个全局变量，即使使用了 AMD。如果你有一些由 AMD 加载器加载的脚本，但是它们仍然想要访问全局，这是很有用的 |

当您观察上面列出的任何模板的代码时，您会注意到它们都通过检查`self`变量的存在来支持 web workers，否则它默认为`this`变量。因此，我们也可以将该检查包含在我们的`UMDwithAMD.js`代码中:

```
// UMDwithAMD.js (without comments).
(function (root, factory) {
    if (typeof define === "function" && define.amd) {
            define([], factory);
    } else if (typeof module === "object" && module.exports) {
            module.exports = factory();
    } else {
            root.myUMDModule = factory();
    }
})(typeof self !== "undefined" ? self : this, function () { // Notice the modification on this line
    return {
            name: "A UMD module",
    };
});
```

最后，这里是本文前面实现的购物车，但这次是在 UMD 编写的。

```
// ShoppingCartAMD.js
(function (root, factory) {
    if (typeof define === "function" && define.amd) {
            define([], factory);
    } else if (typeof module === "object" && module.exports) {
            // Node. Does not work with strict CommonJS, but
            // only CommonJS-like environments that support module.exports,
            // like Node.
            module.exports = factory();
    } else {
            // Browser globals (root is window)
            root.shoppingCartUMD = factory();
    }
})(typeof self !== "undefined" ? self : this, function () {

    var items = [];

    function addItem(name, price) {
            items.push({
             name: name,
             price: price,
          });
    }

    function total() {
            return items.reduce(function (a, b) {
              return a + b.price;
          }, 0);
    }

    // Return the addItem and total functions.
    return {
            addItem,
            total,
    };

    // If you like to create multiple instance of the
    // shopping cart, you can use a constructor function.
    // function Cart() {
    //  this.items = [];
    // }
    // Cart.prototype.addItem = function (name, price) {
    //  this.items.push({
    //      name: name,
    //      price: price,
    //  });
    // };
    // Cart.prototype.total = function () {
    //  return this.items.reduce(function (a, b) {
    //      return a + b.price;
    //  }, 0);
    // };
    // return Cart;
});
```

您可以在浏览器和 Node.js 中测试代码，以确认它能够工作，如果您想在 RequireJS 中使用它，可以直接在`index.html`文件(本文前面创建的)上使用 with `require()`函数，或者创建另一个 UMD 模块，将它用作依赖项。下面给出一个例子:

```
// UseShoppingCartAMD.js

// Please note, this file use shoppingCartUMD.js
// as a dependency.
(function (root, factory) {
    if (typeof define === "function" && define.amd) {
     // AMD. Register as an anonymous module.
     define(["shoppingCartUMD"], factory);
    } else if (typeof module === "object" && module.exports) {
     // Node. Does not work with strict CommonJS, but
     // only CommonJS-like environments that support module.exports,
     // like Node.
     module.exports = factory(require("shoppingCartUMD"));
    } else {
     // Browser globals (root is window)
     root.returnExports = factory(root.shoppingCartUMD);
    }
})(typeof self !== "undefined" ? self : this, function (shoppingCartUMD) {

    shoppingCartUMD.addItem("computer", 20);
    shoppingCartUMD.addItem("mouse", 5);
    shoppingCartUMD.addItem("USB drive", 10);
    alert(shoppingCartUMD.total()); // 35

    // If you did use constructor functions in
    // calculator.js, you'd use the shopping cart
    // as such.

    // let a = new shoppingCartUMD();

    // a.addItem("computer", 20);
    // a.addItem("mouse", 5);
    // a.addItem("USB drive", 10);
    // alert(a.total()); 35
});
```

这就是你的 UMD，随处可用的 JavaScript 模块。

#### UMD 的优势

1.  它在任何地方都可以工作(web 浏览器、Node.js、AMD with RequireJS)

#### UMD 的劣势

1.  语法可能会让人不知所措

### ES 模块

如果你是一名前端开发人员，或者刚刚开始使用 JavaScript 的模块，你可能会发现 UMD 或 AMD(带有 RequireJS)的代码很难使用或理解。

幸运的是， [TC39](https://tc39.es/) ，负责 ECMAScript 规范的委员会已经帮你搞定了。2015 年，该委员会在 JavaScript 中添加了模块作为内置功能。今天，这些模块被称为 es 模块，是 ECMAScript 模块的缩写，如果你听到任何人说模块，很有可能他们正在谈论 ES 模块。

我很确定他们考虑了 UMD 和 AMD 模块系统的语法，因此，ES 模块的语法是可以理解的。此外，如果你使用过 PHP 或 Python 这样的编程语言，我想你会很快理解 es 模块，如果没有，当你阅读 ES 模块代码时，你可以猜到代码的结果。

接下来，我将把 ES 模块简称为模块。

#### 定义和 ES 模块

`export`关键字是定义模块的基础，任何想要使用这个模块的文件都将使用`import`语句导入导出的模块代码(稍后讨论)。因此，模块只不过是导出自己代码的 JavaScript 代码。

当你想导出你的模块代码的一部分时，你需要在它前面加上单词`export`。这个部分可以是一个*变量*、*函数*，或者是一个*类*。你应该知道，代码中任何没有导出的部分都是模块私有的，不能在模块外部访问。

例如，这里有一个模块，它导出一个名为`getMyName`的函数:

```
// getMyName.js

// Remains private
let myName = "Habdul Hazeez";

// Exposed to the world, or anyone
// who uses this file.
export function getMyName() {
    return myName;
}
```

因此，任何打算使用这个模块的 JavaScript 文件都会这样使用它:

```
import { getMyName } from './getMyName.js'

console.log(getMyName); // Habdul Hazeez
```

现在，花一点时间，想想你将如何在 UMD 或 AMD 定义和使用这个模块(相信我，我知道这种感觉)。这就是模块的美妙之处，语法更简洁，也更容易学习。

我说“容易”，但我们不需要太超前。最后两个代码块向您展示了如何定义一个模块以及如何使用它，但是就目前的情况而言，我想问您两个问题:

*   你能向另一个人或你自己解释吗？
*   你能以另一种方式导出和导入一个模块吗？

也许吧，但我不会冒险。我将对其进行分解并解释以下内容:

*   如何在 web 应用程序中使用模块
*   如何导出和导入模块
*   对命名进口的误解
*   模块指定符
*   模块是单件的
*   进口是活的
*   如何在 Node.js 中使用模块
*   模块的特征
*   ES 模块的优势

#### 如何在 web 应用程序中使用模块

为了在您的 web 应用程序中使用模块，您需要在您的 HTML 文件中使用带有附加属性的普通脚本标记。但在此之前，让我们通过执行以下操作来设置项目文件夹:

*   创建一个项目文件夹(使用您选择的名称)。
*   在这个文件夹中创建一个名为`js`的文件夹。
*   打开代码编辑器，创建一个新的`index.html`文件。

现在，在`index.html`中键入以下内容:

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0" >
    <title>Introduction to ES modules</title>
</head>
<body>
</body>
</html>
```

仍然在您的代码编辑器中，在`js`文件夹中创建一个新的 JavaScript 文件，将其保存为`addition.js`并键入以下代码。

```
// js/addition.js

// A function that adds two numbers
export function addition(a, b) {
    return a + b;
}
```

关键要观察的是函数声明前的`export`关键字。这意味着两件事:

1.  该文件是一个模块
2.  任何导入该模块的文件都可以使用该函数

此外，这种风格的导出被称为*内联导出*。当我说“这种风格”时，那意味着还有另一种“风格”。我们稍后再讨论。

模块已经准备好了，但是我们需要另一个 JavaScript 文件来使用这个模块，因此，创建另一个 JavaScript 文件并将其保存为`useaddition.js`，然后键入以下代码:

```
// useaddition.js
import { addition } from './addition.js';

alert(addition(2, 5));
```

切换回`index.html`文件，并在文件末尾添加一个脚本标签:

```
<!-- Code truncated -->
<body>
    <script src="js/useaddition.js"></script>
</body>
<!-- Code truncated -->
```

在这个阶段，`useaddition.js`是你申请的切入点。保存所有文件，启动浏览器并打开`index.html`。在平常的一天，你应该会收到一条提醒信息，现实是:你不会收到任何提醒信息。

当您在 JavaScript 应用程序中遇到错误时，您应该首先查看位于开发者工具中的 web 浏览器控制台，您应该会看到类似于下图中的错误，它将显示为`Uncaught SyntaxError: Cannot use import statement outside a module`。

![Uncaught Syntax Error](img/c8f6f371b264f88782d7af3680119899.png)

问题是为什么？嗯，`index.html`上的脚本标签的`src`属性指向一个 JavaScript 文件(`useaddition.js`)，因此，当浏览器读取`useaddition.js`时，它会发现一个`import`语句，这在正常情况下应该只出现在一个模块中，但浏览器不会将`useaddition.js`识别为一个模块，它会停止读取文件并报告错误。

你还记得我跟你说过的附加属性吗？基本上是`type="module"`告诉浏览器链接的 JavaScript 文件是一个模块。现在，修改`index.html`中的脚本标签，如下所示:

```
<!-- Code truncated -->
<body>
    <!-- Note the added attribute in the script tag -->
    <script type="module" src="js/useaddition.js"></script>
</body>
<!-- Code truncated -->
```

保存所有文件并刷新浏览器。没有任何警告消息，检查开发人员工具中的控制台，又一个错误。

![Another Error](img/f42521dcb9b30ddb157af885764c961b.png)

这次是 CORS 错误。CORS 代表跨来源资源共享，这是一种 HTTP 报头机制，允许**服务器**指示浏览器应该允许从哪个来源加载资源。这些来源可以是一个域、方案或端口。

出于安全原因，浏览器限制从脚本发起的跨来源 HTTP 请求。这正是`useaddition.js`正在努力做的事情。

首先，`useaddition.js`是一个 JavaScript 文件，试图通过使用`file:///`协议的 HTTP 请求获取资源(`addition.js`)，这被认为是一个安全风险，因此浏览器会阻止该请求。此外，它声明来源是`null`，因为它不认为来自浏览器的脚本文件是请求资源的可接受来源。

修复方法是改变原点，如前所述，这个原点可以是域、方案或端口。在我们的例子中，我们将使用 scheme，这是协议的另一种说法，如`http`或`https`，我们使用这种协议的唯一方式是将我们的代码放在服务器上。

有几种方法可以做到这一点，其中包括:

1.  GitHub 页面(免费)
2.  网络生活(免费)
3.  本地主机(免费，但它是你电脑上的服务器)

如果你和我一样，就选最后一个。安装 [XAMPP](https://www.apachefriends.org/) 就可以得到类似 Apache 的服务器。之后，执行以下操作

其中:

*   `projectfolder`是您之前创建的文件夹的名称。

如果您已经正确地做了所有的事情，那么这个模块应该工作了，并且您将在您的 web 浏览器中得到一个警告消息。

![Alert Message in Browser](img/24b77db4e86efbbd57b4c7c70f26a598.png)

现在，我们已经有了一个允许模块工作的环境(如上图所示)，请保持环境活跃，让我们探索一下我们可以导出和导入模块的其他方法，因为您可能已经注意到，我们如何从`addition.js`导出和导入模块只是您可以在 JavaScript 应用程序中使用模块的方法之一。

#### 如何导出和导入模块

在上一节中，您已经学习了如何导出和导入模块，我确实提到了这只是 JavaScript 中使用模块的一种方式。下面列出了其他一些例子:

*   命名导出和命名导入
*   默认导出和默认导入
*   默认导出为命名导出
*   重命名导出并命名导入
*   导出列表+重命名和导入列表+重命名
*   命名导出+默认导出和命名导入+默认导入
*   命名空间导入

##### 命名导出和命名导入

这意味着从模块中导出的任何东西都应该有一个名字。该名称可以是变量名、类名或函数名。稍后，当您导入这个模块时，您将通过名称。

这就是我们在`addition.js`文件中所做的，为了更清楚，这里再重复一遍(以删节版本):

```
// js/addition.js

// "addition" is the name of the export
// and it's what is used by any file
// importing this module.
// This exporting style is known as inline.
export function addition(a, b) {}
```

当您想要导入它时，您必须用一对花括号将导出的名称括起来，这意味着您正在进入模块以获取导出的名称。同样，这里是来自`useaddition.js`的导入语句:

```
// useaddition.js

// Note the curly braces around the
// function name
import { addition } from './addition.js';

// code truncated
```

##### 默认导出和默认导入

顾名思义，这意味着从模块导出的值是默认值。您可以在想要导出的内容前添加`default`关键字。建议每个模块只有一个默认导出。

现在，在代码编辑器中切换到`addition.js`，然后在函数前添加`default`，如下所示:

```
// js/addition.js

// The addition function as a default
// export
export default function addition(a, b) {
    // code truncated
}
```

保存文件，刷新浏览器(最好是硬刷新:`ctrl + Shift + R`)。您应该会得到类似下图所示的错误。

![Uncaught Syntax Error After a Refresh](img/a0a2c80f6669f221ebb15afc68637090.png)

错误的原因在`useaddition.js`中，因为函数`addition`现在是一个*默认的*导出，您需要通过删除花括号来更改导入语句的语法，您应该得到如下结果:

```
// useaddition.js

// Importing the addition function as a default
// export
import addition from './addition.js';

// code truncated
```

保存文件并刷新 Web 浏览器。一切都应该正常。

##### 默认导出为命名导出

JavaScript 允许您使用`as`关键字将导出重命名为别名。此后，您可以使用此别名导入此导出。但是当您导出一个模块的一部分，并使用关键字`default`将其指定为缺省值时，您将在导入模块时使用导出名称，而不是关键字`default`。

因此，您可以将上一节中的默认导出写在`addition.js`中，作为一个命名的默认导出，一切都应该工作了。

```
// js/addition.js

// Rewritten to used a named export
// on a default export
function addition(a, b) {
    return a + b;
}

// addition as a named default export
export {
    addition as default,
};
```

我们出口`addition`的方式是我前面提到的另一种出口“风格”，它被称为*出口条款*。

请注意，如果您在`useaddition.js`中这样写您的进口声明**将不起作用**:

```
// This is an INVALID import statement, and will
// result in an error stating:
// Uncaught SyntaxError: Unexpected token 'default'
import default from "./addition.js";

alert(default(2, 5)); // The code won't execute to this line.
```

##### 重命名导出并命名导入

如上一节所述，您可以重命名导出，并在导入时使用新名称作为导出名称。

```
// js/addition.js

// Rewritten to used a named export
// on addition
function addition(a, b) {
    return a + b;
}

// This is called an export clause
// The function "addition" is now available as "sum"
// for any file that imports this module.
export {
    addition as addNumbers,
};
```

然后，您将在`useaddition.js`中将其用作:

```
// useaddition.js

// This would FAIL because we exported
// addition as "addNumbers".
// import { addition } from "./addition.js"

// Importing a renamed export
import { addNumbers } from "./addition.js";

alert(addNumbers(2, 5));
```

##### 导出列表+重命名和导入列表+重命名

您可以导出和导入模块的多种功能，可以重命名也可以不重命名。如果您给导出重新命名，您将在导入它们时使用新名称。

为了证明这一点，让我们使用本文中的好朋友:购物车。

```
// js/cart_esmodules.js
var items = [];

function addItem(name, price) {
    items.push({
     name: name,
     price: price,
    });
}

function total() {
    return items.reduce(function (a, b) {
     return a + b.price;
    }, 0);
}

// Export multiple items as a list.
// Note, addItem is renamed to addToCart
export {
    total,
    addItem as addToCart,
}
```

当导入时，我们可以使用导出的它们，或者根据需要重命名它们。创建一个新的 JavaScript 文件，用名称`use_esshoppingcart.js`保存，并键入以下代码。

```
// js/use_esshoppingcart.js

// Note, total is renamed to cartTotal.
import {
    addToCart,
    total as cartTotal
} from "./cart_esmodules.js";

addToCart("USB drive", 20);
addToCart("Keyboard", 10);

alert(cartTotal()); // 30
```

接下来，修改`index.html`文件，使`use_esshoppingcart.js`成为应用程序的入口点。

```
<!-- Code truncated -->
<body>
    <!-- Note, the src attribute value has changed -->
    <script type="module" src="js/use_esshoppingcart.js"></script>
</body>
<!-- Code truncated -->
```

保存文件并刷新浏览器。您的输出应该类似于下图。

![Alert Message 30](img/e635cc506eaed27dd3b18937143a6837.png)

##### 命名空间导入

虽然您已经学习了如何使用模块代码的名称或通过重命名来导入模块代码，但是名称空间导入允许您一次导入所有的模块导出。当您这样做时，名称空间导入成为一个对象，其属性是命名导出。

名称空间导入的两个关键要素是:

*   符号:在这个上下文中，它的意思是“一切”
*   别名:这是被称为名称空间的对象

仍然使用`use_esshopingcart.js`，将其修改为使用名称空间导入。

```
// js/use_esshoppingcart.js

// Now, we are using a namespace import
// to import all the module exports.
// This line means, "import everything from
// cart_esmodules.js" as ShoppingCart.
import * as ShoppingCart from "./cart_esmodules.js";

// add items to the cart
ShoppingCart.addToCart("USB drive", 20);
ShoppingCart.addToCart("Keyboard", 10);

alert(ShoppingCart.total()); // 30

// You can test ShoppingCart is an object
// by writing the following code.
// alert(ShoppingCart instanceof Object); // true
```

保存它，并通过刷新浏览器来测试它。

##### 命名导出+默认导出和命名导入+默认导入

这种方法是可行的，但不推荐。

```
// js/cart_esmodules.js

// code truncated

// The addition function is exported
// as a default and the total function
// is exported as a renamed export.
export {
    addItem as default,
    total as cartTotal
};
```

在`use_esshoppingcart.js`中，您将这样编写导入语句:

```
// js/use_esshoppingcart.js

// Importing a default export and a named
// export.
import addItem, { cartTotal } from "./cart_esmodules.js";

// code truncated
```

##### 动态导入

到目前为止，我们讨论的所有导入语句都被称为*静态导入*，它有其局限性。它必须在模块的顶层，并且不能在块中使用。

在动态导入中，您可以使用`import()`操作符“按需”加载代码，从而绕过静态导入带来的约束，在静态导入中，您必须将导入语句放在模块的顶层。

在这个例子中，我们将使用`addition.js`和`useaddition.js`。请注意，我们将使用`addition.js`的最后一个修改版本，它是在*部分中制作的，重命名导出并命名导入*，但这里又是它(没有注释):

```
// js/addition.js
function addition(a, b) {
    return a + b;
}

export {
    addition as addNumbers,
};
```

接下来，我们将使用动态导入重写`useaddition.js`。

```
// js/useadditon.js

// The directory of the file specified
// as a constant. In this case it's just
// a relative path because useaddition.js and
// addition.js are in the same directory. i.e. the
// js directory
const dir = "./";

// Concatenate the directory name to the
// module name,
const moduleSpecifier = dir + "addition.js";

function loadAdditionModule() {
    // The "addition" parameter to 'then' is NOT
    // the exported name of the module from
    // addition.js, instead, the exported function
    // is "addNumbers".
    //
    // Note the import() operator below.
    return import(moduleSpecifier).then(function (addition) {
     const result = addition.addNumbers(2, 5);
     return alert(result);
    });
}

loadAdditionModule(); // 7
```

更新`index.html`文件，使`usadditon.js`成为你的应用程序的入口点，刷新你的浏览器，你应该得到一个`7`的警告信息。

请注意，如果`addition`被导出为`addition.js`中的默认值:

```
// js/addition.js

// code truncated

export {
    addition as default,
};
```

您将在动态导入中使用`default`。因此，`useadition.js`用动态导入会像这样:

```
// js/useadditon.js (working with a default export)
const dir = "./";

const moduleSpecifier = dir + "addition.js";

function loadAdditionModule() {
    return import(moduleSpecifier).then(function (addition) {
     const result = addition.default(2, 5); // Note the "default" on this line
     return alert(result);
    });
}

loadAdditionModule(); // 7
```

就个人而言，我不建议这样做，因为当另一个人或你未来的自己试图弄清楚`default`在这个上下文中是什么意思时，可读性就荡然无存了。

#### 对命名进口的误解

如果你以前在 JavaScript 中做过析构，你可能会认为一个命名的导出是析构的，事实并非如此，尽管它们看起来很相似。

```
import { cartTotal } from "./cart_esmodules.js"; // named import
let [a, b] = [20, 30]; // destructuring
```

此外，重命名是不同的:

```
// A renamed import
import { cartTotal as ShoppingCartTotal} from "./cart_esmodules.js"; // named import

// Destructuring with renaming variables
let myDetails = {first_name: "Habdul", last_name: "Hazeez"};
// Rename first_name and last_name
let {first_name: FirstName, last_name: LastName} = myDetails;

// FirstName: Habdul, LastName: Hazeez
```

#### 模块指定符

模块说明符是标识模块的字符串，例如`./cart_esmodules.js`。它们在 Web 浏览器和 Node.js 中的行为是不同的。

*   相对路径:这是我们如何指定`cart_esmodules.js`的，它以一个点开始。一个例子是`./cart_esmodules.js`
*   绝对路径:它以正斜杠(/)开头，后跟一个目录名。例如`/js/cart_esmodules.js`
*   URL:在指定模块的字符串中添加一个协议。例如`[http://localhost/esmodules/js/cart_esmodules.js](http://localhost/esmodules/js/cart_esmodules.js)`
*   裸路径:它没有像这样的符号，由一个文件名组成。例如`cart_esmodules.js`。目前，网络浏览器不支持
*   深度导入路径:以目录名开始，后面至少跟一个斜杠。例如`js/cart_esmodules.js`。网络浏览器尚不支持

在 Node.js 中，说明符的工作方式如下:

*   相对路径:与 web 浏览器中的工作方式相同
*   绝对路径:不支持。但是你可以使用文件协议`file:///`
*   对于 URL，仅支持`file:`
*   裸路径被解释为包名，并相对于最近的`node_modules`目录进行解析
*   深度导入路径也被解析到最近的`node_modules`目录

#### 模块是单件的

类似于 CommonJS 和 AMD，模块都是单体的。这意味着，如果您多次导入一个模块，则只存在它的一个实例。

为了更好地理解这一点，切换到您的编辑器并修改`use_esshoppingcart.js`以匹配下面的代码(不要保存它，请继续阅读):

```
// js/use_esshoppingcart.js

import * as firstShoppingCart from "./cart_esmodules.js";
import * as secondShoppingCart from "./cart_esmodules.js";
```

在前面的代码块中，`cart_esmodules.js`作为名称空间导入在`firstShoppingCart`和`secondShoppingCart`中被导入了两次，因此，两个购物车都是对象，它们的属性都是模块的命名导出(请参考“名称空间导入”一节)。

现在，你可能会认为`firstShoppingCart`和`secondShoppingCart`是不同的，因为 JavaScript 中的**两个对象不等于**，实际情况是:`firstShoppingCart`和`secondShoppingCart`是同一个对象。

为了确认这一点，在`cart_esmodules.js`中创建两个对象，如下面的代码块所示:

```
// js/use_esshoppingcart.js

// Previous import statements remains the same

// create two objects ( not necessarily with my name :) )
let firstObject = {
    first_name: "Habdul",
    last_name: "Hazeez",
};

let secondObject = {
    first_name: "Habdul",
    last_name: "Hazeez",
};
```

当你测试`firstObject`和`secondObject`是否相等时，你会发现它们并不相同。在`cart_esmodules.js`中增加以下内容:

```
// js/use_esshoppingcart.js
// previous code remains the same

alert(firstObject === secondObject); // false
```

保存文件，确保`inex.html`上的脚本标签指向`use_esshoppingcart.js`的位置。启动(或刷新浏览器)。警报信息应显示为`false`。

现在，是时候测试`firstShoppingCart`和`secondShoppingCart`是否相等了。

添加以下代码:

```
// js/use_esshoppingcart.js
// previous code remains the same

alert(firstShoppingCart === secondShoppingCart); // true
```

这证明了模块是单件的。只有一个实例存在，作为额外的证据，您可以使用`firstShoppingCart`或`secondShoppingCart`对象将商品添加到购物车中，总数将是相同的。

在此之前，确保`cart_esmodules.js`将其函数导出为`addItem`和`cartTotal`，然后修改`use_esshoppingcart.js`如下:

```
// js/use_esshoppingcart.js
// Comment the two alert statements then add
// the following

// add some items
firstShoppingCart.addItem("USB drive", 20);
firstShoppingCart.addItem("Keyboard", 10);

// Use the "second" shopping cart
secondShoppingCart.addItem("JoyStick", 30);

// Check the total
alert(firstShoppingCart.cartTotal()); // 60
alert(secondShoppingCart.cartTotal()); // 60
```

下一节将使用`use_esshoppingcart.js`的当前状态，因此，保持原样，然后继续。

#### 进口是活的

当我们说“导入是活动的”时，这意味着当您修改一个模块的导出值时，任何其他请求该模块的文件都可以访问这个修改后的值。

创建一个名为`testLiveImports.js`的新模块，并保存以下代码:

```
// js/testLiveImports.js
import { cartTotal } from "./cart_esmodules.js";

alert(cartTotal());
```

切换到`index.html`，然后添加一个指向`testLiveImports.js`的脚本标签。现在，您将有两个脚本标记，如下面的代码块所示。

```
<!-- index.html -->
<!-- Preious code remains the same. -->
<script type="module" src="js/use_esshoppingcart.js"></script>
<script type="module" src="js/testLiveImports.js"></script>
```

当你刷新浏览器时，你会发现`testLiveImports.js`中的`cartTotal()`与`use_esshoppingcart.js`中的`cartTotal()`具有相同的值。因此，您应该得到 3 条输出相同的警报消息:`60`。

#### 如何在 Node.js 中使用模块

正如您在前面几节中了解到的，Node.js 使用 CommonJS 模块格式，但它也支持 es 模块。

同时，到目前为止，我们创建的所有模块都是在 Web 浏览器中执行的，现在让我们看看它们在 Node.js 中是如何运行的。

现在，在测试之前，请注意:在 Node 13 之前，ES 模块是一项实验性的技术，因此，您可以通过用`.mjs`扩展名保存它并通过一个标志访问它来使用模块。

另一方面，从节点 13 开始，您可以以两种方式使用模块。它们是:

*   使用`.mjs`扩展名*或*保存模块
*   在最近的文件夹中用`type="module"`创建一个`package.json`文件。

为了让事情更清楚:下面是我们要解释的。

*   如何测试 Node.js <= 12 中的 ES 模块
*   如何测试 Node.js > = 13 中用`.mjs`扩展名保存的模块
*   如何测试 Node.js > = 13 中用`.js`扩展名保存的模块

##### 如何在 Node.js <= 12 中使用 ES 模块

在撰写本文时，Node.js 的当前版本是`15.10.0`和`14.16.0 LTS`，但是如果您有比这些版本更低的 Node.js 版本，下面是您将如何测试它。

我们将使用上一节中的`use_esshopingcart.js`(正如我们离开时一样)，但在此之前，请执行以下操作:

*   制作一份`use_esshopingcart.js`的副本，并用扩展名`.mjs`保存文件。
*   将`alert()`函数改为`console.log()`，因为`alert()`在 Node.js 中不起作用

当你完成后，打开你的控制台，导航到我们在本节中使用的项目文件夹的`js`目录，然后输入以下命令，并按下键盘上的回车键:

```
node --experimental-modules use_esshoppingcart.mjs
```

您应该会得到类似下图的输出。

![Experimental Warning](img/b8cb9fb75264603b956ae4d636a83d74.png)

##### 如何在 Node.js > = 13 中使用以`.mjs`扩展名保存的模块

如果您安装了 Node.js 13 以上版本，并且您的模块以`.mjs`扩展名保存，请键入以下内容并按键盘上的 enter 键:

```
node use_esshoppingcart.mjs
```

输出应该是相同的。

![esshoppingcart Output](img/70109823ae61bfefd38b4d44e6371de8.png)

##### 如何测试 Node.js > = 13 中用`.js`扩展名保存的模块

节点 13 以上允许您使用以`.js`扩展名保存的模块，但是您需要将`{"type": "module"}`添加到最近的`package.json`文件中。

切换到您的编辑器，将下面的 JSON 代码保存在您的`js`目录中:

```
// js/package.json
{
    "type": "module",
}
```

接下来可以用`use_esshoppingcart.js`。不要忘记将`alert()`功能更改为`console.log`()，因为我们只在`.mjs`版本中做了这一更改。

```
# Kindly note the .js extension.
node use_esshoppingcart.js
```

正如所料，输出应该是相同的。

![Same Output](img/54f019f66cef0844efac9824819b6804.png)

#### 模块的特征

如果您读到这里，您已经知道了一个模块的特征。以下是一些例子:

*   模块是单件的
*   模块说明符充当全局 id
*   没有使用全局变量。
*   设计用于异步装载

#### 模块的优势

*   可重用性:相同的模块可以在任意数量的应用程序之间共享
*   语法很容易阅读
*   命名导出使得模块树不可动摇:您可以从一个模块中只导入您需要的东西，从而得到更小的包大小和更好的性能。
*   使用模块，您可以将应用程序的代码库分割成具有独立功能的较小文件。

这就是了。ES 模块。有关 ES 模块的更多信息，请参考进一步阅读部分。

下一个:节点包管理器。

## 节点程序包管理器(NPM)

节点包管理器(Node Package Manager)，俗称 NPM，是世界各地的开发人员用来共享模块(也称为包)的流行包管理器，成熟的应用程序如 [express](https://www.npmjs.com/package/express) 。

NPM 是用 JavaScript 编写的，由 Isaac Z. Schlueter 开发，他从 PHP 和 Perl 程序员分别使用的 PHP 扩展和应用程序库(PEAR)和综合 Perl 存档网络(CPAN)中获得了灵感。

目前，NPM 是世界上最大的软件注册中心，拥有数以千计的软件包。它也是 Node.JS 的默认包管理器。你可以在 npm 网站上找到许多这样的包。

NPM 预装了 Node.js，如果你已经安装了 Node.js，你可以通过在命令行键入`npm`或者 [GitBash](https://git-scm.com/downloads) 来使用它，你会得到类似下图的输出。

![NPM Command Result](img/c54da1b4d3fc98b1585a17832b839363.png)

该图像向您展示了一些可用的`npm`命令选项。您会发现自己最常使用的命令是`npm install`。此命令用于安装在 NPM 注册表上发布的软件包。

像往常一样，小心你下载的软件包，并留意任何具有已知漏洞的软件包。幸运的是，当你安装这样的应用程序时，NPM 会通知你。

为了更熟悉 NPM，你可以在 YouTube 上观看布拉德·特拉弗斯的 NPM 速成班。

## 使用 ES 模块的项目

自从 ES 模块成为主流以来，一些著名的项目已经在其代码库中采用了它，其中包括:

### 反应堆

React 是一个用于构建用户界面(UI)的 JavaScript 库。自 2013 年脸书发布以来，它近年来越来越受欢迎。

以下是使用 ES 模块的 React 代码示例:

```
import React from "react";
import logo from "./logo.svg";
import "./App.css";

function App() {
  return (
    <div className="App">
      // UI code here
    </div>
  );
}

export default App;
```

### 苗条的

Svelte 是 JavaScript 工具箱中的另一个工具，它允许你创建所谓的“[控制论增强的网络应用](https://svelte.dev/)”。

下面是一个来自 Svelte 网站的使用 ES 模块的 Svelte 代码示例:

```
/* App.svelte generated by Svelte v3.32.0 */
import {
    SvelteComponent,
    detach,
    element,
    init,
    insert,
    noop,
    safe_not_equal
} from "svelte/internal";

function create_fragment(ctx) {
    // Code truncated
    // Check https://svelte.dev/tutorial/basics
}

class App extends SvelteComponent {
    constructor(options) {
        super();
        init(this, options, null, create_fragment, safe_not_equal, {});
    }
}

export default App;
```

## 结论

本文解释了 JavaScript 中可用的各种模块系统，以及如何使用它们来创建模块。此外，我们简要讨论了 NPM，它是一个包管理器，用于在软件开发人员之间共享模块(包)。

最后，我们给出了一些流行项目的例子，比如在代码库中使用 es 模块的 React。

## 进一步阅读

## 参考

通过理解上下文，更容易地调试 JavaScript 错误

## 调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

.

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.