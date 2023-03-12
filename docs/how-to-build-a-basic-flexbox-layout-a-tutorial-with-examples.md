# 如何构建一个基本的 flexbox 布局:示例教程

> 原文：<https://blog.logrocket.com/how-to-build-a-basic-flexbox-layout-a-tutorial-with-examples/>

在 flexbox 出现之前，在文档中布局 DOM 元素的首选方法是使用`float`。`float`属性描述了容器在 DOM 中的放置方式和位置，如下所示:

```
.container {
  float: left | right
}

```

当它在 2009 年首次推出时，CSS Flexible Box 模块(或 flexbox)使开发人员在响应网页和应用程序中放置和组织元素变得更加简单。从那时起，它才开始流行起来。今天， [flexbox](https://blog.logrocket.com/flexbox-vs-css-grid/) 仍然是大多数现代网页的主要布局系统。

在本教程中，我们将向您展示如何在 flexbox 布局中对齐元素和分配空间。为了演示 flexbox 是如何工作的，我们将在计算器用户界面中构建和操作元素。

以下是我们将要介绍的内容:

## 什么是 flexbox？

Flexbox 使您能够在文档中布局 web 组件和元素，而不影响底层标记。它从一个`flex`容器开始；在这里，您可以设置内容的水平和垂直布局。flex 容器的内容称为 flex 项目，它们本身可以成为 flex 容器。

要使元素成为 flex 容器，在元素上声明`display: flex;`或`flex: 1;`:

```
.flex-container {
  display: flex;
}

```

然后，我们可以告诉系统 flex 容器的子容器应该如何布局:从左到右，从右到左，从上到下，甚至从下到上:

```
.flex-container {
  display: flex;
  flex-direction: row | column | row-reversed | column-reversed
}

```

要指定柔性项目从`flex-container`主轴开始分层的方式和位置:

```
.flex-container {
  justify-content: center | end | start | flex-end | flex-start | ...
}

```

我们还可以指定从`flex-container`横轴开始灵活项目分层的方式和位置:

```
.flex-container {
  align-items: center | end | start | flex-end | flex-start | ...
}

```

Flexbox 有许多属性可以用来设置我们想要的精确布局。在本教程中，我们将向您展示如何使用上述基本属性来设计一个简单的计算器界面。

我们的成品将是这样的:

![Finished Product](img/7365efcff432f8ff43dca10caa7de743.png)

## 用 flexbox 设计用户界面

我们将从创建一个 HTML 文件开始。我把我的叫做`calc.html`。在您喜欢的代码编辑器中打开 HTML 文件。

首先添加一些基本的 HTML 标签:

```
<html>
    <head>
        <title>Calculator (Flexbox)</title>
        <meta name="viewport" content="width=device-width,initial-scale=1"> 
    </head>
    <body>  
    </body>
</html>

```

我们将页面的标题设为`Calculator (Flexbox)`。接下来，我们将页面的视口宽度设置为加载页面的设备的宽度大小。如果在桌面上查看，页面的宽度将采用桌面的宽度。这同样适用于在手机上查看页面的情况。

让我们把身体的背景颜色设置为灰色。我们还将宽度设置为占据设备的整个宽度。为此，我们必须添加`style`标签，并将我们的 CSS 代码放在那里。

```
<html>
    <head>
        <title>Calculator (Flexbox)</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">    
    <style>
        body {
            width:100%;
            background-color: #dbdbdb;
        }
    </style>
    </head>
    <body>  
    </body>
</html>

```

我们使用百分比单位来设置主体宽度，并使用十六进制值来指定灰色背景颜色。结果如下:

![Gray Background Example](img/01309812b9929052099d38ebe59103fa.png)

## 添加 flex 容器

下一步是添加容器。我们的 flex 容器将扩展主体的宽度，并将它的项目放在中间。

```
<html>
    <head>
        <title>Calculator (Flexbox)</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">    
    <style>
        body {
            width:100%;
            background-color: #dbdbdb;
        }
        .container {
            display:flex;
            width: inherit;
            flex-direction: column;
            justify-content: center;
            margin-top: 15px;
            align-items: center;            
        }
    </style>
    </head>
    <body>  
        <div class="container">
        </div>
    </body>
</html>

```

我们将容器设置为 flex 容器。然后，我们设置宽度来继承其父元素的大小。这将是主体，因为主体的宽度设置为 100 %,容器将继承该值并将其宽度设置为 100 %,占据设备的整个宽度。

`flex-direction: column`将其内容设置为从上到下布局。`justify-content: center;`沿容器主轴中心分布元素。

`margin-top: 15px;`将容器顶部边缘的厚度设置为`15px`。这使得容器向下调整 15 个像素。`align-items: center;`定义了它的伸缩项沿着容器伸缩线横轴的中心对齐。这将导致计算器从页面中间开始计算。

## 向 flex 容器添加内容

让我们在`div#container`中放置一个包装器`div`。

```
<html>
    <head>
        <title>Calculator (Flexbox)</title>
        <meta name="viewport" content="width=device-width, initial-scale=1">    
        <style>
            body {
                width:100%;
                background-color: #dbdbdb;
            }
            .container {
                display: flex;
                width: inherit;
                flex-direction: column;
                justify-content: center;
                margin-top: 15px;
                align-items: center;        
            }
            .wrapper {
                width:500px;                    
            }
        </style>
    </head>
    <body>  
        <div class="container">
            <div class="wrapper">
            </div>
        </div>
    </body>
</html>

```

![The Result](img/a0942789b1d676d643db6626dc2ef03a.png)

这个包装器是放置计算器元素的地方。我们设置宽度来扩展`500px`宽度。包装器将从上到下容纳计算器的屏幕和按钮。

我们的计算器将被分成几列。每一列都将是一个 flex 容器，在一行中显示 flex 项目。第一列将包含计算器输入屏幕，下一列将包含计算器结果屏幕。后续列将保存按钮(一列四个按钮)。

让我们为`.col`类添加样式:

```
<html>
    <head>
        <title>Calculator (Flexbox)</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">    
    <style>
        body {
            width:100%;
            background-color: #dbdbdb;
        }
        .container {
            display:flex;
            width: inherit;
            flex-direction: column;
            justify-content: center;
            margin-top: 15px;
            align-items: center;            
        }
        .wrapper {
            width:500px;                    
        }
        .col {
            width:500px;
            display: flex;
            flex-direction: row;
            justify-content: center;
            align-items: center;
        }
    </style>
    </head>
    <body>  
        <div class="container">
        <div class="wrapper">
        </div>
        </div>
    </body>
</html>

```

我们将`width`设置为`500px`，这是我们希望计算器的大小。我们设置了`display: flex;`，它将 row 类设置为一个 flex 容器。

我们设置弹性项目从左到右排列:`flex-direction: row;`。我们还通过声明`justify-content: center;`和`align-items: center;`，从横轴和主轴的中心开始布置灵活项目。

## 布置屏幕

现在是时候在`wrapper#div`里面布置屏幕和按钮了。我们将从两个屏幕开始:输入屏幕和结果屏幕。

```
<html>
    <head>
        <title>Calculator (Flexbox)</title>
        <meta name="viewport" content="width=device-width, initial-scale=1">    
    <style>
        body {
            width:100%;
            background-color: #dbdbdb;
        }
        .container {
            display:flex;
            width: inherit;
            flex-direction: column;
            justify-content: center;
            margin-top: 15px;
            align-items: center;            
        }
        .wrapper {
            width:500px;                    
        }
        .col {
            width:500px;
            display: flex;
            flex-direction: row;
            justify-content: center;
            align-items: center;
        }
    </style>
    </head>
    <body>  
        <div class="container">
        <div class="wrapper">
            <div class="col">
                <div class="expression" id="expression">
                </div>
            </div>          
            <div class="col">
                <div class="result" id="result"></div>
            </div>          
        </div>
        </div>
    </body>
</html>

```

结果屏幕为`div#result`，输入屏幕为`div#expression`。每一个都在单独的`div#col`中呈现。

接下来，我们将样式化`div#expression`和`div#result`:

```
.expression {
  background-color: white;
  width: inherit;
  height: 100px;
  font-size: 50px;
  border-top: 1px solid gray;
  border-right: 1px solid gray;
  border-left: 1px solid gray;
}
.result {
  width: inherit;
  margin-bottom: 12px;
  height: 54px;
  background-color: white;
  border-bottom: 1px solid gray;
  border-right: 1px solid gray;
  border-left: 1px solid gray;
}

```

我们将背景颜色设置为白色，宽度从父对象中取值。在我们的例子中，这是宽度为 T1 的 T0。因此宽度需要 500 个像素来跨越父级的宽度。`div#expression`的高度为`100px`,`div#result`的高度为`54px`。

我们这样设计是因为输入屏幕应该比结果屏幕高，并且两者都应该包含`154px`个单位。我们将`div#expression`的字体大小设置为`50px`，因为它应该比结果屏幕更粗更大。

接下来，我们标出了屏幕的边框。`div#expression`的右上和左上边框标记为`1px`并有`color: solid gray`。对于`div#result`，`bottom-right-left`边框标记为`1px`，颜色为纯灰色。我们省略了`div#expression`上的`bottom-border`和`div#result`上的顶部边框，这样两者看起来就像一个整体。

结果如下:

![White Box](img/157b5cfae546f7625989ac3c25777df3.png)

## 向 flexbox 布局添加元素

对于这一步，我们将在 flexbox 布局中设置按钮。

我们的计算器用户界面将有以下字符的按钮:1，2，3，4，5，6，7，8，9，0，*，-，+，/，CLR 和=。

我们将在 flexbox 布局中设置如下按钮:

*   1、2 和 3 合二为一`div#col`
*   4、5、6 和+在另一个`div#col`
*   7、8、9 和*在又一个`div#col`
*   CLR，0，=，和/在另一个`div#col`

```
<html>
  <head>
    <title>Calculator (Flexbox)</title>
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <style>
      body {
        width: 100%;
        background-color: #dbdbdb;
      }
      .container {
        display: flex;
        width: inherit;
        flex-direction: column;
        justify-content: center;
        margin-top: 15px;
        align-items: center;
      }
      .wrapper {
        width: 500px;
      }
      .col {
        width: 500px;
        display: flex;
        flex-direction: row;
        justify-content: center;
        align-items: center;
      }
      .expression {
        background-color: white;
        width: inherit;
        height: 100px;
        font-size: 50px;
        border-top: 1px solid gray;
        border-right: 1px solid gray;
        border-left: 1px solid gray;
      }
      .result {
        width: inherit;
        margin-bottom: 12px;
        height: 54px;
        background-color: white;
        border-bottom: 1px solid gray;
        border-right: 1px solid gray;
        border-left: 1px solid gray;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <div class="wrapper">
        <div class="col">
          <div class="expression" id="expression"></div>
        </div>
        <div class="col">
          <div class="result" id="result"></div>
        </div>
        <div class="col">
          <button>1</button>
          <button>2</button>
          <button>3</button>
          <button>-</button>
        </div>
        <div class="col">
          <button>4</button>
          <button>5</button>
          <button>6</button>
          <button>+</button>
        </div>
        <div class="col">
          <button>7</button>
          <button>8</button>
          <button>9</button>
          <button>*</button>
        </div>
        <div class="col">
          <button>CLR</button>
          <button>0</button>
          <button>=</button>
          <button>/</button>
        </div>
      </div>
    </div>
  </body>
</html>
```

结果如下:

![Tiny Buttons](img/0ed53f1aa1db686edb2169ecc02f8d08.png)

这看起来很棒，除了我们的按钮很小。没什么我们不能通过一点点造型来解决的。

## flexbox 中的样式元素

让我们为按钮元素添加一些样式:

```
button {
  margin-right: 2px;
  margin-top: 2px;
  width: inherit;
  font-weight: 700;
  border: 2px solid gray;
  margin-bottom: 2px;
  font-size: 43px;
  text-align: center;
}

```

上面的 CSS 代码会影响页面上的所有按钮。

在按钮区域，我们将底部、顶部和右侧的厚度值分别设置为`2px`。这样它们就不会那么紧密了。我们设置它们的宽度以从它们的父代继承它们的值。父节点是`div#col`，其宽度`500px`从父节点继承到`div#wrapper`。

因为一个`div#col`中有四个按钮，尽管每个按钮都继承了`500px`，但是每个按钮都不会有`500px`的宽度值。相反，500 像素的宽度将在四个按钮之间平均分配。

根据我的计算，这意味着每个按钮的宽度为 123 像素(减去右边距的 2 像素)。

我们将`font-weight`设置为`700`，将`font-size`设置为`43px`，这使得按钮看起来更大，文本更粗。我们将边框的宽度定义为`2px`,并应用纯灰颜色。最后，我们将文本设置为在按钮的中心对齐。

结果将如下所示:

![Larger Buttons](img/1e64abe2ee1435553f53fc17f275cd72.png)

Tada!!!

## 使 flexbox 布局具有响应性

我们就要完成了，但是现在，我们的计算器没有响应。让我们这样做吧。

打开开发工具，点击右边的移动图标开关:

![Open Devtools](img/c3887b2da09c2b4be587bc84ab8e83b5.png)

正如你所看到的，我们的计算器比设备宽度薄得多，一些元素被切掉了。

为了使我们的 flexbox 布局具有响应性，我们将使用`@media`查询 CSS 代码。我们将添加一个`@media`查询，并将查询代码设置为当设备宽度为 500px 时激活:

```
@media (max-width: 500px) {
}

```

500 像素应该覆盖平板电脑和手机的可用宽度。

我们不希望`div#wrapper`类的宽度为 500 像素，因为当在移动设备上查看时，它会在屏幕的不可见部分中延伸。相反，我们将它设置为 100%的设备宽度:

```
@media (max-width: 500px) {
  .wrapper {
    width: 100%;
  }
}

```

如果器件宽度为`322px`，那么`div#wrapper`宽度将为 322px。换句话说，将宽度设置为`100%`告诉 CSS 根据设备宽度分配宽度 px 值。

我们还将`div#container`宽度设置为`100%`:

```
@media (max-width: 500px) {
  .wrapper {
    width: 100%;
  }
  .container {
    width: 100%;
  }
}

```

包含按钮和屏幕的`div#col`应设置为设备宽度的 100%。之前我们设置为取`500px`；这将导致按钮在移动设备上伸展到视图之外。

```
@media (max-width: 500px) {
  .wrapper {
    width: 100%;
  }
  .container {
    width: 100%;
  }
  .col {
    width: 100%;
  }
}

```

接下来，设置 body 从设备继承其宽度:

```
@media (max-width: 500px) {
  .wrapper {
    width: 100%;
  }
  .container {
    width: 100%;
  }
  .col {
    width: 100%;
  }
  body {
    width: inherit;
  }
}

```

这是最终的成品:

![Final Product](img/45105350245692a4b6c093cf425ec21c.png)

现在，我们的计算器用户界面在移动设备上表现良好。目前它是为显示器尺寸为 334 x 453px 的手机设置的。您可以调整宽度来查看计算器调整到适合您想要的屏幕大小。

我们的 flexbox 示例的完整代码如下:

```
<html>
  <head>
    <title>Calculator (Flexbox)</title>
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <style>
      body {
        width: 100%;
        background-color: #dbdbdb;
      }
      .container {
        display: flex;
        width: inherit;
        flex-direction: column;
        justify-content: center;
        margin-top: 15px;
        align-items: center;
      }
      .wrapper {
        width: 500px;
      }
      .col {
        width: 500px;
        display: flex;
        flex-direction: row;
        justify-content: center;
        align-items: center;
      }
      .expression {
        background-color: white;
        width: inherit;
        height: 100px;
        font-size: 50px;
        border-top: 1px solid gray;
        border-right: 1px solid gray;
        border-left: 1px solid gray;
      }
      .result {
        width: inherit;
        margin-bottom: 12px;
        height: 54px;
        background-color: white;
        border-bottom: 1px solid gray;
        border-right: 1px solid gray;
        border-left: 1px solid gray;
      }
      button {
        margin-right: 2px;
        margin-top: 2px;
        width: inherit;
        font-weight: 700;
        border: 2px solid gray;
        margin-bottom: 2px;
        font-size: 43px;
        text-align: center;
      }
      @media (max-width: 500px) {
        .wrapper {
          width: 100%;
        }
        .container {
          width: 100%;
        }
        .col {
          width: 100%;
        }
        body {
          width: inherit;
        }
      }
    </style>
  </head>
  <body>
    <div class="container">
      <div class="wrapper">
        <div class="col">
          <div class="expression" id="expression"></div>
        </div>
        <div class="col">
          <div class="result" id="result"></div>
        </div>
        <div class="col">
          <button>1</button>
          <button>2</button>
          <button>3</button>
          <button>-</button>
        </div>
        <div class="col">
          <button>4</button>
          <button>5</button>
          <button>6</button>
          <button>+</button>
        </div>
        <div class="col">
          <button>7</button>
          <button>8</button>
          <button>9</button>
          <button>*</button>
        </div>
        <div class="col">
          <button>CLR</button>
          <button>0</button>
          <button>=</button>
          <button>/</button>
        </div>
      </div>
    </div>
  </body>
</html>

```

## 结论

Flexbox 为开发人员节省了大量与分层工作相关的压力和时间。如果没有 flexbox，我们将不得不编写大量的 CSS 代码来设计`.container`、`.wrapper`和`.col`类。Flexbox 为我们做了所有的工作，所以我们可以专注于按钮和屏幕样式。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。