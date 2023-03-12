# 原生 CSS 嵌套:你需要知道的

> 原文：<https://blog.logrocket.com/native-css-nesting/>

当我们使用像 Sass 或更少的 CSS 预处理器时，我们可以将 CSS 样式规则嵌套在另一个规则中，以编写干净和可理解的代码。本机 CSS 尚不支持此嵌套规则。目前，这是一个工作草案，只供讨论。

然而，在本教程中，我们将讨论什么是嵌套，以及如何在预处理器中使用嵌套。然后，我们将讨论它的一些优点，以及如何在 CSS 中使用它。

以下是我们将要介绍的内容:

## 什么是 CSS 嵌套？

您可能熟悉这样编写 CSS:

```
.header {
  background-color: blue;
}

.header p {
  font-size: 16px;
}
.header p span:hover{
  color: green
} 

```

考虑用 CSS 嵌套编写这些相同样式的另一种方式:

```
.header {
  background-color: blue;
  p {
    font-size: 16px;
    span {
      &:hover {
        color: green
      }
    }
  }
}

```

这种语法在像 Sass 和 Less 这样的 CSS 预处理程序中是可行的。从上面的两个代码示例中可以看出，嵌套是将一个选择器包含在另一个选择器中。嵌套有助于将相关的样式分组，并在嵌套的层次结构中编写 CSS。

如果您认为这有所不同，请考虑您一直都在用 HTML 做这件事。因此，与其一遍又一遍地编写相同的选择器来设计特定的子元素或伪选择器，不如将它们嵌套在一个选择器下。

## 嵌套的优点

从上面的段落中，您已经可以看到使用嵌套的一些优点。让我们再考虑两个:

1.  编写更加模块化和可维护的样式表。您可以将与该选择器相关的所有样式放在一个地方，而不是在样式表的多个位置放置同一个选择器。这将大大缩短开发时间和调试时间
2.  它允许您嵌套媒体查询。有了嵌套，选择器就不需要单独的媒体查询规则。您可以在定义选择器的地方添加它

现在的问题是，“我们如何在原生 CSS 中使用 CSS 嵌套？”

## 如何在 CSS 中嵌套选择器

在原生 CSS 中嵌套与我们在上面的例子中看到的非常相似。然而，在 native 中，我们必须以“嵌套选择器”语法`&`开始每个嵌套选择器，或者我们使用`@nest`规则。

如果我们用原生 CSS 嵌套重写上面的 CSS 代码，我们将得到以下内容:

```
.header {
  background-color: blue;
  & p {
    font-size: 16px;
    & span {
      &:hover {
        color: green
      }
    }
  }
}

```

如前所述，为了使嵌套有效，每个选择器的开头都需要有`&`。我们可以认为`&`引用了父选择器。这样，如果我们反转上面的 CSS 样式并用它的父选择器替换每个`&`，我们将得到初始的 CSS 结构。

类似于上面的类型选择器，我们也可以嵌套类选择器。

### 嵌套 CSS 类

我们再举一个例子。如果我们想使用原生 CSS 嵌套编写如下所示的复合选择器:

```
h1.header {
  font-weight: 700
}

```

我们这样做:

```
h1 {
  &.header {
    font-weight: 700
  }
}

```

正如我们所看到的，用父选择器`h1`替换`&`，返回给我们`h1.header`。每当我们在同一个元素上添加一个选择器(在本例中是一个类选择器)时，我们必须忽略`&`和选择器之间的空间。

我们再举一个例子。让我们使用嵌套类重写以下样式规则:

```
.foo { color: #000000; }
.foo .bar { color: #fd2020; }
.foo .bar > .baz { color: #ffffff; }

```

对于第一个嵌套类选择器，我们将有:

```
.foo {
  color: #000000;
  & .bar {
    color: #fd2020; 
  }
}

```

然后，添加第二个嵌套类给我们:

```
.foo {
  color: #000000;
  & .bar {
    color: #fd2020; 
    & > .baz {
      color: #ffffff;
    } 
  }
}

```

同样的方法也适用于嵌套条件规则，如媒体查询。看看下面的嵌套规则:

```
.header {
  font-size: 40px

  @media (max-width: 760px ) {
    & {
      font-size: 24px;
    }
  }
}

```

我们可以看到，通过用父选择器`.header`替换`&`，我们将得到下面的等价关系:

```
.header {
  font-size: 40px;
}

@media (max-width: 760px) {
  .header {
    font-size: 24px;
  }
}

```

正如我们所见，CSS 嵌套有助于将相似的样式组合在一起。

### 分组 CSS 选择器

假设我们有一组选择器，如下所示:

```
#header span,
#header a,
p span,
p a {
  color: #0000ff;
}
```

首先，使用 CSS `is()`函数，我们可以使样式规则更紧凑一些，就像这样:

```
:is(#header, p) span,
:is(#header, p) a {
    color: #ff0000;
}

```

然后，为了创建嵌套的样式规则，我们必须以`&`开始每个选择器。在这种情况下，我们将拥有以下内容:

```
#header, p {
    & span, & a { color: #ff0000; }
}

```

没有比这更简单的了。

### `@nest`规则

虽然用`&`直接嵌套看起来可能很完美，但是它无法处理一些有效的嵌套选择器。让我们考虑一个例子:

