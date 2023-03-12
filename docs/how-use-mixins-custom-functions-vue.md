# 如何在 Vue 中使用 mixins 和自定义函数

> 原文：<https://blog.logrocket.com/how-use-mixins-custom-functions-vue/>

***编者按:**这篇博客文章更新于 2022 年 8 月 7 日，包含了为什么你应该在 Vue 中使用 mixins 以及这样做的缺点。*

如果你是一个 Vue 爱好者(像我一样)，并且正在寻找一种方法来扩展你的 Vue 应用程序，你来对地方了。Vue mixins 和指令是一个强大的组合，是在应用程序的不同部分添加更多可重用功能的好方法。

如果你来自面向对象的编程背景，你会看到 Vue mixins 是对父类的模仿。您还将看到指令类似于帮助函数。

如果你没有 OOP 背景，那么可以把 mixins 看作是一个可以被多人共享的工具。如果你在考虑一个办公室，那应该是复印机。如果你正在考虑一个购物中心，它将是购物中心的安全。基本上，mixins 是应用程序的多个部分共享的资源。

## 先决条件

下面是您在继续本文之前需要的一些先决条件。

1.  JavaScript 知识
2.  至少，您已经构建了一个 Vue 应用程序。一个有五个以上的组件是一个加号
3.  如果你共用办公室的复印机，你可以坐在这里的前面

## 什么是 Vue mixins？

Vue 文档对混音是什么以及它们如何工作有一个简单明了的解释。根据文档，mixins 是一种为 Vue 组件分发可重用功能的灵活方式。mixin 对象可以包含任何组件选项。当一个组件使用 mixin 时，mixin 中的所有选项都将“混合”到组件自己的选项中。

更简单地说，这意味着我们可以用它的数据、方法和生命周期组件创建一个组件，并让其他组件扩展它。现在，这不同于在其他组件中使用组件，在其他组件中，您可以在模板中使用名为`<vue-custom></vue-custom>`的自定义组件。

例如，我们可以构建一个普通的 Vue 组件来保存应用程序的基本配置，例如:

*   app name greater 方法
*   页脚的版权公司名称
*   为什么要在 Vue 中使用 mixins？

## Mixins 允许我们重用 Vue 组件中的功能和逻辑。这意味着我们可以在多个组件中使用相同的逻辑或功能，而不必手动重写每个组件中的逻辑。

