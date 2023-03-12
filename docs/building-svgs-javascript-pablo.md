# 使用 Pablo 用 JavaScript 构建 SVG

> 原文：<https://blog.logrocket.com/building-svgs-javascript-pablo/>

JavaScript 和 SVG 的交叉是一个很好的例子，说明了 web 技术如何协同工作来创造比它们各自的规范总和更大的东西。

SVG 可以通过 SVG DOM 接口轻松地使用 JavaScript 来增强 web 的交互性。但是，普通的 JavaScript 工作流可能是复杂和混乱的。幸运的是，已经开发了几个库，如 Pablo 和 gySVG，来帮助用 JavaScript 简化 SVG 的构造和操作，而不会影响性能。

在本文中，我们将介绍 Pablo，并讨论如何使用它来创建简单和复杂的 SVG 形状。

## 巴勃罗是什么？

Pablo 是一个轻量级的开源库，用于简化 JavaScript 中 SVG 的构造和操作。它的功能相对完整，并且有一个友好的、易于探索的 API。

Pablo 主要关注简单性和性能。该库使开发人员能够更轻松地处理动态生成的矢量图形，同时避免普通 JavaScript 的冗长工作流程。

有几个库和框架可用于绘制和操作 SVG。但 Pablo 提供了一种独特的简化方法和一个插件系统，允许动态添加新功能。

## 比较香草 JavaScript 和 Pablo

绘制最简单的 SVG 形状的普通代码往往有几行长。这段冗长的代码会很快变得难以理解和维护。

Pablo 提供了命名方法，如`.line()`和`.circle()`来创建标准类型的 SVG 元素。Pablo 还提供了操纵 SVG 和 HTML 元素来改变它们的外观、大小、位置等的方法。这些方法使代码变得全面但非常简洁。

这里有一个普通 JavaScript 代码和 Pablo 代码的比较。两个示例都呈现了一个简单的 SVG 圆:

```
// vanilla js
const ns = 'http://www.w3.org/2000/svg'

const div = document.getElementById('vector') 

const svg = document.createElementNS(ns, 'svg')

svg.setAttributeNS(null, 'width', '100%')

svg.setAttributeNS(null, 'height', '100%')

div.appendChild(svg)

const circle = document.createElementNS(ns, 'circle')

circle.setAttributeNS(null, 'width', 100)

circle.setAttributeNS(null, 'height', 100)

circle.setAttributeNS(null, 'fill', '#f06')

svg.appendChild(circle)

// Pablo
const svg = Pablo(HTMLElement).svg({height:100}),
    circles = svg.circle({cy:50, r:50});

```

如你所见，Pablo 的代码比普通的 JS 简单。

## Pablo 入门

现在我们已经对 Pablo 的简洁程度有了一些了解，让我们看看如何在项目中设置它。

有两种方法可以开始使用 Pablo:下载脚本并将其添加到 HTML 文档中，或者用 Bower 包管理器安装它。

### 加载 Pablo 脚本

下载 Pablo 的脚本时，您可以选择下载完整的脚本或缩小的脚本。完整的脚本是用于开发的——它很大，并且没有经过优化。缩小版的剧本是用于制作的。它是完整脚本的压缩、优化版本。

完整脚本和精简脚本都可以从各自的脚本页面直接下载:`[pablo.js](http://pablojs.com/downloads/pablo.js)`和`[pablo.min.js](http://pablojs.com/downloads/pablo.min.js)`。

要将任一脚本添加到项目中，请在项目文件夹中创建一个新文件:

*   `pablo.js`获取完整脚本
*   `pablo.min.js`对于缩小的脚本

然后，从脚本页面复制并粘贴代码，并保存。

![Loading a Pablo Script](img/9768e8c972a98190bc201f0d12400dd8.png)

现在，用`pablo.min.js`将脚本文件添加到项目的 HTML 中:

```
<script src="pablo.min.js""></script>
```

或者，使用作为`src`属性传入的下载文件夹的路径来添加脚本:

```
<script src="source/pablo.min.js"></script>

```

### 安装 Pablo 和 Bower

Bower 是一个包管理器，像 Yarn 和 npm 一样，它管理框架、库、资产和实用程序，并确保它们是最新的。

Bower 是一个命令行实用程序。您需要在您的机器上安装最新版本的 Node.js 和 Git。首先，我们使用这个命令来安装 Bower:

```
$ npm install -g bower

```

接下来，我们用以下命令安装 Pablo:

```
$ bower install pablo

```

## 了解 Pablo 积木

我们之前研究了 Pablo 代码块的基本结构。现在，让我们深入了解一下这个库的构建块以及它们是如何工作的。

