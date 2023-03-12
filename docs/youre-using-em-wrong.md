# 你使用了错误的博客

> 原文：<https://blog.logrocket.com/youre-using-em-wrong/>

这是一条崎岖不平的道路，不知何故让我们误用了最重要的文本级语义标签之一。

在 HTML 的黑暗时代，*很少被使用，尽管它很早就成为规范的一部分( [HTML 2.0](http://www.ietf.org/rfc/rfc1866.txt) 标准，1995)。但是在那个时间点和接下来的几年里，(几乎)没有人考虑语义或者甚至关注点的分离。斜体只是简单地用< i >标记标出，我们不会多想。*

然后在沿途的某个地方，有人大喊*“语义！”每个人都开始讨厌可怜的小 T4，我就像一个坏邻居。一个真正的、*真正的*坏邻居。*

风靡一时，据说对可访问性和搜索引擎优化有好处，这让我们在任何地方都使用它。到了 [HTML 4](https://www.w3.org/TR/html401/struct/text.html#edef-EM) ，所有人都知道< em >是为了强调并以斜体显示，< strong >是为了加强强调并以粗体显示。如果你胆敢使用< i >，你会立刻被贴上糟糕开发者的标签。

甚至有传言说*完全没有语义* < i >和< b >随时会像< u >一样被弃用，我们所见即所得的编辑甚至没有一个按钮用于< i >或< b >，他们只是在我们点击斜体图标时放一个< em >，在点击粗体图标时放一个< strong >。

但是…

## 标签不是为了强调

我再说一遍: **< em >是*不是*为了强调**。在 [HTML5](https://www.w3.org/TR/html50/text-level-semantics.html#the-em-element) 、 **< em >是为了*重音*强调**。

这可能看起来是一个微妙的区别，但这就是所有这些混乱的来源。HTML 2、3 和 4 规范对“强调”的定义非常模糊，看起来像是**的缩小版。标记更重要的文本，但不是*那么*重要。**

但是当 HTML5 推出时，他们确保在他们想要的*上画一条更清晰的线，同时将*从一个文本斜体标签重新定义为一个语义标签，几乎包含了斜体的大多数其他用例。**

但是什么是重音呢？重音是改变音高和/或拖动单词来表示特定含义的语音资源。它以改变整个句子意思的方式标记一个单词。它用于纠正、澄清、讽刺、反驳的关键部分等。

以英语为母语的人想都没想就这么做了，但更多时候，我们这些把英语作为第二语言的人不得不耐着性子听完关于英语的无聊课程。

快速看一下规范本身的[例子](https://www.w3.org/TR/html50/text-level-semantics.html#the-em-element)应该会更清楚:他们把短语“猫是可爱的动物”和< em >标签从一个单词改为另一个单词，从而改变了含义，从暗示讨论是关于哪些动物是可爱的，到暗示整个句子的真实性有问题，暗示其他人说它们是丑陋的，到有人把猫和植物混淆的荒谬想法。或者神。可能是神。

## 一些使用案例

我们可以用重音来表达一个单词更高的重要性:

```
<p>"I'm not <em>that</em> into text semantics"</p>
```

对“that”的强调表明，虽然对它感兴趣，但它不会阻止说话者不时使用不正确的标签。

但是我们也可以用它来表示讽刺:

```
<p>Sure, another TV is <em>exactly</em> what our family needs</p>
```

为了纠正或澄清信息，引用 [Quackit 关于文本级语义的优秀指南:](https://www.quackit.com/html/tags/html_em_tag.cfm)

```
<p>"Did you say that you are a <em>chameleon</em>?"</p>
<p>"No, I am a <em>comedian</em>"</p>
```

我们也可以用它来暗示一些含义:

```
<p>What <em>we</em> need to do is finish this project asap</p>
```

这种强调暗示着还有另外一个人*需要做*另外一件事*。*

 *它还被用来标记对抗点:

```
<p><em>Dogs</em> are cute animals</p>
```

我在暗示与声称*其他*动物可爱的其他人(在这种情况下，无论是谁写的 HTML5 规范示例)的对抗，并标记差异点。

标签通常用在单个单词上，也许是两个，但我们有时也可以用在整个句子上，以表明说话者真的在努力表达自己的观点，或者表示紧急情况。感叹词在短语中的正确用法是:

```
<p><em>We need to get out of here!</em></p>
```

有大量的所见即所得编辑器弄错了这一点。从旨在通过拖放简化简单网站创建的实际软件到博客平台；如果你的编辑器有典型的“斜体-粗体-下划线”按钮，那么很可能没有任何语义。

大多数(可能是所有)声称“语义化”的只是对任何斜体文本抛出*，对粗体文本抛出**，而没有考虑*为什么*文本被格式化成那样(这正是“语义化”试图解决的)。**如果没有用于< i >和<em>的独立按钮，或者有一个超级智能算法能够解释*为什么*你将文本设为斜体并为用例应用正确的标签，**这是*而不是*语义。*******

即使是我们的现代工具也达不到这一点。 **Markdown** 的实现对斜体文本使用了与< em >完全相同的方法，对粗体文本使用了< strong >的方法。

上面的一些参考指南甚至说

> 强调，**又名**斜体，带有星号或下划线。

在这一点上，我真的认为可能是开发人员而不是工具。

我明白了，Markdown 是为了快速写作，它甚至在[文档](https://daringfireball.net/projects/markdown/syntax#html)中也这么说

> Markdown 不是 HTML 的替代品，甚至不是 HTML 的替代品。它的语法非常小，只对应于 HTML 标签的一个非常小的子集。这个想法是**而不是**创建一个语法，使得插入 HTML 标签更容易。(…) HTML 是一种**出版**格式；Markdown 是一种**写作**格式。因此，Markdown 的格式化语法只解决可以用纯文本表达的问题。

我们可以(在某些实现中)在 markdown 格式的文本中手动使用*标签，但即使这样，我觉得他们应该在语言中解决这两个问题。*

## 何时不使用

 *在很多情况下，斜体是用来表示*而不是*强调的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在这种情况下，正确的标签实际上是 **< i >。**有些人可能认为< i >是一个非语义标签，但它实际上是一个包罗万象的标签，适用于我们希望斜体文本*没有*强调的用例。

从说明书中引用

> I 元素表示以**备选语音或语气**的文本跨度，或者以指示不同文本质量的方式偏离正常散文

例如，当标记来自另一种语言的惯用短语时，我们应该使用*，以及它们对应的 **lang** 属性，以便屏幕阅读器获得正确的发音:*

```
<p>I would like us all to use better text-level semantics, but <i lang="fr">c'est la vie</i></p>
<p>Major of Springfield, <i lang="la">Corruptus in Extremis</i></p>
```

它也用于分类命名:

```
<p><i lang="la">Carnivorous Vulgaris</i> keeps chasing his prey, <i lang="la">Accelerati Incrediblus</i> </p>
```

要标记一个技术术语:

```
<p>Concepts like <i>closure</i> can be confusing to JavaScript beginners</p>
```

虚构角色的声音:

```
<p><i>- That's what I do: I drink and I know things</i></p>
```

想法:

```
<p><i>I am better than this</i>, she thought as she walked away</p>
```

以及其他一些用例，取决于语言(比如船名)。

另一个引起斜体的标签是 **<引用>** 元素。

它用来标明作品(画、书等)的名称:

```
<p><cite>The persistence of memory</cite> by Salvador Dalí. Painted in 1931</p>
```

还是作者:

```
<p> According to <cite title="You're using <em> wrong. Published by LogRocket (October, 2018)">Facundo Corradini</cite>, we should reconsider the tags we're using to italicise text.
```

在许多平台中,

> 会使文本变成斜体(以及缩进),但是我不认为有人会把它和*混淆，编辑器总是有合适的按钮。但是这可能会导致一些嵌套问题，所以这一点要记住。*

## 为什么它很重要

当然是可及性。每当我们使用错误的标签来斜体显示一个单词时，大多数用户甚至不会注意到。只要我们是按照我们的语言习惯来做的，这个单词就会被读成想要强调的样子。

但是我们让屏幕阅读器的事情变得更加复杂，尤其是在嵌套的时候。如果我们的工作做得正确，语音合成器将能够很容易地做出正确的音高修正。但我们在这方面已经走得太远了，所有人(据我所知)都默认禁用了它，**这是让他们感觉如此不自然的很大一部分原因**。

在某些情况下可以启用，但即使这样，他们还是选择只获取斜体的内容并应用变调..同样糟糕的是。他们正在纠正我们的错误。

但是，也许，如果我们都开始用正确的方式去做，我们可以让他们在未来正常工作，并且感觉更像人类。**我们可以更好的沟通**。

我不得不承认，整篇文章开始于我对这个关于“嵌套强调”的 [CSS-Tricks tweet](https://twitter.com/css/status/1037774960943620097?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E1037774960943620097&ref_url=https%3A%2F%2Flogrocket-blog.ghost.io%2Fyoure-using-em-wrong%2F) 的强烈反对:

> 嵌套强调？解开它。" ` em em { font-style:normal；} " ` pic.twitter.com/YIaBPoKvod

让我们来分解一下:

```
<em>Now <em>that's</em> a change, she thought</em>
em em { font-style: normal;}
```

*现在*那是*的一个变化，她认为*

给定的短语是一个虚构人物的想法，他强调“就是这样”，暗示他们谈论的变化真的很明显，可能与另一个之前的小变化相比。

首先，“这就是变化”是一种想法，所以应该用一个*标签来标记，而不是*。但是正如我们之前讨论的，几乎所有的工具都在强迫我们做这些事情，所以我可以接受。**

真正困扰我的部分是重设斜体的核心思想。对我来说，这似乎是最糟糕的解决方案。它不仅没有传达出这个词的意图重音，实际上还让这个词看起来超出了虚构人物的思维范围。

看看如果我们选择增加加粗的重量，它会更好地传递压力:

```
<i>Now <em>that's</em> a change</i>, she thought
i em { font-weight: bold;}
```

*现在的* ***那是*** *的一个变化*，她想

有些人可能会说下划线同样可以完成工作，这对于印刷来说可能是正确的，但对于网络来说显然是不可行的，因为它会使它看起来像一个链接:

```
<i>Now <em>that's</em> a change</i>, she thought
i em {text-decoration: underline;}
```

或者也许用大写，毕竟只是一个词。就我个人而言，我认为这有点夸张，可能更适合嵌套粗体标签，因为它通常被解释为大喊:

```
<i>Now <em>that's</em> a change</i>, she thought
i em {text-transform: uppercase;}
```

她想，这就是变化

还有一些其他的标签组合会导致斜体范围内的斜体文本，但是它们中的大多数都在其他标签内，所以我认为只要将内部标签设置为粗体就几乎每次都能达到目的。

## 外卖食品

*   每当你用斜体印出一个单词/短语时，想想你为什么要这样做，如果可能的话，选择合适的标签
*   有疑问的时候，大声读出来。或者让别人大声读出来，看看它是否符合你的预期变化
*   如果你在不止一两个单词上使用斜体，*可能不是正确的标签*
*   用*标记整个短语意味着紧急，所以它通常只用于感叹词中*
*   当嵌套产生斜体的标签时，使用粗体。看在 HTML 的份上，不要撤销它们！

我们需要开始考虑为什么我们要将文本设为斜体，并为其使用正确的标签。我们可以改进我们的工具。我们可以让网络变得更好。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)**