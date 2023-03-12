# React 开发人员的 5 个 Vue 技巧

> 原文：<https://blog.logrocket.com/5-vue-tips-for-react-developers/>

## 介绍

对于前端开发人员来说，JavaScript 框架已经变得越来越重要，因为它们从根本上改变了我们构建应用程序的方式。

虽然不是每个人都专业地使用它们，或者根本不使用，但是开发社区肯定喜欢谈论它们。不幸的是，这些讨论经常演变成对 Framework X 比 Framework Y 更好，或者 Framework Z 不是“真正的 JavaScript”的抨击

作为一名工程师，我总是觉得很奇怪，认为一个框架比另一个框架“更好”，而实际上它们都是一样的。本质上，这些工具都在试图解决同样的问题，改善开发者体验；他们只是采取不同的方法。

构建这些框架的目的是让开发人员能够更容易地:

1.  构建可重用的 UI 组件
2.  管理应用程序状态
3.  注入数据

有时会感觉每隔一天就有一个热门的新框架(看看你，苗条)，但 React 和 Vue 是近年来最受欢迎的两个。两者都被大规模地广泛使用，并且拥有大型的、活跃的开源社区。

作为 Guru 的 React 开发人员，我主要使用 React。然而，我最近一直在与当地的 Vue meetup 合作，协调我们的办公空间用于他们的活动。有一段时间没有用 Vue 构建任何东西，我决定这将是构建一些很酷的东西并重新熟悉自己的完美机会，并有将其与 React 进行比较的额外好处。

在我们进一步讨论之前，我只想说:这篇文章并不是要确定 React 和 Vue 哪个更好。相反，我希望从实用的角度来研究这两个框架，看看它们在解决常见问题时有什么不同。检查另一个框架甚至可以告诉我们如何更好地使用我们自己的框架。

## 背景

我最近买了一栋房子，搬到了一个新的社区，所以我不再有机会乘坐地铁，必须依靠公交系统去上班。虽然在稍微长一点的通勤路上有一些时间阅读会很好，但站在外面等一辆似乎永远不会来的公交车就不好了。

这似乎是一个我可以使用 Vue 构建解决方案的问题。尽管我可以很容易地查看 SEPTA 的网站或谷歌地图(稍后将详细介绍)，但我想创建一个简单的应用程序，它会告诉我离下一辆公交车还有多长时间，以便我可以快速查看并跑出门外。

因为任何给定的框架都有比本文所能涵盖的更多的方面，所以我们将把重点放在我在试图实现这个小项目的目标时遇到的差异上:

*   它必须有两个视图:一个用于上班，另一个用于回家
*   它必须显示我上下班乘坐的两条公交线路的信息
*   它必须显示下一班车何时到达每个车站

> 附注:我所在的地方交通管理局没有可靠的 API，所以我最终不得不依赖谷歌地图。为了防止大量的 API 调用，我设置了一个定时任务来访问 API，然后将一个 JSON 文件写入云存储。这个 JSON 是应用程序用来渲染的数据。

最后，应用程序看起来像这样:

> ryancharris 使用 axios、查询字符串、vue

方法

## 正如我们前面所讨论的，React 和 Vue 都有相似的目标，但在方法上略有不同。当我说方法时，我指的是你，开发人员，着手构建组件的方式。

在这两个框架中，Vue 采用了一种更类似于模板的方法，与 Ruby、Elixir 和 PHP 等其他语言中的模型-视图-控制器框架所使用的标记和模板工具没有什么不同。

另一方面，React 感觉更像 HTML-in-JavaScript。看看下面的两个组件，看看你是否能弄清楚发生了什么。

首先，使用 React:

现在 Vue:

```
function Accordion() {
  const [isAccordionOpen, toggleAccordion] = React.useState(false);

  return (
    <div className="Accordion">
      <h3>Accordion Header</h3>
      <button
        onClick={() => {
          toggleAccordion(!isAccordionOpen);
        }}
      >
        Toggle Accordion
      </button>
      {isAccordionOpen && <p>Accordion content lives here...</p>}
    </div>
  );
}
```

一个不一定比另一个好；他们只是不一样。这就是它们如此有趣的原因！

```
<template>
  <div class="accordion">
    <button @click="toggleAccordion">Toggle Accordion</button>
    <p v-if="isAccordionOpen">Accordion content lives here...</p>
  </div>
</template>

<script>
export default {
  name: "Accordion",
  data: function() {
    return {
      isAccordionOpen: false
    };
  },
  methods: {
    toggleAccordion() {
      this.isAccordionOpen = !this.isAccordionOpen;
    }
  }
};
</script>
```

