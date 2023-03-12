# 使用重新组合来编写干净的高阶组件

> 原文：<https://blog.logrocket.com/using-recompose-to-write-clean-higher-order-components-3019a6daf44c/>

***编者按**:自 2018 年 10 月 25 日起[停止](https://github.com/acdlite/recompose/issues/756#issuecomment-438674573)对重组库的主动维护。笔者推荐用 [React 钩子](https://blog.logrocket.com/react-hooks-cheat-sheet-unlock-solutions-to-common-problems-af4caf699e70/)代替。*

如果您喜欢在 React 中通过使用功能组件语法创建小组件，然后使用它们作为片段来创建更大的组件，那么 [Recompose](https://github.com/acdlite/recompose) 可以帮助您对高阶组件(hoc)做同样的事情。

使用重组，可以更容易地创建小的高阶组件，这些组件可以组合成更复杂的组件。使用 Recompose 鼓励的方法，您将不再需要更多的`Class`语法来创建 React 组件。

但是在进入细节之前，让我们开始回顾一些概念…

## 高阶函数

在 JavaScript 中，我们有一种特殊类型的函数，称为高阶函数:

> 一个[高阶函数](https://en.wikipedia.org/wiki/Higher-order_function)是一个处理其他函数的函数，要么是因为它接收其他函数作为参数(在函数体的某个点执行它们)，要么是因为它在被调用时返回一个新函数，或者两者都有。

```
const sum = (a, b) => a + b
const multiplication = (a, b) => a * b

// Our Higher-Order Function
const getResultOperation = op => (a, b) => `The ${op.name} of ${a} and ${b} is ${op(a, b)}`

const getSumResult = getResultOperation(sum)
const getMultiplicationResult = getResultOperation(multiplication)

console.log( getSumResult(2, 5) ) // The sum of 2 and 5 is 7 
console.log( getMultiplicationResult(2, 5) ) // The multiplication of 2 and 5 is 10
```

在上面的例子中，`getResultOperation`接收一个函数并返回一个新函数。所以是高阶函数。

> JavaScript 中最流行的高阶函数是数组方法`[map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)`、`[filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)`或`[reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)`。它们都将一些函数作为参数传递给数组的元素，以获得结果。

## 高阶组件

在 React 中，我们有高阶函数的等价物，但是对于组件，所谓的高阶组件。

> 一个[高阶分量](https://reactjs.org/docs/higher-order-components.html)是取一个分量并返回一个新分量的函数。

高阶分量什么时候有用？嗯，主要是为了重用涉及跨组件行为的逻辑。让我们用下面的场景来解释一下。

假设我们已经有了一个组件`Button`。

```
const Button = ({ type = "primary", children, onClick }) => (
  <button className={`btn btn-${type}`} onClick={onClick}>
    {children}
  </button>
);
```

我们想在这个`Button`的基础上创建另一个`ButtonWithTrack`(同样的道具在`Button`上也可以工作，同样的样式也可以应用)，但是行为有所改进(比如记录它被点击的次数，并在按钮上显示这个值)。

为此，我们可以做…

```
import Button from "./Button";

class ButtonWithTrack extends Component {
  constructor(props) {
    super(props);
    this.state = {
      times: 0
    };
  }
  handleClick = e => {
    let { times } = this.state;
    const { onClick } = this.props;
    this.setState({ times: ++times });
    onClick && onClick();
  };
  render() {
    const { children } = this.props;
    const { times } = this.state;
    return (
      <span onClick={this.handleClick}>
        <Button type={times > 5 ? "danger" : "primary"}>
          {children} <small>{times} times clicked</small>
        </Button>
      </span>
    );
  }
}
```

我们重用了原来的`Button`，所以现在一切正常。

再来看另一个组件，`Link`:

```
const Link = ({ type = "primary", children, href, onClick }) => (
  <a style={styles} className={`badge badge-${type}`} href={href} onClick={onClick}>
    {children}
  </a>
);
```

我们想要添加与我们添加到`Button`中的行为完全相同的行为。

那怎么办呢？我们应该在两个文件中重复 90%的代码吗？或者有没有一种方法可以让我们去掉添加到`ButtonWithTrack`中的逻辑，使其可以应用于`Button`和`Link`组件？

高阶组件来救援了！！

为了解决这个问题，我们可以创建一个更高阶的组件，即一个函数，它接受一个组件，并返回该组件的增强版本，该版本具有我们想要的行为。

例如，我们可以这样做:

```
const withClickTimesTrack = WrappedComponent =>
  class extends Component {
    constructor(props) {
      super(props);
      this.state = {
        times: 0
      };
    }
    handleClick = e => {
      e.preventDefault();
      let { times } = this.state;
      const { onClick } = this.props;
      this.setState({ times: ++times });
      onClick && onClick();
    };
    render() {
      const { children, onClick, ...props } = this.props;
      const { times } = this.state;
      return (
        <span onClick={this.handleClick}>
          <WrappedComponent
            type={times > 5 ? "danger" : "primary"}
            {...props}
          >
            {children} <small>({times} times clicked)</small>
          </WrappedComponent>
        </span>
      );
    }
  };
```

因此，我们可以像这样使用`withClickTimesTrack` HOC 来简化从`Button`创建组件`ButtonWithTrack`的过程:

```
import withClickTimesTrack from "./hoc/withClickTimesTrack";

const Button = ({ type = "primary", children, onClick }) => (
  <button className={`btn btn-${type}`} onClick={onClick}>
    {children}
  </button>
);

const ButtonWithTrack = withClickTimesTrack(Button);
```

现在，我们可以轻松地将相同的增强应用到其他组件，如`Link`:

```
import withClickTimesTrack from "./hoc/withClickTimesTrack";

const Link = ({ type = "primary", children, href, onClick }) => (
  <a style={styles} className={`badge badge-${type}`} href={href} onClick={onClick}>
    {children}
  </a>
);
const LinkWithTrack = withClickTimesTrack(Link);
```

很酷，不是吗？

但是我们可以认为这个 HOC 同时添加了太多的行为(处理程序、状态和新的 UI)。

如果我们把 HOC 背后的逻辑分成更小的部分不是更好吗？

## 作曲技巧

好了，决定了！我们希望将 HOC 的这三种行为隔离开来，这样我们就可以在其他组件中独立地重用它们:

*   添加`times`状态
*   添加自定义`handleClick`
*   显示元素内部的`times`状态

为此，我们可以创建三个 hoc，每个 hoc 将添加一个特定的行为…

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`withStateTimes.js`:

```
const withStateTimes = WrappedComponent =>
  class extends Component {
    constructor(props) {
      super(props);
      this.state = {
        times: 0
      };
    }
    setTimes = (times) => {
      this.setState({ times })
    }
    render() {
      const { times } = this.state
      const { setTimes } = this
      return (
        <WrappedComponent times={times} setTimes={setTimes} { ...this.props } />
      );
    }
  };
```

`withHandlerClick.js`:

```
const withHandlerClick = WrappedComponent => props => {

  let { times, setTimes, children, onClick, ..._props } = props;

  const handleClick = e => {
    e.preventDefault();
    setTimes( ++times );
    onClick && onClick();
  };

  return (

      {children}

  );

}
```

`withDisplayTrack.js`:

```
const withDisplayTrack = WrappedComponent => props => {
  const { children, onClick, handleClick, times, ..._props } = props;
  return (
    <span onClick={handleClick}>
      <WrappedComponent
        type={times > 5 ? "danger" : "primary"}
        {..._props}
      >
        {children} <small>({times} times clicked)</small>
      </WrappedComponent>
    </span>
  )
}
```

有了这三个 hoc，我们就可以以这种方式将它们应用到我们的元素中…

```
const ButtonWithTrack = withStateTimes(withHandlerClick(withDisplayTrack(Button)));
```

这是怎么回事？嗯，`withDisplayTrack(Button)`返回一个在`withHandlerClick`调用中使用的组件，该组件也将返回一个在`withStateTimes`调用中使用的组件，该组件将返回我们的最终组件(`ButtonWithTrack`)。

正如你所看到的，这个想法很好，因为我们可以以这种方式重用我们的代码，但是创建这些 hoc 有点复杂，并且以这种方式应用它们也有点难以理解。

这有什么改进吗？

重组救援！！🙂

## 重写

什么是重组？用他们自己的话说:

> [重组](https://github.com/acdlite/recompose)是功能组件和高阶组件的 React 实用带。把它想象成反应的[洛达什](https://lodash.com/docs/4.17.10#lodash)。

因此，这是一套我们可以用来改进我们的 HOC 的组织、创建和应用的方法，鼓励使用与 HOC 的组合相结合的功能性无状态组件。

先说最常用的重新构图方法:`compose`。

通过`compose`，我们可以*将多个高阶分量组合成一个高阶分量*。

在我们的场景中，使用`compose`，我们现在可以这样表达 hoc 的应用:

```
import { compose } from "recompose";

...

const ButtonWithTrack = compose(
  withStateTimes,
  withHandlerClick,
  withDisplayTrack
)(Button)
```

更清晰易读，对吗？

我们场景中另一个有用的重组方法是`withState`。

该方法创建了一个与我们在`withStateTimes.js`中实现的行为几乎相同的特设

*   它添加了一个状态属性
*   它创建一个*处理程序*来设置这个状态属性的值
*   它允许我们设置一个初始值

所以，用*重组*，现在我们可以这样表达同样的逻辑…

```
...
import { withState } from "recompose";
const withStateTimes = withState('times', 'setTimes', 0)
...
```

真的吗？是的，真的🙂

重组的效用开始变得有意义了，对吗？

让我们继续改进我们场景的代码。让我们坐特设的`withHandlerClick`。为了改进这个特设的创建，我们可以使用 Recompose 的`withHandlers`方法。

```
import { withHandlers } from "recompose";

const withHandlerClick = withHandlers({
  handleClick: props => e => {
    let { times, onClick, setTimes } = props;
    e.preventDefault()
    setTimes( ++times );
    onClick && onClick();
  }
})
```

`withHandlers`方法获取处理程序创建者的对象映射。传递给`withHandlers`的这个对象的每个属性应该是一个高阶函数，它接受一组 props 并返回一个函数处理程序。这样，我们可以生成一个可以访问组件的`props`的处理程序。

在我们的例子中，如果我们用 [React 开发工具](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)调试代码，那么`withDisplayTrack`返回的组件显示为`Unknown`。

为了解决这个问题，我们可以使用 Recompose 的`setDisplayName` to `export` a final HOC 返回一个名为`ComponentWithDisplayTrack`的组件。

```
export default compose(
  setDisplayName('ComponentWithDisplayTrack'),
  withDisplayTrack
);
```

使用方法`lifecycle`,我们可以将生命周期方法添加到我们的函数语法组件中。

在我们的场景中，我们可以添加一个不同版本的按钮来显示待处理消息的数量。

我们可以使用一个`messages` props 创建一个 HOC 来返回按钮的不同视图:

```
import React from "react";
import { compose, setDisplayName } from "recompose";

const withDisplayMessages = WrappedComponent => props => {
  const { children, messages, loading, ..._props } = props;
  return (
    <WrappedComponent {..._props}>
      {children}
      {loading ? (
        <span className="fas fa-spinner fa-pulse"> </span>
      ) : (
        <span className="badge badge-light">{messages}</span>
      )}
    </WrappedComponent>
  );
};

export default compose(
  setDisplayName("withDisplayMessages"),
  withDisplayMessages
);
```

我们可以向我们的组件添加一个`componentDidMount`生命周期方法，它将添加:

*   当我们的假请求开始时，一个`loading`状态被设置为`true`,当它结束时，被设置为`false`
*   一个`messages`状态，这个值将被我们的假请求返回的随机数更新

这里管理的`loading`和`messages`状态都将向返回的组件添加一个新的属性，用于传播相应的值:

```
import { lifecycle } from "recompose";

const getPendingMessages = () => {
  const randomNumber = Math.ceil(Math.random() * 10);
  return new Promise(resolve => {
    setTimeout(() => resolve(randomNumber), randomNumber * 1000);
  });
};

const withDidMountStateMessages = lifecycle({
  componentDidMount() {
    this.setState({ loading: true });
    getPendingMessages().then(messages => {
      this.setState({ loading: false, messages });
    });
  }
});

export default withDidMountStateMessages;
```

有了这些新的 hoc，我们现在可以快速创建我们的新型`Button`:

```
const ButtonWithMessages = compose(
  withDidMountStateMessages, 
  withDisplayMessages
)(Button)
```

有了这些 hoc，我们就可以用很少的几行把这些新的行为转换成一个链接。我们可以添加`defaultProps`来改变链接的默认类型。

```
const LinkWithMessages = compose(
  defaultProps({ type: "info" }),
  withDidMountStateMessages,
  withDisplayMessages
)(Link);
```

## 结论

使用这些方法，我们可以通过轻松地创建另一个版本的`Button`(只是为了显示这种模式的灵活性)来完成我们的演示，该版本跟踪从 3 到 0 的点击，并添加另一个`prop`，以便我们可以在倒计时到达 0 时更改`type`。

```
const ButtonWithTrackCountdown = compose(
  withState('times', 'setTimes', 3),
  withState('type', 'setType', 'primary'),
  withHandlers({
    handleClick: props => e => {
      let { times, onClick, setTimes, setType } = props;
      e.preventDefault()
      if ( times <= 0 ) {  setType('secondary') }
      else { setTimes( --times ) }
      onClick && onClick();
    }
  }),
  withDisplayTrack
)(Button)
```

正如您所看到的，使用 Recompose 可以更容易地将逻辑委托给小的高阶组件，然后*将它们组合成更复杂的 HOC，我们可以使用它来创建组件的不同版本，重用我们的大部分代码。*

此外，重新组合不鼓励使用`Class`语法来创建组件，而鼓励使用功能性无状态组件和更高级的组件相结合。

仅使用功能组件的最重要优势是:

*   他们鼓励代码更加可重用和模块化
*   他们不鼓励庞大复杂的组件做太多的事情

基本上，一旦您了解了重组方法的工作方式，它就简化了 React 组件的开发和组织。

有更多的方法可以用来以更简单的方式产生更多的高阶分量。

在[官方回购](https://github.com/acdlite/recompose)中，你可以找到一些对你的项目有用的[重组食谱](https://github.com/acdlite/recompose/wiki/Recipes)。

此外，这里有这篇文章中使用的代码和结果的现场演示。

那么，现在你对*重新作曲*有了更多的了解……你的第一印象是什么？在创建组件时，你认为这是一个好方法吗？

我的看法是…我喜欢！我真的很喜欢 Recompose 鼓励的面向创建小而简单的片段(组件和 hoc)的模式，这些片段可以用于以易读的方式创建更复杂的片段，并且是面向函数式编程的。

嗯，那是我的看法。你呢？

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)