对象是巴勃罗最重要的方法。它包含几个属性，可用于创建 SVG 元素并将其附加到 DOM 中预先存在的元素上。它还用于创建新的和预先存在的 HTML 或 SVG 元素的类似数组的结构(称为集合)。我们将在下面的章节中更详细地讨论这些。

当登录到控制台时，`Pablo()`方法返回一个空的 Pablo 集合:

```
const collection = Pablo();
alert(collection.length); // 0

```

要将 Pablo 加载到文档中，我们必须将它附加到 DOM 中预先存在的 HTML 元素中。假设文档中有一个 class 属性为`elem`的 div 元素:

```
<div class="elem"></div>

```

我们可以将 Pablo SVG 附加到文档中的 div 容器，方法是将类或 id 作为参数传递给`Pablo()`方法，然后链接一个`.svg()`方法，将向量的宽度和高度作为参数指定:

```
const svg = Pablo(.mycontainer).svg({
    width: 200,
    height: 100
});

```

上面的代码在 DOM 中创建了一个`<svg></svg>` HTML 元素，然后将它附加到我们之前创建的 div 容器中。

DOM 中的输出如下所示:

```
<div class="mycontainer">
    <svg version="1.1" width="200" height="100"></svg>
</div>

```

## 向集合中添加元素

集合是一个类似数组的对象，当 Pablo 在 DOM 中创建或选择任何元素时，它包含 SVG 和 HTML 元素。可以直接处理元素，但是集合对象上的方法通常用于操作和过滤 Pablo 中的元素。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

但是，有一些方法与标准 JS 数组中使用的方法是等效的，比如`.push()`、`.pop()`、`.forEach()`、`.map()`和`.filter()`。这些方法就像在标准数组对象中一样工作。例如，可以使用`.push()`方法将元素添加到集合中，或者使用`.pop()`方法移除元素。

向集合追加元素就像创建一个新元素，设置它的属性对象，然后用`.push()`、`.concat()`或`.unshift()`方法将它链接到集合一样简单:

```
const collection = Pablo(['circle', 'path']);

collection.push(Pablo.rect({width: 200, height: 100}));

alert(collection.lenght) //3

```

在本例中，我们创建了一个集合，传入了一个元素方法的数组，然后用`.push()`方法向该数组添加了一个新的矩形形状。`.push()`方法将新元素追加到集合的末尾。它相当于 jQuery 中的`.add()`。

