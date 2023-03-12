# 深入了解 Vue 事件处理

> 原文：<https://blog.logrocket.com/deep-dive-vue-event-handling/>

构建动态网站时，您需要监听不同类型的事件。为了在我们的网站或 web 应用程序上驱动这些类型的交互，我们可以使用事件处理。

在 Vue 中处理事件有几种不同的方法，但最佳解决方案将取决于您正在收听的事件的类型、您希望对事件做出的反应以及您希望通过事件实现的目标。

例如，如果用户点击一个按钮，提交一个表单，或者只是移动他们的鼠标，你可以添加一个反应，比如显示一个动画或者调用一个函数。添加这些类型的事件为最终用户提供了丰富的 UX。

在本文中，我们将介绍在 Vue 中处理事件的基础知识，并学习如何在现实应用中应用事件处理。我们将学习如何通过监听事件、拦截事件并使用下述方法处理事件来改变或阻止元素的行为。

要理解本教程，你应该熟悉 JavaScript 中的[事件。我们开始吧！](https://blog.logrocket.com/how-to-dynamically-create-javascript-elements-with-event-handlers/)

## 创建内嵌事件

首先，让我们看看 Vue 中事件处理的一个常见用例，创建内联事件。通过 Vue 的原生指令`v-on`，可以监听浏览器的原生 DOM 事件。在下面的代码块中，我们将侦听按钮上的单击事件，然后更改按钮的文本:

```
<template>
  <div>
<button v-on:click='text="Clicked!"'>{text}</button>
</div>  
</template>
<script>
export default {
  name: 'InlineEvent',
  data: function () {
    return {
      text: "Click Me"
    }
  }
}
</script>

```

或者，我们可以使用`v-on`指令调用一个函数来显示警告:

```
<template>
  <div>
<button v-on:click='showAlert'>Show alert</button>
</div>  
</template>
<script>
export default {
  name: 'InlineEvent',

  methods:{
  showAlert: ()=>{
    alert("Hola, buenos dia")
  }
  }
}
</script>

```

在上面的两个例子中，我们向 DOM 元素添加了`v-on`指令来拦截它的事件:

```
<button v-on:click="eventHandler"></button>

```

我们还向`v-on`指令添加了一个参数，这是我们想要处理的事件的名称。在我们的例子中，参数是`click`。

接下来，我们将一个表达式绑定到指令，这通常是一个您想要用来处理事件的方法。我们称我们的指令为`eventHandler`，并使用了`v-on`指令的简写:

```
<button @click="eventHandler"></button>

```

### Vue 中的常见事件

在我们到目前为止的例子中，我们已经听到了发生的事件`onclick`。让我们回顾一下其他一些流行的活动类型:

#### 鼠标事件

*   `onclick`
*   `ondragstart`
*   `ondrop`
*   `dblclick`
*   `onmousedown`
*   `onmouseup`
*   `mouseover`

#### 键盘事件

*   `onkeydown`
*   `onkeypress`
*   `onkeyup`

#### HTML 表单和对象事件

*   `onchange`
*   `onsubmit`
*   `onreset`
*   `onscroll`
*   `onerror`

在 [Vue 文档](https://vuejs.org/v2/guide/events.html?)中描述了更多的事件，您可以在设计您的应用程序时从中选择。您应该回顾并试验这些，以确保您的设计对用户来说尽可能直观。

### 在`v-on`上调用元素

在 Vue 中，您可以使用在元素的`v-on`指令上调用的方法，这些方法将包含复杂的逻辑，每当触发事件时都会执行这些逻辑。例如，让我们考虑一个触发的警报`onclick`:

```
<template>
  <div>
<button v-on:click='showAlert("Hello, good morning")'>Show alert</button>
</div>  
</template>
<script>
export default {
  name: 'InlineEvent',
  methods:{
  showAlert: (message)=>{
    alert(message)
  }
  }
}
</script>

```

在上面的代码块中，我们直接从`v-on`指令向`showAlert`方法传递消息。然而，我们可以通过向函数添加更多的上下文来改进这段代码。在下面的代码块中，我们将通过连接`Hello`和`showAlert`函数中传递的任何文本来更新`showAlert`显示的消息:

```
<template>
  <div>
<button v-on:click='showAlert("Good Morning!")'>Show alert</button>
</div>  
</template>
<script>
export default {
  name: 'InlineEvent',

  methods:{
  showAlert: (message)=>{
    alert(`Hello, ${message}`)
  }
  }
}
</script>

```

既然我们知道了如何截取和创建一个新事件，那么让我们来学习如何改变和阻止一个事件的默认行为。

## 修改和阻止事件

在 Vue 中，我们可以使用事件修改器来改变事件的行为或阻止默认事件。

例如，假设我们有一个包含按钮的表单。默认情况下，如果用户单击按钮或按 enter 键，表单将被提交，页面将被刷新。在单页面应用程序中，我们希望避免完全重载页面，所以我们将使用 AJAX 提交表单。

### 防止默认行为

为了防止事件的默认行为，我们可以调用 Vue 中的`.prevent`修饰符，它调用本机的`event.preventDefault()`方法。这一系列步骤防止事件在表单提交后重新加载页面。

另一方面，`.stop`修改器通过调用`event.stopPropagation()`完全停止 DOM 事件的默认行为。例如，在下面的代码中，我们在`v-on`指令的`submit`上使用了`.stop`和`.submit`修饰符:

```
<template>
  <div>
    <h2>Form</h2>
    <form
      v-on:submit.stop.prevent="log('clicked!', $event)">
      <button type="Submit">
        Click Me
      </button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'EventModifier',
  methods: {
    log(msg, event) {
      console.log(event.target.tagName, msg);
    }
  }
}
</script>

```

这两种方法执行类似的任务，并一起使用。您可以使用`.prevent`来拦截表单的默认行为，并使用`.stop`来[防止父元素上的事件冒泡](https://blog.logrocket.com/deep-dive-into-event-bubbling-and-capturing/)。

### 更改默认行为

要更改默认行为，让我们看看下面代码块中的例子。默认情况下，当用户点击`Click Me`链接时，他们会看到一个警告。然后，谷歌将在一个新的标签页中打开:

```
<a href = "http://www.google.com" v-on:click = "alert('Hola, buenos dias')">Click Me</a>

```

然而，如果我们不想在显示警告后打开链接，我们可以使用`.prevent`修饰符，如下所示:

```
<a href = "http://www.google.com" v-on:click.prevent = "alert('Hola, buenos dias')">Click Me</a>

```

`.prevent`修饰符阻止链接打开，只执行分配给标签的方法。现在，单击该按钮将发送一条警告消息，而无需打开链接。

### 其他事件修饰符

在我们的例子中，我们在 Vue 中使用了`.prevent`修饰符。让我们看看其他几个流行的修改器及其功能:

*   `.self`:仅当`event.target`是元素本身时触发事件
*   `.once`:防止事件执行多次
*   `.keyup`:监听键盘事件
*   `.capture`:在处理触发事件的元素之前，处理以内部元素为目标的事件

### 关键事件修饰符

Vue 中的按键事件修改器为聆听键盘提供了广泛的支持。例如，我们可以使用按键修饰符来监听特定按键被按下的事件。注意，有多个单词的键名通常用 kebab 大小写。

在下面的例子中，我们正在监听`enter`键事件:

```
<input type = "text" v-on:keyup.enter = "alert('Hola, buenos dias!')" />

```

当上面的输入元素被聚焦时，按下 enter 按钮将触发警告功能。

按键事件修改器遵循结构`v-on.keyevent.keyname`。也可以像`v-on.keyup.ctrl.enter`一样嵌套多个键名。

## 创建自定义事件

在 Vue 中，父组件可以通过 prop 属性向其子组件传递数据，但是子组件不能向父组件发送数据。但是，我们可以使用`$emit`让父组件监听子组件，并允许子组件向父组件传递数据。

在下面的例子中，包含父组件的`App.vue`正在监听子组件`Child.vue`:

```
//App.vue (parent component)
<template>
  <div>
    <child @show-alert="updateAlert"> </child>
  </div>
</template>
<script>
import Child from "@/components/Child.vue";
export default {
  components: {
    Child,
  },
  methods: {
    updateAlert(item) {
      alert(item);
    },
  },
};
</script>

//Child.vue (child component)

<template>
  <div>
    <button class="Button" @click="emitAlert('Hello world')">Show alert</button>
  </div>
</template>
<script>
export default {
  methods: {
    emitAlert(item) {
      this.$emit("show-alert", item);
    },
    updateAlert(item) {
      alert(item);
    },
  },
};
</script>

```

## 结论

尽管事件处理看起来很简单，但是不正确或不完整的事件处理程序可能会使您的代码混乱，甚至会破坏您的应用程序。在本教程中，我们学习了在 Vue 应用程序中处理事件的几种不同方法。

我们介绍了如何使用`v-on`指令添加内联事件，并回顾了一些可以用来定制应用程序的不同类型的事件。我们还学习了如何使用修饰符来防止或改变事件的默认行为。最后，我们讲述了如何使用`$emit`改变子组件和父组件之间的通信。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。