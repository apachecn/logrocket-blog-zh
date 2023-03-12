# 与 Immer - LogRocket Blog 反应的不变性

> 原文：<https://blog.logrocket.com/immutability-in-react-with-immer/>

我们知道 JavaScript 是一种动态、多范式、弱类型的语言。这意味着我们可以在 JavaScript 代码中应用许多不同的范例，例如，面向对象的 JavaScript、命令式 JavaScript、函数式编程 JavaScript 等。许多 JavaScript 开发人员开始在他们的应用程序中采用函数式编程范式。

创建了一些库，现在 React 的采用正在开发人员中大规模扩展和增长，不变性概念也开始被更多地使用和讨论。让我们首先理解什么是不变性，然后我们将看看如何在 React 应用程序中使用函数式编程的概念。

## 不变

在函数式编程语言中，最有趣也是最重要的概念之一是不变性。不变性的全部含义是“无法改变”，如果我们有一个对象，并想创建一个新的对象，我们应该复制实际的对象，而不是变异它。

在创建应用程序时，我们需要考虑用户，更重要的是，用户数据。在您的应用程序中创建、修改、删除和替换的所有数据都很重要，应该得到正确的观察、存储和管理。这就是为什么我们应该创建更好的标准或概念来处理我们的数据。

但是为什么我们的应用程序中要有一个不可变的代码呢？嗯，我们可以利用不变性在某些方面获益，例如:

*   可读性 —如果你有一个不可变的代码，你的应用程序对你和你的团队来说将变得更加可读，更容易理解到底发生了什么以及每段代码在执行什么
*   可维护性——有了不可变的代码，当错误发生时，你的应用程序将变得更容易调试和维护——这是我们不容易避免的，它们总是会发生——它将很容易找到错误发生的地方和出错的地方
*   更少的副作用 —这对于可维护性来说可能是更积极的一点，但是当你有一个不可变的代码时，在你的应用程序中产生负面副作用的机会就减少了。您将最终得到更易于管理的代码，并且您的应用程序中出现意外错误的机会也会减少

## 反应的不变性

在 React 应用程序中，应用程序最重要的部分是状态数据。你应该适当地关心和管理它，否则，它会引起错误，你会非常容易地丢失数据，这可能是你最糟糕的噩梦。

React 开发人员都知道我们不应该直接改变状态，而应该使用`setState`方法。但是为什么呢？

这是 React 背后的主要思想之一——跟踪变化，如果有什么变化，重新呈现组件。你不能简单地改变你的状态，因为它不会在你的组件中触发一个 render。通过使用`setState`方法，您将以一种不可变的方式创建一个新的状态，React 将知道某些事情发生了变化，并将重新呈现相应的组件。

我们在 Redux 中也有类似的行为，Redux 是 React 应用程序最著名和最常用的状态管理库。Redux 将状态表示为不可变的对象，为了改变你的状态，你应该使用纯函数传递你的新状态数据，这些纯函数被称为`reducers`。Reducers 不应该改变状态，以避免应用程序中的副作用，并确保 Redux 跟踪当前的状态数据。

我们可以看到，在 React 社区中，不变性的概念越来越多地被使用，变得越来越普遍。但是为了确保我们做的方式正确，我们可以使用一个库来完成这项工作。

## 总是（德语词）