```
.header {
  background-color: white;
  .dark & {
    background-color: blue; 
 }
}

```

上述嵌套对嵌套选择器有效。但是 CSS 中的直接嵌套不能处理它。这就是`@nest`规则的用武之地。

`@nest`规则允许 CSS 中的嵌套更加灵活。因此，`@nest`规则允许嵌套不受太多约束，只要嵌套选择器中有嵌套选择器(`&`)，而不是只在嵌套选择器的开头有嵌套选择器。

上面的例子可以使用`@nest`规则来解决:

```
.header {
  background-color: white;
  @nest .dark & {
    background-color: blue; 
 }
}

```

这相当于:

```
.header {
  background-color: white;
}

.dark .header {
    background-color: blue; 
}

```

另一个例子:

```
.header {
  background-color: white;
  @nest :not(&) {
    background-color: blue;
  }
}

```

这相当于:

```
.header {
  background-color: white;
}

:not(.header) {
  background-color: blue;
}

```

注意，为了使`@nest`规则有效，直接嵌套选择器(`&`)必须出现在嵌套选择器中，否则它将无效。所以下面的例子是无效的，因为没有嵌套选择器:

```
.header {
  background-color: white;
  @nest .dark {
    background-color: blue; 
 }
}

```

*注意，现在并不是所有的浏览器都支持 CSS，但是你可以使用 [PostCSS](https://blog.logrocket.com/getting-started-with-postcss-in-2019-484262a4d725/) 将你的嵌套 CSS 转换成浏览器能理解的有效 CSS。*

## CSS 嵌套和特异性

在 CSS 中，特异性是一组规则，决定哪些样式应用于元素。如果两个或多个选择器应用于同一个元素，则应用特异性最高的那个。例如，考虑下面的例子:

```
<html>
  <h1 class="header" id="header" />
</html&gt;

<style>
  #header {
    color: red;
  }
  .header {
    color: blue;
  }

  h1 {
    color: green;
  }
</style>

```

CSS 是级联的，所以理想情况下应该应用样式表中应用的最后一个选择器(`h1`)。
但是，`H1`元素的颜色会是`red`，因为`id`选择器(`#header`)有更高的特异性。关于特异性的更详细的解释，你可以阅读这篇关于特异性的详细文章。

这与 CSS 嵌套有什么关系？让我们看看当我们使用嵌套时浏览器中发生了什么。考虑这个例子:

```
#header, p {
  & span {
    color: red;
  }
}

```

这相当于:

```
:is(#header, p) span { color: red }

```

在所有选择器中，`:is`选择器使用了最高的特异性，因此很难覆盖样式。例如，如果你有一个`p`元素，你想改变`span`的颜色，如下所示:

```
<p class="paragraph">
  <span>hey there <span>
</p>

<style>
  #header, p {
    & span {
      color: red;
    }
  }
 .paragraph {
    & span {
      color: green;
    }
  }
</style>

```

颜色`red`仍将被应用，因为`#header`的特异性高于类别`.paragraph`的特异性。

这对于避免求助于非常复杂的选择器或使用`!important`来覆盖样式非常重要。接下来，让我们考虑一些关于嵌套的一般准则。

## 使用嵌套的常见指南

### 避免过度嵌套

由于嵌套使得嵌套样式变得容易，所以您可能会尝试嵌套选择器。考虑下面的例子:

```
main {
  & section {
    background-color: red;
    & ul {
        background-color: green;
        & .list {
          font-size: 16px;
          & .link {
            color: pink;
              & :hover {
                color: blue
              }
          }
        }
      }
  }
}

```

这相当于

```
main section {
  background-color: red;
}

main section ul {
  background-color: green;
}

main section ul .list{
  font-size: 16px;
}

main section ul .list .link{
  color: pink;
}

main section ul .list .link:hover{
  color: blue;
}

```

最后一个选择器有六层嵌套。如果您想要覆盖样式，这可能会导致许多特殊性问题。一般来说，嵌套深度保持在三层。您可以使用 [stylelint](https://blog.logrocket.com/using-stylelint-improve-lint-css-scss-sass/) 来检查这一点。更喜欢尽可能使用带有描述性名称的类。

### 嵌套选择器后的样式被忽略

在使用嵌套时，记住这一点是很好的。考虑这个例子:

```
main {
  & section {
    background-color: red;
  }
  color: green;
}

```

`color: green`将被忽略，因为它在嵌套选择器之后。使用 CSS 嵌套，任何样式都应该在嵌套的选择器之前应用。这是 CSS 嵌套和其他预处理程序嵌套的一个区别。

## 结论

嵌套是一个令人兴奋的特性，将很快被添加到原生 CSS 中。在本文中，我们讨论了以下内容:

*   嵌套将有助于保持样式表的模块化和可维护性。这是因为通过嵌套，与选择器、子/父选择器甚至媒体查询相关的所有样式都可以嵌套在同一个位置
*   如何以及何时使用直接嵌套和`@nest`规则
*   为什么在使用嵌套时要记住特殊性并避免过度嵌套

关于 CSS 嵌套的进一步研究，可以参考 CSS 嵌套模块的 [W3C 草案。](https://drafts.csswg.org/css-nesting/)

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。