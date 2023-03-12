# 用 Vue Property Decorator 和 TypeScript 定义属性

> 原文：<https://blog.logrocket.com/define-properties-vue-property-decorator-typescript/>

Vue 3 引入了一种新的、[更好的方式来创建组件，即组合 API](https://blog.logrocket.com/new-features-in-vue-3-and-how-to-use-them-2/#thecompositionapi) 。Composition API 的特性使得在组件增长时更好地维护代码、轻松地重用代码以及编写可靠的代码成为可能，同时还提供了类型脚本支持。

然而，Vue 组件严重依赖于 JavaScript 对象的`this`关键字，这使得创建 TypeScript 对象变得令人困惑。为了解决这个问题，Vue 类组件插件使用 ECMAScript decorators 将静态类型的值直接传递给 Vue 中的组件，使编译器理解正在发生的事情。

在本文中，您将了解如何通过在基于 Vue 类的组件中支持 TypeScript，直接在 Vue 组件中的类上定义属性，如数据、方法、计算属性、props 和观察器。我们将使用 Vue Class Component 和 Vue Property Decorator，我们还将讨论各种 JavaScript 等价物。

### 先决条件

要跟随本教程，您需要了解 TypeScript。您还需要在本地机器上安装 npm 和 Node.js *≥* v12.22.0。要确认 Node.js 已安装，请在终端中运行以下命令:

```
node -v

```

如果没有安装 Node.js，可以按照官方 [Node.js](https://nodejs.org/) 网站上的说明操作。最后，你需要熟悉[建立一个 Vue 项目](https://cli.vuejs.org/guide/installation.html)。我们开始吧！

### 目录

## 项目设置

打开你的终端，进入你想要保存项目的目录。运行以下命令。请注意，您可以随意命名您的项目:

```
vue create vue-props-example

```

在设置您的 Vue 项目时，您会被问及一系列问题。出于本教程的目的，您应该选择以下配置:

| 提示 | [计]选项 |
| --- | --- |
| 请选择一个预设 | 手动选择功能 |
| 检查项目所需的功能 | 以打字打的文件 |
| 选择您想要用来启动项目的 Vue 版本 | 3.x |
| 使用类样式的组件语法？ | Y |
| 在 TypeScript 旁边使用 Babel？ | Y |

![Vue Configurations Display](img/9ed4f81865e5e3ee4a21b471e87ff265.png)

如果你看一下`package.json`文件，你会看到通过用 TypeScript 安装我们的项目，我们也安装了`[vue-class-component](https://github.com/vuejs/vue-class-component)`插件，它用于定义类风格的组件，在创建 TypeScript 和 Vue 3 项目时默认安装。

`App.vue`文件使用 Vue 类组件，它使用`Options`装饰器来定义一个 Vue 组件。此时，您有了一个包含 TypeScript 功能的 Vue 3 项目。

安装 Vue 属性装饰器

## Vue 的一些最重要的部分在 Vue 类组件中缺失了，比如 ECMAScript 装饰器像`props`、`inject`、`model`、`ref`、`emit`和`provide`。因此，Vue 社区引入了一个名为 [Vue Property Decorator](https://github.com/kaorun343/vue-property-decorator) 的库，它完全依赖于 Vue 类组件，并且现在得到了 Vue 核心团队的完全认可。

我们将通过在项目目录中运行以下命令，在项目中安装 Vue Property Decorator:

现在，我们完全可以在 TypeScript 中使用基于类的组件。

```
npm i vue-property-decorator

```

创建 TypeScript Vue 组件

## 使用类风格的组件语法，我们将通过扩展 Vue 对象来构建组件。在`App.vue`中，复制并粘贴以下代码，用 TypeScript 创建一个简单的单文件组件:

上面的代码直接在类和方法上定义属性，这在`@Component(componentConfig)`装饰器的帮助下是可能的。在编译期间，`@Component(componentConfig)` decorator 将类转换成 Vue 可以理解的格式。

```
&lt;template>
  <div>
    <label>Update inputData
      <input :value="inputData" @input="updateInputData($event)"/>
    </label>
    <div>{{ inputData }}</div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'

@Component
export default class App extends Vue {
  // Data property
  inputData: string = 'My Input Data'

  // Component method
  updateInputData ($event: { target: { value: string } }) {
    this.inputData = $event.target.value
  }
}
</script>

```

现在，当应用程序被编译时，如果你在`[http://localhost:8080/](http://localhost:8080/)`看你的浏览器，你会看到一个输入框和一些文本阅读`myInputData`。`myInputData`将与输入域交互，相应地更新以反映用户在浏览器上输入文本的变化。

定义道具

## 使用来自 Vue 属性装饰库的`@Prop`装饰器，我们可以在类上定义道具。我们还可以为道具提供额外的细节，比如`required`、`type`和`default`，以实现数据对象的可重用性。

我们首先从 Vue 属性装饰器库中导入`Prop`装饰器，并编写以下代码:

上面的代码是用 TypeScript 编写的，定义了各种叫做`text`、`name`、`age`、`phone`和`job`的道具。为了避免修改道具，分配了`readonly`属性。

```
<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'

@Component
export default class App extends Vue {
  @Prop() readonly text!: string
  @Prop({default: 'John doe'}) readonly name: string
  @Prop({required: true}) readonly age: number
  @Prop(String) readonly phone: string
  @Prop({required: true, type: String, default: 'Student'}) readonly job: string
}
</script>

```

在 JavaScript 中，上面的代码相当于下面的代码:

定义计算属性

```
&lt;script>
export default {
  props: {
    text,
    name: {
      default: 'John doe'
    },
    age: {
      required: true,
    },
    phone: {
      type: String
    },
    job: {
      required: true,
      type: String,
      default: 'Student'
    }
  }
}
</script>

```

## 计算出的属性被写成`getters`和`setters`，并在类上定义。为了进行演示，下面的示例包含 TypeScript 中的计算属性，这些计算属性获取一个名为`computedProp`的计算属性，并返回一个随机数，将其显示在浏览器上:

与上述代码等价的 JavaScript 代码是:

```
<template>
  <div>{{ computedProp }}</div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'

@Component
export default class App extends Vue {
  get computedProp() {
    return Math.random()
  }
}
</script>

```

定义观察者

```
<style>
export default {
  computed: {
    computedProp() {
      return Math.random()
    }
  }
}
</style>

```

## 我们可以通过从 Vue Property Decorator 库中导入`@Watch(propertyString, config)` decorator 来定义类的观察者。您会注意到这与通常用 JavaScript 编写的完全不同。

下面是 TypeScript 中的一个例子，它观察`myWatchedData`何时触发`onDataChanged`:

与上述代码等价的 JavaScript 代码是:

```
<template>
  <div>
    <label>Update myWatchedData
      <input :value="myWatchedData" @input="updateMyData($event)"/>
    </label>
    <div>{{ myWatchedDataStatus }}</div>
  </div>
</template>

<script lang="ts">
import { Component, Watch, Vue } from 'vue-property-decorator'

@Component
export default class App extends Vue {
  myWatchedData: string = 'Watched Data'
  myWatchedDataStatus: string = ''

  @Watch('myWatchedData')
  onDataChanged(value: string, oldValue: string) {
    this.myWatchedDataStatus = 'Watched Data Changed'
  }

  updateMyData ($event: { target: { value: string } }) {
    this.myWatchedPData = $event.target.value
  }
}
</script>

```

使用上面的代码，当您在`[http://localhost:8080/](http://localhost:8080/)`访问浏览器时，您会看到一个输入字段。现在，更改输入值将显示消息`Watched Data Changed`。

```
&lt;script>
export default {
  data() {
    return {
      myWatchedProperty: null
    }
  }

  methods: {
    onPropertyChanged(value, oldValue) {
      // ...
    }
  }

  watch: {
    myWatchedProperty: {
      handler: 'onPropertyChanged',
      immediate: false,
      deep: true
    }
  }
}
</script>

```

结论

## 在本文中，我们使用 Vue 类组件和 Vue 属性装饰器直接在 Vue 组件中的类上定义属性，允许我们在基于 Vue 类的组件中支持 TypeScript。

本文介绍了各种各样的装饰者，如`component`、`prop`、`watch`、`get`。你可以在上面链接的 Vue Class Component 和 Vue Property Decorator 的文档中了解更多关于 Decorator 和基于类的声明的信息。

对于初学者来说，用 TypeScript 创建 Vue 组件可能有点混乱，但是当你习惯了它，你就会享受到它带来的所有好处，使你的应用程序更加可靠。我希望你喜欢这篇文章，快乐编码！

像用户一样体验您的 Vue 应用

## 调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。

Modernize how you debug your Vue apps - [Start monitoring for free](https://lp.logrocket.com/blg/vue-signup).