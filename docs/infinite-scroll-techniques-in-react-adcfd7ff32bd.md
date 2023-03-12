# React 中的无限滚动技术

> 原文：<https://blog.logrocket.com/infinite-scroll-techniques-in-react-adcfd7ff32bd/>

![](img/150c76036ebc88002413c648acb161b2.png)

### 介绍

**无限滚动**是一种网页设计技术，当用户向下滚动页面时，它会持续加载内容，从而消除了分页的需要。

内容通常通过向服务器发出请求来异步加载。通常这可以改善网站的用户体验。

但并不总是如此。有时候很可怕。

无限滚动技术上需要给 window 对象或者某个 div 添加一个`scroll`事件监听器。这将确定滚动何时到达 div 的底部，然后相应地执行操作。

在本教程中，我将解释在 React 中实现无限滚动的两种方法:

1.  第一种方法描述了从头开始实现一切
2.  第二种方法使用已经可用的无限卷轴库/组件

要完成本教程，需要对 React 有基本的了解。

[![](img/37fb366b01f8ba4b23129468d1953129.png)](https://logrocket.com/signup/)

### 从头开始实施

如前所述，无限滚动是将事件侦听器附加到 DOM 元素，同时观察滚动条何时到达 div 的底部。

看看下面这个组件的渲染函数:

```
render() {
return (
  <div
    className="App"
    ref="myscroll"
    style={{ height: "420px", overflow: "auto" }}
  >
    <header className="App-header">
      <h1 className="App-title">Welcome to React</h1>
    </header>
    <ul>
    </ul>
  </div>
);
}
```

假设您想在每次带有类`App`的 div 到达 div 末尾时将更多的`li`项加载到`ul`标签中，您如何处理这个问题？

首先，注意有一个对名为`myscroll`的 div 的引用，这使得使用`this.refs.myscroll`访问 React 中的元素成为可能。

在构造函数中声明一个初始状态:

```
constructor(props) {
    super(props);
    this.state = {
      items: 20,
      loading: false
    };
}
```

在这里，您声明了两种状态:`items`和`loading`。项目包含了可显示为`li`标签的项目数量，而加载状态将在无限加载器获取更多项目时显示。

接下来，创建一个呈现所有项目的函数:

```
showItems() {
    var items = [];
    for (var i = 0; i < this.state.items; i++) {
      items.push(<li key={i}>Item {i}</li>);
    }
    return items;
}
```

该函数遍历当前的商品数量，并创建一个显示商品编号的`li`标签。

现在，更新您的渲染函数以显示这些项目:

```
render() {
    return (
      <div
        className="App"
        ref="myscroll"
        style={{ height: "420px", overflow: "auto" }}
      >
        <header className="App-header">
          <h1 className="App-title">Welcome to React</h1>
        </header>
        <ul>
          {this.showItems()}
        </ul>
        {this.state.loading
          ? <p className="App-intro">
          loading ...
        </p>
          : ""}
      </div>
    );
}
```

你所拥有的是一个普通的组件，它显示了几个显示商品编号的`li`。如何给这个组件添加无限滚动？还记得那个叫`myscroll`的`ref`吗？你现在可以用它了。定义`ComponentWillMount`方法:

```
componentDidMount() {
    // Detect when scrolled to bottom.
    this.refs.myscroll.addEventListener("scroll", () => {
      if (
        this.refs.myscroll.scrollTop + this.refs.myscroll.clientHeight >=
        this.refs.myscroll.scrollHeight
      ) {
        this.loadMore();
      }
    });
}
```

在上面的方法中，一个滚动监听器被添加到引用目标 div 的`myscroll` ref 中。在这里，您使用元素的`scrollTop`属性获取滚动位置(相对于窗口顶部)，然后将其添加到`clientHeight`属性(文档的高度)。

接下来，检查这两个属性的总和是否大于或等于滚动条的高度。如果假设为真，那么已经到达了 div 的底部。然后，一个名为`loadMore`(接下来将创建)的新函数被触发。

下面是`loadMore`函数的样子:

```
loadMore() {
    this.setState({ loading: true });
    setTimeout(() => {
        this.setState({ items: this.state.items + 20, loading: false });
    }, 2000);
}
```

在这个方法中，`loading`首先被设置为`true`，因此显示加载 div。接下来，调用超时功能，在此之后，项目增加`20`，并且`loading`状态被设置回假。

然而，超时的原因是为了引起一点延迟。在您的应用程序中，您可能想对您的服务器发出一个`fetch or` `axios`调用，然后改变状态。

但是不管你的用例是什么，这个概念都是一样的。

最终组件应该是这样的:

```
import React, { Component } from "react";
    import logo from "./logo.svg";
    import "./App.css";

    class App extends Component {
      constructor(props) {
        super(props);
        this.state = {
          items: 20,
          loading: false
        };
      }
      componentDidMount() {
        // Detect when scrolled to bottom.
        this.refs.myscroll.addEventListener("scroll", () => {
          if (
            this.refs.myscroll.scrollTop + this.refs.myscroll.clientHeight >=
            this.refs.myscroll.scrollHeight
          ) {
            this.loadMore();
          }
        });
      }

      showItems() {
        var items = [];
        for (var i = 0; i < this.state.items; i++) {
          items.push(<li key={i}>Item {i}</li>);
        }
        return items;
      }

      loadMore() {
        this.setState({ loading: true });
        setTimeout(() => {
          this.setState({ items: this.state.items + 20, loading: false });
        }, 2000);
      }

      render() {
        return (
          <div
            className="App"
            ref="myscroll"
            style={{ height: "420px", overflow: "auto" }}
          >
            <header className="App-header">
              <img src={logo} className="App-logo" alt="logo" />
              <h1 className="App-title">Welcome to React</h1>
            </header>
            <ul>
              {this.showItems()}
            </ul>
            {this.state.loading
              ? <p className="App-intro">
                  loading ...
                </p>
              : ""}

          </div>
        );
      }
    }

    export default App;

```

### 使用无限卷轴库

虽然第一种方法展示了在 React 应用程序中实现无限滚动是多么容易，但是您可能不满足于自己实现事件侦听器。

你也可能只是想要一个简单的解决方案，因为你有点懒(或者，更有可能的是，时间紧迫)。

没关系，我会掩护你的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

为此，您可以使用`React-infinite-scroller`，此处可用。这里有一个如何使用它的例子:

```
import React, { Component } from "react";
    import logo from "./logo.svg";
    import "./App.css";

    import InfiniteScroll from "react-infinite-scroller";

    class Scroll2 extends Component {
      constructor(props) {
        super(props);
        this.state = {
          items: 20,
          hasMoreItems: true
        };
      }

      showItems() {
        var items = [];
        for (var i = 0; i < this.state.items; i++) {
          items.push(<li key={i}> Item {i} </li>);
        }
        return items;
      }

      loadMore() {
        if(this.state.items===200){

          this.setState({ hasMoreItems: false});
        }else{
            setTimeout(() => {
            this.setState({ items: this.state.items + 20});
        }, 2000);
        }

      }

      render() {
        return (
          <div className="App">
            <header className="App-header">
              <img src={logo} className="App-logo" alt="logo" />
              <h1 className="App-title"> Welcome to React </h1>{" "}
            </header>

            <div style={{height:'200px', overflow:'auto'}}>
              <InfiniteScroll
                loadMore={this.loadMore.bind(this)}
                hasMore={this.state.hasMoreItems}
                loader={<div className="loader"> Loading... </div>}
                useWindow={false}
              >
                {this.showItems()}{" "}
              </InfiniteScroll>{" "}
            </div>{" "}
          </div>
        );
      }
    }

    export default Scroll2;

```

看上面的代码，注意它看起来和前面的方法相似？这个代码与前一个方法的主要区别是什么？

*   这里，没有附加事件侦听器
*   这里没有引用 div，因为不需要它
*   未定义加载状态。相反，有一个`hasMoreItems`用于告诉无限滚动组件分离事件监听器
*   对`loadMore`函数的修改，一旦项目 his 200，就将`hasMoreItems`状态设置为假

### 结论

现在你有了两种不同的方法，可以让你在 React 应用中实现无限滚动。

对于那些想自己参与编写事件侦听器的人来说，您已经看到了它是多么简单和容易。

对于那些不想参与附加事件侦听器的人来说，有一种方法也适用于您。

有什么评论或意见吗？评论区就是干这个的。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)