为了更好地处理状态数据，创建了一个名为 Immer 的库来帮助我们。创建 Immer 是为了帮助我们拥有一个不可变的状态，它是一个基于[“写时复制”机制](https://www.computerhope.com/jargon/c/copy-on-write.htm)创建的库，这是一种用于在可修改的资源上实现复制操作的技术。

Immer 非常容易理解，这是 Immer 的工作方式:

1.  你有你的实际状态数据
2.  Immer 将复制您的实际状态数据，并创建一个新的临时“草稿”。这个草稿将是下一个状态数据的代理
3.  创建草稿后，Immer 将使用草稿更新您的状态数据，这是您下一个状态数据的代理
4.  简而言之，Immer 将这样处理您的状态:

![how Immer deals with state](img/7bba116048ec8a749be2336ad7784a65.png)

## 入门指南

要开始使用 Immer，您需要首先安装它:

```
yarn add immer
```

现在，我们将在组件中导入 Immer。该库导出一个名为`produce`的默认函数:

```
produce(currentState, producer: (draftState) => void): nextState
```

`produce`函数的第一个参数是我们当前的状态对象，第二个参数是一个函数，它将获取我们的`draft`状态，然后执行我们想要的更改。

让我们创建一个名为`Users`的简单组件，我们将创建一个用户列表。我们将创建一个名为`users`的简单状态，它将是一个用户数组，另一个名为`users`的状态将是一个对象。在那个对象里面，我们将有`user`的`name`:

```
this.state = {
  user: {
    name: "",
  },
  users: []
}
```

现在，让我们从 Immer 导入`produce`函数，并创建一个名为`onInputChange`的新函数。每次我们输入时，我们都会改变`user`的`name`的值。

```
onInputChange = event => {
  this.setState(produce(this.state.user, draftState => {
    draftState.user = {
      name: event.target.value
    }
  }))
}
```

React 的`setState`方法接受一个函数，所以我们传递来自 Immer 的`produce`函数，在`produce`函数内部，我们传递我们的`user`状态作为第一个参数，作为第二个参数，我们使用一个函数。在这个函数中，我们改变了`user`的`draftState`，使其等于输入值。因此，我们跟踪输入的值，并将其保存在我们的`user`状态中。

现在我们已经正确保存了用户状态，让我们每次点击按钮时提交一个新用户。我们将创建一个名为`onSubmitUser`的新函数，我们的函数如下所示:

```
onSubmitUser = () => {
  this.setState(produce(draftState => {
    draftState.users.push(this.state.user);
    draftState.user = {
      name: ""
    }
  }))
}
```

你现在可以注意到我们再次使用了`setState`，传递了我们的`produce`函数，但是现在我们只使用了`draftState`作为参数，并且我们不再使用当前状态作为参数。但是为什么呢？

嗯，Immer 有一个叫做 [curried producers](https://immerjs.github.io/immer/docs/curried-produce) 的东西，如果你把一个函数作为第一个参数传递给你的`produce`函数，它将被用于 curry。我们现在有一个“curried”函数，这意味着这个函数将接受一个状态，并调用我们更新的 draft 函数。

因此，最终，我们的整个组件将看起来像这样:

```
class Users extends Component {
  constructor(props) {
    super(props);
    this.state = {
      user: {
        name: ""
      },
      users: []
    };
  }

  onInputChange = event => {
    this.setState(
      produce(this.state.user, draftState => {
        draftState.user = {
          name: event.target.value
        };
      })
    );
  };

  onSubmitUser = () => {
    this.setState(
      produce(draftState => {
        draftState.users.push(this.state.user);
        draftState.user = {
          name: ""
        };
      })
    );
  };

  render() {
    const { users, user } = this.state;
    return (
      <div>
        <h1>Immer with React</h1>
        {users.map(user => (
          <h4>{user.name}</h4>
        ))}
        <input type="text" value={user.name} onChange={this.onInputChange} />
        <button onClick={this.onSubmitUser}>Submit</button>
      </div>
    );
  }
}
```

既然我们已经使用 Immer 和类组件创建了我们的例子，你可能会问，有可能使用 Immer 和 React 钩子吗？是的，它是！

## 大部分是钩子

`useImmer`钩子与 React 中的`useState`钩子非常相似。首先，让我们安装它:

```
yarn add use-immer
```

让我们创建一个名为`UserImmer`的新组件，在该组件中，我们将从`use-immer`导入`useImmer`钩子:

```
import React from 'react';
import { useImmer } from "use-immer";

const UserImmer = () => {
  ...
}
export default UserImmer;
```

我们的组件中有两种状态。我们的用户列表中有`users`，还有`user`:

```
const [user, setUser] = useImmer({
  name: ''
})
const [users, setUsers] = useImmer([])
```

现在，让我们创建一个与上一个例子同名的函数，`onInputChange`，在这个函数中，我们将更新我们的`user`的值:

```
const onInputChange = (user) => {
  setUser(draftState => {
    draftState.name = user
  })
}
```

现在让我们创建我们的`onSubmitUser`函数，它将在我们每次点击按钮时添加一个新用户。与前面的例子非常相似:

```
const onSubmitUser = () => {
  setUsers(draftState => {
    draftState.push(user)
  })

  setUser(draftState => {
    draftState.name = ""
  })
}
```

你可以看到我们同时使用了`setUsers`和`setUser`函数。我们首先使用`setUsers`函数将`user`添加到我们的`users`数组中。之后，我们使用`setUser`函数将`user`的`name`值重置为空字符串。

我们的整个组件将如下所示:

```
import React from 'react';
import { useImmer } from "use-immer";
const UserImmer = () => {
  const [user, setUser] = useImmer({
    name: ''
  })
  const [users, setUsers] = useImmer([])
  const onInputChange = (user: any) => {
    setUser(draftState => {
      draftState.name = user
    })
  }
  const onSubmitUser = () => {
    setUsers(draftState => {
      draftState.push(user)
    })
    setUser(draftState => {
      draftState.name = ""
    })
  }
  return (
    <div>
      <h1>Users</h1>
      {users.map((user, index) => (
        <h5 key={index}>{user.name}</h5>
      ))}
      <input
        type="text"
        onChange={e => onInputChange(e.target.value)}
        value={user.name}
      />
      <button onClick={onSubmitUser}>Submit</button>
    </div>
  )
}
export default UserImmer;
```

我们现在有一个使用 Immer 的组件，它有一个不可变的状态。这很容易开始，更容易维护，并且我们的代码可读性更好。如果您计划从 React 中的不可变性开始，并希望使您的状态不可变且更安全，Immer 是您的最佳选择。

另一件对你来说很重要的事情是你不仅可以和 React 一起使用 Immer，也可以和普通的 JavaScript 一起使用。所以，如果你打算用普通的 JavaScript 构建一个简单的应用程序，并且希望有一个不可变的状态，你可以很容易地使用 Immer。从长远来看，拥有一个更自信、写得更好、更易维护的应用程序会对你有很大帮助。

## 结论

在本文中，我们学习了 React 中的不变性，以及如何使用 Immer 获得不可变的状态——使我们的应用程序更加安全、可读和可维护。要了解更多关于 Immer 的信息，你可以查看它的[文档](https://immerjs.github.io/immer/docs/introduction)，如果你想了解这个神奇的库，你可以参加这个[课程](https://egghead.io/courses/immutable-javascript-data-structures-with-immer)。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

代替猜测错误发生的原因，或者要求用户提供截图和日志转储，LogRocket 允许您重放问题，就像它们发生在您自己的浏览器中一样，以快速了解出了什么问题。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。