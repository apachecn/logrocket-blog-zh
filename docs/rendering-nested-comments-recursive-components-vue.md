# Vue 递归组件:渲染嵌套注释

> 原文：<https://blog.logrocket.com/rendering-nested-comments-recursive-components-vue/>

大多数现代社交网络都包括一个功能，用户可以通过评论特定的评论来回复评论。如果我们将它形象化，我们的评论数据将如下所示:

```
- Comment A
                - comment a1
                                - comment a12
                - comment a2
- Comment B
- Comment C

```

`Comment A`有子评论`comment a1`和`comment a2`。反过来，`comment a1`有子评论`comment a12`，它也可以有自己的子评论。

有了这个结构，我们就可以拥有一个包含无数层子注释的注释。您可能已经熟悉了这种结构化数据的方法，即所谓的树。例如，想想你电脑上的目录，其中一个文件夹可以有子文件夹等等。

在本文中，我们将探索如何在 Vue 中使用递归组件来管理树状结构的数据。不讨论递归就很难讨论 Vue 递归组件，所以在探索什么是递归组件以及学习如何在 Vue 中创建递归组件之前，让我们先回顾一下基础知识。

## 递归:快速介绍

最简单的形式，递归是我们用来指调用自身的函数的术语。例如，考虑下面的函数:

```
function sum_numbers(arr, n) {
        return sum_numbers(arr, n - 1) + arr[n - 1];
}

```

尽管上面的函数有问题，但它可以被认为是递归的，因为它在自己的主体中引用了自己。然而，这个定义并不是包罗万象的。递归是解决问题的一种方法。它基于这样一个前提:给定一个问题，如果我们知道它的子问题的解，我们就能找到它的解。

例如，上面的`sum_numbers`函数计算给定数组`arr = [1, 2, 3, 4, 5]`中所有数字的总和。在和问题中，如果我们知道五之前所有数字的和，那么我们可以把我们的问题归结为`arr`中数字的和等于最后一个元素和最后一个元素之前所有数字的和。

我们上面定义的`sum_numbers`函数中的表达式`return sum_numbers(arr, n - 1) + arr[n - 1];`正是我们刚刚描述过的。

为了描述在给定输入`[1, 2, 3, 4]`的情况下，我们的函数将如何从头到尾执行，请考虑下面的代码:

```
**sum_numbers([1, 2, 3, 4], 4)
    |
        calls
                |**
**sum_numbers([1, 2, 3], 3) + 4
    |
        calls
                |
sum_numbers([1, 2], 2) + 3
                |
        calls
                |
sum_numbers([1], 1) + 2
                |
        calls
                |
sum_numbers([], 0) + 1 --** WE HAVE A PROBLEM HERE

```

你可能会注意到我们仍然有一个问题；我们的递归函数试图给一个数字添加一个空列表。其实更大的问题是，我们的递归函数会无限地一直调用自己。

为了确保我们的递归函数不会执行到无穷远，我们需要一个基本案例。你可以把基本情况看作是我们希望函数停止调用自己的点。在我们的例子中，`sum_numbers`函数应该停止调用自己，如果它只有一个数字的话。如果数组只剩下一个数，那么就没有什么可以和这个数相乘了，在这种情况下，我们只返回这个数。

有了这些知识，我们现在可以修改我们的函数，得到如下所示的基本情况:

```
// where n is the length of arr
function sum_numbers(arr, n) {
        if(n <= 1){ //Base Case
                return arr[0];
        }else{
                return sum_numbers(arr, n - 1) + arr[n - 1];
        }
}

```

这就是递归的本质，但是它和 Vue 递归组件有什么联系呢？

## Vue 递归组件:概述

记住，Vue 中的组件是可重用的 Vue 实例。大多数时候，当我们在 Vue 中创建一个组件时，只是为了在其他地方重用它。例如，想象一个电子商务网站，你可以在多个页面上展示产品。你可以只在不同的页面上呈现一个`Product Component`,而不是在每个需要的页面上重复`Product Component`的代码。

如果一个 Vue 组件在它自己的模板中引用它自己，它就被认为是递归的。回想一下，我们提到过组件被设计为在其他组件中重用。递归组件在这方面不同于常规组件。除了在其他地方重用之外，递归组件还在它们的模板中引用自己。

为什么一个组件会引用它自己？当您在其他组件中呈现一个组件时，来宾组件是子组件，而在其中呈现该组件的组件是父组件。

在我们之前的`Product Component`示例中，该组件可以将`ProductReview`作为它的子组件。在这种情况下，我们有两个不同的组件用于这些组件所代表的实体是有意义的，因为`Product`和`Reviews`在每个方面都是不同的。

但是，如果我们以 a `Comment`和`Sub-comment`为例，那么故事就变了。这两个组件代表相同的东西。子评论也是评论。因此，因为`Comment`和`Sub-comment`在结构上是相同的，所以我们有两个不同的组件是没有意义的。我们可以只有一个引用自身的`Comment`组件。还是太抽象了？请参见下面的片段:

```
<template>
  <li class="comment">
    <span>{{ comment.comment }}</span>
    <comment v-for="reply in comment.replies" :comment="reply"></comment>
  </li>
</template&gt;

<script>
export default {
  name: "comment",
  props: {
    comment: Object
  }
};
</script>

```

