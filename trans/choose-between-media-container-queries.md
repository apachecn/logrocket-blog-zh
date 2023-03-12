# 何时以及如何在媒体查询和容器查询之间进行选择

> 原文：<https://blog.logrocket.com/choose-between-media-container-queries/>

每个关注响应式设计领域最新事件的人都会同意 CSS 容器查询的引入是一个游戏改变者。这是自从引入媒体询问以来最令人兴奋的事情。

但是什么是容器查询呢？为什么容器查询很重要，如何使用它们？

在本文中，我们将通过以下部分回答所有这些问题以及更多问题:

别再浪费时间了，让我们开始吧。

很久以前，大多数网站都很难在不同的屏幕尺寸上呈现组件，尤其是随着手机革命的到来。大多数网站通过创建一个域名为`m.`的新网站来解决这个问题。如果你用过`m.facebook.com`，这就是原因。

所有这些都发生在媒体询问之前。有了媒体查询，网站可以被构建成适合特定的视窗，所以在智能手机上放大来查看网站上有什么的日子已经一去不复返了。

媒体查询提供了关于某些样式的条件语句，然后根据条件是否满足来实现或不实现。

虽然媒体查询发生了很大变化，但它们仍然有一个问题:它们不可重用。使用媒体查询，您可以创建一个响应式元素并实现它，即使它在标准用例中看起来很好，但如果它被移动到一个不同的容器中，其 CSS 属性会影响元素的尺寸，那么它可能不会很好地工作。为了让它正常工作，你需要添加更多的 CSS 属性。

这就是容器查询的用武之地。使用容器查询，您现在可以将单个元素本身作为目标，并使它们能够适应几乎任何给定的容器或布局。

容器查询类似于媒体查询；唯一的区别是，它们使容器或元素基于容器的可用空间做出响应，而不是视口。但这提出了一个问题:我们还需要媒体的询问吗？

以上问题的答案是肯定的！尽管容器查询解决了它们相当大的局限性，但是媒体查询仍然有大量的用例。

媒体查询可以而且应该仍然用于页面布局，页面布局通常位于 DOM 的最顶层，而不是嵌套在另一个容器中。它们最适合这样做，因为主页布局不应该适应除了视窗之外的任何东西。

