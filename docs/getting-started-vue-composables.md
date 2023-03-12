# Vue 组件入门

> 原文：<https://blog.logrocket.com/getting-started-vue-composables/>

Vue 3 引入了几个新概念和不同的处理逻辑的方式，其中之一就是 Vue composables。Vue 组件允许我们提取反应状态和功能，以便在其他组件中重用。

在本文中，我们将通过在 Vue 应用程序中实现 Vue 组件来全面理解它们。我们开始吧！

## 什么是组件视图？

Vue composables 是使用组合 API 来实现反应性和可重用逻辑的功能。Composables 充当一个外部函数，它提取反应状态和功能，以便跨几个其他组件使用。可组合函数也可以称为组合函数。

Composables】类似于 Vue 2 中 Options API 中的 mixins，以及 React 中钩子的实现。

## 可组合功能的可重用性

在现代 web 应用程序中，代码库总是被分成不同的组件，提供一个有组织的结构和简单的工作流程。使用可组合函数，您可以通过将功能提取和导入到一个外部文件中来进一步简化您的工作流，该文件可以在多个组件中使用。

[可组合函数背后的概念是由组合 API](https://blog.logrocket.com/vue-composition-api-vs-react-hooks/) 带来的，它给你充分的灵活性，根据逻辑考虑将你的组件代码组织成更小的函数。

可组合函数的可重用特性提供了许多好处。首先，提取的逻辑可以重用，用易于导航的结构促进代码组织。Composables 还可以作为一个使用反应状态的实用函数，并且可以针对应用程序中的不同目的进行定制。

最后，composables 可以根据用例管理输出的状态，允许您选择状态应该是全局的还是局部的。与其他状态管理工具相比，这种自由消除了一些复杂性和依赖性。

关于可组合函数的另一个有趣的事情是，它们可以相互嵌套，这意味着一个可组合函数可以调用一个或多个其他可组合函数。因此，我们能够将复杂的逻辑分解成更小的单元，就像我们如何使用组件来分离整个应用程序一样。

## 从头开始创建可组合的 Vue

让我们通过在 Vue 应用程序中创建和使用 composables 来尝试一下。为了跟随本教程，您需要安装 Node.js 和 [Vue CLI](https://cli.vuejs.org/guide/installation.html) 。首先，创建 Vue 项目，如下所示:

```
vue create vue-composables

```

项目创建后，使用以下代码导航到`vue-composables`:

```
cd vue-composables
```

对于一个有组织的结构，我们将创建一个单独的文件夹来处理项目所需的所有组件。

使用 [Web Speech API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API) ，我们将创建一个用于语音识别的自定义可组合组件。web Speech API 使开发人员能够在 Web 浏览器中实现语音识别和语音合成功能，使 Web 应用程序能够将语音数据转换为可读的文本格式。在本教程中，我们将创建一个可在您的应用程序中重用的语音识别组件。

API 用`SpeechRecognition()`构造函数初始化，它创建一个新的语音识别对象实例。

在创建可组合组件时，有一些重要的事情需要注意，包括命名约定。可组合函数使用骆驼大小写，其名称以前缀`use`开头。例如，我们用于演示的可组合函数将被命名为`useSpeechRecognition`。

就像常规函数一样，可组合函数接受可用于需要动态数据的函数的参数。最好从 composables 返回一个`refs`对象。因此，即使可组合组件被析构以用于不同的组件，这些值仍将保持它们的反应性。

每个可组合组件的基本结构如下:

```
export default function useComposableFunction() {
  // The logic
  return {// return values}
}

```

让我们创建`useSpeechRecognition`合成函数。在`composable`文件夹中，创建一个名为`useSpeechRecognition.js`的新文件，然后在其中放置以下逻辑功能:

```
import { ref,watch} from 'vue'

// the function accepts parameters
export function useSpeechRecognition({lang,continuous,interimResults}) {
    const isListening = ref(false)
    const isFinal = ref(false)
    const result = ref('')
    const error = ref(')

   // Intialize the web speech API
    const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition
    // Checks if the browser supports the API
    const isSupported = Boolean(SpeechRecognition)
    // The speech recognition constructor
    const recognition = SpeechRecognition? new SpeechRecognition() : false

     // a function to set it for listening
    const start = () => {
      isListening.value = true
    }

     // a function to stop it from listening
    const stop = () => {
      isListening.value = false
    }

   // Checks if the browser supports
    if (isSupported) {
      recognition.continuous = continuous
      recognition.interimResults = interimResults
      recognition.lang = lang

      recognition.onstart = () => {
        isFinal.value = false
      }

      // the method to get the result of the transcription
      recognition.onresult = (event) => {
        // raw words that the user spoke
        const transcript = Array.from(event.results)
          .map((result) => {
            isFinal.value = result.isFinal
            return result[0]
          })
          .map(result => result.transcript)
          .join('')

        result.value = transcript
        error.value = undefined
      }

      // method to handle error
      recognition.onerror = (event) => {
        error.value = 'Speech recognition error detected: ' + event.error
      }

      recognition.onend = () => {
        isListening.value = false
      }

      watch(isListening, () => {
        if (isListening.value)
        // Starting the speech recognition
          recognition.start()
        else
        // stopping the recognition
          recognition.stop()
      })
    }
  }

```

使用 Web Speech API，我们能够创建一个函数来接受来自用户的声音，并将它们转换成可读的文本。首先，我们确保我们的函数接受参数，然后初始化 Web Speech API，检查浏览器是否支持它。我们添加了一个函数来检查我们的 API 是否在监听`isListening.value`，将值设置为`true`或`false`。

使用`recognition.onresult`方法，我们得到转录的结果。此外，我们添加了一些错误处理方法。这种格式使我们不必根据我们构建的内容在不同的文件中重复这种逻辑。

接下来，我们返回对象中的值和方法:

```
    return {
      isSupported,
      isListening,
      isFinal,
      recognition,
      result,
      error,
      start,
      stop,
    }

```

然后，我们在组件中使用 composable。在`App.vue`文件中，我们首先导入以下函数:

```
import {useSpeechRecognition} from './composables/useSpeechRecognition'

```

通过调用函数，我们存储了可组合的返回值。我们还传递参数值:

```
const { isListening, isSupported, stop, result,start,error } = useSpeechRecognition({
        lang:'en-US',
        continuous: true,
        interimResults : true,
      })

```

设置完成后，我们将从组件中返回将要在组件中使用的值:

```
return {isListening,isSupported,stop,result,start,error}

```

最后，我们在组件中实现该功能:

```
<template>
  <div class="app">
    <div v-if="!isSupported">
      Your browser does not support SpeechRecognition API,
      <a
        href="https://caniuse.com/mdn-api_speechrecognition"
        target="_blank"
      >more details</a>
    </div>
    <div v-else >
      <header>
      <h1> Speech Recognition  </h1>
      <i class="header-icon fas fa-microphone-alt"></i>
    </header>
    <main>
      <div class="mic-buttons">
        <!-- To handle the controls -->
    <button v-if="!isListening" @click="start">
        Speak 
      </button>
      <button v-if="isListening" class="orange" @click="stop">
        Stop
      </button>
      </div>
      <h2> English Transcript </h2>
      <!-- Conditionals to handle errors -->
      <p v-if="error" >{{error}}</p>
      <div v-else>
      <textarea v-model="result" class="text-transcript" cols="30" rows="10">  </textarea>
      </div>
    </main>
    </div>
  </div>
</template>

```

这里是完整代码的[链接，以防你想尝试一下。](https://codesandbox.io/s/speech-recognition-vue-composable-91wzi4)

有了这个组件，我们可以轻松地在我们的 Vue 应用中实现语音识别功能，该功能也可重复用于不同的功能。

## 可组合函数的外部库

在 Vue 应用程序中使用 composables 的另一种方法是通过外部开源库，创建这些库是为了利用定制的组合 API 函数。

### 武尤斯

完全用 TypeScript 编写的 [VueUse](https://vueuse.org) 库提供了 200 多个可组合的函数，适用于 Vue 2 和 3 应用。这些功能可以通过 CDN 定制并在您的项目中使用，也可以通过 npm 直接安装:

```
npm i @vueuse/core

<script src="https://unpkg.com/@vueuse/core"></script>

```

文档写得很好，有交互式演示和代码。这些功能[分为九种类型](https://vueuse.org/functions.html):浏览器、传感器、动画、状态、元素、组件、手表、网络、实用程序和杂项。你可以从[官方文档](https://vueuse.org/functions.html)中获得关于不同功能的更多信息。

### 视图-零部件

vue-composable 是一个库，它提供了现成的、通用的组合 API 函数。vue-composable 完全由 TypeScript 构建，支持 Vue 2 和 Vue 3 应用程序。您可以使用 Yarn 或 npm 安装它:

```
# install with yarn
yarn add @vue/composition-api vue-composable
# install with npm
npm install @vue/composition-api vue-composable

```

它提供的一些功能类别有事件、日期、格式、断点、存储、i18n 和 Web。可以从[官方文档](https://pikax.me/vue-composable/)中了解更多。

## 结论

可组合函数分别提取逻辑功能，以便在其他几个组件中重用，这样就不必在多个不同的实例中复制代码来获得相同的结果。

composables 背后的概念改变了我们在应用程序中处理可重用性的方式，提供了一种更简单、更灵活、更有效的方法。现在是重构代码库以便在应用程序中使用组件的好时机！

如果你有任何问题，你可以在推特上联系我或者在下面留言。感谢阅读！

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。