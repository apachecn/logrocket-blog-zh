# 如何用 Vue 实现无限滚动

> 原文：<https://blog.logrocket.com/implement-infinite-scroll-vue/>

通过无限滚动，当用户向下滚动页面时，网页加载新内容，从而允许用户[查看新内容，而不必点击新页面](https://blog.logrocket.com/ux-design/pagination-vs-infinite-scroll-ux/)或刷新页面。当您希望在用户需要时加载大量数据或图像，而不是一次全部加载时，此功能特别有用。

多年来，Twitter、脸书和 Instagram 等社交网站普及了无限卷轴。在本教程中，我们将使用 [Vue 实现无限滚动，这是一个用于构建用户界面和单页应用程序的 JavaScript 框架](https://blog.logrocket.com/how-to-use-vue-3-typescript/)。我们开始吧！

*向前跳转*

## Vue 入门

Vue 是一个带有虚拟 DOM 的轻量级框架；它提供了一种以声明方式向 DOM 呈现数据的方法，并使用双向数据绑定来在数据发生变化时保持 DOM 最新。

我真正欣赏 [Vue 3 的是代码](https://blog.logrocket.com/definitive-guide-vue-3-components/)的紧凑性。包括脚本、HTML 和样式在内的所有东西都在一个文件中有自己的位置。这种解决方案对于像我们这样最简单的项目来说是理想的，并且是开始摆弄一个想法的一个很好的选择。

## 项目描述

在我们的应用程序中，我们将实现一些可以在其他项目中派上用场的特性。我们将从随机在线数据生成器下载数据。对于这个项目，我们将使用[http://random-data-api.com](http://random-data-api.com/)，它提供了一个有用的 API 和真实的数据，从而让我们看到我们的应用程序在现实世界中是如何工作的。

对于每一批新数据，当新数据被下载到浏览器中时，会出现一条短消息。这始终是一个好的实践，尤其是考虑到 API 的响应速度会有多慢。因此，用户会意识到在引擎盖下发生了一些事情。

对于显示数据的网格，我们将使用砖石 CSS 布局。在这个项目中，我们将使用我能找到的最简单的 CSS 布局之一。您可以轻松地将它用于其他框架，而无需太多的修改。

下图显示了最终结果:

![Vue Infinite Scroll App Demo](img/151c9568ab4e011d3bd222d8dbf73769.png)

项目思路相当简单；在页面的第一印象中，第一批数据被下载到浏览器中并被可视化。当用户滚动可视化数据的组件时，点击页面底部将加载新的一批数据。在当前可视化结束时，新数据将被推送到 UI，并且将有一个新的空间来容纳它。

接下来，您可以在 GitHub 上找到这个项目[的完整代码库。在下一节中，我们将详细讨论代码中最有趣的部分。](https://github.com/rosdec/ethernal_scroll)

## `masonry`组件

我们的目标是检测用户何时到达包含“无限”数据的组件底部。在源代码中，这个[组件被称为`masonry`](https://blog.logrocket.com/css-grid-guide/) 。我们可以通过操作以下三个属性来实现这一点:

*   `masonry.clientHeight`:整个`masonry`元素高度的像素数
*   项目列表中我们已经滚动过的像素数
*   `window.scrollHeight`:项目列表中当前包含的像素数

以下代码实现了这一想法:

```
  mounted() {
    const masonry = document.querySelector('#infinite-list');
    document.title = "Ethrnal Scroll Demo";
    masonry.addEventListener('scroll', e =&amp;gt; {
      if (masonry.scrollTop + masonry.clientHeight &amp;gt;= masonry.scrollHeight) {
        this.getNextBatch(30);
      }
    })
  },
};

```

在下图中，您可以看到这三个变量是如何相互作用的。当`scrollTop`加上`clientHeight`大于`scrollHeight`时，那么我们已经滚动到列表的末尾，我们需要另一批项目:

![Scrolltop Clientheight Scrollheight Demo Diagram](img/b78470ddad101c04d103c56490bc1043.png)

在下图中，您可以看到当我们滚动到内容末尾时，值是如何变化的:

![Values Change Reach List Bottom](img/d733b6cba7a398f3abf40fc379f492d3.png)

一旦我们清除了检查我们是否到达列表底部的机制，我们必须在代码中找到一个好的地方来添加这个检查。一个自然的地方是`scroll`事件。因此，每次我们来回滚动时，`masonry`组件将能够控制我们是否到达列表的底部。

另一件值得注意的事情是，我们在 Vue 提供的`mounted()`方法中为 scroll 事件分配了监听器，允许我们在组件挂载后执行代码。

在讨论更多滚动之前，让我们看一下我们的应用程序将处理的数据:

```
  data() {
    return {
      loading: false,
      items: [],
      page_count: 0,
      sizes: ["tall", "taller", "tallest"]
    };
  },

```

在`data()`中，我们有组件的状态。让我们仔细看看:

*   `loading`:一个布尔变量，当我们想要显示`Loading next batch`标签时，它将是`true`。参见`App.vue`文件中的第 14 行
*   `items[]`:包含显示数据的对象数组
*   `page_count`:我们从随机来源下载的批次数量
*   `sizes[]`:一个数组，包含我们上面描述的瓷砖的三种样式的标签

```
grid-container {
      display: grid;
      /* 1 /
      grid-auto-rows: 50px;
      / 2 /
      grid-gap: 10px;
      / 3 /
      grid-template-columns: repeat(auto-fill, minmax(30%, 1fr));
      / 4 */
      overflow: auto;
      height: 30vh;
      border: 2px solid;
      border-radius: 5px;
      text-align: center;
      margin: 0 0 35px 0;
    }
    .tall {
      grid-row: span 2;
      background-color: #7632ed;
    }
    .taller {
      grid-row: span 3;
      background-color: #925cef;
    }
    .tallest {
      grid-row: span 4;
      background-color: #b9a3de;
    }
```

上面的代码显示了在`App.vue`的`<style>`部分中定义的 CSS 的细节，在这里我们定义了`masonry`组件的不同瓦片。选择器、`tall`、`taller`和`tallest`被随机分配给下载的数据:

```
getNextBatch(size) {
  this.loading = true;
  axios.get("https://random-data-api.com/api/v2/users?size=" + size + "&amp;amp;response_type=json").then((response) =&amp;gt; {
    response.data.map((x) =&amp;gt; {
      const new_item = {
        name: x.first_name + " " + x.last_name,
        dob: x.date_of_birth,
        location: x.address.state,
        size: this.sizes[Math.floor(Math.random() * this.sizes.length)]
      };
      this.items.push(new_item)
    })
    this.loading = false;
    this.page_count++;
  });
}

```

我们在上面显示的 Vue 应用程序的唯一方法`getNextBatch`中实现了从随机源加载一批数据。它用`axios.get()`从随机数据源 API 下载一批新的物品，指定我们想要的物品数量。一旦数据通过，它被操作来提取一些字段并组装一个对象，该对象将被追加到`items[]`数组中。

`getNextBatch()`也做两件简单的事情。首先，它更新了页面的数量，或者我们已经下载的批次的数量，并且从`App.vue`文件中显示和隐藏了`Loading next batch`标签。

请注意我们如何重复使用代码。我们还在页面加载后运行的`beforeMount()`方法中使用了`getNextBatch()`，但是还没有显示任何组件。这个调用的作用是用一些要在`masonry`组件中可视化的数据填充`items[]`数组。

## 结论

在本文中，我们探索了在用 Vue 编写的网页上实现无限滚动的简化解决方案。

为了便于在其他项目中重用，我们在专用的`<div>`中实现了无限滚动功能。我们无限卷轴的另外两个部分在代码中被很好地分开了，包括下载一批新数据的函数和我们调用这个函数的地方。这将有助于开发人员用更有用的东西替换`getNextBatch()`函数中 API 的 URL。

Vue3 对于编写一个小的概念证明特别有效，就像这个例子一样。但是，您也可以通过组合组件来实现更复杂的功能。我希望你喜欢这篇文章，并

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。