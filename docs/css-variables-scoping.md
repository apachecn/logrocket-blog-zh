# CSS 变量:作用域- LogRocket 博客

> 原文：<https://blog.logrocket.com/css-variables-scoping/>

我们可以像在其他编程语言中声明变量一样在 CSS 中声明变量。

我们在 CSS 中声明一个被赋值的变量。然后可以在 CSS 规则集中使用该变量。

例如，在 JavaScript 中，我们使用关键字`let`、`var`和`const`来声明变量:

```
let nine = 9;
var ten = 10;
var eight = 8;
```

然后，我们通过引用它们的变量名来使用它们:

```
>> nine
9
>>  ten
10
>> eight 
8
```

我们也可以在 CSS 中这样做。要在 CSS 中声明变量，我们使用以下格式:

```
--varName
```

在 CSS 中，它看起来像这样:

```
body {
    --mainColor: limegreen;
}
```

假设我们声明了一个名为`--mainColor`值为`limegreen`的变量。这个变量包含一个颜色名称。

为了使用 CSS 中的变量，我们使用`var()`:

```
var(--varName);
```

`var`将检索传递给它的变量的值，并用值替换它本身:

```
body {
    --mainColor: limegreen;
}

div {
    color: var(--mainColor);
}
```

这里，`div`使用`var`函数消耗`mainColor`变量。`var`将从`--mainColor`中检索值`limegreen`，并用`--mainColor`变量的值`limegreen`代替自己。

因此,`div`元素中文本节点的颜色将是浅绿色。换句话说，CSS 代码将转换为:

```
body {
    --mainColor: limegreen;
}

div {
    color: limegreen;
}
```

## 辖域

CSS 层次结构中声明 CSS 变量的位置将决定它在整个层次结构的较低级别中的可见性级别。

在整个页面中使用的 CSS 变量在`:root`伪选择器或`html`选择器中声明。

这是因为页面上的所有元素都包含在一个 HTML 元素中，所以在 HTML 元素或其`:root`选择器中声明的 CSS 变量对于其子元素的使用是可见的。

`:root`是附加到文档中 HTML 元素根的伪选择器。在 RSS 文档中，`:root`元素被附加到 RSS 元素上。

通常，CSS 变量仅对声明它的父元素的子元素可见。

现在，我们有了这个:

```
<body>
    <div>Div 1</div>
    <div>Div 2</div>
</body>
```

`body`元素是其子元素`div`、`Div 1`和`Div 2`的父元素。

所以这是可行的:

```
body {
    --bgColor: limegreen;
}

div {
    background: var(--bgColor);
}
```

CSS 变量`bgColor`在 body 元素中声明，并在`div`中使用，以将 div 元素的背景设置为浅绿色。

这对我们的两个 div 元素是可见的，因为它们是 body 元素的子元素，body 元素是声明它所使用的 CSS 变量的地方。

现在，如果反过来是正确的:

```
body {
    background: var(--bgColor);
}

div {
    --bgColor: limegreen;
}

<body>
    <div>Div 1</div>
    <div>Div 2</div>
</body>
```

`--bgColor`变量在 div 元素中声明，在 body 元素中使用。现在，body 元素位于 div 元素之上，所以 body 元素看不到`--bgColor`变量。因此，主体元素的背景不会变成石灰绿色。

在这种情况下，`--bgColor`变量对于 div 元素下面的元素是可见的:

```
body {
    background: var(--bgColor);
}

div {
    --bgColor: limegreen;
}

p {
    background: var(--bgColor);
}

<body>
    <div>Div 1
        <p>Paragraph 1</p>
    </div>
    <div>Div 2
        <p>Paragraph 2</p>
    </div>
</body>

```

`--bgColor`变量将对`p`元素可见，因为它们是`div`的子元素。

我们在这里可以看到 CSS 变量的可见性依赖于一种父子关系。子元素在其父元素的范围内，因此它可以在父元素的范围内使用 CSS 变量声明。

主题化是用 CSS 变量完成的，主题化通常会传播整个 DOM 树，所以 CSS 变量通常设置在`html`元素或`:root`元素中。这是因为没有元素放在 HTML 元素之外:

```
:root {
    --bgColor: lightcoral;
    --mainColor: limegreen;
    --borderColor: seagreen;
}
```

在极少数情况下，当我们想要使用 CSS 变量来主题化 DOM 树的一部分或 DOM 分支时，我们将在 DOM 分支的根元素中设置 CSS 变量，这样它将沿着分支树向下传播。

```
<html>
    <body>
        <div class="branch">
            <p>
                Paragraph 1
            </p>
        </div>
        <p>
            Paragraph 2
        </p>
    </body>
</html>
```
```css
.branch {
    --brBgColor: palevioletred;
    --brMainColor: blueviolet;
}

p {
    background: var(--brBgColor);
    color: var(--brMainColor);
}
```

