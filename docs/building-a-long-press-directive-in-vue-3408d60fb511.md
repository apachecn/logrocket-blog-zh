# 在 Vue 中构建一个长按指令

> 原文：<https://blog.logrocket.com/building-a-long-press-directive-in-vue-3408d60fb511/>

你是否曾经想通过按住一个按钮几秒钟来执行你的 Vue 应用程序中的一个功能？

你有没有想过在你的应用程序中创建一个按钮来帮助清除单个输入(或者按住一个按钮清除整个输入)？

你有吗？很好。我也是。你来对地方了。

本文将解释如何通过按下(或按住)按钮来执行功能和移除输入。

首先，我将解释如何在 VanillaJS 中实现这一点。然后，为它创建一个 Vue 指令。

系好安全带。我要让你大吃一惊了。

## 理论

为了实现长按，用户需要按住按钮几秒钟。

为了在代码中复制这一点，我们需要监听鼠标“点击”按钮被按下的时间，启动一个计时器，不管我们希望用户在执行功能之前按住按钮多长时间，并在设定的时间过去后执行功能。

相当简单！然而，我们需要知道用户何时按下按钮。

## 操作方法

当用户点击一个按钮时，在点击事件之前会触发另外两个事件:`mousedown`和`mouseup`。

当用户按下鼠标按钮时会调用`mousedown`事件，而当用户释放按钮时会调用`mouseup`事件。

我们需要做的就是:

1.  一旦`mousedown`事件发生，启动定时器
2.  一旦在 2 秒标记之前触发了`mouseup`事件，即一个完整的点击事件，清除该定时器并且不执行该功能

只要计时器在到达我们设置的时间之前没有被清零——即`mouseup`事件没有被触发——我们就可以说用户没有释放按钮。因此，它被认为是一个长按，然后我们可以继续执行上述功能。

## 实际的

让我们深入研究代码，完成这项工作。

首先，我们必须定义三件事，即:

1.  存储计时器的变量
2.  启动定时器的启动功能
3.  取消定时器的取消功能

### 可变的

这个变量基本上保存了`setTimeout`的值，所以当`mouseup`事件发生时我们可以取消它。

我们将变量设置为`null`,这样我们就可以在取消它之前检查变量以了解当前是否有一个活动的计时器。

### 启动功能

