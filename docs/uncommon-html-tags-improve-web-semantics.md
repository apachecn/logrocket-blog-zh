# 改善网络语义的不常见的 HTML 标签

> 原文：<https://blog.logrocket.com/uncommon-html-tags-improve-web-semantics/>

文字很重要。它们是有意义的，把某些词并排放在一起可以让我们向他人传达信息。标点符号也是如此。一个有标点符号的句子比没有标点符号的句子更容易理解。

我对语法和标点符号产生了兴趣——大概可以称之为“语义学”作为一名前端开发人员，我对 web 的标点符号很感兴趣——也就是说，使用正确的标签来标记页面——就像我对信息的显示一样。

我们的网页是文档。它们包含一个主标题，由一个`h1`元素表示。如果它们内部有节，每个节将有一个`h2`元素，如果这些节中的任何一个有进一步的子节，这些子节将由一个`h3`元素命名。

如果我们在看网站的主导航，我们期望一个`ul`被放置在一个`nav`元素中。侧边栏可能会在`aside`元素中(尽管不是每个人都认为这是目前最好的元素)。

我们都知道这些通用的 web 语义。然而，有许多 HTML 元素可供我们用来标记我们的网页，了解它们对我们大家都有好处。你可以在 Mozilla MDN Web 文档中找到 HTML 元素的完整列表。

## 为 SEO 和可访问性使用 HTML5 标签

你为什么应该知道这些标签？如果你在乎可访问性(你在乎！)，你会希望你的页面能被尽可能多的用户理解。如果你关心 SEO，你会希望确保机器人能够理解、索引和返回你在搜索引擎结果中的内容，以及在社交媒体帖子(如 Twitter 卡)中的内容。

基本上，拥有一个“语义网[站点]”意味着一个网站，正如蒂姆·伯纳尔·里斯所说，“信息被赋予明确的含义，更好地使计算机和人合作。”

让我们来看看一些不常用的元素，这将有助于我们创建一个更加语义化的 web。

## 您应该使用的重要 HTML 标签

### `<address>` HTML 标签

你可能会认为`<address>`元素只是用来给网站添加地址的。它*可以*用于此目的，但更具体地说，它用于为最近的父元素`<article>`或最近的父元素`body`提供“联系信息”。(我常想它应该被称为`<contact>`元素。)让我们来看一些例子:

提供博客文章作者的联系信息。确保将它放在您的`<article>`元素中。

```
<address>
  Mark Conroy can be contacted at <a href="https://mark.ie/contact">his personal website</a>.<br>
  On social media you can contact him via <a href="https://twitter.com/markconroy">Twitter</a> and <a href="https://www.linkedin.com/in/conroymark/">LinkedIn</a>.
</address>

```

您可以在网站的页脚包含您的企业联系信息:

```
<body>

  <header>
  Logo, Menu, Search, etc.
  <header>

  <main>
  The main body
  </main>

  <footer>
    <div>
      Probably some newsletter signup form, 
      and links to cookies and privacy policies
    </div>
    <address>
      Our Great Company<br>
      123 Main Street<br>
      New York.
    </address>
  </footer>

</body>

```

`<address>`元素可以很好地帮助人们找到你的位置、电话号码等。，在搜索引擎结果页面上。忽略它，后果自负。

### 使用`<time>`表示持续时间

`<time>`元素用于表示特定的时间或持续时间。使用它的一个例子是博客发布的日期，如下所示:

```
<div>Published: <time>June 23, 2021 </time></div>

```

这是使用`time`元素的一个基本例子。它通常与一个`datatime`属性一起使用，机器很容易读懂。该属性可以使用任何机器可读的格式，比如 Unix 时间戳。

```
<time datetime="1625572800">June 23, 2021 </time>

```

您还可以使用 time 元素来描述持续时间，例如阅读博客文章可能需要多长时间。

```
<time datetime="PT7M">7m</time><span>Reading time</span>

```

然后你可以用几种方式使用`time`元素，让机器明白你的意思。让我们假设有一个前端会议即将召开:

```
<p>LogRocketBlogConf will take place on <time datetime="2018-08-01">August 1</time> all fully online.</p>

<p>Talks will start at <time datetime="09:00">09:00</time> and every hour thereafter until <time datetime="17:00">17:30</time> giving you <time datetime="PT8H30M">8h 30m</time> of pure frontend bliss.</p>

```

### 使用`<abbr>`作为缩写

`<abbr>`元素有助于让机器知道你正在谈论的东西是其他东西的缩写，但对于可能不完全熟悉你所在行业的缩写的观众来说更是如此。

如果缩写是众所周知的，例如 NASA 或 USA，那么你不需要把它放在一个`<abbr>`标签中。但如果不是，这个标签就是你最好的朋友。更好的是，它还有一个可选的`title`属性，您可以使用它来完整呈现您的短语。

```
<p>I'm tired arguing about whether <abbr title="Hypertext Markup Language">HTML</abbr> and <abbr title="Cascading Style Sheets">CSS</abbr> are programming languages. Let's move on from that.</p>

```

将全文放在内联也很好。在这种情况下，您可以去掉`title`属性，就像这样:

