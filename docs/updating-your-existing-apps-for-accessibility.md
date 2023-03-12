# 更新您现有的应用程序的可访问性

> 原文：<https://blog.logrocket.com/updating-your-existing-apps-for-accessibility/>

web 拥有不断增长的用户基础，并且比以往任何时候都更多的活动围绕着 web 应用程序。对于开发人员和产品经理来说，构建不仅适用于大量用例，而且适用于各种能力的界面是非常重要的。万维网联盟(W3C) 创建了一套规范，向那些在使用网络应用时可能面临挑战的个人展示如何让他们能够访问网络应用。这包括有身体、视觉、语言、听觉和智力障碍的人。

JavaScript 可以说是用于构建 web 应用程序的最流行的语言，它的两个最流行的框架是 [React](https://github.com/facebook/react) 和 [Vue](https://github.com/vuejs/vue) 。让我们来看看如何让使用任一框架构建的 web 应用程序更容易被有限制的用户访问。

## 用 ARIA 属性改进标记

易访问的富互联网应用程序(ARIA)属性是 web 应用程序易访问性的重要组成部分。您可以使用它们来指定属性，这些属性定义了元素被转换成[可访问性树](https://developers.google.com/web/fundamentals/accessibility/semantics-builtin/the-accessibility-tree)的方式。

为了演示如何使用 ARIA 属性来提高 React 应用程序的可访问性，假设我们有一个电子商务应用程序，我们希望简化结账过程。

```
render() {
  return (
      <div>
        <h3>"Click below to use Prime's lifetime access at $10.99 per month"</h3>
        <button onClick={this.makePayment}>Pay Here</button>
      </div>
    );
  }
}

render(<Payment />, document.getElementById("root"));

```

问题是:如果这个 web 应用程序使用了[屏幕阅读器](https://accessibility.its.uconn.edu/2018/08/22/what-is-a-screen-reader-and-how-does-it-work/#)，它可能会检测到按钮，但不会检测到`<h3>`标签中的文本。因此，没有意识到这一点的视障用户可能会不知不觉地注册一项服务，每隔一个月就会被扣除一次费用。我们可以使用 ARIA 属性使其更易访问。

```
  render() {
    return (
      <div>
        <h3> Click below to use Prime's lifetime access at $10.99 per month </h3>
        <button
          onClick={this.makePayment}
          aria-label="Click below to use Prime's lifetime access at $10.99 per month"
        >
          Pay Here
        </button>
      </div>
    );
  }

```

在上面的代码示例中，`aria-label`告诉应用程序的用户这个按钮到底是用来做什么的。但是如果`<h3>`标签中的文本真的很长怎么办？我们不想在`aria-label`中塞进一整段话。让我们修改我们的`return`语句，以包含另一个 ARIA 属性:

```
render() {
    return (
      <div>
        <h3 id="lifetimeAccess">
          Click below to use Prime's lifetime access at $10.99 per month
        </h3>
        <button 
          onClick={this.makePayment} 
          aria-labelledby="lifetimeAccess"
        >
          Pay Here
        </button>
      </div>
    );
  }

```

使用`aria-labelledby`属性，屏幕阅读器可以检测到具有终生访问权的`id`的元素是按钮的标签。

对于 Vue，除了语法上的变化之外，这几乎是相同的事情:

```
<template>
  <div>
    <h3 :id="`lifetimeAccess`">
      Click below to use Prime's lifetime access at $10.99 per month
    </h3>
    <button 
      @click="makePayment()" 
      :aria-labelledby="`lifetimeAccess`"
    >
      Pay Here
    </button>
  </div>
</template>

```

## 管理焦点

在访问你的应用程序时，给用户提供如何处理焦点的选项是很重要的。键盘焦点是一个很好的选择，因为它允许手腕活动受限的人轻松访问您的应用程序。Vue 通过使用[自定义指令](https://vuejs.org/v2/guide/custom-directive.html)实现键盘焦点。

```
<template>
  <div id="app">
    <div v-if="flow == 'search'">
      <input type="text" placeholder="Enter your query" v-model="search" v-focus>
      <button>Search</button>
    </div>
  </div>
</template>

<script>
import Vue from "vue";

Vue.directive("focus", {
  inserted: function(el) {
    el.focus();
  },
  update: function(el) {
    Vue.nextTick(function() {
      el.focus();
    });
  }
});

export default {
  name: "App",
  data() {
    return {
      flow: "search",
      search: null
    };
  }
};
</script>

```

在上面的代码示例中，`v-focus`被全局注册为自定义指令。然后它被插入到 DOM 中，并被包装在一个`nextTick`中。这将[保持焦点事件，直到 DOM 被更新](https://vuejs.org/v2/api/#Vue-nextTick)并且输入被显示。

[https://www.youtube.com/embed/IpdlakY6Wjg](https://www.youtube.com/embed/IpdlakY6Wjg)

视频

如上面的短片所示，聚焦的元素是当前接收输入的元素。React 用`refs`完成了同样的事情。您可以[使用 refs 来访问 DOM 节点或者 React 在`render`方法中创建的元素](https://reactjs.org/docs/refs-and-the-dom.html)。

这里，我们将为想要添加元素的组件创建一个 ref，然后使用`componentDidMount`生命周期方法更新焦点:

```
import React, { Component } from "react";
import { render } from "react-dom";

class App extends Component {
  constructor(props) {
    super(props);
    this.focusDiv = React.createRef();
  }
  componentDidMount() {
    this.focusDiv.current.focus();
  }
  render() {
    return (
      <div className="app">
        <input tabIndex="-1" ref={this.focusDiv} placeholder="Enter your query" />
        <button>Search</button>
      </div>
    );
  }
}
render(<App />, document.getElementById("root"));

```

`tabIndex`的值被设置为`-1`,以允许您将编程焦点设置在原本不可聚焦的元素上。配置键盘焦点时，不要添加移除元素轮廓或边框的 CSS 样式，因为这些样式会影响元素处于焦点时显示的轮廓。

## 路线转换中的可达性

在使用 React 或 Vue 构建的单页应用程序中，屏幕阅读器在导航路线方面有一定的局限性。在导航期间，这些框架的路由软件处理来自浏览器的一些导航动作，以防止主机 HTML 页面的不断重新加载。

屏幕阅读器依靠浏览器来更新导航，但是由于这个功能是由框架来处理的，所以对于视觉有障碍的用户来说，接下来就是完全无声的页面转换。其他的例子是我们的应用程序中的错误情况、内容和状态变化，这些在视觉上可能非常清楚，但是屏幕阅读器却没有检测到。

`react-aria-live`是一个 React 库，它使用 [ARIA live regions](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions) 来宣布应用程序中的路由更改。假设我们想让视力受损的用户知道电子商务应用程序中的`Order`页面已经加载:

```
import React, { Component } from "react";
import { LiveAnnouncer, LiveMessage } from "react-aria-live";

class App extends Component {
  state = {
    message: ""
  };
  componentDidMount() {
    document.title = "Orders Page";
    setTimeout(() => {
      this.setState({ message: "The Orders page has loaded" });
    }, 3000);
  }
  render() {
    return (
      <LiveAnnouncer>
        <h1 tabIndex="-1"> Confirm your orders here</h1>
        <LiveMessage message={this.state.message} aria-live="polite" />
        ); }
      </LiveAnnouncer>
    );
  }
}

export default App;

```

在上面的代码示例中，`LiveAnnouncer`包装了整个应用程序，并呈现了一个可视的隐藏消息区域，可以广播`aria-live`消息。`LiveMessage`组件不必与`LiveAnnouncer`存在于同一个组件中；只要它存在于由`LiveAnnouncer`包装的组件树中，它就被用于使用`assertive`或`polite`音调来传达消息。

Vue 用类似于`react-aria-live`的库`vue-announcer`通知屏幕阅读器路线变化。在这里，您可能还需要手动配置消息。让我们复制同一个`Orders`页面，只是这次使用 Vue:

```
<template>
  <div id="app">
    <h1 tabindex="-1">Confirm your orders here</h1>
  </div>
</template>
<script>
export default {
  name: "App",
  head: {
    title: {
      inner: "Orders Page"
    }
  },
  methods: {
    mounted() {
      setTimeout(() => {
        let message = `The Orders page has loaded`;
        this.$announcer.set(message);
      }, 3000);
    }
  }
};
</script>

```

在上面的代码示例中，`this.$announcer`通过在页面加载三秒后发送听觉消息来通知用户。

## 摘要

实现可访问性的第一步是承认有些人不会以传统方式使用你的应用和设备。开发满足他们需求的应用程序有助于增加用户保留率，并展示您对包容性的承诺。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。