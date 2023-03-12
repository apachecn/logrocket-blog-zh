# ResizeObserver API:示例教程

> 原文：<https://blog.logrocket.com/how-to-use-the-resizeobserver-api-a-tutorial-with-examples/>

最近，我在工作中遇到了一个具有挑战性的设计:一个顶部有一排按钮的组件。问题是，每当组件不够宽，无法容纳所有按钮时，这些动作就需要移到下拉菜单中。

随着基于组件的框架(如 React 和 Vue.js)以及原生 web 组件的日益流行，构建一个能够适应不同屏幕宽度和不同容器宽度的 UI 已经变得越来越普遍。同一个组件可能需要在一个宽的主内容区域和一个窄的侧栏中工作——跨越所有的设备。

## 什么是`ResizeObserver`？

API 是一个很好的工具，可以用来创建适应用户屏幕和容器宽度的 ui。使用`ResizeObserver`，我们可以在调整元素大小时调用一个函数，就像监听窗口`[resize](https://developer.mozilla.org/en-US/docs/Web/API/Window/resize_event)`事件一样。

`ResizeObserver`的用例可能不会立即显而易见，所以让我们看几个实际例子。

## 填充容器

对于我们的第一个例子，假设您想要在页面的英雄部分下面显示一行随机的励志照片。您只想加载填充该行所需数量的照片，并且希望在容器宽度改变时根据需要添加或删除照片。

我们可以利用`resize`事件，但是每当用户折叠侧面板时，组件的宽度可能也会改变。这就是`ResizeObserver`派上用场的地方。

参见 [CodePen](https://codepen.io) 上的[笔
resize observer-填充容器](https://codepen.io/kevinleedrum/pen/WNjvZJo)作者凯文·德拉姆([@凯文·里德姆](https://codepen.io/kevinleedrum) )
。

看看这个例子的 JavaScript，前几行设置了我们的观察者:

```
const resizeObserver = new ResizeObserver(onResize);
resizeObserver.observe(document.querySelector(".container"));

```

我们创建一个新的`ResizeObserver`，向构造函数传递一个回调函数。然后我们告诉新的观察者要观察哪个元素。

请记住，如果您遇到需要，可以用一个观察者观察多个元素。

之后，我们进入 UI 的核心逻辑:

```
const IMAGE_MAX_WIDTH = 200;
const IMAGE_MIN_WIDTH = 100;

function onResize(entries) {
  const entry = entries[0];
  const container = entry.target;
  /* Calculate how many images can fit in the container. */
  const imagesNeeded = Math.ceil(entry.contentRect.width / IMAGE_MAX_WIDTH);
  let images = container.children;

  /* Remove images as needed. */
  while (images.length > imagesNeeded) {
    images[images.length - 1].remove();
  }
  /* Add images as needed. */
  while (images.length < imagesNeeded) {
    let seed = Math.random().toString().replace(".", "");
    const newImage = document.createElement("div");
    const imageUrl = `https://picsum.photos/seed/${seed}/${IMAGE_MAX_WIDTH}`;
    newImage.style.backgroundImage = `url(${imageUrl})`;
    container.append(newImage);
  }
}

```

在定义了图像的最小和最大宽度之后(这样它们就可以填满整个宽度)，我们声明了我们的`onResize`回调。`ResizeObserver`将一组`[ResizeObserverEntry](https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserverEntry)`对象传递给我们的函数。

因为我们只观察一个元素，所以我们的数组只包含一个条目。entry 对象提供了调整后的元素的新尺寸(通过`contentRect`属性)，以及对元素本身的引用(`target`属性)。

使用我们更新的元素的新宽度，我们可以计算应该显示多少个图像，并将其与已经显示的图像数量进行比较(容器元素的`children`)。之后，就像删除元素或添加新元素一样简单。

出于演示目的，我展示了来自 [Lorem Picsum](https://picsum.photos/) 的随机图像。

## 将弹性行更改为列

我们的第二个例子解决了一个相当常见的问题:每当一行中放不下这些元素时(没有溢出或换行)，就将 flex 行的元素更改为列。

有了`ResizeObserver` API，这是完全可能的。

参见 [CodePen](https://codepen.io) 上 Kevin Drum([@ kevinleedum](https://codepen.io/kevinleedrum))
的笔 [resize observer–Flex Direction](https://codepen.io/kevinleedrum/pen/PomRrRe)。

本例中的`onResize`函数如下所示:

```
let rowWidth;

