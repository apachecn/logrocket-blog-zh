# 使用 React Apollo 库模仿 GraphQL 请求

> 原文：<https://blog.logrocket.com/mock-graphql-requests-react-apollo-library/>

在本文中，我们将讨论如何测试 React 组件，其中组件中的数据和数据更改依赖于 GraphQL API。在开始之前，让我们快速回顾一下我们对 GraphQL 和 React 库的理解，我们将使用 React 库与 graph QL API React Apollo 进行交互。

*向前跳跃*:

## GraphQL

GraphQL 是一种灵活而高效的与 API 交互的方式，它允许客户端准确地指定他们需要什么数据，并接收带有所请求信息的响应。GraphQL 可以从客户端(如前端 web 应用程序)或服务器的角度使用。

为了在 GraphQL 中检索数据，使用了查询。例如，以下查询可用于向服务器请求待办事项列表:

```
query TodoList {
  todoList {
    id
    title
    description
  }
}

```

上面的查询将返回一个包含`id`、`title`、`description`和`date`字段的`todo`条目对象列表。

除了检索数据，GraphQL 还支持对服务器进行变更。从数据库中删除特定待办事项的简单变异示例可能如下所示:

```
mutation deleteTodo($id: ID!) {
  deleteTodo(id: $id) {
    id
  }
}

```

这种变异接受要删除的待办事项的`id`，成功后返回已删除事项的`id`。

## 反应阿波罗图书馆