反应与视图

## 下面，我强调了开发人员在构建应用程序时经常执行的五项任务，并创建了如何使用任一框架实现预期结果的示例。

1.条件渲染

### 开发人员使用的一种常见策略称为条件呈现。本质上，这是一种“if X，then Y”的奇特说法。这通常是我们向用户显示或隐藏部分界面的方式。

我们可以在`<template>`标签内的`App.vue`中看到这样的例子。我们的两个组件(`<Work />`和`<Home />`)正在使用 Vue 的 v 绑定进行有条件渲染。在这种情况下，如果我们的本地状态(即`data.isWorkScreen`)的值为`true`，用户就会看到`<Work />`。否则，他们看到的，`<Home />`。

在 React 中，我们会稍微做些不同。有很多方法可以复制上面看到的行为，但最直接的方法是在渲染函数中使用内联 JSX 表达式。

```
<div id="app">
  <h1 class="app__header">{{createHeader}}</h1>
  <Toggle @toggled="handleToggleChange"/>
  <Work v-if="isWorkScreen"/>
  <Home v-else/>
</div>
```

2.呈现列表

```
<div id="App">
  <h1 className="App_header">My Cool App</h1>
  <Toggle onClick={handleToggleChange} />
  {isWorkScreen ? <Work /> : <Home />}
</div>
```

### 我发现自己在构建应用程序时经常做的另一件事是遍历数据对象，并为列表中的每一项创建组件实例。例如，这将是在`<ToDoList />`中创建每个`<Item />`的好方法。

更多来自 LogRocket 的精彩文章:

* * *

### 在我们的例子中，我们在`Work.vue`中创建了`<BusRoute />`的两个实例，在`Home.vue`中创建了另外两个实例。在这两个文件中，我们使用`v-for`绑定遍历我们的路由信息，以便创建每个单独的路由组件。

* * *

为了在 React 中复制这一点，我们可以使用像`.map`这样简单的东西为数据对象中的每一项创建一个`<BusRoute />`。然后，我们可以把这个值代入我们返回的 JSX 树。

```
<div class="Work">
  <BusRoute
    v-for="bus in buses"
    :key="`BusRoute-${bus.routeNumber}`"
    :routeNumber="bus.routeNumber"
    :origin="bus.origin"
    :destination="bus.destination"
    :jsonUrl="bus.jsonUrl"
  />
</div>
```

3.渲染孩子

```
const { buses } = this.state;

const busRoutes = buses.map(bus => {
  return (
    <BusRoute
      key={`BusRoute-${bus.routeNumber}`}
      routeNumber={bus.routeNumber}
      origin={bus.origin}
      destination={bus.destination}
      jsonUrl={bus.jsonUrl}
    />
  )
})

return (
  <div className="Work">
    {busRoutes}
  </div>
);
```

### 这一条可能看起来很简单，而且在大多数情况下，确实如此。然而，从 React 的背景来看，在处理子组件时有一个“陷阱”。

在 React 中，整个过程很简单。您只需导入文件，然后在渲染中使用它。

Vue 组件需要一个额外的步骤。除了导入和使用子组件，Vue 组件必须使用 Vue 实例的`component`字段在本地注册它们的组件依赖关系。这种开销旨在帮助实现高效捆绑，从而减少客户端下载大小。

在我们的总线应用程序中是最好的例子，因为它负责在生命周期的不同点呈现三个子组件。

4.加载组件数据

```
export default {
  name: "App",
  components: {
    Home,
    Toggle,
    Work
  }
}
```

### 在 Vue 中向组件注入数据类似于在钩子被释放之前在 React 中做的事情。和 React 的`componentDidMount`一样，Vue 也有一个类似的生命周期方法，叫做`created`，在这里你可以从一个外部数据源获取数据，并且表面上把它存储在本地状态。

这是这两种生命周期方法相继出现的情况。

反应:

视图:

```
componentDidMount() {
  axios({
    method: "get",
    url: this.props.jsonUrl
  })
    .then(res => {
      const { routes } = res.data;
      this.setState({
        departureTime: routes[0].legs[0].departure_time.text
      });
    })
    .catch(err => {
      console.log(err);
    });
}
```

别忘了我们的新朋友，胡克斯！如果您正在使用 React 16.8(或更新版本)，那么您可能已经开始编写使用钩子的函数组件了，所以下面是在 React 中如何实现上述功能:

