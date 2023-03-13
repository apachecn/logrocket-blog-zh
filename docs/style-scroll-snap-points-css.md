# 如何用 CSS 设置滚动捕捉点的样式

> 原文：<https://blog.logrocket.com/style-scroll-snap-points-css/>

滚动对齐是一种交互增强(如果实现正确的话)，现在是一种原生 CSS 特性，可以帮助用户对齐某些滚动点。这样做的好处是，它可以帮助用户捕捉到应该一起观看的各种内容元素，减少整体所需的滚动量，并防止过度滚动。

在本教程中，您将学习如何构建包含可捕捉内容的 HTML 容器。我们将使用 CSS 的[滚动捕捉属性](https://blog.logrocket.com/how-to-use-css-scroll-snap/),看看你附近的网络浏览器中的其他 CSS 滚动捕捉特性。目标是避免使用 JavaScript 来构建更高性能的解决方案，因为对于滚动捕捉来说，JavaScript 不再是必需的。我们将只使用 CSS。

此外，您可能已经正确地猜测到，对于滚动捕捉，有许多与 [UX 相关的](https://blog.logrocket.com/category/ux-design/)警告，我们将在后面讨论。

我们开始吧！

*向前跳转:*

## 开始我们的 CSS 项目

首先，这是我们要做的:

请看 [CodePen](https://codepen.io) 上丹尼尔·施瓦茨( [@mrdanielschwarz](https://codepen.io/mrdanielschwarz) )
的笔 [CSS 滚动抓拍](https://codepen.io/mrdanielschwarz/pen/XWBpmKy)。

澄清一下，这是内容的五个抓拍点。

以下是完整的代码:

```
<!doctype html>
<html>

<head>
    <style>
        * {
            margin: 0;
            line-height: 1.5;
            box-sizing: border-box;
            font-family: sans-serif;
        }

        main {
            height: 100vh;
            overflow-y: scroll;
            scroll-snap-type: y mandatory;
        }

        html,
        body {
            overflow: hidden;
        }

        section {
            height: 100vh;
            display: flex;
            background: #eee;
            scroll-snap-align: start;
            scroll-snap-stop: always;
        }

        section:nth-child(odd) {
            background: #ddd;
        }

        div {
            margin: auto;
            text-align: center;
        }
    </style>
</head>

<body>
    <main>
        <section>
            <div>
                <h1>Section 1</h1>
                <p>Lorem ipsum howdy</p>
            </div>
        </section>
        <section id="section-2">
            <div>
                <h2>Section 2</h2>
                <p>Lorem ipsum howdy</p>
            </div>
        </section>
        <section>
            <div>
                <h2>Section 3</h2>
                <p>Lorem ipsum howdy</p>
            </div>
        </section>
        <section>
            <div>
                <h2>Section 4</h2>
                <p>Lorem ipsum howdy</p>
            </div>
        </section>
        <section>
            <div>
                <h2>Section 5</h2>
                <p>Lorem ipsum howdy</p>
            </div>
        </section>
    </main>
</body>

</html>

```

请注意，子组合符(`>`)用于说明 HTML 元素的层次结构——要知道这根本不是编写 CSS 选择器的最佳方式。

在我们开始之前，让我解释一下每个 [CSS 选择器](https://blog.logrocket.com/level-up-your-css-selector-skills/)的作用。

通用选择器[重置一些东西](https://blog.logrocket.com/what-should-modern-css-boilerplate-look-like/)。例如，边距。它与 CSS 滚动捕捉没有任何关系，所以我们可以忽略这些规则:

```
* { }

```

这是滚动捕捉容器，它和捕捉点本身一样重要(就 CSS 而言):

```
main { }

```

这是五个捕捉点:

```
main > section { }

```

此规则确保每隔一个捕捉点部分有不同的颜色，这样我们就可以很容易地观察到每个捕捉点部分的开始和结束位置。它只存在于本教程中:

```
main > section:nth-child(odd) {
    background: #ddd;
}

```

此规则针对每个捕捉点部分的内容。具体来说，它确保内容在节内居中。这也只是为了本教程的目的:

```
main > section > div {
    margin: auto;
    text-align: center;
}

```

好了，现在让我们深入 CSS 滚动捕捉细节。

首先，必须为滚动捕捉容器指定一个`height`(或者为水平容器指定一个`width`)。幸运的是，这不需要是固定的高度，所以如果您不知道容器有多长(它甚至可能是响应性的)，您可以使用视口单位来代替。我使用了`100vh`，它使容器和视口一样大(垂直流动)。

这确实是一个奇怪的要求，但仍然是一个要求:

```
main {
    height: 100vh;
    max-height: 100vh; /* this will also work */
}

```

奇怪的是，容器也需要`overflow-y: scroll`:

```
main {
    height: 100vh;
    overflow-y: scroll;
}

```

如果你有两个滚动条，那么你可能还需要加入以下内容:

```
html, body {
    overflow: hidden;
}

```

最后，CSS 属性`scroll-snap-type`通过指定滚动捕捉的行为来激活滚动捕捉。它由两部分组成，中间用空格隔开。

第一部分可以是`x`、`y`或`both`——指定我们希望滚动捕捉在哪个轴上起作用。在这个例子中，我们使用了`y`——例如，垂直滚动捕捉。

第二部分可以是`mandatory`或者`proximity`，它决定了用户释放滚动时会发生什么。使用`mandatory`，滚动将总是吸附到最近的吸附点。使用`proximity`，只有当滚动接近捕捉目标时，滚动才会被捕捉，否则不做任何事情。

如果你坚持让用户只捕捉特定的区域，请使用`mandatory`,如果你认为捕捉很好，请使用`proximity`。值得注意的是，这两个选项都对用户的体验产生了负面影响(至少在一开始是这样)，因为他们不太可能期望滚动会突然停止。

当用户进行大胆的轻弹滚动时，滚动捕捉效果最好，这将产生很好的效果，但只有当用户事先知道滚动会以这种方式运行时。出于这个原因，滚动捕捉最适合致力于让滚动捕捉成为一种熟悉的 [UX 模式](https://blog.logrocket.com/ux-design/better-form-design-ux-tips-tools-tutorial/)或者用户定期返回的网站。

简而言之，这是一个有用的效果，但是如果你只是用它来增加一个登陆页面的趣味，最好避免它。滚动捕捉应被视为 UX 模式:

```
main {
    height: 100vh;
    overflow-y: scroll;
    scroll-snap-type: y mandatory;
}

```

值得注意的是，在整个网页只是一个巨大的滚动捕捉体验的场景中，我们可以使用根(`<html>`)作为我们的滚动捕捉容器。在这种情况下，我们不需要设置`height`或`overflow-y`，因为`<html>`已经有了它们。然而，这在`<body>`上不起作用，原因不明。

## 声明捕捉点

在快照目标上声明`height: 100vh;`可以确保它们充分利用所有可用的垂直空间。例如，将每个快照目标视为一个假想产品的单一卖点，必须完整查看才能获得最大效果(如幻灯片)。

但是，拥有大型快照目标会带来两个潜在问题。首先，如前所述，如果用户不执行大胆的 flicky 滚动，他们就有不能捕捉到下一个捕捉目标的风险，在某些情况下，会退回到上一个目标(正如你所想象的，这将是相当令人沮丧的)。记住:如果你不重用这个模式，不利用用户对它的熟悉，滚动捕捉就不值得。

第二个问题是，如果对齐目标使用完整的视区，并且内容大于移动设备上的视区，滚动对齐将阻止内容被访问，因此在使用滚动对齐之前，请确保它补充了内容:

```
main > section {
    height: 100vh;
}

```

`scroll-snap-align`接受各种关键字值(`start`、`end`、`center`)，这些关键字值决定了 web 浏览器应该吸附到吸附目标的哪一侧。例如，在水平对齐目标上声明`scroll-snap-align: start;`将确保 web 浏览器靠左对齐。对于垂直捕捉目标，顶部。

`scroll-snap-align`似乎没有缺省值，因为不提供缺省值会导致滚动捕捉失败，所以尽管您无论如何都希望`start`是缺省值，但我们仍然必须声明它:

```
main > section {
    height: 100vh;
    scroll-snap-align: start;
}

```

`scroll-snap-stop: always;`确保用户不会意外跳过快照目标。使用它没有正确或错误的时间，它只是归结为偏好，所以它可能值得你的用户进行 A/B 测试。请注意，它不适用于`scroll-snap-type: <axis> proximity`:

```
main > section {
    height: 100vh;
    scroll-snap-align: start;
    scroll-snap-stop: always;
}

```

## 设置当前捕捉目标的样式

[网页浏览器对 CSS 滚动捕捉的支持](https://caniuse.com/?search=scroll-snap-type)很高。也就是说，它仍在开发中，现在即将出现的是只使用 CSS 来设计快照目标和/或 kickstart 动画的能力。更确切地说，即将推出的`:snapped` [CSS 伪选择器](https://blog.logrocket.com/css-pseudo-elements-guide/)。

一旦浏览器支持`:snapped`伪选择器(以及类似的选择器，`:snapped-x`和`:snapped-y`)，你就能像这样使用它们了:

```
section:snapped {
    /* CSS declarations */
}

```

就是这么简约直白！自然，样式化对齐目标的能力对于非全屏的对齐目标更有用。看看下面来自 [Chrome DevRel 团队](https://web.dev/state-of-css-2022/#scroll-snap-features-are-too-limited)的演示，它模拟了`:snapped`可能如何在这些场景中工作:[水平](https://snap-gallery.netlify.app/horizontal/target/)，[垂直](https://snap-gallery.netlify.app/vertical/target/)，以及[双轴](https://snap-gallery.netlify.app/2d/target/)。

### 使用 JavaScript 的替代方法

当我们等待 web 浏览器采用`:snapped`时，有一个替代的 JavaScript 解决方案，涉及[交叉点观察器 API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) ，它本质上观察视口，等待某些元素进入其中(在这种情况下，元素是捕捉目标)。当一个 snap 目标出现时，我们可以添加一个添加样式的类。看看下面的例子。

在第一个例子中，`Observer`指向一个特定的捕捉点。它等待它进入视图，然后给它添加一个类——这非常适合于对特定的捕捉点做一些独特的事情，例如触发一个独特的 [CSS 过渡/动画](https://blog.logrocket.com/creating-smoother-css-transitions-animate-css-grid/):

```
let callback = (entries, observer) => {
    entries.forEach(entry => {
        if (!entry.isIntersecting) {
            /* remove class when snap target becomes invisible */
            entry.target.classList.remove("transition");
        } else {
            /* add class when snap target becomes visible */
            entry.target.classList.add("transition");
        }
    });
};

/* only 50% of the element needs to be visible */
let options = {
    threshold: 0.5
}

let observer = new IntersectionObserver(callback, options);

/* do this to #section-2 specifically */
let section2 = document.querySelector("#section-2")

observer.observe(section2);

```

在第二个示例中，`forEach`用于遍历所有快照目标，为每个快照目标添加一个单独的`Observer`,但有一个对所有快照目标具有相同效果:

```
let callback = (entries, obs) => {
    entries.forEach(entry => {
        if (!entry.isIntersecting) {
            entry.target.classList.remove("styles");
        } else {
            entry.target.classList.add("styles");
        }
    });
};

let options = {
    threshold: 0.5
}

let observer = new IntersectionObserver(callback, options);

/* target all section elements */
document.querySelectorAll("section").forEach(section => observer.observe(section));

```

最终，[交叉点观察器 API](https://blog.logrocket.com/lazy-loading-using-the-intersection-observer-api/) 完成了和`:snapped`最终要做的一样的事情，但是它需要 JavaScript 和更多的代码。它还会导致 web 性能的轻微下降。

然而，交叉点观察器 API 的好处是，我们可以用它来做比添加和删除 CSS 更多的事情(例如，我们可以用它来播放和重启视频)。此外，`[threshold](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API#intersection_observer_options)`选项决定了在调用`callback`之前，捕捉目标需要在视口中的位置，这为我们提供了对类何时被切换的更细粒度的控制。

同样值得关注的是`snapChanging()`和`snapChanged()`，这两个 [JavaScript 事件](https://blog.logrocket.com/custom-events-in-javascript-a-complete-guide/)最终应该会得到 web 浏览器的支持。虽然我看不出他们会带来什么交集观察者 API 没有的东西。

以下是它们的使用方法，以供参考:

```
document.querySelector("#section-2").addEventListener("snapchanging", event => {
  /* do something */
});
document.querySelector("#section-2").addEventListener("snapchanged", event => {
  /* do something */
});

```

你怎么想呢?有用，还是没用？如果你有任何想法，请在下面的评论区留言。

## 结束语

如果使用得当，滚动捕捉会很有趣。尽管如此，并没有太多的场景可以增强 UX。在适当的情况下，一旦用户熟悉了滚动捕捉在网站上作为 UX 模式的用法，他们会感觉非常直观。

希望你能更舒服地享受滚动捕捉，因为只使用 CSS 就能非常容易地实现。与此同时，重要的是要谨慎使用它，如果它看起来不仅仅是一种虚荣心。

我建议[关注`:snapped` CSS 伪选择器](https://drafts.csswg.org/css-scroll-snap-2/)，因为一旦网络浏览器支持它，它将减少所需的 JavaScript 数量。

一如既往地感谢您的阅读！

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。