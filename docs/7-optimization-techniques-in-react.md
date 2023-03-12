# React 中的 7 个优化技巧

> 原文：<https://blog.logrocket.com/7-optimization-techniques-in-react/>

积极的用户体验的一个关键因素是速度，即用户必须等待多长时间才能第一次看到你的网站或应用程序的内容。亚马逊[报告](http://robotics.stanford.edu/~ronnyk/2007IEEEComputerOnlineExperiments.pdf)每 100 毫秒加载时间销售损失 1%，沃尔玛[报告](http://infographicjournal.com/how-page-load-time-can-impact-conversions)每秒加载时间转化率提高+2%。

负面的统计数据会导致用户满意度下降，最终导致客户减少。缓解这一问题的一个解决方案是适当地优化您的应用程序。

在计算机科学中，**优化**是从一组可用的备选方案中选择最佳元素(根据某种标准)。

React 允许我们构建管理自身状态的封装组件，然后将它们组合成复杂的 ui。

这些组件构成了我们 UI 的一小部分。这意味着很多时候我们会无意中创建冗余组件，并以可能影响应用程序整体加载时间的方式构建代码。

如前所述，解决方案是优化，在这种情况下，我们可以将其描述为编写特定代码块、函数或组件的最佳方式，以实现可重用性，并减少以近乎即时的方式返回信息所需的时间。

借助一些内置的 API，如`React.Component`、`React.PureComponent`和生命周期方法 React offers，我们可以优化我们的组件，以确保我们的应用程序快速有效的加载时间。

通常，我们的组件由 [CSS](https://blog.logrocket.com/a-guide-to-css-pseudo-elements/) 、JS 和 HTML 代码组成。能够确定某些部分何时出现将对你的页面速度有很大的影响。

在本教程中，我们将学习使用这些内置 API 的各种优化方法、生命周期方法和其他一些通用技术，以确保您改进 React 代码。

> 本教程假设您对 React 有基本的了解。你可以在这里了解更多关于 React [的信息。](https://reactjs.org)

## 如何使用 React？片段，以避免向 DOM 添加额外的节点

在应用程序的某些时候，您需要返回多个元素。从一个表格列表到一组相关文本，您肯定会遇到需要返回一组数据的情况。

您的代码将如下所示:

```
// Parent.js
class Parent extends React.Component {
    render() {
        return (
            <h1>Hello there!</h1>
            <h1>Hello there again!</h1>
        )
    }
}
```

如果你使用的是 linter，你会看到错误:`JSX parent expressions must have one parent element`你将被迫把两个元素都包装在一个**父元素**中，又名 **div** ，就像这样:

```
<div>
  <h1>Hello there!</h1>
  <h1>Hello there again!</h1>
</div>
```

尽管一切正常，但是创建了一个额外的不必要的 div。这可能会导致在我们的应用程序周围创建许多无用的元素，并且在某些情况下，当我们的渲染数据以特定的顺序来自子组件时，也会导致无效的 HTML。考虑以下代码:

```
// Table.js
class Table extends React.Component {
  render() {
    return (
      <table>
        <tr>
          <Columns />
        </tr>
      </table>
    );
  }
}

class Columns extends React.Component {
  render() {
    return (
      <div>
        <td>column one</td>
        <td>column two</td>
      </div>
    );
  }
}
```

上面的代码将在我们的表组件中呈现以下内容:

```
<table>
  <tr>
    <div>
      <td>column one</td>
      <td>column two</td>
    </div>
  </tr>
</table>
```

这肯定不是预期的输出，因为它是无效的 HTML 语法。**片段**为你解决这个。我们可以将列组件重写为:

```
// columns.js
class Columns extends React.Component {
  render() {
    return (
      <React.Fragment>
        <td>column one</td>
        <td>column two</td>
      </React.Fragment>
    );
  }
}
```

现在您得到了预期的输出，更好的是没有创建额外的 DOM 节点。这可能看起来很小，但实际上页面上的元素越多，加载的时间就越长。因此，审计你的代码片段并更新它们，在必要的地方使用片段对数据进行分组，肯定会提高你的代码及其性能。点击了解更多关于片段[的信息。](https://reactjs.org/docs/fragments.html)

## 使用 React。悬念和反应。懒得以声明方式加载组件

通常，您希望仅在请求时加载应用程序的部分内容。例如，只有当点击购物车图标时才加载购物车数据，当用户滚动到一个长图像列表的底部时才加载图像，等等。

延迟加载是一种流行的优化技术，广泛用于加速应用程序的加载时间。

`React.Lazy`帮助我们按需加载组件，从而减少我们应用程序的加载时间，因为只有需要的组件才会按要求出现。

由于其简单的语法，它可以很容易地使用，没有麻烦。它将一个回调函数作为参数，该参数将使用动态`import()`语法加载组件的文件。

```
// MyComponent.js
class MyComponent extends Component{
    render() {
        return (<div>MyComponent</div>)
    }
}
const MyComponent = React.lazy(()=>import('./MyComponent.js'))
function App() {
    return (<div><MyComponent /></div>)
}
```

在幕后，在编译时，当我们的 [webpack](https://blog.logrocket.com/new-features-in-webpack-5-2559755adf5e/) 命中`React.lazy()`和`import()`语句时，它会创建一个单独的包。这个过程叫做[码分](https://reactjs.org/docs/code-splitting.html)。我们最终的应用程序将被分成多个包含 UI 块的包，它们将在需要时被加载。

## 使用反应悬念

在组件被换入时，会有一个小的时间延迟，给你的用户留下一个屏幕冻结的体验。使用`React.Suspense`向用户提供流程结果的更新或反馈。

`React.Suspense`用于包装惰性组件，以在加载组件时显示回退内容。

```
// MyComponent.js
const Mycomponent = React.lazy(()=>import('./component.js'))
function App() {
    return (
    <div>
        <Suspense fallback={<div>loading ..</div>}>
            <MyComponent />
        </Suspense>
    </div>)
}
```

现在，每当组件被加载，有一个后备文本**加载延迟。。**将被渲染。点击了解更多关于`React.Suspense`和`.Lazy`T4 的信息。

## 使用 shouldComponentUpdate()防止不必要的重新渲染

在我们的应用程序中，大多数时候，我们最终会在屏幕上显示一个组件的实例。例如，在一个博客页面上，我们可能通过一个博客文章组件显示不同的博客文章，该组件反过来也呈现类似按钮的组件。如果管理不当，按钮组件的状态更改会导致所有按钮组件重新呈现。解决这个问题的方法是使用`shouldComponentUpdate`方法。

`shouldComponentUpdate()`用于让 React 知道组件的输出是否不受当前状态或道具变化的影响。默认情况下，它会在每次状态改变时重新呈现。无论组件是否应该重新渲染，它总是返回一个布尔值作为响应。默认情况下，它总是返回 true。

调用 **shouldComponentUpdate** 方法，将 **nextProps** 作为第一个参数，将 **nextState** 作为第二个参数:

```
shouldComponentUpdate(nextProps, nextState){
    return nextProps.next !== this.props.next  
}
```

现在，如果下一个道具没有改变，就没有理由通过重新渲染来改变组件的外观。这可能看起来不是很大的改进，然而，在一个有这么多组件的应用程序中，重新渲染`shouldComponentUpdate`将有助于提高性能。

> N/B: `shouldComponentUpdate`如果设置后忘记了，可能会导致意想不到的问题，因为您的 React 组件不会正常更新。**考虑用内置的** [PureComponent](https://reactjs.org/docs/react-api.html#reactpurecomponent) 代替手写`shouldComponentUpdate()`。

## 使用 React。纯组件

React 没有在我们的组件中使用`shouldComponentUpdate`方法，而是引入了一个内置了`shouldComponentUpdate`实现的新组件，即`React.PureComponent`组件。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`React.PureComponent`类似于[的反应。组件](https://reactjs.org/docs/react-api.html#reactcomponent)。他们之间的区别在于[的反应。组件](https://reactjs.org/docs/react-api.html#reactcomponent)没有实现[shouldcomponentdupdate()](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)，但是`React.PureComponent`用一个浅属性和状态比较实现了它。

不需要额外的代码，您所需要做的就是在您的类声明中使用它:

```
// use this
class MyComponent extends React.PureComponent{
    render() {
        return (<div>MyComponent</div>)
    }
}
// instead of
class MyComponent extends React.Component{
    render() {
        return (<div>MyComponent</div>)
    }
}
```

现在，有了纯组件，我们不再需要编写:

```
shouldComponentUpdate(nextProps, nextState){
    return nextProps.next !== this.props.next  
}
```

它已经默认为我们实现了这一点。

虽然这是推荐的方法，但仅当您期望拥有简单的属性和状态时，才使用**shouldcomponentdupdate**扩展`PureComponent`，或者当您知道深层数据结构已经改变时，才使用 [forceUpdate()](https://reactjs.org/docs/react-component.html#forceupdate) 。或者，考虑使用[不可变对象](https://facebook.github.io/immutable-js/)来促进嵌套数据的快速比较。点击了解更多[。](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)

## 用 ComponentDidUnmount()移除未使用的 DOM 元素

使用 React 时，考虑从 DOM 中移除元素时会发生什么是很重要的。它们真的会消失吗？或者，即使不向用户显示，代码也只是随意放置？

周围有未使用的代码会导致一个叫做内存泄漏的问题。React 通过为我们提供`componentWillUnmount`方法为我们解决了这个问题。

`componentWillUnmount()`用于当组件从 DOM 中移除时，阻止任何未使用的代码运行。

您可以用这种方法进行几次清理，比如使定时器无效，取消网络请求，或者清理在`componentDidMount()`中创建的任何订阅。

考虑以下组件:

```
// App.js
class App extends Component {
    constructor(props) {
        super(props);
        this.state = {};
    }
    componentDidMount() {
        document.addEventListener("click", this.closeMenu);
    }
    openMenu = () => { }
    closeMenu = () => { }
    render() {
        return (
          <a href ="#" onClick = {this.closeMenu}>X</a>
        );
    }
}
```

在上面的代码中，当您单击没有`componentDidUnmount()`的 **X** 链接时，菜单被关闭，但是在组件被挂载时创建的事件监听器仍然可用。

为了解决这个问题，我们可以向组件添加一个`componentDidUnmount()`:

```
componentWillUnmount() {
   document.removeEventListener("click", this.closeMenu);
}
```

现在，当点击按钮时，事件监听器被用 **componentDidUnmount()** 方法移除。

> 注意:你**不应该调用`componentWillUnmount()`中的 setState()** ，因为组件永远不会被重新渲染。一旦组件实例被卸载，它将永远不会被再次装载。点击了解更多信息[。](https://reactjs.org/docs/react-component.html#componentwillunmount)

## 使用 React。缓存组件备忘录

加速应用程序的一种方法是实现[记忆](https://en.wikipedia.org/wiki/Memoization)。

> [记忆化](https://en.wikipedia.org/wiki/Memoization)是一种优化技术，主要用于通过存储昂贵的函数调用的结果并在相同的输入再次出现时返回缓存的结果来加速程序。

记忆化的函数速度更快，因为如果用与前一个函数相同的值调用该函数，而不是执行函数逻辑，它将从缓存中取结果。

在 React 中，组件多次改变状态并不罕见。有些组件不需要改变状态就可以存在，这种情况并不少见。如果您有几个很少改变状态的组件，您应该考虑缓存它们。

为我们提供了一个简单的 API 来实现记忆。它在 **React V16.6.0** 中可用。考虑以下组件:

```
// userDetails.js
const UserDetails = ({user}) =>{
    const {name, occupation} = user;
    return (
        <div>
            <h4>{name}</h4>
            <p>{occupation}</p>
        </div>
    )
}
```

目前，每次调用 userDetails 函数时，它都会反复执行该函数，即使这些细节很少改变。我们可以使用 **React.memo** 来缓存它:

```
export default React.memo(UserDetails)
```

仅此而已！如您所见，不需要复杂的代码。只需将您的组件包装在 **React.memo** 函数中，React 会为您处理其余的事情。

> **React.memo** 是一个[高阶组件](https://reactjs.org/docs/higher-order-components.html)。这和[的反应差不多。PureComponent](https://reactjs.org/docs/react-api.html#reactpurecomponent) 但是对于**功能组件**而不是**类**。点击了解更多[。](https://reactjs.org/docs/react-api.html#reactmemo)

## 结论

在本教程中，我们探索了几种优化 React 组件以获得更好性能的方法。我们只讨论了几个，因为在优化应用程序时有很多方法和工具。

应该根据需要优化应用程序，因为在一些简单的场景中，优化组件可能是致命的。

需要记住的一件事是你的项目的规模和复杂性，它是仅仅用于演示还是简单的用例？还是会被部署到人们每天使用的地方？如果是后者，那么是时候考虑优化你的应用程序了。你可以在这里找到更多关于 React 如何处理内部优化的信息[。有问题或知道其他优化应用的有效方法吗？在评论里说吧。编码快乐！](https://reactjs.org/docs/optimizing-performance.html)

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)