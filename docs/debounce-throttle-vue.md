# 如何在 vue 中声讨和节流

> 原文：<https://blog.logrocket.com/debounce-throttle-vue/>

当您构建任何类型的 web 应用程序时，性能都是您主要关心的问题，尤其是对于进行交互的站点。当应用程序执行繁重的计算或调用 API 时，侦听用户键入输入、滚动和调整大小等事件可能会导致应用程序变得无响应。

`debounce`和`throttle`是两种通过限制函数调用来降低事件处理程序速度的技术。在本文中，我们将学习如何在 Vue 应用程序中去抖和节流观察器和事件处理程序。

## 目录

## 创建一个`debounce`函数

去抖是一种技术，可以通过等待触发事件直到一定时间过去来提高应用程序的性能；执行 `debounce` 功能允许功能在再次运行前等待设定的时间。

在进入代码之前，我们先了解一下去抖背后的想法。作为一个例子，假设一个用户在一个文本框中输入，当用户输入时，我们想从一个 API 中获取数据。如果我们调用 API 的次数和用户在键盘上输入的次数一样多，应用程序将会产生大量的网络请求，导致性能下降。

让我们假设你想去抖一个函数，在我们的例子中，是`search`函数。我们将该函数设置为在特定时间段后执行，该时间段是用户在输入每个新字符之前离开键盘的估计时间。如果用户在这个时间段内输入，我们将函数调用推迟到下一个时间间隔。使用这种方法，我们可以减少键入间隔内的函数调用次数:

下面是`debounce`函数的代码片段:

```
export function debounce(fn, wait){
    let timer;
   return function(...args){
     if(timer) {
        clearTimeout(timer); // clear any pre-existing timer
     }
     const context = this; // get the current context
     timer = setTimeout(()=>{
        fn.apply(context, args); // call the function if time expires
     }, wait);
   }
}

```

`debounce`函数有两个参数，要去抖的函数和以毫秒为单位的等待时间。它返回一个函数，以后可以调用:

```
const debouncedFunction = debounce(function() { ... }, 300);
console.log(typeof debouncedFunction); // `function`
When the debounce function is triggered:
```

在上面的代码中，我们取消了任何预先存在的超时，并根据指定的等待时间安排了一个新的超时。当超时过期时，我们用参数调用回调函数。

## 创建一个`throttle`函数

当用户在特定时间内没有执行事件时，`debounce`调用函数，而当用户正在执行事件时，`throttle`以特定时间间隔调用函数。

例如，如果我们用 300 毫秒的定时器去抖`search`函数，那么只有当用户在 300 毫秒内没有执行搜索时，才会调用该函数。然而，如果我们用 300 毫秒限制搜索函数，那么当用户输入时，每 300 毫秒调用一次该函数。

下面是我们示例中的`throttle`函数的代码片段:

```
export function throttle(fn, wait){
    let throttled = false;
    return function(...args){
        if(!throttled){
            fn.apply(this,args);
            throttled = true;
            setTimeout(()=>{
                throttled = false;
            }, wait);
        }
    }
}

```

当`throttle`函数被触发时，`throttled`变量被设置为`false`，所提供的函数被带参数调用。

在函数调用之后，我们将`throttled`变量设置为`true`。如果在此指定时间内有任何事件发生，该函数不会被调用，直到`throttle`变量被设置为`true`。`setTimeout`负责在等待时间到期后给`throttled`赋值一个变量。

## 谴责观察者

让我们创建一个简单的组件，我们的任务是当用户在文本框中键入内容时调用 Fetch API 并记录值:

```
<template>
 <div id="app">
  <input v-model="value" type="text" />
  <p>{{ value }}</p>
</div>
</template>
<script>
export default {
  data() {
    return {
      value: "",
    };
  },
  watch: {
    value(newValue, oldValue) {
      console.log("value changed: ", newValue, oldValue);
      // call fetch API to get results
    }
  }
};
</script>

```

在上面的例子中，每次用户输入一个值，它都会被记录到控制台并调用 API。我们不能如此频繁地调用 API 而不降低应用程序的性能。因此，我们将对上面的活动进行去抖，并在观察器中调用这个去抖函数。为此，我们可以使用前面创建的`debounce`函数。

下面是去抖后的代码:

```
<template>
 <div id="app">
  <input v-model="value" type="text" />
  <p>{{ value }}</p>
</div>
</template>
<script>
import {debounce} from "./Utils.js";
export default {
  data() {
    return {
      value: "",
    };
  },
  created(){
     this.debouncedFetch = debounce((newValue, oldValue)=>{
            console.log("value changed: ", newValue, oldValue);
           // call fetch API to get results
     }, 1000);
  },
  watch: {
    value(...args) {
      this.debouncedFetch(...args);
    }
  }
};
</script>

```

在上面的代码中，如果自上一次输入活动以来已经过了 1000 毫秒，用户可以登录到控制台或调用 API。

我们将通过创建一个`debouncedFetch`的实例在观察器上实现去抖动，该实例调用带有回调和等待时间的`debounce`函数。这个实例是在`created()`钩子中创建的，然后我们在观察器中用正确的参数调用`debouncedFetch`。

## 谴责事件处理程序

去抖动观察器和事件处理程序是相似的。让我们考虑同样的例子，用户在文本框中输入内容。键入之后，我们将登录到控制台并调用 Fetch API。

下面是去抖前的例子:

```
<template>
  <input v-on:input="onChange" type="text" />
</template>
<script>
export default {
  methods: {
    onChange(event) {
      console.log('changed value', event.target.value);
      // call fetch API to get results
    }
  }
};
</script> 

```

现在，我们将去抖`onChange`事件处理程序，以限制用户输入时的函数调用。

在`created()`钩子中，通过传递一个事件处理程序和一个等待时间来创建一个`debounce`函数的实例，并将其命名为`onChangeDebounced`。然后，将`onChangeDebounced`分配给一个`@input`事件处理程序:

```
<template>
  <input @input="onChangeDebounced" type="text" />
</template>
<script>
import {debounce} from './Utils.js';
export default {
  created() {
    this.onChangeDebounced = debounce(event => {
      console.log('changed value:', event.target.value);
      // call fetch API to get results
    }, 1000);
  },
};
</script>

```

## 结论

在本教程中，我们学习了 Vue 中的去抖动和节流观察器和事件处理程序。`debounce`和`throttle`是两种强大的技术，通过限制函数调用来降低事件处理程序的速度，从而提高应用程序的性能。

既然您已经理解了`debounce`和`throttle`是如何工作的，请在您自己的项目中尝试一下。您应该会看到立竿见影的性能优势。留下评论，让我知道进展如何！

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。