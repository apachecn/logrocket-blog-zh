# 纯 CSS 组件- LogRocket 博客

> 原文：<https://blog.logrocket.com/css-only-components/>

我们知道 CSS 是用来设计 UI 组件的样式的，但是你知道你也可以用 CSS 来增加交互性吗？

在这篇文章中，我们将会找到答案。

JavaScript 很棒——这篇文章的目的不是要证明你根本不需要 JavaScript。然而，最好意识到您可以构建许多 UI 组件功能，而不需要额外依赖 JavaScript。

## 如何用 CSS 添加交互性

让我们来看看一些模式，它们将帮助我们仅用 CSS 就能为我们的 UI 组件添加交互性。

### 当两个独立的元素需要一起工作时

在 HTML 中，我们可以用元素的`id`属性和`<a>`标签中的`href`来建立链接和元素之间的关系。

这里有一个例子:

```
<a href="#p1">Jump to the paragraph</a>

<p id="p1">Hello! I'm a paragraph.</p>
```

如果我们点击链接，页面会自动跳转到带有`id="p1"`的段落，因为`<a>`的`href`与之匹配。我们现在有了这两个元素之间的关系。

现在，如果我们想在`<p id="p1">`处于焦点时修改样式或添加额外的样式，我们可以使用`:target`伪类。`:target`帮助我们找到与 URL 片段匹配的`id`元素，即`href`。

```
p:target {
  background-color: thistle;
}
```

现在，当页面在链接被点击后跳转到该段落时，该段落也将具有如上所述的背景颜色。

让我们看一些使用这种模式的 UI 组件的例子。

### 仅 CSS 模式/对话框