查看 Pablo [文档](http://pablojs.com/api/)以获得可用于操作集合的方法的完整列表。

## 使用元素方法创建 SVG 形状

现在，让我们看看如何用 Pablo 创建基本的 SVG 形状，以及如何将它们附加到创建的 SVG 元素上。

元素方法用于创建与方法同名的新 SVG 元素。例如，圆形、矩形和线条元素将分别用`.circle()`、`.rect()`和`.line()`方法创建。这些元素嵌套在 DOM 中的`<svg></svg>`元素下，创建了一个类似于以下示例的嵌套结构:

```
<svg>
    <line x1="5" y1="195" x2="295" y2="5" stroke="green" stroke-width="10"/>
</svg>

```

我们可以通过直接在集合上调用这些元素来独立地创建这些元素，并把它们附加到 DOM 上的一个元素中。

或者，我们可以简单地将它们链接到元素:

```
/* Append an <svg> element to an HTML element */
const svg = Pablo(demoElement).svg({
    width: 220,
    height: 220
});

/* Create a <circle> element, wrapped in a collection */
const circle = Pablo.circle();

/* Create a <rectangle> element, wrapped in a collection */
const rect = Pablo.rect();

/* Append to svg element */
svg.append(circle, rect)

```

## 使用方法链接创建 SVG 形状

帕布罗很大程度上是受到了 jQuer [y](https://blog.logrocket.com/the-history-and-legacy-of-jquery/) 的启发。它使用类似 jQuery 的链接方法调用模式来操作 SVG 和 HTML 元素。这种技术使得在一条语句中对同一个元素运行多个连续的方法成为可能。

要创建一个链，只需将一个方法附加到前面的方法上:

```
/* Append an <svg> element to an HTML element */
const svg = Pablo(demoElement).svg({
    width: 220,
    height: 220
});

/* Append a <rect> element to the <svg> */
svg.rect({width:200, height:100}).transform('translate', 70).attr('fill', 'turquoise')

```

在这个例子中，我们将`.rect()`、`.transform()`和`.attr()`方法链接到 SVG 元素。Pablo 追加一个宽 200px 高 100px 的矩形，用 CSS `transform`属性旋转元素，然后给 shape 元素设置一个 attribute 属性来改变矩形的颜色。

我们可以通过添加换行符和缩进来格式化代码块，以避免出现混乱语法的兔子洞:

```
/* Append an <svg> element to an HTML element */
const svg = Pablo(demoElement).svg({
    width: 220,
    height: 220
});

/* Append a <rect> element to the <svg> */
svg.rect({width:200, height:100})
   .transform('translate', 70)
   .attr('fill', 'turquoise')

```

在上面的例子中，Pablo 将忽略空白，并将该块作为一长行代码执行。

> 添加外部样式表/笔此处添加的任何 URL 都将按顺序添加，并放在编辑器中的 CSS 之前。通过使用其 URL 和适当的 URL 扩展名，您可以使用另一个笔中的 CSS。JavaScript 预处理器巴贝尔包括 JSX 处理。

这种将特定命名方法链接到元素的技术使我们能够快速创建多个 SVG 形状并将其附加到 DOM。

## 将外部 SVG 添加到集合

可以使用`.load()`方法将外部 SVG 文件导入到集合中。这个方法接受一个到 SVG 的路径字符串:

```
const rocket = Pablo(demoElement).load('/rocket.svg');
    /* Find some elements */
    rocket.find('path, rect')
        /* Change their attributes */
        .attr('opacity', 0.2)

```

回调函数可以作为第二个参数插入到方法中。方法可以直接从回调函数链接到外部 SVG 文件:

```
Pablo(demoElement).load('/rocket.svg', function(rocket){
    /* Find some elements */
    rocket.find('path, rect')
        /* Change their attributes */
        .attr('opacity', 0.2)
});

```

现在，让我们看一下创建复杂集合的几种元素操作方法。

### `.attr()`

`.attr()`方法用于将一个命名方法的属性设置为一个指定值:

```
const svg = Pablo(demoElement).svg({height:100}),
    rect = svg.rect({
        width: 200,
        height:100,
    });

rect.attr('fill', 'blue');

```

在这个例子中，我们创建了一个新的集合，并将一个名为`.rect()`的方法附加到这个集合中。接下来，我们调用了`.attr()`方法，并为元素添加了`blue`的`fill`属性。

当在包含多个元素的集合上调用`.attr()`方法时，可以通过传入一个数组作为值来为每个元素设置不同的值。

```
const svg = Pablo(demoElement).svg({height:100}),
    circles = svg.circle({cy:50, r:50}).duplicate(4);
        .attr({
           fill: ['red', 'green', 'blue', 'orange', 'purple'],
           cx: [50, 150, 250, 350, 450]
         });         

```

在本例中，数组中的第一项将用于设置第一个元素的属性，第二项将用于设置第二个元素的属性，依此类推。

我们还可以用一个`.attr()`方法和一个指定的对象为集合中的所有元素设置多个属性:

```
const svg = Pablo(demoElement).svg({height:100}),
    circles = svg.circle({cy:50, r:50}).duplicate(4);
        .attr({
            x: 50,
            y: -50,
            width: 200,
            height:100,
            fill: 'orange',
            transform: 'rotate(45)'
         });         

```

### `.duplicate([amount])`

`.duplicate([amount])`方法对集合中的所有元素进行深度克隆。该方法在 DOM 中的原始元素之后插入重复的元素，并返回新的集合。

```
const svg = Pablo(demoElement).svg({height:40})
square = svg.rect({width:40, height:40});

square.duplicate(5)
    // Set x position for each element
    .attr('x', function(el, i){
        return i * 50;
    });

```

在这个例子中，一个正方形被复制了五次。

> 添加外部样式表/笔此处添加的任何 URL 都将按顺序添加，并放在编辑器中的 CSS 之前。通过使用其 URL 和适当的 URL 扩展名，您可以使用另一个笔中的 CSS。JavaScript 预处理器巴贝尔包括 JSX 处理。

`.find(selector)`

### `.find(selector)`方法用于搜索与插入的 CSS 选择器或选择器列表匹配的元素，然后在新的集合中返回这些后代。

在这个例子中，`.find()`方法从导入的 SVG 中返回所有的`.path()`和`.rect()`元素，然后将一个不透明度属性附加到返回的集合中的每个元素上。

```
Pablo(demoElement).load('/rocket.svg', function(rocket){
    /* Find some elements */
    rocket.find('path, rect')
        /* Change their attributes */
        .attr('opacity', 0.2)
});

```

使用 Pablo 创建 SVG 事件

## 有了 Pablo，你就不用担心用`Element.addEventListener`手动给矢量图形添加事件监听器了。该库提供了几种方法来管理可以链接到元素的本地和自定义事件。

`.on()`

### `.on()`方法向集合中的每个元素添加事件监听器。像`click`或`mouseout`这样的事件类型可以作为一个字符串传递到方法中，旁边是一个包含事件逻辑的回调函数:

在这个例子中，我们创建了一个圆圈，并将一个`click`事件链接到它。单击时，圆的`fill`属性将变为`blue`。

```
const svg = Pablo(elem).svg({
    width: 200,
    Height: 100
})

const circles = svg.circle();
circles.on('click', function(circle){
    circle.attr({fill: 'blue'})
});

```

添加外部样式表/笔此处添加的任何 URL 都将按顺序添加，并放在编辑器中的 CSS 之前。通过使用其 URL 和适当的 URL 扩展名，您可以使用另一个笔中的 CSS。JavaScript 预处理器巴贝尔包括 JSX 处理。

> Add External Stylesheets/Pens Any URLs added here will be added as s in order, and before the CSS in the editor. You can use the CSS from another Pen by using its URL and the proper URL extension. JavaScript Preprocessor Babel includes JSX processing.

使用 Pablo 创建 SVG 动画

## Pablo 提供了几种创建动画效果的方法。我们可以使用 Pablo CSS 的`transform()`、`transition()`和`transformCss()`方法或者 SVG 原生的`<animate>`和`<animateMotion>`元素来创建效果。

`transition(property, duration)`

### 方法在集合中的每个元素上创建 CSS 过渡。当设置了过渡并修改了命名的 CSS 属性时，更改将在指定的持续时间内发生。在这个语法中，`property`代表 CSS 属性的名称，`duration`代表以毫秒为单位的过渡长度。

我们首先创建一个圆，然后复制它三次。我们将一个过渡效果链接到一个延迟持续时间为 1 毫秒的圆，以便为圆的`stroke-width`设置过渡效果。最后，我们链接一个`click`事件，增加和减少循环的`stroke-width`。

```
const container = Pablo(demoElement),
    svg = container.svg({width:'100%', height:160}),
    circles = Pablo.circle().duplicate(3).attr({
        r: 50,
        cx: function(el,i){return i * 140 + 80},
        cy: 80,
        stroke: 'lightblue',
        fill: 'darkblue',
        cursor: 'pointer'
    }).appendTo(svg);

// Transition any changes to `stroke-width` over 1000ms
circles.transition('stroke-width', 1000);

container.on('click', function(){
    // Change the `stroke-width`
    circles.css('stroke-width', 60);

    // Change it back after a delay
    window.setTimeout(function(){
        circles.css('stroke-width', 0);
    }, 750);
});

```

添加外部样式表/笔此处添加的任何 URL 都将按顺序添加，并放在编辑器中的 CSS 之前。通过使用其 URL 和适当的 URL 扩展名，您可以使用另一个笔中的 CSS。JavaScript 预处理器巴贝尔包括 JSX 处理。

> Add External Stylesheets/Pens Any URLs added here will be added as s in order, and before the CSS in the editor. You can use the CSS from another Pen by using its URL and the proper URL extension. JavaScript Preprocessor Babel includes JSX processing.

`Pablo(elements, [attributes])`

### 当一个元素和一个属性作为参数传递给函数时，`Pablo(elements, [attributes])`函数返回一个新的集合。

`elements`参数可以代表一个 DOM 元素、另一个集合或元素数组。如果指定了参数,`attributes`将在集合中的每个元素上设置:

`Pablo(selector, [context])`

```
const element = document.getElementById('foo'),
    collection = Pablo(element, {fill:'red'});

```

### `Pablo(selector, [context])`函数使用浏览器的本地选择器引擎`Element.querySelectorAll`来选择指定的 CSS 类、ID 或以逗号分隔的选择器列表，然后在新的集合中返回它们。

由于该函数使用浏览器的本地选择器引擎，因此 SVG 和 HTML 元素都可以作为目标。

如果指定了 context 参数，它将使函数只返回所提供的上下文的后代元素。上下文可以是一个元素或一组集合。

```
// A single element, specified by id
Pablo('#foo');

// Multiple elements, specified by class
Pablo('.bar');

// Multiple selectors
Pablo('circle, line, #foo, .bar');

```

上面的代码将只返回形状的`myShapes`集合中具有`.hexagon`类的元素集合或单个元素。

```
Pablo('.hexagon', myShapes);

```

结论

## 在本文中，我们介绍了如何开始使用 Pablo，以及如何使用 Pablo 用 JavaScript 简洁地创建简单和复杂的 SVG 元素。我们还查看了几个用例，展示了一些可用于 SVG 的 Pablo 实用程序。我们仅仅触及了帕布罗身上的皮毛。查看 Pablo [文档](http://pablojs.com/api/)以了解该介绍。

您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

## 毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。

Build confidently — [Start monitoring for free](https://lp.logrocket.com/blg/javascript-signup).