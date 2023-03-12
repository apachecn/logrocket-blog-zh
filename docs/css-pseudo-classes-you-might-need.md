# 你可能需要的 CSS 伪类

> 原文：<https://blog.logrocket.com/css-pseudo-classes-you-might-need/>

CSS 伪类是添加到选择器中的关键字，它指定了所选元素的特殊状态。

你可能已经熟悉了常用的，比如`:hover`。

在本帖中，我们将探索一些鲜为人知和不常使用的伪类及其实际用例。

### `:is()`

该函数将一个选择器列表作为其参数，并选择与列表中的一个选择器匹配的任何元素。

现在，我们这样写:

```
article > h1, article > h2, article > h3 {
  color: #555;
}

button:focus, button:hover {
  border: 1px solid orangered;
}
```

会变成这样:

```
article > :is(h1,h2,h3) {
  color: #555;
}

button:is(:focus, :hover) {
  border: 1px solid orangered;
}
```

正如您所看到的，这对于简洁地编写大型选择器非常有用。

某些旧版本的浏览器支持此功能，因为`:is` [以前被称为](https://github.com/w3c/csswg-drafts/issues/3258) `:matches()`。

我们还可以使用更老的、带有供应商前缀的伪类— `:any()` —它的行为方式与`:is()`类似。

```
article > :-webkit-any(h1,h2,h3) {
  color: #555;
}

article > :-moz-any(h1,h2,h3) {
  color: #555;
}

article > :matches(h1,h2,h3) {
  color: #555;
}
```

### `:focus-within`

这表示已获得焦点的元素或包含已获得焦点的元素。

```
<form>
  <label for="name">Name:</label>
  <input type="text" id="name">
  <label for="email">Email</label>
  <input type="email" id="email">
</form>
```

当它的任何输入元素被聚焦时，我们可以修改表单样式。

```
form:focus-within {
 background: coral;
}
```

这是一个仅用 CSS 实现的下拉菜单，使用了`:focus-within`
(【https://codepen.io/ananyaneogi/pen/KKwbyQX】[)](https://codepen.io/ananyaneogi/pen/KKwbyQX)

### `:focus-visible`

当元素处于焦点状态时应用。

在这种情况下，大多数浏览器默认显示一个*聚焦环*。

该选择器可用于根据用户与元素的交互方式(如鼠标、键盘等)提供不同的焦点指示器。)

在这个例子中，当我们用键盘导航链接时(例如，通过 tab 键)，只有这样焦点样式才会被应用。

这有助于应用突出的样式来引导使用键盘导航的用户，而不改变使用鼠标时的焦点状态。

> body { background-color:# f7f7f 7；显示器:flex 身高:100vhjustify-content:居中；对齐-项目:居中；字体系列:无衬线字体；} section { border: 2px 虚线# ccc 填充:2em} h1 { margin-bottom:1.8 em；} ul { list-style-type:无；填充:0；}李{ margin-bottom:1.6 em；字号:1.2em}李:最后一个孩子{ margin-bottom:0；} a { color:cadet blue；文字-装饰:无；字体粗细:粗体；} a:对焦，a:悬停，a:主动{ color:# 9f 64 a 9；} /*点击时避免焦点线的技巧，只有用键盘导航时才会应用焦点线*/a:focus:not(:focus-visible){ outline:2px solid transparent；} a:focus { outline:2px solid # 9f 64 a 9；}

### `:only-child`和`:only-of-type`

`:only-child`选择一个父元素的唯一子元素。这意味着该父元素中只存在一个元素。即使是不同的元素类型，也不会被认为是独生子女。一个元素，没有例外！

```
p:only-child {
      color: magenta;
    }

    <article>
      <p>A simple paragraph</p> <!-- p will be of magenta colour -->
    </article>

    <!-- ❌ no element selected because p is not the only child -->
    <article>
      <h1>Heading</h1>
      <p>A simple paragraph</p>
    </article>
```

选择一个元素，该元素是父元素中特定类型的唯一子元素。有其他不同类型的兄弟姐妹没问题。

```
p:only-of-type {
color: magenta;
```

```
}
```

```
<!-- p will be of magenta colour, because even though article contains more than one child but still <p> is the only one of it's own type -->
<article>
  <h1>Heading</h1>
  <p>A simple paragraph</p>
</article>
```

这里有一个使用`:only-child`的实际例子，如果一个产品卡是唯一剩余的产品卡，则向该产品卡添加“快点，剩余最后一件商品”类型的消息。

> 李. product-card:独生子女:后{ content:"赶紧！最后一件物品。

`:not()`

### 这表示与它作为参数的选择器列表不匹配的元素。

这不是一个非常实用的例子，但是我们可以使用`:not()`和其他伪类来获得有趣的结果。我们接下来会看到。

```
<article>
  <p>A simple paragraph</p>
  <span>Span text</span>
</article>

/* Everything except the <p> inside the <article> will be of magenta colour! */
article :not(p) {
  color: magenta;
  }
```

**注意**:嵌套`:not`即`:not(:not(...))`无效。

`:empty`

### 这表示任何没有子元素的元素。子元素可以是元素节点，也可以是包含空格的文本。

这里有一个使用`:empty`和`:not()`消除不包含内容的空标签的例子。

```
/* Selects any <span> that contains no content */
span:empty {
   background: magenta;
}
```

添加外部样式表/笔此处添加的任何 URL 都将按顺序添加，并放在编辑器中的 CSS 之前。通过使用其 URL 和适当的 URL 扩展名，您可以使用另一个笔中的 CSS。JavaScript 预处理器巴贝尔包括 JSX 处理。

> Add External Stylesheets/Pens Any URLs added here will be added as s in order, and before the CSS in the editor. You can use the CSS from another Pen by using its URL and the proper URL extension. JavaScript Preprocessor Babel includes JSX processing.

更多来自 LogRocket 的精彩文章:

* * *

* * *

`:placeholder-shown`

### 这表示当前显示占位符文本的任何输入元素。

在本例中，我们突出显示了表单中用户尚未填写的必填字段。

我们通过简单地定位占位符仍在显示的输入来表明字段还没有被填充。

{框大小:边框-框；} .container { font-family:sans-serif；边框:1px 虚线# ccc 填充:30px 字体大小:14px 边距:30px} .容器 div { margin-bottom:20px；} .容器分区:最后一个子级{ margin:0；} .容器标签{ display:block；字体大小:13px 边距-底部:5px} .容器输入{ font-size:inherit；font-family:继承；边框:2px 纯色# 999；显示:块；宽度:100%；填充:10px} .容器输入:必选:占位符-显示{ border: 2px 纯色洋红色；}

> { box-sizing: border-box; } .container { font-family: sans-serif; border: 1px dashed #ccc; padding: 30px; font-size: 14px; margin: 30px; } .container div { margin-bottom: 20px; } .container div:last-child { margin: 0; } .container label { display: block; font-size: 13px; margin-bottom: 5px; } .container input { font-size: inherit; font-family: inherit; border: 2px solid #999; display: block; width: 100%; padding: 10px; } .container input:required:placeholder-shown { border: 2px solid magenta; }

如果仔细观察上面的例子，您会注意到另一个伪类`:required,`与`:placeholder-shown`一起使用。

是我们拥有的众多与表单相关的伪类之一。接下来，我们将看看一些与表单相关的伪类，它们使表单的用户体验更好。

`:required`

### 这表示任何设置了所需属性的输入元素。这对于突出显示用户在提交表单之前必须输入的字段非常有用。

`:read-only`

### 这表示用户不可编辑的元素。

`:invalid</code`

### 这表示其内容无法验证的任何输入表单元素。

`:valid`

### 这表示其内容验证成功的任何输入表单元素。

下面是一个使用前面提到的与表单相关的伪类的例子:

{框大小:边框-框；} .container { font-family:sans-serif；边框:1px 虚线# ccc 填充:30px 字体大小:14px 边距:30pxdiv { margin-bottom:20px；位置:相对；&:最后一个孩子{ margin:0；} }标签{ display:block；字体大小:13px 边距-底部:5px} button { border: 1px solid rgba(0，0，0，0.9)；颜色:# fff

> 结论

请记住，在生产中使用它们之前，请始终参考浏览器兼容性。

### 然而，不要让浏览器的不一致性阻止你尝试新事物:只要准备好后备，你就可以开始了。

在 [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes) 上找到更多与这些伪类相关的文档。

你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

## .

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

[LogRocket](https://lp.logrocket.com/blg/css-signup) is like a DVR for web and mobile apps, recording everything that happens in your web app or site. Instead of guessing why problems happen, you can aggregate and report on key frontend performance metrics, replay user sessions along with application state, log network requests, and automatically surface all errors.

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).