> 对话框被隐藏，直到它匹配锚定目标*/。覆盖{位置:绝对；top:0；底部:0；左:0；右:0；背景:rgba(0，0，0，0.8)；转场:不透明度 250ms 可见性:隐藏；不透明度:0；/*对话框在匹配锚定目标时淡入*/&:target { visibility:visible；不透明度:

以下是 HTML 和 CSS 的简化版本:

```
<!-- #dialog creates the relationship between the button link and dialog -->
    <a class="button" href="#dialog">Click To Open Modal</a>

    <!-- wrap dialog body in "overlay" -->
    <!-- role="dialog" will let the screen reader know the purpose of this section -->
    <div id="dialog" class="overlay" role="dialog">
      <div class="dialog-body">

          <!-- href="#" resets the target, hence the dialog closes  -->
          <a class="close" href="#" aria-label="Close dialog">&times;</a>

            <!-- content -->
        </div>
    </div>

    .overlay {
    /* Positioning and styling of the overlay */
      position: absolute;
      top: 0;
      bottom: 0;
      left: 0;        
      right: 0;
      background: rgba(0, 0, 0, 0.8);
      transition: opacity 250ms;

    /* Dialog is hidden until it matches an anchor target */
      visibility: hidden;
      opacity: 0;

      /* Dialog fades in when it matches an anchor target */
      &:target {
        visibility: visible;
        opacity: 1;
      }
    }

    .dialog-body {
      position: relative;
      margin: 50px auto; /* center the dialog body */
      width: 300px;
      background: #fff;

    /* positioning the close button inside the dialog body */
      .close {
        position: absolute;
        width: 20px;
        height: 20px;
        top: 20px;
        right: 20px;
      }
    }
```

注意:你不能用`esc`键关闭对话框，也不能在对话框内捕捉焦点。

### 灯箱式图像浏览器

> 没有描述

下面是代码的简化版本:

一些警告:两个图像一直都在加载。为了用 JavaScript 额外解决这个问题，我们可以只在单击缩略图时加载更大尺寸的图像。

```
<!-- thumbnail image -->
    <a href="#img1">
      <img src="https://picsum.photos/id/999/300/300" alt="description of image">
    </a>

    <!-- lightbox image (visually hidden) -->
    <div class="lightbox" id="img1">
      <a href="#" class="close" aria-label="close image">&times;</a>
      <img src="https://picsum.photos/id/999/800/400" alt="description of image">
    </div>

    .lightbox {
      /* Hidden lightbox image */
      display: none;
      position: fixed;
      z-index: 999;
      width: 100%;
      height: 100%;
      text-align: center;
      top: 0;
      left: 0;
      background: rgba(0, 0, 0, 0.5);

      img {
          /* Add some padding to the image when enlarged */
        max-width: 90%;
        max-height: 80%;
        margin-top: 2%;
      }

      &:target {
        /* Show the lightbox */
        outline: none;
        display: block;
      }
    }
    .close {
      position: absolute;
      top: 20px;
      right: 20px;
      font-size: 3em;
      color: #fff;
      text-decoration: none;
    }
```

单选按钮控制的 UI 组件

## 单选按钮的问题在于，在一组具有相同`name`属性的单选按钮中，只有一个可以是`checked`。

我们可以利用这一点来创建基于这一原则的 UI 组件，也就是说，在一个给定的实例中只能选择一个部分(比如选项卡和折叠)。

因为这些组件是单选按钮，所以您可以使用箭头键轻松地导航它们。不需要额外的设置。

仅 CSS 选项卡

### 如果你想在池塘的另一边过着霍格沃茨风格的生活，你可以选择 CSS 标签。Ilvermorny 的创始人伊索尔特·塞尔(Isolt Sayre)一直希望她能去霍格沃茨，而这所学校似乎肯定体现了它的一些传统。霍格沃茨因为你爱它。

> CSS-Only Tabs Ilvermorny If you fancy living a Hogwarts-style life across the pond. The founder of Ilvermorny, Isolt Sayre, always wished she could go to Hogwarts, and the school definitely seems to embody some of its traditions. Hogwarts Because you love it.

代码的简化版本:

仅支持 CSS 的手风琴

```
<div class="tabs" role="tablist">

    <!-- all the radio buttons has name i.e radioTab so at a time only one can be checked --> 
    <div class="tab" role="tab">
        <input type="radio" name="radioTab" id="tab-1" checked>
        <label for="tab-1">Label 1</label>
          <div class="content">
              Content for label 1
          </div>
      </div>

      <div class="tab" role="tab">
        <input type="radio" name="radioTab" id="tab-2">
        <label for="tab-2">Label 2</label>
          <div class="content">
              Content for label 2
          </div>
      </div>

      <div class="tab" role="tab">
        <input type="radio" name="radioTab" id="tab-3">
        <label for="tab-3">Label 3</label>
          <div class="content">
              Content for label 3
          </div>
      </div>
    </div>

    .tabs {
      position: relative;
      display: flex;
      align-items: flex-start;
      min-height: 200px; /* give a min height, can be anything you want */
    }

    .tab {
      display: flex;
      flex-direction: column;

      label {
        background-color: #0094a7;
      }

      /* hide the radio buttons visually*/
      [type="radio"] {
        position: absolute;
        height: 0;
        width: 0;
        overflow: hidden;
        clip: rect(0, 0, 0, 0);

        /* change color of active tab */
        &:checked ~ label {
          background: #007584;
        }

        /* makes the active tab's content visible */
        &:checked ~ label ~ .content {
          opacity: 1;
          z-index: 1; /* increase the z-index so the content is in focus*/
        }
      }
    }

    .content {
      position: absolute;
      left: 0;
      right: 0;
      bottom: 0;
      top: 3em;
      width: 100%;
      height: 100%;
      z-index: -1;
      opacity: 0; /* hides the tab content by default */
    }
```

### 如果你想在池塘的另一边过着霍格沃茨风格的生活，你可以试试 CSS 专用的手风琴。Ilvermorny 的创始人伊索尔特·塞尔(Isolt Sayre)一直希望她能去霍格沃茨，而这所学校似乎肯定体现了它的一些传统。霍格沃茨因为你爱它。

> CSS-Only Accordion Ilvermorny If you fancy living a Hogwarts-style life across the pond. The founder of Ilvermorny, Isolt Sayre, always wished she could go to Hogwarts, and the school definitely seems to embody some of its traditions. Hogwarts Because you love it.

代码的简化版本:

仅 CSS 的动态工具提示

```
<div class="accordion">
    <!-- all the radio buttons has name i.e radioPanel so at a time only one can be checked --> 
      <div>
        <input type="radio" name="radioPanel" id="panel-1" checked>
        <label for="panel-1">Panel 1</label>
        <div class="content">
          Content for Panel 1
        </div>
      </div>
      <div>
        <input type="radio" name="radioPanel" id="panel-2">
        <label for="panel-2">Panel 2</label>
        <div class="content">
          Content for Panel 2
        </div>
      </div>
      <div>
        <input type="radio" name="radioPanel" id="panel-3">
        <label for="panel-3">Panel 3</label>
        <div class="content">
          Content for Panel 3
        </div>
      </div>
    </div>

    .accordion {
      /* Visually hide the radio input */
      input[type="radio"] {
        position: absolute;
        height: 0;
        width: 0;
        overflow: hidden;
        clip: rect(0, 0, 0, 0);

        &:checked ~ .content {
          max-height: 20em; /* give a max height, can be anything */
        }

        &:checked + label {
          background-color: #3aa13a;
        }
      }

      label {
        position: relative;
        display: block;
        background-color: darkseagreen;
        color: white;
        transition: all 300ms ease-in-out;
        cursor: pointer;
      }

      .content {
        /* so that the content is scrollable when it exceeds the mentioned max height*/
        overflow: auto; 

        max-height: 0em;
        position: relative;
        transition: all 300ms ease-in-out;
      }
    }
```

### 使用`attr()` CSS 函数，我们可以使用任何自定义属性在 html 标记中定义内容，然后获取值。

这目前仅适用于`content`属性。结合使用这些方法，我们可以创建包含动态内容的工具提示。

更多来自 LogRocket 的精彩文章:

* * *

### ISRO 是印度的太空探索机构。其愿景是“在进行空间科学研究和行星探索的同时，利用空间技术促进国家发展”。NASA 是美国联邦政府的一个独立机构，负责民用太空计划以及航空和航天研究。

* * *

> ISRO is the space exploration body of India. Its vision is to “harness space technology for national development while pursuing space science research & planetary exploration”. NASA is an independent agency of the United States Federal Government responsible for the civilian space program, as well as aeronautics and aerospace research.

在 CodePen 示例中，我使用了`aria-label`来访问内容。

```
<p>
      <span data-title="Hello in french">Bonjour!</span> How are you?
    </p>

    span {
      position: relative;
      color: blue;
      cursor: pointer;
      border-bottom: 2px dotted currentcolor;

      &:before {
        /* content inside will come from the data-title attribute as defined in HTML */
        content: attr(data-title); 

        opacity: 0;
        position: absolute;
        top: 30px;
        right: -90px;
        font-size: 14px;
        width: 100px;
        padding: 10px;
        color: #fff;
        background-color: #555;
        border-radius: 3px;
        pointer-events: none;
      }

      &:hover:before {
        opacity: 1;
      }
    }
```

在上面的例子中，我使用了`data-title`，这只是为了说明`attr()`可以从任何自定义属性中访问内容。

结论

## 这些仅仅是几个例子，展示了如何用 CSS 实现交互性。

下一次构建组件时，考虑一下在不增加 JavaScript 文件的情况下，仅使用 CSS 就可以实现多少功能。

以下是网络上其他一些很棒的例子:

你的前端是否占用了用户的 CPU？

## 随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。

参考

## [无线遥控网页设计](https://alistapart.com/article/radio-controlled-web-design/)

1.  MDN 参考上`[:target](https://developer.mozilla.org/en-US/docs/Web/CSS/:target)`
2.  MDN reference on `[:target](https://developer.mozilla.org/en-US/docs/Web/CSS/:target)`