[媒体查询](https://blog.logrocket.com/new-media-queries-you-need-to-know/)也可以用于样式化全局元素，如间距、字体大小或颜色。例如，如果你建立一个大字体的网站，那么对于手机屏幕来说，字体和间距可能会太大。这可以通过以下媒体查询来解决:

```
:root {
   --font-size-hl: 20px;
   --font-size-h2: 15px;
   --font-size-h3: 12px;
   --font-size-p: 8px;

   --letter-spacing: 8px;
}

@media (min-width: 300px) {
   :root {
       --font-size-hl: 13px;
       --font-size-h2: 10;
       --font-size-h3: 8px;
       --font-size-p: 5pxrem;

       --letter spacing: 4px;
   }
}

```

shine 的一个媒体查询场景是当你必须从页面中删除一个组件时。例如，看看这个 Instagram 主页:

![Desktop view of Instagram feed and message bar](img/b28b9f537618cf232b7f89315c03f5d1.png)

在桌面上，消息栏看起来非常好，但是大多数移动设备没有合适的屏幕尺寸来显示它。对于媒体查询，可以删除整个消息选项卡，相同的页面将显示如下。

![Instagram mobile view hides message bar ](img/db321fdb2bb271d60c50711b846310d2.png)

值得注意的是，媒体查询不仅仅是为了检测屏幕大小以及它们如何影响组件。

媒体查询还可以用于与用户偏好相关的不同查询，例如:

## 何时以及如何使用容器查询

[容器查询](https://blog.logrocket.com/what-are-container-queries-in-css/)允许您定位特定的容器，并将其配置为基于容器的宽度和高度显示，而不必使用媒体查询。它主要用于卡组件，但它很灵活，因此您可以将它用于多个组件。

对于普通 CSS，容器查询中使用的属性与媒体查询中使用的属性有很多相似之处。您需要了解的唯一其他属性是:

*   `container-type`:用于将元素声明为查询容器，允许子组件查询
*   `container-name`:用于为容器指定一个名称，以便进一步过滤

让我们看一个快速的卡片示例。首先，让我们用 HTML 草拟一张卡片。

```
<div class="container">
  <div class="contain-container contain-container--small">
    <h2>first example</h2>
    <div class="contain">
      <div class="contain__thumbnail"&gt;</div>
      <div class="contain__content">

        <h3 class="contain__title">Lorem Ipsium in english</h3>
        <p class="contain__description">But I must explain to you how all this mistaken idea of denouncing pleasure and praising pain was born and I will give you a complete account of the system, and expound the actual teachings of the great explorer of the truth, the master-builder of human happiness. No one rejects, dislikes, or avoids pleasure itself, because it is pleasure, but because those who do not know how to pursue pleasure rationally encounter consequences that are extremely painful. Nor again is there anyone who loves or pursues or desires to obtain pain of itself, because it is pain, but because occasionally circumstances occur in which toil and pain can procure him some great pleasure. .</p>
      </div>
    </div>
  </div>
  <div class="contain-container contain-container--large">
    <h2>Second example</h2>
    <div class="contain">
      <div class="contain__thumbnail"></div>
      <div class="contain__content">
        <h3 class="contain__title">Lorem Ipsium in english</h3>
        <p class="contain__description">To take a trivial example, which of us ever undertakes laborious physical exercise, except to obtain some advantage from it? But who has any right to find fault with a man who chooses to enjoy a pleasure that has no annoying consequences, or one who avoids a pain that produces no resultant pleasure.</p>
      </div>
    </div>
  </div>
</div>

```

虚拟文本只是 lorem ipsum 的翻译版本。这是我个人的怪癖。

接下来，让我们放入 CSS。

```
body {
  background: #5f64e2;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica,
    Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
  color: #fdf2f2;
}

.container {
  padding: 16px;
  margin: auto;
  display: flex;
  gap: 16px;
}

.contain-container {
  margin: 16px auto;
  flex-grow: 0;
  contain: layout inline-size;
  container-type: inline-size;
  --css-contain: layout inline-size;
}

.contain-container--small {
  width: 300px;
}

.contain-container--large {
  width: 600px;
}

.contain {
  padding: 16px;
  border-radius: 8px;
  background: black;
  margin-bottom: 16px;
  color: white;
}

  .contain__thumbnail {
    height: 130px;
    min-width: 130px;
    background: red;
    border-radius: 4px;
  }

  .contain__title {
    margin-top: 4px;
    margin-bottom: 8px;
  }

  .contain__description {
    margin: 0;
  }

```

上述代码的结果将如下所示:

![Two container query cards](img/390a8719a8a4490e7fa2a6512b35df43.png)

两张卡基本相同，只是一张宽 300px，另一张宽 600px。通过容器查询，我们可以用下面的代码使右边的卡片看起来和行为非常不同。

```
@container (min-width: 400px) {
  .contain {
    background-color: white;
    color: black;
    display: flex;
    gap: 16px;
  }
}

```

这段代码告诉任何超过 400 像素宽的容器使用白色背景，使用`display: flex`，文本颜色为黑色。结果将如下所示:

![Updated query cards](img/9fef8517da4c6670833c0c5d9e2911fd.png)

对右侧卡片的更改仅适用于卡片必须占据宽度≥ 400px 的空间的情况，因此，如果没有足够的空间来显示，或者如果它正在被移动设备查看，卡片将恢复到这种状态。

![Container Query cards in mobile view](img/0a97ddf3863d6c95d4e46d1ea6e37100.png)

### 使用 CSS-in-JS 的容器查询

我知道 JSS，或 [CSS-in-JS](https://blog.logrocket.com/tag/css-in-js) ，现在是一个非常有争议的话题。有些人，像[这家伙](https://www.freecodecamp.org/news/you-dont-need-css-in-js-why-i-use-stylesheets/)，讨厌 JSS，不想让你靠近它。但是其他人，像[这个家伙](https://jxnblk.com/blog/why-you-should-learn-css-in-js/)，绝对喜欢它，希望你使用它。

不管你喜不喜欢 JSS，你都得承认它非常方便。就像普通的 CSS 一样，在 JSS 也可以使用容器查询。这里有一个非常简单的例子来说明它是如何工作的。

首先，让我们构建四个简单的容器。

```
<div class=cquery>
  <h1>To take a trivial example, which of us ever undertakes laborious physical exercise, except to obtain some advantage from it? </h1>
</div>

<div class=cquery style=width:80%>
  <h1>To take a trivial example, which of us ever undertakes laborious physical exercise, except to obtain some advantage from it?</h1>
</div>

<div class=cquery style=width:60%>
  <h1>To take a trivial example, which of us ever undertakes laborious physical exercise, except to obtain some advantage from it?</h1>
</div>

<div class=cquery style=width:40%>
  <h1>To take a trivial example, which of us ever undertakes laborious physical exercise, except to obtain some advantage from it?</h1>
</div>

```

接下来，让我们添加 JSS。我加了一些注释，让它更容易理解。

```
function JSinCSS() {
  //target an html element with id ='JSinCSS' and declare it tag
  let tag = document.querySelector("#JSinCSS");

  //if there is no html element with id ='#JSinCSS'
  if (!tag) {
    //create a style tag like this
    //<style></style>
    tag = document.createElement("style");

    //set id of style tag to be 'JSinCSS'
    //<style id='JSinCSS'></style>
    tag.id = "JSinCSS";
    //we call document.head.appendChild to append the style element to the head tag as a child.
    document.head.appendChild(tag);
  }

  //inside the style tag add innerHTML to it

  tag.innerHTML = `

    body:before {
      content: '${innerWidth} x ${innerHeight}';
    }

    .cquery {
      border: 4px solid red;
      background-color: green;
    }

    ${containerQuery('.cquery', 'this.offsetWidth > 500', `

      $this {
        background: blue;
      }

      $this h1 {
        font-size: 30pt;
      }

    `)}

    ${containerQuery('.cquery', 'this.offsetWidth > 800', `

      $this {
        background: purple;
      }

      $this h1 {
        font-size: 30pt;
      }

    `)}

    ${containerQuery('.cquery', 'this.offsetWidth > 1000', `

      $this {
        background: gold;
      }

      $this h1 {
        font-size: 30pt;
      }

    `)}

  `

}

function containerQuery(selector, test, stylesheet) {
    //target the container element and declare it tag
  var tag = document.querySelectorAll(selector);
  var style = "";
  var count = 1;

  for (var i = 0; i < tag.length; i++) {
      //tag.length return the number of nodes in tag
      // run a forLoop so when i == tag.length 
      //declare a function that returns test
    var func = new Function(`return ${test}`);
    var attr = (selector + test).replace(/[= "'><+\.]/g, "");

    if (func.call(tag[i])) {
      tag[i].setAttribute(`data-${attr}`, count);

      var css = stylesheet.replace(/\$this/g, `[data-${attr}="${count}"]`);

      style += css;

      count++;
    } else {
      tag[i].setAttribute(`data-${attr}`, "");
    }
  }

  return style;
}

//call the function in to listen to different events and call the function JSinCSS
window.addEventListener("load", JSinCSS);
window.addEventListener("resize", JSinCSS);
window.addEventListener("input", JSinCSS);
window.addEventListener("click", JSinCSS);

```

上面的代码创建了四个不同的容器，然后用`containerQuery`标签设置了一个容器查询，这样容器的颜色会根据不同的大小而变化。结果看起来是这样的。

![Four container query cards in yellow, purple, blue, and green](img/4e005fcae68ac47f519ad465b6fac7a3.png)

从 HTML 中，你可以看到这是同一个容器。但在宽度≥1000px 时，容器颜色变为金色。宽度为 800 像素和 500 像素时，容器分别变为紫色和蓝色。低于该值时，它保持默认的绿色。所以，如果这些容器必须占据≤1000 像素的空间，你会得到这个:

![Containers become purple blue and green in smaller space](img/35c665f1974fc0a6e8f460cb2ec58400.png)

这里是 [CodePen 链接](https://codepen.io/Fimbosky1/full/poaPbyY)如果你想玩玩代码。

## 结论

响应式设计的整个要点是实现通用性和效率，随着容器查询的引入，我们离响应式设计的完美又近了一步。它解决了媒体查询的许多限制，但它不能完全取代媒体查询。

我个人认为，媒体查询和容器查询就像鸡块和辣酱:它们都有各自的用途，但合在一起，它们将震撼你的世界。我希望你能愉快地使用它们。

下一集见。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。