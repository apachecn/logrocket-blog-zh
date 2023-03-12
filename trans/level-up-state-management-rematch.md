# 通过复赛提升你的状态管理

> 原文：<https://blog.logrocket.com/level-up-state-management-rematch/>

状态管理决定了数据如何在应用程序中创建、更新和传递，是前端开发的一个重要方面。在不断变化的技术世界中，用于完成状态管理的库[并不缺乏，以至于决定使用哪一个变得令人困惑和不知所措。](https://blog.logrocket.com/react-hooks-state-management/)

在本文中，我将向您介绍 [Rematch](https://rematchjs.org) ，这是一个我已经使用了一段时间的状态管理库。如果你已经喜欢使用 Redux，那么你一定会爱上 Rematch。建立在 Redux 之上，Rematch 是 Redux 的一切，没有许多令人困惑的配置和样板。

这篇文章是为那些希望探索新的状态管理库和已经在使用 Redux 的前端开发人员准备的。我们将探索 Rematch 是如何工作的，详细说明它的特性，然后构建一个简单的待办事项应用程序来演示如何使用 Rematch。我们开始吧！

### 目录

## 什么是复赛？

复赛是建立在 Redux 上的状态管理库。根据官方文件，[复赛是没有样板文件](https://rematchjs.org/docs/)的重复最佳实践。

Redux 是一个非常棒的状态管理工具，具有优秀的开发工具，但是要在项目中使用它是相当麻烦的。你必须添加很多配置，甚至安装一些其他的助手库，比如 [Redux Thunk](https://github.com/reduxjs/redux-thunk) 来让它工作。

Rematch 使用相同的 Redux 概念，但提供了一种更简单的方式来设置中央存储和管理应用程序中的状态，这意味着不需要使用 Redux Thunk 进行配置，也不需要其他 Redux 样板，如 switch 语句和操作创建器。相反，Rematch 提供了这些现成的东西，这对前端开发人员来说绝对是个好消息。Rematch 还有一个持久化器来持久化存储数据。

在我们学习如何使用 Rematch 之前，让我们来看看它的一些很酷的特性。

## 显著的复赛特征

Rematch 构建于 Redux 之上，允许轻松迁移、代码互操作性、使用 Redux 开发工具等等。然而，与普通的 Redux 不同，Rematch 只需要一个文件就可以处理 Thunks、switch 语句、动作类型和动作创建者。因此，您不需要添加任何复杂的配置，减少了样板文件的整体大小。

复赛包括内置的副作用，支持本地 JavaScript async/await 调用外部 API。Rematch 公开了一个 API 接口，用于创建扩展其功能的自定义插件。

用 TypeScript 编写，Rematch 很容易支持 TypeScript，并为所有方法、状态和 reducers 提供了自动完成功能。最后，重匹配是框架不可知的。它与 React 配合使用效果很好，但也与 Vue、Angular 等配合使用。

## 重新比赛入门

为了理解 Rematch 的工作原理，让我们构建一个使用 Rematch 来管理状态的待办事项应用程序。首先，让我们安装一个新的 React 应用程序模板:

```
npx create-react-app my-todo

```

app 模板安装完成后，`cd`进入`my-todo`。让我们继续安装重赛:

```
npm install @rematch/core

```

我们还将使用 React Redux 的一些功能，所以让我们安装:

```
npm install react-redux

```

最后，用`npm start`启动开发服务器。

### 模型

要使用重匹配，我们首先必须定义模型。模型将状态、reducers 和异步操作集中在一个地方，显示 Redux 存储的快照以及它是如何变化的。模型是在状态管理中起核心作用的对象。

这些问题有助于您理解模型中需要包含哪些内容:

*   我的初始状态是什么？`state`
*   我如何改变状态？`reducers`
*   我如何处理异步动作？`effects with async/await`

让我们为我们的待办事项应用程序创建模型:

```
export const myTodos = {
    state: {
        1: { todo: 'Learn React' },
    },

    reducers: {
        addTodo(state, todo) {
            return {
                ...state,
                [Date.now()]: { todo }
            }
        },
        removeTodo(state, id) {
            delete state[id]
            return {
                ...state
            }
        }
    },

    effects: {
        async asyncRemoveTodo(id) {
            await new Promise(resolve => setTimeout(() => resolve(), 1000))
            this.removeTodo(id)
        }
    }
}

```

我们创建了一个包含初始状态的`state`对象，这是一个`todo`任务。我们还在`reducer`中创建了更新状态的方法。最后，我们设置了一个承诺，模拟一秒钟后解析的`effects`内的 API 调用。`effects`用于运行 API 调用之类的副作用。接下来，我们需要初始化存储。

### 用`init()`初始化 Redux 存储

通过调用`init()`方法，您构建了一个完全配置的 Redux 存储。它接受一个具有模型和插件等属性的`config`对象，返回一个具有附加重匹配功能的完全配置的 Redux 存储。

创建的存储具有 Redux 存储的所有功能，包括`dispatch()`和`subscribe()`方法。此外，Rematch 还提供定制特性，比如用于延迟加载新模型的`addModel()`方法。

让我们初始化`index.js`文件中的存储:

```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

import { Provider } from "react-redux";

import { init } from "@rematch/core";
import * as models from "./model";

const store = init({ models });

ReactDOM.render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>,
  document.getElementById('root')
);

```

注意，我们使用了 React Redux 中的`Provider`组件，这是 React 的官方 Redux UI 绑定库。`Provider`组件使得需要访问它的嵌套组件可以使用这个存储。我们还导入了模型，并用它初始化了商店。

现在，我们将创建两个新组件，一个用于显示待办事项，另一个用于创建待办事项。在`src`中创建一个名为`components`的文件夹，并在其中创建两个文件，`Todo.js`和`AddTodo.js`。

在`Todo.js`文件中，我们将访问状态以获取和显示待办事项:

```
// Todo.js
import React from 'react';
import { useSelector } from 'react-redux'

function Todo() {

    const todosArray = useSelector((state) => {
        let todosIds = Object.keys(state.myTodos)
        return todosIds.map(id => ({
            ...state.myTodos[id],
            id,
        }))
    })

return (
        <ul>
            {
                todosArray?.map(val => {
                    return (
                        <div key={val.id}>
                            <li>{val.todo}</li>
                        </div>
                    )
                })
            }
        </ul>
    )
}

export default Todo;

```

在上面的代码中，我们从 React Redux 导入了`useSelector`钩子，以便从我们的组件访问商店。我们获取状态，这是一个对象，并将其转换为一个数组，这样我们就可以轻松地遍历并显示它。

让我们继续在`AddTodo.js`组件中创建一个添加待办事项的表单:

```
// AddTodo.js

import React, { useState } from 'react'

function AddTodo() {
    const [myTodo, setMyTodo] = useState('')

    const handleChange = (event) => {
        setMyTodo(event.target.value)
    }

    return (
        <div>
            <form>
                <input type='text' placeholder='Enter todo' value={myTodo} onChange={handleChange} />
                <button type="submit">Add Todo</button>
            </form>
        </div>
    )
}

export default AddTodo;

```

### 派遣行动

您可以使用 dispatch 在模型中触发 reducers 和 effects，就像 plain Redux 一样。您可以直接调用 dispatch 或者使用`dispatch\[model][action\](payload)`简写，这允许您标准化您的操作，而无需编写操作类型或操作创建者。

让我们导入`useDispatch`钩子，并在用户提交表单时分派一个动作来创建一个`todo`:

```
// AddTodo.js

import { useDispatch } from 'react-redux'

    const dispatch = useDispatch()

    const handleSubmit = event => {
        event.preventDefault();
        dispatch.myTodos.addTodo(myTodo)
        setMyTodo('')
    }

// call the `handleSubmit` on form submit

 <form onSubmit={handleSubmit}>
          <input type='text' placeholder='Enter todo' value={myTodo} onChange={handleChange} />
           <button type="submit">Add Todo</button>
   </form>

```

`AddTodo.js`的完整代码如下所示:

```
// AddTodo.js

import React, { useState } from 'react'
import { useDispatch } from 'react-redux'

function AddTodo() {
    const [myTodo, setMyTodo] = useState('')

    const dispatch = useDispatch()

    const handleChange = (event) => {
        setMyTodo(event.target.value)
    }
    const handleSubmit = event => {
        event.preventDefault();
        dispatch.myTodos.addTodo(myTodo)
        setMyTodo('')
    }

    return (
        <div>
            <form onSubmit={handleSubmit}>
                <input type='text' placeholder='Enter todo' value={myTodo} onChange={handleChange} />
                <button type="submit">Add Todo</button>
            </form>
        </div>
    )
}

export default AddTodo;

```

真的就这么简单。让我们回到`Todo.js`来实现`delete`和`async`删除功能。像往常一样，我们将导入`useDispatch`钩子来调度`delete`动作。

下面是`Todo.js`的完整代码:

```
// Todo.js

import React from 'react';
import { useSelector, useDispatch } from 'react-redux'

function Todo() {

    const todosArray = useSelector((state) => {
        let todosIds = Object.keys(state.myTodos)
        return todosIds.map(id => ({
            ...state.myTodos[id],
            id,
        }))
    })

    const dispatch = useDispatch()

    const handleDelete = (id) => {
        dispatch.myTodos.removeTodo(id)
    }

    const handleAsyncDelete = (id) => {
        dispatch.myTodos.asyncRemoveTodo(id)
    }

    return (
        <ul>
            {
                todosArray?.map(val => {
                    return (
                        <div key={val.id}>
                            <li>{val.todo}</li>
                            <button onClick={() => handleDelete(val.id)}>Delete</button>
                            <button onClick={() => handleAsyncDelete(val.id)}>Async Delete</button>
                        </div>
                    )
                })
            }
        </ul>
    )
}

export default Todo;

```

我们可以将这两个组件导入到`App.js`中，并保存它们以便在浏览器中查看。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 结论

由于 Redux 的复杂性和繁琐性，许多前端开发人员都避免学习它。然而，Rematch 很好地抽象出了大部分工作，使得建立 Redux 商店变得容易和快速。

由于 Rematch 的极简和平易近人的 API，您可以开发美丽和健壮的应用程序，并利用 Redux 的既定功能。我希望你喜欢这篇文章！如果你有任何问题，一定要留下评论。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)