这个函数由一个 [`setTimeout`](https://www.w3schools.com/jsref/met_win_settimeout.asp) 组成，它基本上是 Javascript 中的一个方法，允许我们在函数中指定的特定持续时间后执行函数。

记住，在创建点击事件的过程中，有两个事件被触发。但是我们需要启动计时器的是`mousedown`事件。因此，如果是点击事件，我们不需要启动计时器。

### 取消功能

这个函数基本上如其名所言，取消调用 start 函数时创建的`setTimeout`。

为了取消`setTimeout`，我们将使用 JavaScript 中的`[clearTimeout](https://www.w3schools.com/jsref/met_win_cleartimeout.asp)`方法，它基本上清除了用`[setTimeout()](https://www.w3schools.com/jsref/met_win_settimeout.asp)`方法设置的计时器。

在使用`clearTimeout`之前，我们首先需要检查`pressTimer`变量是否被设置为空。如果它没有被设置为空，这意味着有一个活动的计时器。所以，我们需要清除计时器，并且，你猜对了，将`pressTimer`变量设置为`null`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

一旦触发了`mouseup`事件，这个函数就会被调用。

### 设置触发器

剩下的就是给你想要添加长按效果的按钮添加事件监听器。

```
addEventListener("mousedown", start);
addEventListener("click", cancel);
```

### 用 Vue 指令来包装这一切

当创建一个 Vue 指令时，Vue 允许我们定义一个全局的或者局部的指令给一个组件，但是在这篇文章中我们将走全局路线。

让我们构建实现这一点的指令。

首先，我们必须声明自定义指令的名称。

这基本上注册了一个名为`v-longpress`的全局定制指令。

接下来，我们添加带有一些参数的`bind` [钩子函数](https://vuejs.org/v2/guide/custom-directive.html#Hook-Functions)，它允许我们引用指令绑定到的元素，获取传递给指令的值，并识别指令使用的组件。

```
Vue.directive('longpress', {
  bind: function (el, binding, vNode) {

  }
}
```

接下来，我们在 bind 函数中添加长按 JavaScript 代码。

```
Vue.directive('longpress', {
    bind: function (el, binding, vNode) {

        // Define variable
        let pressTimer = null

        // Define funtion handlers
        // Create timeout ( run function after 1s )
        let start = (e) => {

            if (e.type === 'click' && e.button !== 0) {
                return;
            }

            if (pressTimer === null) {
                pressTimer = setTimeout(() => {
                    // Execute something !!!
                }, 1000)
            }
        }

        // Cancel Timeout
        let cancel = (e) => {
            // Check if timer has a value or not
            if (pressTimer !== null) {
                clearTimeout(pressTimer)
                pressTimer = null
            }
        }

        // Add Event listeners
        el.addEventListener("mousedown", start);
        // Cancel timeouts if this events happen
        el.addEventListener("click", cancel);
        el.addEventListener("mouseout", cancel);
    }
})
```

接下来，我们需要添加一个函数来运行将被传递给`longpress`指令的方法。

```
Vue.directive('longpress', {
    bind: function (el, binding, vNode) {

        // Define variable
        let pressTimer = null

        // Define funtion handlers
        // Create timeout ( run function after 1s )
        let start = (e) => {

            if (e.type === 'click' && e.button !== 0) {
                return;
            }

            if (pressTimer === null) {
                pressTimer = setTimeout(() => {
                    // Execute function
                    handler()
                }, 1000)
            }
        }

        // Cancel Timeout
        let cancel = (e) => {
            // Check if timer has a value or not
            if (pressTimer !== null) {
                clearTimeout(pressTimer)
                pressTimer = null
            }
        }
        // Run Function
        const handler = (e) => {
            // Execute method that is passed to the directive
            binding.value(e)
        }

        // Add Event listeners
        el.addEventListener("mousedown", start);

        // Cancel timeouts if this events happen
        el.addEventListener("click", cancel);
        el.addEventListener("mouseout", cancel);

    }
})
```

现在，我们可以在我们的 Vue 应用程序中使用该指令，它将正常工作，直到用户在指令值中添加一个不是函数的值。因此，一旦发生这种情况，我们必须通过警告用户来防止这种情况。

为了警告用户，我们向 bind 函数添加了以下内容:

```
// Make sure expression provided is a function
if (typeof binding.value !== 'function') {
  // Fetch name of component
  const compName = vNode.context.name
  // pass warning to console
  let warn = `[longpress:] provided expression '${binding.expression}' is not a function, but has to be`
  if (compName) { warn += `Found in component '${compName}' ` }
  console.warn(warn)
}
```

最后，如果这项指令也适用于触摸设备，那就太好了。所以我们为`[touchstart](https://developer.mozilla.org/en-US/docs/Web/Events/touchstart)`、`[touchend](https://developer.mozilla.org/en-US/docs/Web/Events/touchend)`、`[touchcancel](https://developer.mozilla.org/en-US/docs/Web/Events/touchcancel)`添加事件监听器。

将所有东西放在一起:

```
Vue.directive('longpress', {
    bind: function (el, binding, vNode) {
        // Make sure expression provided is a function
        if (typeof binding.value !== 'function') {
            // Fetch name of component
            const compName = vNode.context.name
            // pass warning to console
            let warn = `[longpress:] provided expression '${binding.expression}' is not a function, but has to be`
            if (compName) { warn += `Found in component '${compName}' ` }

            console.warn(warn)
        }

        // Define variable
        let pressTimer = null

        // Define funtion handlers
        // Create timeout ( run function after 1s )
        let start = (e) => {

            if (e.type === 'click' && e.button !== 0) {
                return;
            }

            if (pressTimer === null) {
                pressTimer = setTimeout(() => {
                    // Run function
                    handler()
                }, 1000)
            }
        }

        // Cancel Timeout
        let cancel = (e) => {
            // Check if timer has a value or not
            if (pressTimer !== null) {
                clearTimeout(pressTimer)
                pressTimer = null
            }
        }
        // Run Function
        const handler = (e) => {
            binding.value(e)
        }

        // Add Event listeners
        el.addEventListener("mousedown", start);
        el.addEventListener("touchstart", start);
        // Cancel timeouts if this events happen
        el.addEventListener("click", cancel);
        el.addEventListener("mouseout", cancel);
        el.addEventListener("touchend", cancel);
        el.addEventListener("touchcancel", cancel);
    }
})
```

现在参考我们的 Vue 组件:

```
<template>
    <div>
        <button v-longpress="incrementPlusTen" @click="incrementPlusOne">{{value}}</button>
    </div>
</template>

<script>
export default {
    data() {
        return {
            value: 10
        }
    },
    methods: {
        // Increment value plus one
        incrementPlusOne() {
            this.value++
        },
        // increment value plus 10
        incrementPlusTen() {
            this.value += 10
        }
    }
}
</script>
```

## 结论

如果你想了解更多关于自定义指令、可用的钩子函数、可以传递给钩子函数的参数以及函数 shorthands 的信息，伟大的 guys @vuejs 已经做了很好的解释[这里](https://vuejs.org/v2/guide/custom-directive.html)。

干杯！！！

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。