```
<p>JavaScript (<abbr>JS</abbr>) is the programming language of the web.</p>

```

### `<data>` HTML 标签

`<data>`元素为一段特定的内容创建了一个机器可读的关系(或者 MDN 上称之为“翻译”)。例如，如果你有一家 t 恤店，你卖三种颜色和三种尺寸的 t 恤，那就是九种不同的款式。

这些变化中的每一个都有一个单独的 SKU 或产品 ID。您可以用数据元素(不是数据属性)来表示唯一标识符，如下所示:

```
<ul>
  <li><data value="lr-gr-sm">Log Rocket T-shirt: Green, Small</data></li>
  <li><data value="lr-gr-med">Log Rocket T-shirt: Green, Medium</data></li>
  <li><data value="lr-gr-lrg">Log Rocket T-shirt: Green, Large</data></li>
  <li><data value="lr-bl-sm">Log Rocket T-shirt: Blue, Small</data></li>
  <li><data value="lr-bl-med">Log Rocket T-shirt: Blue, Medium</data></li>
  <li><data value="lr-bl-lrg">Log Rocket T-shirt: Blue, Large</data></li>
  <li><data value="lr-rd-sm">Log Rocket T-shirt: Red, Small</data></li>
  <li><data value="lr-rd-med">Log Rocket T-shirt: Red, Medium</data></li>
  <li><data value="lr-rd-lrg">Log Rocket T-shirt: Red, Large</data></li>
</ul>

```

### 使用`<mark>`突出显示

`<mark>`标签用于突出显示文本。它不同于使用斜体或粗体，因为由该标签标记的文本不是由原作者完成的。你可以把它想象成用荧光笔突出你正在阅读的书中的文字。

这本书的原作者不这样做。相反，您这样做是因为您特别想突出显示该文本的某些内容。

我发现它在创建网页时很有帮助，并且需要记住向页面添加一些内容，这样我以后就不会忘记了(这并不完全是我的本意，因为我是作者，但它非常方便)。经常在写博文的时候，我可能会有这样的话:

参见 [CodePen](https://codepen.io) 上马克康罗伊([@马克康罗伊](https://codepen.io/markconroy) )
的笔 [日志火箭博客样本——马克元素](https://codepen.io/markconroy/pen/VwbKYpo)。

使用`<mark>`标签的例子经常可以在搜索结果页面中看到，搜索引擎会突出显示您搜索的短语，而不是原作者。在这种情况下，突出显示的文本仅在该搜索上下文中重要。

参见 [CodePen](https://codepen.io) 上马克康罗伊([@马克康罗伊](https://codepen.io/markconroy) )
的 Pen [Log Rocket 博客样本——马克元素 2](https://codepen.io/markconroy/pen/wvdzBpg) 。

### 将`<bdo>`和`<bdi>`用于双向文本

当您希望文档中有双向文本时，请使用`<bdo>`标签。如果您用从左到右的语言编写，这很方便，但是您需要引用另一种从右到左的语言(如阿拉伯语)。

参见 [CodePen](https://codepen.io) 上马克康罗伊([@马克康罗伊](https://codepen.io/markconroy) )
的 Pen [Log Rocket 博客样本——bdo](https://codepen.io/markconroy/pen/RwVGNMW)。

有一个与`<bdo>`标签相对应的标签叫做`<bdi>`。此标签用于双向内容，应将其与周围的文本分开考虑。如果你动态地将文本注入到你的文档中，但是不知道注入的文本将会是什么语言，那么使用`<bdi>`。

### 使用`<wbr>`进行断词

你有没有遇到过导致水平滚动的长文本字符串，比如一行放不下的长 URL？嗯，标签是你的朋友。这个标签建议在一个合适的地方进行分词。

因此，如果你有一个像`[https://example.com/this/is/a/long/url](https://example.com/this/is/a/long/url)`这样的网址，它不适合放在一行中(例如，在一部手机上)，你可以建议浏览器在某些地方把这个词分开。

可以这样做:`[https://example.com/<wbr>this/is/a/long/url](https://example.com/<wbr>this/is/a/long/url)`。现在，如果您调整页面大小，该文本字符串将在`[https://example.com/](https://example.com/)`后断开。

### `<i>`作为惯用文本

我感觉你可能在使用`<i>`元素。但是我也有一种感觉，你是把它用于像这样的图标:`<i class="fa fa-facebook"></i>`而不是作为一种使文本倾斜的方式。当你想给你读过的一本书起个名字的时候，你可以用这个。

`<p>I enjoy <i>One Hundred Years of Solitude</i> by Gabriel Garcia Marquez.</p>`，会这样渲染:我喜欢读马尔克斯的*百年孤独*。让我们试着正确使用这个元素，这样屏幕阅读器就能理解我们，而不是让人们为了自己的用途劫持我们的语义。

## 结论

这是对一些 HTML 元素的快速总结，我认为这些元素可以得到更广泛的应用，并有助于更具语义的 web、更好的可访问性和改进 SEO。我强烈建议您查看一下 [MDN docs 网站](https://developer.mozilla.org/en-US/docs/Web/HTML/Element)上的所有可用内容，以便更加熟悉可用内容。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)