function onResize(entries) {
  const entry = entries[0];
  const container = entry.target;
  if (!rowWidth)
    rowWidth = Array.from(container.children).reduce(
      (acc, el) => getElWidth(el) + acc,
      0
    );
  const isOverflowing = rowWidth > entry.contentRect.width;
  if (isOverflowing && !container.classList.contains("container-vertical")) {
    requestAnimationFrame(() => {
      container.classList.add("container-vertical");
    });
  } else if (
    !isOverflowing &&
    container.classList.contains("container-vertical")
  ) {
    requestAnimationFrame(() => {
      container.classList.remove("container-vertical");
    });
  }
}

```

该函数将所有按钮的宽度相加，包括边距，以计算出一行中显示所有按钮所需的容器宽度。我们将这个计算出的宽度缓存在一个`rowWidth`变量中，这个变量在我们的函数之外，这样我们就不会在每次调整元素大小时浪费时间去计算它。

一旦我们知道了所有按钮所需的最小宽度，我们就可以将其与新的容器宽度进行比较，如果按钮放不下，就将行转换成列。为了实现这一点，我们只需在容器上切换一个`container-vertical`类。

## 容器查询呢？

一些可以用`ResizeObserver`解决的问题可以用 CSS [容器查询](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Container_Queries)更有效地解决，现在 Chrome Canary 也支持了。然而，容器查询的一个缺点是它们需要已知的`min-width`、`aspect-ratio`等值。

另一方面,`ResizeObserver`给了我们无限的权力来检查整个 DOM，并根据我们的需要编写复杂的逻辑。另外，它已经在所有主流浏览器中得到支持。

## 响应工具栏组件

还记得我提到的工作问题吗，我需要响应性地将按钮移动到下拉菜单中？我们最后的例子非常相似。

从概念上讲，这个例子建立在前面的例子之上，因为我们再次检查何时溢出一个容器。在这种情况下，我们需要在每次删除一个按钮时重复这一检查，看看是否还需要删除另一个按钮。

为了减少样板文件的数量，我在这个例子中使用 Vue.js，尽管这个想法应该适用于任何框架。我还使用[弹出器](https://popper.js.org/)来定位下拉菜单。

参见 [CodePen](https://codepen.io) 上 Kevin Drum([@ kevinleedum](https://codepen.io/kevinleedrum))
的 Pen [resize observer-Responsive Toolbar](https://codepen.io/kevinleedrum/pen/ExmdYNN)。

这个例子的代码要多得多，但是我们将对它进行分解。我们所有的逻辑都存在于 Vue 实例(或组件)中:

```
new Vue({
  el: "#app",
  data() {
    return {
      actions: ["Edit", "Save", "Copy", "Rename", "Share", "Delete"],
      isMenuOpen: false,
      menuActions: [] // Actions that should be shown in the menu
    };
  },

```

我们有三个组成组件“状态”的重要数据属性。

*   数组列出了我们需要在 UI 中显示的所有动作
*   boolean 是一个标志，我们可以切换显示或隐藏动作菜单
*   数组将保存一个应该在菜单中显示的动作列表(当没有足够的空间将它们显示为按钮时)

我们将根据需要在我们的`onResize`回调中更新这个数组，然后我们的 HTML 将自动更新。

```
  computed: {
    actionButtons() {
      // Actions that should be shown as buttons outside the menu
      return this.actions.filter(
        (action) => !this.menuActions.includes(action)
      );
    }
  },

```

我们使用一个名为`actionButtons`的 [Vue 计算属性](https://blog.logrocket.com/understanding-computed-properties-in-vue-js/)来生成一个应该显示为按钮的动作数组。是`menuActions`的逆。

有了这两个数组，我们的 HTML 模板可以简单地迭代它们来分别创建按钮和菜单项:

```
  <div ref="container" class="container">
    <!-- Action buttons -->
    <button v-for="action in actionButtons" :key="action" @click="doAction(action)">
      {{ action }}
    </button>
    <!-- Menu button -->
    <button ref="menuButton" v-show="menuActions.length" @click.stop="toggleMenu">
      &hellip;
    </button>
    <!-- Action menu items -->
    <div ref="menu" v-show="isMenuOpen" class="menu">
      <button v-for="action in menuActions" :key="action" @click="doAction(action)">
        {{ action }}
      </button>
    </div>
  </div>

```

如果你不熟悉 Vue [模板语法](https://vuejs.org/v2/guide/syntax.html)，不要太担心。只需知道我们正在用这两个数组中的`click`事件处理程序动态地创建按钮和菜单项，并且我们正在基于那个`isMenuOpen`布尔值显示或隐藏一个下拉菜单。

`ref`属性还允许我们从脚本中访问这些元素，而不必使用`querySelector`。

Vue 提供了几个生命周期方法，使我们能够在组件第一次加载时设置我们的观察器，并在组件被销毁时清理它:

```
  mounted() {
    // Attach ResizeObserver to the container
    resizeObserver = new ResizeObserver(this.onResize);
    resizeObserver.observe(this.$refs.container);
    // Close the menu on any click
    document.addEventListener("click", this.closeMenu);
  },
  beforeDestroy() {
    // Clean up the observer and event listener
    resizeObserver.disconnect();
    document.removeEventListener("click", this.closeMenu);
  },

```

现在有趣的部分来了，这是我们的`onResize`方法:

```
  methods: {
    onResize() {
      requestAnimationFrame(async () => {
        // Place all buttons outside the menu
        if (this.menuActions.length) {
          this.menuActions = [];
          await this.$nextTick();
        }

        const isOverflowing = () =>
          this.$refs.container.scrollWidth > this.$refs.container.offsetWidth;

        // Move buttons into the menu until the container is no longer overflowing
        while (isOverflowing() && this.actionButtons.length) {
          const lastActionButton = this.actionButtons[
            this.actionButtons.length - 1
          ];
          this.menuActions.unshift(lastActionButton);
          await this.$nextTick();
        }
      });
    },

```

您可能注意到的第一件事是，我们已经将一切都包装在对`[requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame)`的调用中。这只是限制了我们的代码运行的频率(通常每秒 60 次)。这有助于避免`ResizeObserver loop limit exceeded`控制台警告，每当您的观察者回调试图在单个动画帧中运行多次时，就会发生这种警告。

这样一来，我们的`onResize`方法就可以在必要时重置为默认状态。默认状态是所有动作都由按钮而不是菜单项来表示。

作为这个重置的一部分，它等待对`[this.$nextTick](https://vuejs.org/v2/api/#Vue-nextTick)`的调用，这个调用告诉 Vue 继续更新它的虚拟 DOM，所以我们的容器元素将回到它的最大宽度，所有的按钮都显示出来。

```
        // Place all buttons outside the menu
        if (this.menuActions.length) {
          this.menuActions = [];
          await this.$nextTick();
        }

```

现在我们有了一整行按钮，我们需要检查这一行是否溢出，这样我们就知道是否需要将任何按钮移动到我们的动作菜单中。

识别元素是否溢出的一个简单方法是比较它的`scrollWidth`和`offsetWidth`。如果`scrollWidth`更大，则元素溢出。

```
        const isOverflowing = () =>
          this.$refs.container.scrollWidth > this.$refs.container.offsetWidth;

```

我们的`onResize`方法的其余部分是一个`while`循环。在每次迭代中，我们检查容器是否溢出，如果是，我们将一个动作移入`menuActions`数组。只有当我们不再溢出容器时，或者当我们将所有动作都移到菜单中时，循环才会中断。

注意，我们在每次循环后都在等待`this.$nextTick()`，所以容器的宽度允许在变化到`this.menuActions`后更新。

```
        // Move buttons into the menu until the container is no longer overflowing
        while (isOverflowing() && this.actionButtons.length) {
          const lastActionButton = this.actionButtons[
            this.actionButtons.length - 1
          ];
          this.menuActions.unshift(lastActionButton);
          await this.$nextTick();
        }

```

这包含了我们战胜这一挑战所需的所有魔法。我们的 Vue 组件中的大部分代码与下拉菜单的行为有关，这超出了本文的范围。

## 结论

希望这些例子突出了`ResizeObserver` API 的用处，特别是在基于组件的前端开发方法中。与 [CSS 媒体查询](https://blog.logrocket.com/new-media-queries-you-need-to-know/)、崭露头角的[容器查询](https://blog.logrocket.com/what-are-container-queries-in-css/)和`resize`事件一起，它有助于在现代网络上建立响应界面的基础。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.