这为我们提供了一定程度的灵活性，并允许我们减少代码重复，确保我们遵守流行的 [DRY](https://www.digitalocean.com/community/tutorials/what-is-dry-development) (不要重复自己)原则。

另一件使混合重要的事情是，它们在定义的范围之外没有任何效果。

在 Vue 中使用 mixins

## 让我们创建一个简单的 mixin:

有趣的是，我们可以用 mixins 重构这个组件中的逻辑。当您需要在多个组件中重复这种精确的逻辑时，这很方便。

```
<template>
  <div>
    <div>{{title}}</div>
    <div>{{copyright}}</div>
  </div>
</template>
<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      title: 'Mixins are cool',
      copyright: 'All rights reserved. Product of super awesome people'
    };
  },
  created: function() {
    this.greetings();
  },
  methods: {
    greetings: function() {
      console.log('Howdy my good fellow!');
    }
  }
};
</script>

```

让我们在项目的`myMixin.js`文件中创建一个简单的 mixin:

好吧，这对于混音来说再简单不过了。现在，如果我们在组件中使用它，您将会看到它的神奇之处。

```
export const myMixin = {
  data() {
    return {
      title: 'Mixins are cool',
      copyright: 'All rights reserved. Product of super awesome people'
    };
  },
  created: function() {
    this.greetings();
  },
  methods: {
    greetings: function() {
      console.log('Howdy my good fellow!');
    }
  }
};

```

为了使用它，我们可以导入它，并在模板中执行以下操作:

全球与本地混合

```
<template>
  <div>
    <div>{{title}}</div>
    <div>{{copyright}}</div>
  </div>
</template>
<script>
import myMixin from "/myMixin";
export default {
  name: "HelloWorld",
  mixins: [myMixin]
};
</script>

```

## 需要注意的一点是，有两种类型的混合——全局混合和局部混合。

本地混合是我们在上面用`myMixin.js`文件解释过的。mixin 被定义在一个单独的`.js`文件中，并被导入到我们的 Vue 项目的单独组件中使用。

另一方面，全球混合允许我们做更多的事情。与本地 mixins 类似，我们也有自己的`myMixin.js`文件。这一次，我们将它直接导入到我们的`main.js`文件中，使它自动对我们项目中的所有组件全局可用。

例如，一旦我们创建了`myMixin.js`文件，我们就转到`main.js`文件并导入它，如下所示:

现在，我们的 Vue 组件中的任何组件都可以访问 mixin 文件中的功能，而不需要单独导入。

```
import { createApp } from 'vue'
import App from './App.vue'
import myMixin from './myMixin'

const app = createApp(App);
app.mixin(GlobalMixin);

app.mount('#app')   

```

Vue 中的指令

## 指令是像`v-for`这样的方法，我们可以创建它们来修改模板上的元素。你知道如果不满足条件，`v-if`是如何隐藏组件的吗？如果我们用一个指令在一个长句下面划线怎么样？

我们甚至可以稍微改变一下文字来突出它。我们可以注册全局指令，以便我们的 Vue 应用程序中的所有组件都可以使用它们。我们还有特定于该特定组件的本地指令。太棒了，对吧？

现在让我们在`main.js`中创建一个全局指令。

注册一个名为`v-highlight`的全局自定义指令:

这里，我们要改变附加到这个指令的元素的样式。另外，如果有一种颜色作为`value`附加到指令上，我们将它设置为背景色。如果没有，我们设置背景色为`blue`。

```
// It's app.directive in Vue 3
    Vue.directive('highlight', {
      // When the bound element is inserted into the DOM...
      // Use "mounted" instead of "inserted" in Vue 3
      inserted: function(el, binding) {
        // set the colour we added to the bind
        el.style.backgroundColor = binding.value ? binding.value : 'blue';
        el.style.fontStyle = 'italic';
        el.style.fontSize = '24px';
      }
    });

```

更多来自 LogRocket 的精彩文章:

* * *

### 现在，如果我们使用这个指令，您应该会看到文本的某些部分发生了变化。

* * *

要使用它，我们可以在模板中执行以下操作:

Vue 中的过滤器

```
  <template>
      <div>
        <p v-highlight>Hello There!</p>
        <p v-highlight="red">This is a red guy</p>
      </div>
    </template>

```

## 这是我们要看的另一个定制助手。过滤器在很多方面帮助了我们(如果这是你第一次遇到它们，你可能会因为没有早点知道而生气)。我们可以全局或局部定义过滤器，就像指令一样。

过滤器可用于对文本应用通用格式，或对数组或对象进行大量过滤。它们是 JavaScript 函数，所以我们可以将它们定义为接受尽可能多的参数。此外，我们可以将它们链接起来，并使用多个过滤器。很酷，对吧？

让我们定义一个简单的过滤器，将正文的第一个单词大写(这在显示用户提供的姓名时非常有用):

为此，我们可以在模板中执行以下操作:

```
Vue.filter('capitalize', function(value) {
  if (!value) return '';
  value = value.toString();
  return value.charAt(0).toUpperCase() + value.slice(1);
});

```

现在，无论我们在哪里使用这个过滤器，第一个字符总是大写。

**注意**，过滤器在 Vue 2 中仍然适用，但在 Vue 3 中已被弃用。

> 将它整合在一起

## 我们将使用我们所学的一切来构建一个简单的 Vue 应用程序。

首先，让我们定义我们的 mixin:

然后我们在`main.js`中定义我们的指令:

```
const myMixin = {
  data() {
    return {
      title: 'mixins are cool'
    };
  },
  created: function() {
    alert('Howdy my good fellow!');
  }
};

```

现在，让我们在`main.js`中定义我们的过滤器:

```
Vue.directive('highlight', {
  inserted: function (el, binding) {
    el.style.color = binding.value ? binding.value : "blue"
    el.style.fontStyle = 'italic'
    el.style.fontSize = '24px'
  }
})

```

最后，用简单的模板来看看这些东西是否真的有用:

```
Vue.filter('capitalize', function (value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})

```

就是这样！

```
<template>
  <div id="app">
    <p v-highlight>{{title | capitalize}}</p>
    <p v-highlight="'red'">This is a red guy</p>
    <p>{{'this is a plain small guy' | capitalize}}</p>
  <div>
</template>
<script>
  export default {
  name: "HelloWorld",
  mixins: [myMixin]
};
</script>

```

混血儿的艰辛

## Vue 3 现在提供了其他共享功能的方式，提供了更好的开发者体验，比如使用 [composables](https://vuejs.org/guide/reusability/composables.html#mouse-tracker-example) 。

在使用 Options API 时，Mixins 仍然很有效，但是您可能会遇到一些缺点，包括:

命名冲突

### 在使用 mixins 时，组件中出现冲突名称的可能性更大。这可能是一个挑战，尤其是在新开发人员继承遗留代码库，而他们并不完全熟悉 mixin 中现有的属性名的情况下。

这可能会导致我们的 Vue 应用程序出现不必要的行为。

难以理解和调试

### 类似于我在上面强调的潜在命名问题，对于一个新开发人员来说，弄清楚混合以及它们如何影响组件是有点压力的，特别是如果他们正在处理全局混合的话。

一般来说，弄清楚组件中的所有功能可能很困难。

结论

## 当构建复杂程度可能会增加的应用程序时，我们在这里提到的一切都会派上用场。您希望定义许多可重用的函数，或者以一种可以跨组件重用的方式对它们进行格式化，这样您就不必一次又一次地定义相同的东西。

最重要的是，你想有一个单一的真相来源。奉献一点时间去改变。

想到你现在可以用这些功能构建很酷的东西，我就很兴奋。请与我分享它们。

像用户一样体验您的 Vue 应用

## 调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。

Modernize how you debug your Vue apps - [Start monitoring for free](https://lp.logrocket.com/blg/vue-signup).