尽管有问题，上面的组件可以被认为是递归的，因为它引用了自身。像递归函数一样，递归组件也必须有一个基本用例，这在上面的代码中是没有的。这里要注意的另一件重要的事情是，对于一个能够引用自身的组件，必须定义`name`选项。在我们这里，我们的是`name: "comment"`。

现在我们已经了解了 Vue 中的递归组件，让我们看看如何使用它们来构建一个嵌套的注释接口。

### 先决条件

*   JavaScript 知识
*   [使用 Vue 的经验](https://blog.logrocket.com/getting-started-vue-composables/)
*   本地安装的 Node.js
*   安装了 Vue 和 Vue CLI
*   您选择的任何 IDE 我将使用 [VS 代码](https://code.visualstudio.com/Download)

## 设置 Vue 开发环境

首先，我们将初始化一个新的 Vue 项目。启动您系统的终端窗口，并导航到您想要在其中创建 Vue 项目的任何目录。在您的终端中运行命令`vue create nested-comments`。`nested-comments`是我们项目的名称。

然后会提示您添加配置。继续选择默认值。完成后，将为您生成一个具有下图所示结构的项目:

![Vue Nested Comments Folder Structure](img/7608c845648ffbb6b1768256eefd463a.png)

在项目的根目录下运行`vue serve`命令来启动 Vue 的开发服务器并进行测试。

要将我们的嵌套注释呈现给 DOM，首先，删除`src/views`和`src/components`中的所有文件。然后，创造`src/components/Comment.vue`。`Comment.vue`组件将包含我们的递归组件代码。

将下面的代码复制并粘贴到您的`Comment.vue`组件中:

```
<script>
        export default {
          name: "recursive-comment",
          props: {
            comment: {
              type: String,
              required: true,
            },
            replies: {
              type: Array,
              default: () => [],
            },
          },
        };
</script>

```

在上面的代码片段中，我们将组件命名为`recursive-component`。记住，在 Vue 中，递归组件必须声明一个`name`。此外，我们的组件期望道具`comment`和`replies`在它被引用的任何地方被传递给它。

现在我们已经设置了组件的`script`部分，将下面的代码片段复制并粘贴到组件中脚本部分的正上方:

```
&lt;template>
  <li>
    <span class="comment">{{ comment }}</span>

    <ul class="replies" v-if="replies.length">
      <div v-for="(item, index) in replies" :key="index">
        <recursive-comment
          v-bind="{
            comment: item.comment,
            replies: item.replies,
          }"
        />
      </div>
    </ul>
  </li>
</template>

```

上面的代码片段设置了组件的`template`部分。`recursive-comment`组件在自己的模板中引用自己。从根本上说，这使得我们的`Comment component`是递归的。`v-if="replies.length"`是我们的基础案例。一旦条件评估为`false`，递归调用将终止。

接下来，我们只需要在`App.vue`中呈现我们的组件。为此，用下面的代码片段覆盖项目中的`src/App.vue`内容:

```
<template>
  <ul v-for="(item, index) in comments" :key="index" class="comments">
    <Comment
      v-bind="{
        comment: item.comment,
        replies: item.replies,
      }"
    />
  </ul>
</template>

<script>
import Comment from "@/components/Comment";

export default {
  data: () => ({
    comments: [
      {
        comment: "First comment",
        replies: [
          {
            comment: "sub-comment 1 for comment 1",
            replies: [
              {
                comment: "sub-sub-comment 1",
                replies: [
                  {
                    comment: "sub-sub-sub-comment 1",
                  },
                  { comment: "sub-sub-sub-comment 2" },
                ],
              },
              { comment: "sub-sub-comment 2" },
            ],
          },
          { comment: "sub-comment 2 for comment 1" },
        ],
      },

      {
        comment: "Second comment",
        replies: [
          {
            comment: "sub-comment 1 for comment 2",
            replies: [
              { comment: "sub-sub-comment 1" },
              { comment: "sub-sub-comment 2" },
            ],
          },
          { comment: "sub-comment 2 for comment 2" },
        ],
      },
    ],
  }),

  components: {
    Comment,
  },
};
</script>

<style>
.comments ul {
  padding-left: 16px;
  margin: 6px 0;
}
</style>

```

在上面的代码片段中，我们用一些虚拟数据初始化了注释列表，但是理想情况下，您应该从数据库中提取这些数据。然后，我们在`App.vue`模板中呈现我们的`Comment.vue`，将每个组件作为`prop`传递给它。

保存您的更改并使用`vue serve`运行您的服务器。最后，把你的浏览器指向`[http://localhost:8080](http://localhost:8080)`。您应该会看到下面的界面:

![Nested Comments Dummy Data Display](img/d4f147328024cfb1e6487759168e0989.png)

## 结论

虽然我们的例子不是典型的注释部分，但我们的目标是探索如何利用 Vue 中递归组件的能力将嵌套数据(如注释)呈现给 DOM。

我们看到了如何通过创建一个在自己的模板中引用自己的组件来做到这一点。当呈现看似不同但结构相同的数据实体时，这种递归方法特别有用。例如，以我们的评论和回复为例。

乍一看，似乎我们需要两个组件，一个用于评论，另一个用于回复。但是，使用递归方法，我们可以用一个组件同时呈现两者。最重要的是，我们的组件将呈现所有的评论和回复，直到它命中基本案例。我希望你喜欢这篇文章，如果你有任何问题，一定要留下评论。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。