```
created: function(jsonUrl) {
  axios({
    method: "get",
    url: this.$props.jsonUrl
  })
    .then(res => {
      const { routes } = res.data;
      this.departureTime = routes[0].legs[0].departure_time.text;
    })
    .catch(err => {
      console.log(err);
    });
}
```

5.从父组件管理组件状态

```
React.useEffect(() => {
  axios({
    method: "get",
    url: props.jsonUrl
  })
    .then(res => {
      const { routes } = res.data;

      // setState is part of a useState hook unseen here
      setRoute(routes[0].legs[0].departure_time.text);
    })
    .catch(err => {
      console.log(err);
    });
}, []);
```

### 之前，我们使用本地状态(即`App.vue`中的`data.isHomeScreen`)来确定用户是否看到了`Home`或`Work`子组件。但是这个价值是如何变化的呢？很棒的问题！

如果你看了 Codesandbox，我肯定你看到了`Home`和`Work`并不孤单；他们有一个兄弟姐妹叫做`Toggle`，其中有一些我们以前没有见过的有趣的标记。

`@toggled=”handleToggleChange”`是另一个 v 绑定`v-on`的简写，它告诉 Vue 触发本地方法`handleToggleChange`。

```
<Toggle @toggled="handleToggleChange"/>
```

该功能只需改变本地状态，即可在主屏幕和工作屏幕之间切换。但是什么是`toggled`？

```
methods: {
  handleToggleChange(newView) {
    this.isWorkScreen = Boolean(newView === "work");
  }
}
```

要回答这个问题，我们必须看看`Toggle`组件的内部。在模板中，有两个`ToggleButtons`，它们都有`v-on:clicked`绑定。这些基本上相当于 React 中的`onClick`道具。

这两个回调函数都切换一些本地状态，但是这里重要的部分是每个函数的最后一行:`this.$emit(...)`。

```
<template>
  <div class="toggle">
    <ToggleButton
      @clicked="workClicked"
      :class="{'toggle__button': true, 'active': workActive}"
      text="Work"
    />
    <ToggleButton
      @clicked="homeClicked"
      :class="{'toggle__button': true, 'active': homeActive}"
      text="Home"
    />
  </div>
</template>
```

注意第一个参数，`“toggled”`。本质上，这一行触发了一个事件，由于我们上面添加的`@toggled`绑定，`App`现在正在监听这个事件。

```
methods: {
  workClicked() {
    this.workActive = true;
    this.homeActive = false;
    this.$emit("toggled", "work");
  },
  homeClicked() {
    this.workActive = false;
    this.homeActive = true;
    this.$emit("toggled", "home");
  }
}
```

在 React 中，我们做了一些类似的事情，但不是监听从子组件发出的事件，而是将一个函数属性从父组件传递给子组件，并在子组件的本地事件监听器中执行该函数。

同样，这里的结果是相同的:子组件正在操纵其父组件的状态。

```
function Child(props) {
  return (
    <div>
      <button onClick={() => props.toggleView("work")}>
        Work
      </button>
      <button onClick={() => props.toggleView("home")}>
        Home
      </button>
    </div>
  );
}

function Parent() {
  const [isWorkView, setWorkView] = React.useState(true);
  return <Child toggleView={setWorkView} />;
}
```

最后…

## 出于几个原因，在 React 之外的框架中构建一个应用程序，即使是这么小的一个应用程序，也是一个有趣的尝试。我不仅获得了使用另一个框架的经验，而且自从我开始使用它以来，我第一次被迫以一种非反应的方式思考。

这也许是最重要的一点:探索其他技术来帮助我们跳出常规工具的框框进行思考是很重要的。尽管我认为我仍然更喜欢 React，但我不认为它比 Vue 更好——只是不同而已。

以下是我经历这个过程后的一些其他想法:

[LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

## 调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试反应应用的方式-[开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

像用户一样体验您的 Vue 应用

## 调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。

我更喜欢和 React 一起工作。诚然，这很可能是由于我过去的经验和对它的熟悉，但我更喜欢它的 HTML-in-JavaScript 方法，而不是 Vue 的基于模板的方法。

1.  我认为 Vue 对于来自传统 MVC 背景的开发人员来说更容易掌握，因为它与模板很相似。
2.  我很想尝试用 Vue 在更大的范围内进行构建，以便真正感受它。到目前为止，我喜欢单文件组件！
3.  I would love to try building with Vue at a larger scale in order to really get a feel for it. So far, I am loving Single-File Components!

* * *