由 [Apollo GraphQL](https://www.apollographql.com/) 团队构建的 Apollo Client 是一个工具包，旨在使客户端应用程序易于与 GraphQL API 通信。React Apollo 库提供了一组可以集成到 React 组件中的特定工具。

### `useQuery()`

React Apollo 提供的执行 GraphQL 查询的主要功能之一是`useQuery()`钩子。这个钩子将 GraphQL 文档作为它的第一个参数，并返回一个包含查询请求的`data`、`loading`和`error`状态的`result`对象。

例如，考虑下面的`TodoList`组件，它使用`useQuery()`钩子从我们上面分享的`TodoList`查询示例中请求数据:

```
import * as React from 'react';
import { useQuery } from "@apollo/react-hooks";

const TODO_LIST = `
  query TodoList {
    todoList {
      id
      title
      description
    }
  }
`;

export const TodoList = () => {
  const { loading, data, error } = useQuery(TODO_LIST);

  if (loading) {
    return <h2>Loading...</h2>
  }

  if (error) {
    return <h2>Uh oh. Something went wrong...</h2>
  }

  const todoItems = data.map((todo) => {
    return <li>{todo.title}</li>
  })

  return (
    <ul>{todoItems}</ul>
  )
}

```

在此示例中，组件在查询请求进行过程中显示加载消息，如果请求失败则显示错误消息，如果请求成功则显示待办事项列表。

试图在单元测试中呈现上述组件可能会由于缺少查询或其结果的上下文(`loading`、`data`、`error`等)而导致失败。).正因为如此，我们可以使用 [@apollo/react-testing](https://www.apollographql.com/docs/react/v2/api/react-testing/) 库来模拟 GraphQL 请求。模拟请求允许我们正确地测试组件，而不依赖于到 API 的实时连接。

### `MockedProvider`

`@apollo/react-testing`库包括一个名为`MockedProvider`的工具，它允许我们创建一个用于测试目的的`ApolloProvider`组件的模拟版本。`ApolloProvider`组件是一个顶级组件，它包装了我们的 React 应用程序，并在整个应用程序中提供 Apollo 客户端作为上下文。

使我们能够在测试中从我们的 GraphQL 请求中指定我们想要的确切响应，允许我们模拟这些请求，而无需实际向 API 发出网络请求。

让我们看看如何在加载、错误和成功状态下模拟 GraphQL 请求。

## 模仿 GraphQL 请求

### 装载状态

为了在加载状态模拟 GraphQL 请求，我们可以用`MockedProvider`包装组件，并提供一个空数组作为`mocks`属性的值:

```
import * as React from 'react';
import { render } from "@testing-library/react";
import { TodoList } from '../TodoList';

describe("<TodoList />", () => {
  it("renders the expected loading message when the query is loading", async () => {
    const {
      /* get query helpers*/
    } = render(
      <MockedProvider mocks={[]}>
        <TodoList></TodoList>
      </MockedProvider>
    );

    // assertions to test component under loading state
  });
});

```

假设我们使用 [Jest](https://jestjs.io/) 作为单元测试框架，使用 [react-testing-library](https://testing-library.com/docs/react-testing-library/intro/) 作为测试工具，我们可以断言组件在其标记中呈现了预期的文本:

```
import * as React from 'react';
import { render } from "@testing-library/react";
import { TodoList } from '../TodoList';

describe("<TodoList />", () => {
  it("renders the expected loading message when the query is loading", async () => {
    const { queryByText } = render(
      <MockedProvider mocks={[]}>
        <TodoList />
      </MockedProvider>
    );

    // assert the loading message is shown
    expect(queryByText('Loading...')).toBeVisible();
  });
});

```

### 错误状态

通过用`MockedProvider`包装我们的组件并提供模拟`request`和`error`(或`errors`)属性值，我们可以通过为我们的 GraphQL 请求指定准确的错误响应来模拟错误状态:

```
import * as React from 'react';
import { render } from "@testing-library/react";
import { TodoList } from '../TodoList';

const TODO_LIST = `
  query TodoList {
    todoList {
      id
      title
      description
    }
  }
`;

describe("<TodoList />", () => {
  // ...

  it('renders the expected error state', async () => {
    const todoListMock = {
      request: {
        query: TODO_LIST,
      },
      error: new Error('Network Error!'),
    };

    const { /* queries */ } = render(
      <MockedProvider mocks={[todoListMock]}>
        <TodoList></TodoList>
      </MockedProvider>,
    );
    // assertions to test component under error state
  });
});

```

我们将让我们的单元测试断言当`TodoList` GraphQL 查询因网络错误而失败时,`<TodoList />`组件正确地显示预期的错误消息:

```
import * as React from 'react';
import { render } from "@testing-library/react";
import { TodoList } from '../TodoList';

const TODO_LIST = `
  query TodoList {
    todoList {
      id
      title
      description
    }
  }
`;

describe("<TodoList />", () => {
  // ...

  it('renders the expected error state', async () => {
    const todoListMock = {
      request: {
        query: TODO_LIST,
      },
      error: new Error('Network Error!'),
    };

    const { queryByText } = render(
      <MockedProvider mocks={[todoListMock]}>
        <TodoList></TodoList>
      </MockedProvider>,
    );

    // assert the error message is shown
    expect(queryByText('Uh oh. Something went wrong...')).toBeVisible();
  });
});

```

### 成功状态

最后，为了在成功状态下测试 GraphQL 请求，我们可以使用`MockedProvider`实用程序组件来包装我们的组件，并为模拟 GraphQL 对象中的`request`和`result`属性提供模拟值。`result`属性用于在我们的测试中模拟 GraphQL 请求的预期成功结果:

```
import * as React from 'react';
import { render } from "@testing-library/react";
import { TodoList } from '../TodoList';

const TODO_LIST = `
  query TodoList {
    todoList {
      id
      title
      description
    }
  }
`;

describe("<TodoList />", () => {

  // ...

  // ...

  it('renders the expected UI when data is available', async () => {
    const todoListMock = {
      request: {
        query: TODO_LIST,
      },
      result: {
        data: {
          todos: [
            { 
              id: '1',
              title: 'Todo Item #1',
              description: 'Description for Todo Item #1',
            },
            { 
              id: '2',
              title: 'Todo Item #2',
              description: 'Description for Todo Item #2',
            }
          ]
        },
      },
    };

    const { /* queries */ } = render(
      <MockedProvider mocks={[todoListMock]}>
        <TodoList></TodoList>
      </MockedProvider>,
    );

    // assertions
    // ...
  });
});

```

GraphQL API 请求是异步的，这意味着在断言请求的结果之前，我们通常需要在测试中指定一段等待时间。React 测试库提供了`[waitFor](https://testing-library.com/docs/dom-testing-library/api-async/#waitfor)`工具来处理这种情况。

当模仿 API 调用并等待模仿承诺解决时，可以在单元测试中使用。在上面的单元测试中使用`waitFor`的一个例子是:

```
import * as React from 'react';
import { render, waitFor } from "@testing-library/react";
import { TodoList } from '../TodoList';

const TODO_LIST = `
  query TodoList {
    todoList {
      id
      title
      description
    }
  }
`;

describe("<TodoList />", () => {

  // ...

  // ...

  it('renders the expected UI when data is available', async () => {
    const todoListMock = {
      request: {
        query: TODO_LIST,
      },
      result: {
        data: {
          todos: [
            { 
              id: '1',
              title: 'Todo Item #1',
              description: 'Description for Todo Item #1',
            },
            { 
              id: '2',
              title: 'Todo Item #2',
              description: 'Description for Todo Item #2',
            }
          ]
        },
      },
    };

    const { queryByText } = render(
      <MockedProvider mocks={[todoListMock]}>
        <TodoList></TodoList>
      </MockedProvider>,
    );

    // use the waitFor utility to wait for API request to resolve
    await waitFor(() => {
      // assert the title for each todo item is visible
      expect(queryByText('Todo Item #1')).toBeVisible();
      expect(queryByText('Todo Item #2')).toBeVisible();
    });
  });
});

```

## 结论

单元测试是软件开发的一个重要方面。它验证我们的代码正在做我们期望它做的事情，并确保它与它交互的其他代码一起正确工作。

当测试与 API 通信的 React 组件时，重要的是避免在我们的单元测试中发出实际的 API 请求，以节省时间和资源。如本文所述，模拟 GraphQL API 请求允许我们有效地测试 React 组件的行为，而不会增加实际 API 请求的开销。

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