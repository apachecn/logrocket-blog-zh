# 使用本地存储状态日志博客使网络存储变得简单

> 原文：<https://blog.logrocket.com/web-storage-made-simple-use-local-storage-state/>

use-local-storage-state 包通过将`localStorage`的使用抽象到一个 React 钩子中，使得在浏览器中持久化数据变得更加容易。它可以保存数据并自动解析它们，我们可以在应用程序的多个地方使用它。

在这篇文章中，我们将看看如何使用钩子来帮助我们更简单地在 React 应用中使用`localStorage`。

## 入门指南

首先，我们通过编写以下内容来安装该软件包:

```
npm install use-local-storage-state
```

那么我们可以如下使用它:

```
import React, { useEffect } from "react";
import useLocalStorageState from "use-local-storage-state";

export default function App() {
  const [todos, setTodos] = useLocalStorageState("todos", []);

  useEffect(() => {
    setTodos(["eat", "drink"]);
  }, []);

  return (
    <div className="App">
      {todos.map((t, i) => (
        <p key={i}>{t}</p>
      ))}
    </div>
  );
}
```

我们从 use-local-storage-state 导入了`useLocalStorageState`钩子，然后将钩子放在我们的`App`组件中。为了在组件加载时设置组件的值，我们在第二个参数中使用带有空数组的`useEffect`钩子，这样它只在`App`第一次渲染时加载。一旦我们这样做了，`todos`从钩子中得到值，我们可以在返回的`div`中呈现它们。

如果我们查看浏览器控制台的`localStorage`部分，它位于 Chrome 控制台的**应用**选项卡下，我们可以看到键是`todos`，我们将其设置为`useLocalStorageState`的第一个参数。它的值`["eat", "drink"]`，就是我们用`setTodo`调用的数组。钩子为我们传递了值，而不需要我们使用`JSON.stringify`，这很方便。

## 更新`localStorage`

为了更新`localStorage`值，我们可以编写以下代码:

```
import React, { useEffect, useState } from "react";
import useLocalStorageState from "use-local-storage-state";

export default function App() {
  const [todos, setTodos] = useLocalStorageState("todos", []);
  const [todo, setTodo] = useState("");

  const onClick = () => {
    setTodos([...todos, todo]);
    setTodo("");
  };

  useEffect(() => {
    setTodos(["eat", "drink"]);
  }, []);

  return (
    <div className="App">
      <input value={todo} onChange={e => setTodo(e.target.value)} />
      <button onClick={onClick}>Create</button>
      {todos.map((t, i) => (
        <p key={i}>{t}</p>
      ))}
    </div>
  );
}
```

我们添加了一个输入元素和一个`onClick`处理程序来将条目添加到`todos`数组中。这将自动保存在`localStorage`中，因为我们调用了`useLocalStorageState`返回的`setTodos`函数。

## 删除项目

要删除一个待办事项，我们可以写:

```
import React, { useEffect, useState } from "react";
import useLocalStorageState from "use-local-storage-state";

export default function App() {
  const [todos, setTodos] = useLocalStorageState("todos", []);
  const [todo, setTodo] = useState("");

  const onClick = () => {
    setTodos([...todos, todo]);
    setTodo("");
  };

  const onDelete = index => {
    const newTodos = todos.filter((_, i) => i !== index);
    setTodos(newTodos);
  };

  useEffect(() => {
    setTodos(["eat", "drink"]);
  }, []);

  return (
    <div className="App">
      <input value={todo} onChange={e => setTodo(e.target.value)} />
      <button onClick={onClick}>Create</button>
      {todos.map((t, i) => (
        <p key={i}>
          {t} <button onClick={onDelete.bind(undefined, i)}>Delete</button>
        </p>
      ))}
    </div>
  );
}
```

我们添加了一个`onDelete`函数，它调用`todo`数组上的`filter`来返回一个没有指定索引的新数组。然后我们传递返回给`setTodos`的`newTodos`数组。

为了让`index`进入函数，我们在`Delete`按钮的`onClick`中加入了`onDelete.bind(undefined, i)`。`bind`返回一个新函数，它有我们想要传入的参数，在本例中，这些参数是我们想要删除的条目的索引。

## 编辑一个项目并在多个地方使用同一个钩子

为了添加编辑功能，我们希望创建一个组件来显示值并允许我们执行编辑。为此，我们写道:

```
import React, { useEffect, useState } from "react";
import { createLocalStorageStateHook } from "use-local-storage-state";

const useTodos = createLocalStorageStateHook("todos");

const Todo = ({ name, index, onDelete }) => {
  const [todos, setTodos] = useTodos();
  const [todo, setTodo] = useState("");
  const [editing, setEditing] = useState(false);

  const save = () => {
    const newTodos = [...todos];
    newTodos[index] = todo;
    setTodos(newTodos);
    setEditing(false);
  };

  useEffect(() => {
    setTodo(name);
  }, []);

  return (
    <div>
      {(() => {
        if (editing) {
          return (
            <p>
              <input value={todo} onChange={e => setTodo(e.target.value)} />
              <button onClick={save}>Save</button>
            </p>
          );
        } else {
          return <p>{name}</p>;
        }
      })()}
      <button onClick={() => setEditing(editing => !editing)}>Edit</button>
      <button onClick={onDelete}>Delete</button>
    </div>
  );
};

export default function App() {
  const [todos, setTodos] = useTodos();
  const [todo, setTodo] = useState("");

  const onClick = () => {
    setTodos([...todos, todo]);
    setTodo("");
  };

  const onDelete = index => {
    const newTodos = todos.filter((_, i) => i !== index);
    setTodos(newTodos);
  };

  return (
    <div className="App">
      <input value={todo} onChange={e => setTodo(e.target.value)} />
      <button onClick={onClick}>Create</button>
      {todos.map((t, i) => (
        <Todo key={i} index={i} name={t} onDelete={onDelete.bind(this, i)} />
      ))}
    </div>
  );
}
```

在上面的代码中，我们创建了一个`Todo`组件来保存待办事项，输入允许我们编辑待办事项。我们还调用了`createLocalStorageStateHook`函数，这样我们就可以在`Todo`和`App`中共享`localStorage`的内容。在`Todo`和`App`中，我们使用由`createLocalStorageStateHook`返回的`useTodo`钩子。

在`Todo`的`save`中，我们复制了一个`todos`数组，并根据`index`值设置`todo`的当前值。然后我们调用由`useTodos`返回的`setTodo`来保存新的 todo 项。用`false`调用`setEditing`禁用输入。

`Todo`中的`onDelete`函数来自`App`。我们在`onDelete`上调用`bind`，然后将它作为`onDelete`的值传入，以便用正确的索引调用它。我们现在可以点击**编辑**，输入新的待办事项值，并将新值保存到`localStorage`。

另外，请注意，我们移除了`App`中的`useEffect`钩子，这样它就可以从`localStorage`中检索项目，而不是用钩子中的值重置它们。现在我们有了一个完整的待办事项应用程序，我们可以用它来添加、编辑和删除项目。

## 结论

use-local-storage-state 包为我们提供了一种在 React 应用中使用`localStorage`的简单方法。有了它，我们可以轻松地创建、更新和删除数据。

有了这个，我们就不再需要用`JSON.parse`或者`JSON.stringify`来处理`localStorage`值了。我们要做的就是调用内置钩子返回的函数，这些函数为我们保存值。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)