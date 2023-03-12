# CSS 参考指南:calc() - LogRocket 博客

> 原文：<https://blog.logrocket.com/css-reference-guide-calc/>

[Chidume Nnamdi Follow](https://blog.logrocket.com/author/chidumennamdi/) I'm a software engineer with over six years of experience. I've worked with different stacks, including WAMP, MERN, and MEAN. My language of choice is JavaScript; frameworks are Angular and Node.js.

# CSS 参考指南:`calc()`

## 

2020 年 9 月 30 日 2 分钟读完 750

CSS `calc()`函数用于计算 CSS 中简单的算术表达式。

#### *向前跳转:*

* * *

算术表达式是加、减、除和乘。这些表达式遵循优先顺序:

*   **P**–括号
*   **D**–分部
*   **M**–乘法
*   **A**–加法
*   **S**–减法

表达式中的任何括号都是从左到右首先计算的。然后执行除法运算，然后是乘法、加法，最后是减法。

例如，我们在`calc`中有这样一个表达式:

```
calc(4px - (9px + 1px) / 5px * 9px)
```

参见 [CodePen](https://codepen.io) 上 Chidume David([@ philipsz-davido](https://codepen.io/philipsz-davido))
的 Pen [css 计算示例](https://codepen.io/philipsz-davido/pen/XWdqmpy)。

括号`(9px + 1px)`首先被求值，所以我们现在有了这个:

```
4px - 10px / 5px * 9px
```

接下来是除法 op. `10px / 5px`是这样评估的:

```
4px - 2px * 9px
```

然后，评估乘法 op，`2px * 9px`:

```
4px - 18px
```

最后，评估减法 op，`4px - 18px`:

```
-14px
```

* * *

## 值类型

并不是 CSS 中的所有值类型都允许出现在`calc`函数中。允许的值包括:

*   `<length>`
*   `<frequency>`
*   `<angle>`
*   `<time>`
*   `<percentage>`
*   `<number>`
*   `<integer>`

* * *

## 规则

使用`calc`功能时，必须严格遵守某些规则。

**1。)**加减运算符的 RHS 和 LHS 值必须是相同的单位类型，也可以是一个`number`和一个`integer`。

*   `5px + 2`无效，因为这些值属于不同的单位类型。第一种是`<length>`型，而第二种是`<number>`。
*   `2px + 5vh`有效，因为两者都是`<length>`单元类型；表达式中的操作数可以是任何`<length>`语法值。
*   `3.4 - 4`有效。

**2。)**加法和减法运算符的值必须是空白的；失败将导致表达式的不正确解析和评估。

*   `4px-5vh`无效，因为`-`符号前后没有空格；该表达式将被解释为 4px 和负 5vh。要纠正这一点，只需在它们之间添加空格:`4px - 5vh` 。

**3。)**乘法运算中的一个值必须是`<number>`(或`<integer>`)。

*   `4px * 5em`无效，因为所有值都是`length`单位类型。
*   `4px * 5`是有效的，因为 RHS 值是一个`<number>`。

> **注意:**，乘法和除法运算符不需要空格，不像加法和减法运算符，例如:`4*5px`是有效的。但是，出于一致性的考虑，建议使用它们。

**4。)**在除法运算中，RHS 值必须是一个`<number>`。

*   `4px / 5em`无效，因为 RHS 值(`5em`)是长度单位。`4px / 5` 有效是因为 RHS 值(`5`)是一个 `<number>` 。
*   评估后返回值的单位类型由 LHS 单位类型决定。如果 LHS 的单位类型是`<integer>` ，那么返回的单位类型是 `<number>` 。

**5。)**任何产生 0 的除法都被视为无效。

*   `4rem/0`产量为 0，所以无效。

**6。)**`calc`功能最多支持 20 个术语。

*   一个术语是`<length>`、`<frequency>`、`<angle>`、`<time>`、`<percentage>`、`<number>`或`<integer>`。如果超过了这个项数，那么`calc`中的表达式参数无效。

* * *

## CSS 变量和`calc`

CSS 变量可以用在`calc`表达式 arg 中。请注意，CSS 变量用于在选择器中存储变量，并通过`var`函数在样式中的任何地方使用它们。

```
::root {
    --width: 500px;
    --height: 900px;
}

div.burger {
    width: calc(10% - var(--width));
    height:  calc(12% + var(--height));
}
```

我们在根作用域中声明了 CSS 变量`--width`和`--height`，然后使用`calc`函数中的`var(...)`函数检索它们。

`div.burger`宽度长度将为`calc(10% - 500px)`。`calc(10% - var(--width))`语句中的`var(--width)`将替换为`500px`。`div.burger`的高度将会是`calc(12% + 900px)`。

* * *

## `calc`用法示例

在开发过程中经常会派上用场。让我们看看哪里可以用`calc`代替`margin`来设置一个按钮左右两边的间隙。

```
.btn {
    padding: 6px 12px;
    margin-bottom: 0;
    font-weight: 400;
    cursor: pointer;
    text-align: center;
    white-space: nowrap;
    display: inline-block;
    background-image: none;

    border-radius: 3px;
    box-shadow: none;
    border: 1px solid transparent;
}

.btn-block {
    width: calc(100% - 20px);
}
```

`.btn.block`选择器在它的父容器上拉伸按钮，左右之间有 20px 的间隙。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用情况、内存使用情况等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。