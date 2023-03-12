# 扩展 CSS when/else 链:第一个 look - LogRocket 博客

> 原文：<https://blog.logrocket.com/extending-css-when-else-chains-first-look/>

CSS 作者使用 CSS 条件规则，根据应用样式表的处理器或文档的功能来定义一组规则。其中一些规则使作者能够在样式表中执行逻辑。

例如，CSS 作者使用`@media`进行媒体查询，并有选择地对文档应用样式。类似地，`@support`规则用于查询用户代理对新 CSS 特性的支持。

就其本身而言，这些规则非常有用。然而，很多时候，CSS 作者可能需要结合这些规则来创建条件语句。这是常见的做法，因为不同的情况下适用不同的样式规则。

目前，这种行为在 CSS 中很难实现，因为作者必须精心制作样式表以适应所有情况。

关于这些问题，提出了两个新的条件规则来解决它们:`@when`和`@else`。在发帖的时候，`[@when](https://tabatkins.github.io/specs/css-when-else/)` [提案](https://tabatkins.github.io/specs/css-when-else/)被[决议通过，被 CSSWG](https://twitter.com/TerribleMia/status/1438183093387599881) 采纳为“下一级 CSS 条件”。

但是它们是什么，我们如何使用它们？

在本文中，我们将首先看看这些新的条件规则，包括它们在样式表中的一些实际用途。

## 使用`@when`创建通用条件规则

提议的`@when`规则一般化了条件，因此您可以将规则块一般化为使用两种或更多种查询，而不是为特定任务使用特定的条件规则，比如使用`@support`的特性查询。

你可以把它想象成其他 CSS 条件规则的包装器，比如`@media`和`@support`。

因此，假设您想要测试媒体屏幕是否低于 769 像素，并执行两个特性查询来检查[浏览器是否支持网格和 flex 显示特性](https://blog.logrocket.com/css-grid-or-css-frameworks/)。如果`true`，你也希望每个网格列只设置一项。

如果没有`@when`，你可能不得不写类似这样的东西:

```
@media (max-width: 769px) {
    @supports (display: grid) and (display: flex) {
        .grid {
            grid-template-columns: 1fr;
        }

        .flex {
            flex-direction: row;
        }
    }
}

```

在上面的代码中，我们在激活下面的样式表之前检查了三个条件，并使用了两个不同的条件规则。

但是使用`@when`，我们可以创建一个通用的规则，将所有不同的条件打包成一个语句:

```
@when media(max-width: 769px) and supports(display: grid) and supports(display: flex) {
    .grid {
        grid-template-columns: 1fr;
    }

    .flex {
        flex-direction: row;
    }
}

```

这很好，但它提出了以下问题:如果`@when`语句中的条件(查询)计算结果为`false`，会发生什么？

技术上来说什么都不会发生。如果`@when`中的条件失败，CSS 引擎会忽略它的样式块，并返回到默认样式。

同样，这对于简单的情况非常有用。但是如果你想创造比两个条件更长的东西呢？假设条件一是`true`，我们想要应用样式块一。否则，如果条件二是`true`，我们想要应用样式二。如果两个条件都不满足`true`，我们就回到默认风格。

CSS 条件的第 4 级规范提出了在 CSS 中创建条件规则链的`@else`规则。

### 使用`@when`内嵌

`@when`也可以内联使用。在下面的例子中，[我们将在一个元素上设置一个每列三项的网格](https://blog.logrocket.com/understanding-css-grid-by-building-your-own-grid/)。然后，当`width`小于 400 像素时，我们必须更改为每个网格列一个项目:

```
.container {
    width: 80%;
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    padding: 1rem;
    @when element(max-width: 400px) {
        grid-template-columns: 1fr;
        padding: 2rem;
    }
}

```

通过在上面的样式块中使用`@when`,我们可以指定不同的样式规则，以便在元素达到指定条件时激活，比如当它的宽度小于 400px 时。

## 用`@else`创建条件链

`@else`条件规则允许作者创建一系列条件规则，从而形成一个条件链。

在前面的代码中，我们创建了一个通用条件来制作媒体，支持查询，并在条件为`true`时应用一组样式。然而，如果`@when`中指定的条件评估为假，则没有回退条件。

通过使用`@else`，我们可以创建一个条件语句链，每个语句包含一个要评估的条件。

如果所有其他条件评估为`false`，则在链的末端是一个回退:

```
@when media(min-width: 768px) and supports(display: grid) {
    div {
        display: grid;
        grid-template-columns: 1fr;
    }
  } @else supports(clip-path: circle(1px)) and supports(transform: skewY(1deg)) {
        div {
            display: block;
         }

         .blog img {
          clip-path: circle(50%);
          }

          .showcase {
          transform: skewY(10deg);
          }
    }
  } @else {
    /* Fallback. In case all of the above conditions evaluates to false */
      div {
        display: block;
      }

      img {
        width: 100%;
        height: 100%;
      }
}

```

如果没有条件链接，这是实现相同目标的方式:

```
  /* If  width is smaller than 769px */
@media (max-width: 768px) {
    @supports (display: grid) {
      div {
        grid-template-columns: 1fr;
      }
    }
    @supports not (display: grid) {
      @supports (clip-path: circle(1px)) and (transform: skewY(1deg)) {
        div {
            display: block;
        }

        .blog img {
            clip-path: circle(50%);
        }

        .showcase {
            transform: skewY(10deg);
          }
      }
      @supports not ((clip-path: circle(1px)) and (transform: skewY(1deg))) {
        div {
            display: block;
        }

        .blog img {
            width: 100%;
            height: 100%;
        }
      }
    }
  }

  /* If  width is larger than 769px */
  @media (min-width: 769px) {
    @supports (clip-path: circle(1px)) and (transform: skewY(1deg)) {
           div {
            grid-template-columns: repeat(3, 1fr);
            }

            .blog img {
              clip-path: circle(50%);
            }

            .showcase {
              transform: skewY(10deg);
            }
    }
    @supports not ((clip-path: circle(1px)) and (transform: skewY(1deg))) {
         div {
            grid-template-columns: repeat(3, 1fr);
          }

          .blog img {
            width: 100%;
            height: 100%;
          }
      }
  }

```

与条件链相比，这些代码是不可读的，并且许多内容在不同的代码块中是重复的。你的造型越复杂，一切都变得越困难。

注意，在条件链中，只选择一个规则。例如，如果第一个规则中的条件评估为`true`，那么链中的后续规则必须评估为`false`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 编写媒体查询的新方法

到目前为止，我们使用`min-width`和`max-width`为样式设置断点。从语义上讲，这些术语可能很难理解。

`min-width`指更大的屏幕，`max-width`指更小的屏幕。这可能很难理解，尤其是对初学者来说。

考虑到这一点，在针对媒体查询的第 4 级规范中引入了一种更具语义的方式来编写查询条件。

因此，不要这样写媒体查询:

```
@media (max-width: 769px) {
    .grid {
        grid-template-columns: 1fr;
    }

    .flex {
        flex-direction: row;
    }
}

@media (min-width: 769px) {
    .grid {
        grid-template-columns: repeat(3, 1fr);
    }

    .flex {
        flex-direction: column;
    }

    body {
        text-align: center;
    }
}

```

你很快就可以这样写了:

```
@media (width >= 769px) {
    .grid {
        grid-template-columns: repeat(3, 1fr);
    }

    .flex {
        flex-direction: column;
    }
}

@media (width <= 769px) {
    .grid {
        grid-template-columns: 1fr;
    }

    .flex {
        flex-direction: row;
    }
 }
}

```

与使用`min`和`max`属性相比，这种语法更有意义，因为您不需要理解`max-width`和`min-width`实际上代表什么。使用比较运算符使得媒体查询更容易阅读和理解。

## 支持 CSS 条件规则的扩展

除了`@when`和`@else`，4 级更新包括支持规则的扩展，允许测试支持的字体技术。

CSSWG 条件规则模块第 4 级草案为新的条件规则如何查询受支持的字体技术提供了一个很好的例子:

```
@when font-technology(color-COLRv1) and font-technology(variations) {
    @font-face { font-family: icons; src: url(icons-gradient-var.woff2); }
}
@else font-technology(color-SVG) {
    @font-face { font-family: icons; src: url(icons-gradient.woff2); }
}
@else font-technology(color-COLRv0) {
    @font-face { font-family: icons; src: url(icons-flat.woff2); }
}
@else {
    @font-face { font-family: icons; src: url(icons-fallback.woff2); }
}

```

因此，在上面的代码中，我们测试了三种不同的字体颜色技术。第一个规则测试`COLRv1`，它支持渐变和字体变化。如果浏览器支持它们，它会相应地设置字体，并忽略链的其余部分。

否则，它将测试第二条规则，依此类推。最终，只有一种字体样式会被激活。

## 结论

`@when`和`@else`可能会到来，[但不是没有问题。人们强烈反对使用](https://github.com/w3ctag/design-principles/issues/335) `[@when](https://github.com/w3ctag/design-principles/issues/335)`，因为`@if`被认为是一个更常见的名字。

但是，`@when`被选中是为了避免与 [Sass，一个流行的 CSS 预处理器](https://blog.logrocket.com/how-to-write-reusable-css-with-sass/)冲突。

SASS 已经使用了一个名为`@if`的规则，它控制一个样式块是否被评估或激活。

然而，其他人建议使用不同的名字，比如`@where`。

不管它的名字是什么，不可否认的是，当这些规则被 web 浏览器正式实现时，将会非常有用。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用情况、内存使用情况等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。