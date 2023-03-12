# CSS 参考指南:flex 

> 原文：<https://blog.logrocket.com/css-reference-guide-flex/>

CSS `flex`属性是用来在灵活项目上设置`flex-grow`、`flex-shrink`和`flex-basis`属性的简写。这些属性决定了灵活项的对齐和排序方式，以及它们在父容器中的灵活性。

#### *向前跳转:*

在此回顾一个例子:

参见 [CodePen](https://codepen.io) 上 Chidume David([@ philipsz-davido](https://codepen.io/philipsz-davido))
的[笔 css flex 示例](https://codepen.io/philipsz-davido/pen/XWdEPQM)。

句法

* * *

## `flex`属性的一般语法是:

`<flex-grow>`

```
flex: <flex-grow> <flex-shrink> <flex-basis>
```

##### 定义伸缩项的`flex-grow`因子。它的值是无单位的，是一个`<number>`。它的初始值是 0。

`<flex-shrink>`

##### 定义伸缩项的`flex-shrink`因子。它的值是无单位的，是一个`<number>`。它的初始值是 1。

`<flex-basis>`

##### 定义弹性项目的初始长度。它的值被指定为一个`<'width'>` —一个绝对的`<length>`、`<percentage>`或`calc()`结果，或者关键字`auto`。它的初始值是`auto`。

规则

* * *

## 属性可以有一个、两个或三个值。规则如下:

一个价值:

#### 然后`flex-grow`被赋予该值。`flex-shrink`取默认值 1，`flex-basis`省略时假定为 0。

*   If the one value is a unitless `<number>`, like so:

    ```
    flex: 3;
    ```

    如果一个值是一个`<percentage>`或一个`<length>`，那么它就变成了`flex-basis`。`flex-grow`和`flex-shrink`省略时默认为 1。

    ```
    flex: 3%  // explodes to: flex: 1 1 3%  // or flex-grow: 1 flex-shrink: 1 flex-basis: 3%
    ```

    ```
    flex: 3px  // explodes to:  flex: 1 1 3px  // or flex-grow: 1 flex-shrink: 1 flex-basis: 3px
    ```

    ```
    flex: 3;

    // explodes to:

    flex: 3 1 0

    // or

    flex-grow: 3
    flex-shrink: 1
    flex-basis: 0
    ```

*   两个值:

* * *

#### 如果在`flex`属性中设置的两个值都是无单位的`<number>`，那么`flex-grow`取第一个值，`flex-shrink`取第二个值。`flex-basis`被假定为 0。

```
flex: 4 2  // explodes to:  flex: 4 2 1%
```

*   如果其中一个数字是`<percentage>`，另一个是`<number>`，`flex-basis`取`<percentage>`值，`flex-grow`取另一个值。`flex-shrink`默认为 1。

    ```
    flex: 4% 5  // explodes to:  flex: 5 1 4%
    ```

    ```
    flex: 6 3%  // explodes to:  flex: 6 1 3%
    ```

*   三个价值观:

* * *

#### 组件属性

* * *

## `flex-grow`

### 这定义了如果 flex 容器中有可用空间，灵活项目将增长的程度。这种增长与同一 flex 容器中其他灵活项目的增长成比例。

上面的 flex 项不会增长到填满空间，因为它们的增长因子为零。如果我们将其中一个项目的弹性增长率设置为 1:

```
flex-grow: 0
flex-grow: 0
flex-grow: 0
```

第三个项目只会拉伸或增长以填充右侧的空间。

```
flex-grow: 0
flex-grow: 0
flex-grow: 1
```

如果我们也将第二项设置为弹性增长 1:

第二项和第三项将向右延伸以填满空间，但会在两者之间平均分配。第三项不会像以前那样占那么大的空间。可用空间将在项目 2 和 3 之间划分，然后将被填满。

```
flex-grow: 0
flex-grow: 1
flex-grow: 1
```

如果我们将所有项目的弹性增长率设为 1:

可用空间将在三者之间分配，它们将按比例填满空间。

```
flex-grow: 1
flex-grow: 1
flex-grow: 1
```

如果我们将第三项设置为弹性增长值 2:

第三项占据的空间是第一项和第二项的两倍。

```
flex-grow: 1
flex-grow: 1
flex-grow: 2
```

这是一个简单的`1:1:2`比。比率之和为`(1 + 1 + 2) = 4`。

如果要填充的空间是 700px，第一项将占用`(1/4 * 700)`，即 175px。同样，第二项将占用 175px，第三项将占用`(2/4 * 700) = 350px`——是 175px 的两倍。

`flex-shrink`

* * *

### 这定义了当 flex 容器中没有相对于其兄弟 flex 项目的可用空间时，flex 项目将收缩的程度。

假设我们有一个宽度为 700 像素的 flex 容器，它有三个宽度为 300 像素的 flex 项目。从图中可以看出，我们确信 flex 项——特别是第三个 flex 项——将溢出它们的父容器。

在第三个项目上使用`flex-shrink`将使其收缩以适合 flex 容器。本质上，第三项会根据`flex-shrink`的值变窄或变短。

如果我们在项目上设置以下`flex-shrink`值:

第三项将缩小 1 倍。正如我们在`flex-grow`中看到的，这些值是比率，告诉我们如何在要削减的弹性项目中分配超额溢出:`0:0:1`。

```
flex-shrink: 0
flex-shrink: 0
flex-shrink: 1
```

超额溢出为`900px - 700px = 200px`。因此，第三个项目必须缩小 200 像素——从 300 像素缩小到 100 像素——才能放入 flex 容器。

如果第二项的`flex-shrink`设置为 2:

那么第二项会比第三项缩水两倍。我们知道超额溢出是 200px，所以第二项会流出`(2/3 * 200) = 133.3px`，所以它的宽度会是 166.6px .第三项会流出`(1/3 * 200) = 66.6px`，所以它的宽度会是 233.3px。

```
flex-shrink: 0
flex-shrink: 2
flex-shrink: 1
```

三项合计:`300 + 166.6 + 233.3 = 699.9` —约 700px。

`flex-basis`

* * *

### 此属性设置伸缩项目的大小。在增长以填充可用空间或收缩以避免溢出后，它将被添加到 flex 项的大小中。

计算弹性项目的大小，并将其添加到所占空间的大小中，然后计算其他项目的大小。

假设我们有一个宽度为 700 像素的 flex 容器。它包含三个 flex 项目，所有项目的`flex-grow`值都为 1，并且具有以下`flex-basis`值:

百分比值告诉伸缩项增长父项宽度的 2%或 3%。当`flex-grow`为 1 时，所有 flex 项将平均共享可用空间:`(700 / 3) = 233.3px`。

```
flex-basis: 0
flex-basis: 2%
flex-basis: 3%
```

所以第二项会增长到 231px，第三项会增长到 236px，第一项会减少到 225px。

你的前端是否占用了用户的 CPU？

## 随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

Modernize how you debug web and mobile apps — [Start monitoring for free](https://lp.logrocket.com/blg/css-signup).