在`div.branch`中声明的 CSS 变量对于`p`元素`Paragraph 1`将是可见的，因为它是`div`的子元素，类名为`branch`。背景颜色和文本颜色将分别被涂成`palevioletred`和`blueviolet`颜色。

元素`Paragraph 2`不会受到样式的影响，因为 CSS 变量`--brBgColor` `--brMainColor`对它来说是不可见的。这是因为它不是`div.branch`的子元素。

这就是 CSS 变量的作用域。

### 全球范围

在`:root`选择器中声明的 CSS 变量被认为是在全局范围内。这意味着可以在 CSSOM 中的任何地方访问它们。

为什么有效？

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

就像我们之前了解到的一样，`:root`选择器附加在文档的根目录下。web 文档中的所有元素都在根文档下，所以在`:root`中声明的 CSS 变量将级联到文档的所有级别。或者，文档中的所有元素都可以看到声明的 CSS 变量。

### 局部范围

就像在`:root`选择器中一样，CSS 变量也可以在 CSSOM 层次结构的所有级别或特定的选择器中声明。

现在，在这些级别或选择器中声明的 CSS 变量只对选择器及其子节点是可见的或局部的。

### 提升

CSS 变量被提升，并且在浏览器中呈现相应 HTML 元素的样式之前，它们被移动到 CSSOM 的顶部。

就像在 JavaScript 中，CSS 变量是可以被吊起来的。这意味着 CSS 变量可以在声明之前使用。

```
var num2 = 90

var add = num1 + num2

var num1 = 10

log(add)
```

在上面的例子中，`num1`变量在声明之前就被使用了。也就是说`num1`被吊起来了。运行代码时，add 将记录 100。尽管在声明之前首先被使用，JavaScript 仍然能够获得值并执行操作。

CSS 变量也是如此:

```
body {
    background-color: var(--bgColor);
}

:root {
    --bgColor: rgb(221, 221, 221);
}
```

如您所见，CSS 变量`--bgColor`在被声明到`:root`伪选择器之前就被使用了。代码运行得非常好！

所以，CSS 变量可以先访问，后声明。这使得 CSS 变量成为一个非常强大的特性。

## 检查支持

CSS 变量在主流浏览器中得到广泛支持，尽管在 Chrome 和 Firefox 的旧版本中可能缺乏支持。IE 和 Edge 对 CSS 变量的支持正在进行中。

因为不是所有的浏览器都支持 CSS 变量，所以我们可以使用`@supports`来检测 CSS 变量特性支持。

```
@supports(--bgColor: rgb(221, 221, 221)) {

}
```

第二个选项是设置回退值:

```
:root {
    --primaryColor: blue;
}

button {
    color: blue;
    color: var(--primaryColor);
}
```

我们在带有颜色`blue`的`:root`选择器中设置了一个`--primaryColor`变量。然而，我们不确定运行 CSS 的浏览器是否支持 CSS 变量。为了让代码工作，我们在按钮选择器中添加了一个回退值。这确保了按钮在不支持 CSS 变量的浏览器中使用我们的原色。

## 使用 CSS 变量的优势

### 主题

CSS 变量作用域改进了我们在 CSS 中添加和修改主题的方式。有了 CSS 变量，CSS 中的主题化就不需要额外的不同主题的样式表了。相反，你需要做的就是更新 CSS 变量。

### **造型**

> 利用 CSS 变量范围改进了样式表的大小、特异性和语义。

假设我们有一个按钮:

```
button {
    padding: 10px 5px;
}
```

具有不同的按钮样式:

```
.btn-danger {
    background-color: orange;
}

.btn-success {
    background-color: lightblue;
}
```

有了 CSS 变量，我们不必在每个按钮样式中都定义`background-color`。

```
button {
    --btnBgColor: blue;
    padding: 10px 5px;
    background-color: var(--btnBgColor);
}

.btn-danger {
    --btnBgColor: orange;
}

.btn-success {
    --btnBgColor: lightblue;
}
```

我们简单地在每个按钮样式中分配新的值。我们不再需要覆盖基本样式。

看到了吗？CSS 变量非常强大，作用域特性使它们成为简洁、模块化设计系统的理想工具。

## 结论

我们今天刚刚处理了 CSS 中的一个潜在错误。

正如我们在 JavaScript 和其他语言中有作用域一样，在 CSS 变量中也是如此。我们已经看到了 CSS 变量的一个主要应用:主题化。它能给我们提供更多的东西。

如果你对此有任何问题，或者我应该添加，纠正或删除的任何内容，请随时评论，发电子邮件或发短信给我。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。