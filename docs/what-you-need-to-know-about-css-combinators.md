# 关于 CSS 组合子你需要知道什么

> 原文：<https://blog.logrocket.com/what-you-need-to-know-about-css-combinators/>

在 CSS 中，我们使用组合子根据选择器的关系来影响 HTML 的各个部分。这里的关系一般是亲子、亲孙、亲玄孙、亲玄孙等。

## 为什么要学习 CSS 组合子？

学习组合子可以让你更好地编写 CSS，并帮助你避免过多的 CSS 代码。当你想修改你不能访问的 HTML 或者有巨大的 CSS 代码库和太多的元素时，它们就派上了用场。组合子还可以帮助您精确定位 HTML 中您想要高精度样式化的部分，因为它们基于选择器之间的关系。

在本指南中，我们将探索各种可以用来设计 HTML 风格的 CSS 组合子。

## 后代选择器(空格)

此选择器影响指定父选择器的后代或子选择器。

后代选择器通常用在主题化中，当你想在不同的 HTML 标签上设置相同的样式时。

```
div h3 {
    ...
}

```

该选择器是后代选择器。请注意，它由一个空格表示。大括号内的样式代码将只影响 HTML 中 div 内的`h3`标签。

```
div p {
    ...
}

```

样式代码将影响所有`div`元素中的`p`元素。

```
<div>
    <p>I'm paragraph1</p>
    <ul>
        <li>
            <p>I'm paragraph2</p>
        </li>
    </ul>
<div>
<div>
    <p>I'm paragraph3</p>
<div>
<p>I'm paragraph4</p>

```

三个`p`元素——`I'm paragraph1`、`I'm paragraph2`和`I'm paragraph3`——将受到样式的影响，因为它们在`div`元素内部。

在后代选择器中，无论父子关系的层次结构如何，它都会影响元素。

在上面的代码中，`I'm paragraph2`不是`div`元素的直接子元素，但是它受到了影响，因为它是`div`元素的后代。换句话说，它是`div`元素的孙元素。

## 子选择器(`>`)

子选择器只选择指定元素的直接子元素。用`>`箭头表示。

```
div > p {
    color: orangered;
}

```

这将把`div`元素的所有直接`p`子元素的颜色设置为橙红色。

```
<div>
    <p>I'm paragraph1</p>
    <ul>
        <li>
            <p>I'm paragraph2</p>
        </li>
    </ul>
</div>
<div>
    <p>I'm paragraph3</p>
</div>
<p>I'm paragraph4</p>

```

`p`元素`I'm paragrapg1`和`I'm paragraph3`将被设置为橙红色，因为它们是`div`元素的直接子元素。

`I'm paragraph2`是`div`元素的后代/孙，但不是直接子元素，因此它没有被着色。`I'm paragraph4`没有被着色，因为它不是`div`元素的子元素。

## 相邻兄弟选择器(`+`)

该选择器选择并影响位于指定元素之后的元素。它；s 用加号(`+`)表示。

```
div + p {
    color: orangered;
}

```

放置在`div`元素之后的所有`p`元素将被设计成橘红色。

```
<div>
    <p>I'm paragraph1</p>
    <ul>
        <li>
            <p>I'm paragraph2</p>
        </li>
    </ul>
</div>
<div>
    <p>I'm paragraph3</p>
</div>
<p>I'm paragraph4</p>

```

只有`I'm paragraph4`是橙红色的，因为它紧接在`div`元素之后，意味着它是`div`元素的兄弟。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`I'm paragraph1`、`I'm paragraph3`和`I'm paragraph2`是`div`元素的子元素。因为它们不是任何`div`元素的兄弟，所以它们不受影响。

让我们在`I'm paragraph4`后面加第五个`p`。

```
<div>
    <p>I'm paragraph1</p>
    <ul>
        <li>
            <p>I'm paragraph2</p>
        </li>
    </ul>
</div>
<div>
    <p>I'm paragraph3</p>
</div>
<p>I'm paragraph4</p>
<p>I'm paragraph5</p>

```

`I'm paragraph5`是`div`元素的同级，就像`I'm paragraph4`一样，但是它不会受到颜色的影响，因为它不是紧接在`div`元素之后，因此不是 div 元素的相邻同级。

## 通用同级选择器(`~`)

常规同级选择器选择指定元素的所有同级。它由波浪号(`~`)表示。

```
div ~ p {
    color: orangered;
}

```

这将把所有`p`兄弟元素和`div`元素的颜色设置为橙红色。

```
<div>
    <p>I'm paragraph1</p>
    <ul>
        <li>
            <p>I'm paragraph2</p>
        </li>
    </ul>
</div>
<div>
    <p>I'm paragraph3</p>
</div>
<p>I'm paragraph4</p>
<p>I'm paragraph5</p>

```

只有`I'm paragraph4`和`I'm paragraph5`会受到影响，因为它们是`div`元素的兄弟。

## 结论

在本教程中，我们讨论了 CSS 中的所有组合子:后代、子、相邻和一般兄弟。

让我们列出一些要点:

*   `Descendant`处理子元素和后代元素，不管子元素的层次结构如何
*   `Child`影响直接子元素
*   `Adjacent`影响相邻元素和放置在指定元素之后的元素
*   `General Sibling`影响指定元素的所有兄弟元素

现在，您的 CSS 工具箱中又多了一项技能。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。