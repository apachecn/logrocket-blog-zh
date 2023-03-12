# JavaScript 和 Vue 动画

> 原文：<https://blog.logrocket.com/javascript-and-vue-animations/>

# JavaScript 和 Vue 动画

## 2020 年 7 月 27 日 3 min 读 981

在[之前的一篇文章](https://blog.logrocket.com/understanding-transitions-and-animations-in-vue/)中，我们介绍了 Vue 的过渡和动画，以及如何使用只带有 CSS 和`transition`标签的过渡类来创建效果。有趣的是，通过单独使用 JavaScript 或将其与 CSS 结合使用，您可以做更多的事情。

标签附带了一个叫做事件挂钩的东西，它允许 JavaScript 影响转换。事件挂钩影响元素，就像 CSS 中的转换类一样。这样，在动画或过渡发生的每个阶段，您都可以在 DOM 中添加或删除 HTML 元素时运行 JavaScript 代码。

这些钩子使您能够在动画开始之前、动画进行过程中以及动画完成之后立即运行 JS 代码。这个额外的功能可以让您对 UI 有更多的控制和灵活性，促进交互，并改善用户体验。

`transition`标签为 JavaScript 提供了默认的事件钩子，这些钩子将它们自己附加到方法上，这些方法有实际的代码。

`@before-enter`允许 JavaScript 代码在附加到 DOM 之前运行一帧

```
<transition
  @before-enter="beforeEnter"
  @enter="enter"
  @after-enter="afterEnter"
  @enter-cancelled="enterCancelled"

  @before-leave="beforeLeave"
  @leave="leave"
  @after-leave="afterLeave"
  @leave-cancelled="leaveCancelled"
> 
  [...]
</transition>

```

*   发出附加到 DOM 时运行的代码
*   发出附加到 DOM 后运行的代码
*   发出清除动画并中止播放的代码(你也可以有其他类型的代码)
*   允许 JavaScript 代码在从 DOM 中移除之前运行一帧
*   发出从 DOM 中删除时运行的代码
*   `@after-leave`发出从 DOM 中删除后运行的代码
*   `@leave-cancelled`发出动画被取消时运行的代码
*   让我们看一个例子。

上面的例子提供了一个如何结合 JavaScript 和 CSS 中的事件挂钩来创建动画的用例。在这种情况下，我们在 CSS 中使用`@keyframes`来创建从 DOM 中附加和移除的样式。然后，我们为事件挂钩创建了方法，在每个用 JavaScript 附加/移除的阶段，我们向控制台添加了注释，向元素添加了样式。

```
<template>
  <div>
    <div class="container">
    <button @click="display = !display">Switch</button>
    <transition
      @before-enter="beforeEnter"
      @enter="enter"
      @after-enter="afterEnter"
      @enter-cancelled="enterCancelled"

      @before-leave="beforeLeave"
      @leave="leave"
      @after-leave="afterLeave"
      @leave-cancelled="leaveCancelled">
    <div class="item" v-if="display">1</div>
    </transition>
  </div>
  </div>
</template>
<style scoped>
  body {
    align-content: center;
  }
    .container {
        display: grid;
        grid-gap: 20px;
        width: 500px;
        margin: 0 auto;
      }
    .item {
      background-color: blue;
      height: 100px;
    }
    .fadeInClass {
      animation : fadeIn 1s;  
    }
    .fadeOutClass {
      animation : fadeOut 1s;  
    }
    @keyframes fadeIn {
      0% {
        opacity : 0
      }
      100% {
        opacity : 1;
      }
    }

    @keyframes fadeOut {
      0% {
        opacity : 1;
      }
      100% {
        opacity : 0;
      }
    }

</style>

<script>
export default {
  data () {
    return {
      display : false
    }
  },
  methods : {
    beforeEnter () {
      console.log("about to")
    },
    enter (el, done) {
       el.classList.add('fadeInClass');
      console.log("enter")
       done();
    },
    afterEnter (el) {
      el.addEventListener('animationend', () =>{el.classList.remove('fadeInClass'); } )
      console.log("after enter")
    },
    enterCancelled ()  {
     console.log("enter cancelled")
    },
    beforeLeave () {
      console.log("beforeLeave")
    },
    leave (el, done) {
      el.classList.add('fadeOutClass')
      console.log("leave")
       done();
    },
    afterLeave () {
      console.log("after-leave");
    },
     leaveCancelled ()  {
     console.log("leave cancelled")
    }
  }
}
</script>

```

注意:我们只在`enter`和`leave`中添加了`done()`，因为它有助于 Vue 中的元素知道 JavaScript 何时完成过渡/动画，因为它不像 CSS 那样定时。

有趣的是，您也可以单独使用任何事件挂钩来运行 JavaScript 代码，因此您不需要将所有挂钩都附加到转换标签上就可以工作。你只能用你需要的东西。

例如，您可以简单地将`@after-leave`添加到已经使用 CSS 和 transition 类的 transition 标签中，在动画从 DOM 中移除后，它将运行您想要的任何代码。

这里有一个例子:

`@after-leave`被附加到 transition 标签上，该标签已经在使用 transition 类进行操作。`@after-leave`钩子在元素从 DOM 中移除后生效。然后它运行`afterLeave()`函数并显示关于随机数的声明。这可以用我们之前讨论过的所有其他事件挂钩来重现。

```
<template>
  <div>
    <div class="container">
    <button @click="display = !display">Switch</button>
    <transition  @after-leave="afterLeave" name="block">
    <div class="item" v-if="display" >1</div>
    </transition>
    <div id="comment"></div>
  </div>
  </div>
</template>
<style scoped>
  body {
    align-content: center;
  }
    .container {
        display: grid;
        grid-gap: 20px;
        width: 500px;
        margin: 0 auto;
      }
    .item {
      background-color: blue;
      height: 100px;
    }
    .block-enter {
    }
    .block-enter-active {
      animation : fadeIn 1s;
    }
    .block-leave {
    }
    .block-leave-active {
       animation : fadeOut 1s 
    }
      @keyframes fadeIn {
      0% {
        opacity : 0
      }
      100% {
        opacity : 1;
      }
    }

    @keyframes fadeOut {
      0% {
        opacity : 1;
      }
      100% {
        opacity : 0;
      }
    }
</style>

<script>
export default {
  data () {
    return {
      display : false
    }
  },
  methods : {

    afterLeave () {
      document.getElementById('comment').innerHTML = `The random number is ${(Math.random())}`
    }
  }
}
</script>

```

如果你使用的 JavaScript 钩子没有任何 CSS，你可以添加`:css="false"`到你的转换标签中。这告诉转换标签不要监听任何 CSS，因为默认情况下它通常会监听。

结论

```
<transition
      @before-enter="beforeEnter"
      @enter="enter"
      @after-enter="afterEnter"
      @enter-cancelled="enterCancelled"

      @before-leave="beforeLeave"
      @leave="leave"
      @after-leave="afterLeave"
      @leave-cancelled="leaveCancelled" 
      :css="false">
    <div class="item" v-if="display">1</div>
    </transition>

```

## 使用 JavaScript，您可以完全通过 DOM 控制元素，这取决于您希望在项目中实现什么。您可以随时在动画的任何地方使用转换挂钩来安装 JavaScript，这为您提供了精确操作元素的灵活性，并为您的用户创建更好、更具伸缩性的应用程序。

要了解更多，请查看这个[速成班](https://www.youtube.com/watch?v=r1zF9aXQdLU&feature=youtu.be)。

像用户一样体验您的 Vue 应用

## 调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。

Modernize how you debug your Vue apps - [Start monitoring for free](https://lp.logrocket.com/blg/vue-signup).