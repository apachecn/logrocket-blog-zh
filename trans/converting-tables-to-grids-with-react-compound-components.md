# 使用 React 复合组件将表格转换为网格

> 原文：<https://blog.logrocket.com/converting-tables-to-grids-with-react-compound-components/>

假设您必须构建一个用户管理仪表板，通过一个简单的 UI 来激活和停用用户，该 UI 包括一个表格和一个按钮来切换每个用户的活动状态。然而，并不是所有的用户都喜欢表格，那么如果我们允许用户在表格和网格布局之间动态切换会怎么样呢？虽然这可能会给应用程序添加额外的代码，但它也可以提供用户满意度，这是任何产品的主要目标。

在本文中，我们将通过利用 [React compound components](https://blog.logrocket.com/creating-compound-components-in-react-and-angular/) 模式和少量 [Context API 的](https://blog.logrocket.com/react-reference-guide-context-api/)风格来构建一个简单的`LayoutSwitch`组件，以使我们的生活更加轻松。

这是一张 GIF 图，展示了我们将在本文结束时构建的完整的`LayoutSwitch`组件。

![Layout Switches From List To Grid By Clicking Button](img/958d2867520abf130955da4759f2bef9.png)

## 入门指南

理想情况下，`LayoutSwitch`组件应该是现有 React 应用程序的一部分。为了这篇文章和实验，让我们用 [Create React App](https://blog.logrocket.com/getting-started-with-create-react-app-d93147444a27/) 样板文件创建一个新的 React 应用。

```
# using yarn
yarn create react-app react-layout-switch
# using npx
npx create-react-app react-layout-switch

```

切换到应用程序目录并启动 React 应用程序:

```
# switch to app directory
cd react-layout-switch
# start using yarn
yarn start
# start using npm
npm run start

```

这篇文章主要关注于构建`LayoutSwitch`组件；由于时间关系，我们不会在本教程中编写任何样式，所以将`App.css`和`index.css`中的样式替换为[这个 Gist 文件](https://gist.github.com/KRRISH96/496d482a2385e9e35e951bf93ed8c862)中的样式。

## 组件概述

下面是我们将要构建的组件的概述。

### 基础组件

*   `App`是根组件(在这个用例中，这将是`AdminDashboard`或类似的东西)
*   `UsersTable`是用于呈现表格布局的 JSX
*   `UsersGrid`是生成网格布局的 JSX

可以为每个业务逻辑添加更多的布局，例如 Trello 式的下拉板，用户可以在活动和非活动状态之间移动，或者使用投诉管理系统进行打开/暂停/关闭状态控制。表格或网格布局可以提供数据的快速概览，而电路板布局可以提供更多的控制。这个用例没有限制。

### 布局控制组件

*   `LayoutSwitch`是保存布局状态并控制子组件呈现的父组件
*   `Options`是布局选项按钮的包装组件
*   `Button`是每个布局选项的单独按钮
*   `Content`是所有布局组件的包装器组件，包括网格、表格等等

`Options`和`Content`将各自的组件组合在一起，提供对渲染逻辑和样式的更多控制。

## 获取数据和初始布局设置

设置`App`组件，该组件显示从 [JSON 占位符 API](https://jsonplaceholder.typicode.com/) 获取的用户列表。

将以下代码添加到`App.jsx`:

```
import React from 'react';
import { useFetch } from './hooks/useFetch';
import './App.scss';

function App() {
  const {
    data: users,
    error,
    loading,
  } = useFetch('/users');
  if (error) {
    return <h2 className="error">{error}</h2>;
  }
  return (
    <main className="container app">
      <h1>Users</h1>
      {loading && <h3>Loading Users...</h3>}
      {users !== null ? (
        <React.Fragment>
          {/** Coming Soon... Table, Grid and what not */}
        </React.Fragment>
      ) : (
        <h3>No Users Yet</h3>
      )}
    </main>
  );
}
export default App;

```

`useFetch`是一个简单的定制钩子，它接受一个端点并使用[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 来获取并返回一个具有`data`、`error`和`loading`值的对象。

在`src/hooks`下创建一个名为`useFetch.js`的新文件，并从[这个 GitHub Gist](https://gist.github.com/KRRISH96/e6e3f6524545bc29b47838215795e851) 中复制代码。或者，您可以使用 [React Query](https://blog.logrocket.com/whats-new-in-react-query-3/) 、 [SWR](https://swr.vercel.app/) 或任何其他偏好来获取数据。

现在用户数据已经可用，让我们添加`UsersTable`和`UsersGrid`以在各自的布局中呈现用户列表。

首先，添加`UsersTable.jsx`:

```
import React from 'react';

function UsersTable({ users }) {
  return (
    <div className="users-table-container">
      <table className="users-table">
        <thead className="users-table__head">
          <tr>
            <th>#</th>
            <th>Name</th>
            <th>Company</th>
            <th></th>
          </tr>
        </thead>
        <tbody className="users-table__body">
          {users.map(({ id, name, username, company }) => (
            <tr key={username}>
              <td>{id}</td>
              <td>
                <p>{name}</p>
              </td>
              <td>
                <p>{company.name}</p>
              </td>
              <td>
                <button>View Posts</button>
              </td>
            </tr>
          ))}
          {!users.length && (
            <tr>
              <td colSpan={4}>No users....</td>
            </tr>
          )}
        </tbody>
      </table>
    </div>
  );
}
export default UsersTable;

```

然后，添加`UsersGrid.jsx`:

```
import React from 'react';

function UsersGrid({ users }) {
  return (
    <div className="user-grid-container">
      <div className="user-cards__list">
        {users.map(({ id, name, username, company }) => (
          <div key={username} className="user-card card">
            <h3 className="user-name">
              <p>{name}</p>
            </h3>
            <p className="company-name">
              Company: <span>{company.name}</span>
            </p>
            <span className="user-posts-link">
              <button>View Posts</button>
            </span>
          </div>
        ))}
        {!users.length && <h3>No users....</h3>}
      </div>
    </div>
  );
}
export default UsersGrid;

```

现在，更新`App.jsx`来渲染两个组件:

```
.....
import UsersTable from './UsersTable';
import UsersGrid from './UsersGrid';

    .....
    {users !== null ? (
      <React.Fragment>
        <UsersTable users={users} />
        <UsersGrid users={users} />
      </React.Fragment>
    ) : (
    ......

```

两种布局都将一个接一个地呈现。接下来我们将继续研究`LayoutSwitch`来控制布局。

## `LayoutSwitch`及其子女

通过利用复合组件模式，可以将交换逻辑抽象为专用组件`LayoutSwitch`。

让我们首先为`activeLayout`添加一个状态，该状态呈现包装在这个根组件下的子组件。用`defaultLayout`初始化`activeLayout`，这是这个组件的子组件旁边的唯一道具。

```
import React, { useState } from 'react';

function LayoutSwitch({ children, defaultLayout }) {
  const [activeLayout, setActiveLayout] = useState(defaultLayout);

  return (
    <React.Fragment>
      {children}
    </React.Fragment>
  );
}

```

为了与子组件共享该状态并允许从子组件(在本例中是按钮组件)更新状态，我们可以使用 React 的上下文 API。

创建一个`LayoutContext`，并添加`activeLayout`和`setActiveLayout`作为提供者的值。

```
import React, { useState, createContext } from 'react';
const LayoutContext = createContext();

function LayoutSwitch({ children, defaultLayout }) {
  const [activeLayout, setActiveLayout] = useState(defaultLayout);
  const value = {
    activeLayout,
    setActiveLayout,
  };
  return (
    <LayoutContext.Provider value={value}>
      {children}
    </LayoutContext.Provider>
  );
}
```

使用`React.useContext`钩子，我们可以从其他组件的上下文中读取数据:

```
const context = useContext(LayoutContext);

```

但是对于`LayoutSwitch`之外的组件，这个上下文将不可用，也不应该被允许。因此，让我们添加自定义钩子`useLayoutContext`，以便于阅读，并在根提供者组件之外使用时引发错误。

```
function useLayoutContext() {
  const context = useContext(LayoutContext);
  if (!context) {
    throw new Error(
      `Components that require LayoutContext must be children of LayoutSwitch component`
    );
  }
  return context;
} 

```

例如:

```
# using useLayoutContext
const context = useLayoutContext();

```

既然已经设置了基本组件，让我们添加`Content`组件。这将呈现与`activeLayout`状态匹配的子组件，如`Table`或`Grid`。

`UsersTable`和`UsersGrid`将是孩子，每个孩子有一个道具`layout`，让`Content`组件与状态进行比较，并呈现一个匹配的状态。

对于给定的活动布局状态，`Content`呈现器决定呈现哪个布局组件，是`Table`还是`Grid`。

```
function Content({ children }) {
  const { activeLayout } = useLayoutContext();
  return (
    <React.Fragment>
      {children.map(child => {
        if (child.props.activeLayout !== activeLayout) return null;
        return child;
      })}
    </React.Fragment>
  );
}

```

现在，我们有了存储`activeLayout`的内容和状态。但是我们实际上如何在布局之间切换呢？

为此，添加`Button`组件，该组件从上下文中获取布局的`setActiveLayout`，并相应地更新状态。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
function Button({ children, layoutPreference, title }) {
  const { activeLayout, setActiveLayout } = useLayoutContext();
  return (
    <button
      className={`layout-btn ${
        activeLayout === layoutPreference ? 'active' : ''
      }`}
      onClick={() => setActiveLayout(layoutPreference)}
      title={title}
    >
      {children}
    </button>
  );
}

```

我们还可以添加`Options`组件来设计样式，并获得对按钮的更多控制。

```
function Options({ children }) {
  return (
    <div className="layout-switch-container">
      {children}
    </div>
  );
}

```

我们已经添加了所有必要的组件，一切看起来都很好。但还可以更好。

`LayoutSwitch`应单独负责渲染和控制与布局相关的组件。任何不相关的东西都会破坏 UI 或组件本身。

因此，让我们使用[反应`isValidElement`](https://reactjs.org/docs/react-api.html#isvalidelement) 和`[child.type.name](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/name)`来确保不相关的组件和元素不会与`LayoutSwitch`及其子元素一起呈现。

因此，要做到这一点，我们必须迭代子节点并验证每个子节点。如果它是一个有效的元素，渲染它。否则，忽略它或抛出一个错误说这是不允许的。

对于`LayoutSwitch`，只允许`Options`和`Content`组件作为子组件。

```
function LayoutSwitch({ children, ... }) {
  ....
  return (
    <LayoutContext.Provider value={value}>
      {children.map(child => {
        if (!React.isValidElement(child)) return null;
        if (![Options.name, Content.name].includes(child.type.name)) {
          throw new Error(
            `${
              child.type.name || child.type
            } cannot be rendered inside LayoutSwitch
            Valid Components are [${Options.name}, ${Content.name}]`
          );
        }
        return child;
      })}
    </LayoutContext.Provider>
  );
  ....
}

```

让我们也给`Options`组件类似的权力。只允许`Button`。

```
function Options({ children }) {
  return (
    <div className="layout-switch-container">
      {children.map(child => {
        if (!React.isValidElement(child)) return null;
        if (child.type.name !== Button.name) {
          throw new Error(
            `${
              child.type.name || child.type
            } cannot be rendered inside LayoutSwitch.Options
            Valid Components are [${Button.name}]`
          );
        }
        return child;
      })}
    </div>
  );
}

```

### `Component.name`

请注意，不要使用组件名称作为字符串来进行`child.type.name`的相等性检查。

```
# DO NOT DO THIS
child.type.name === "Options"

```

当通过`uglify/webpack`缩小代码时，组件名称在生产中不会保持不变。`Options`不会显示为“选项”；相反，它将显示为任何单个字符，例如本例中的`y`或`t`。

现在，当我们读取组件名并比较`child.type.name`和`Component.name`时，它总是为各自的子组件和组件产生相同的值。

```
child.type.name === Options.name
# y === y or t === t or whatever === whatever

```

### 导出`LayoutSwitch`和复合组件

我们可以单独导出所有组件，并导入每个组件。

```
# export individually
export { LayoutSwitch, Options, Button, Content };

# usage
import { LayoutSwitch, Options, Button, Content } from './LayoutSwitch';
<LayoutSwitch>
  <Options>...</Options>
  <Content>...</Content>
</LayoutSwitch>

```

另一个简单的方法是在`LayoutSwitch`下命名所有组件并导入一个组件。

```
# OR, export under single namespace
LayoutSwitch.Button = Button;
LayoutSwitch.Options = Options;
LayoutSwitch.Content = Content;

export default LayoutSwitch;

# usage
import LayoutSwitch from './LayoutSwitch';
<LayoutSwitch>
  <LayoutSwitch.Options>...</LayoutSwitch.Options>
  <LayoutSwitch.Content>...</LayoutSwitch.Content>
</LayoutSwitch>

```

用什么方式进出口完全取决于你。

有了我们需要编写的所有组件，我们就可以把东西连接在一起了。

## 收尾工作

是时候将`LayoutSwitch`及其子复合组件放在一起了。我建议将所有选项作为一个`[Object.freeze](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)`常量，这样子元素或任何外部因素都不能改变布局选项对象。更新后的`App`组件应该看起来像下面的代码:

```
...
import { LayoutSwitch, Options, Button, Content } from './LayoutSwitch';
import { BsTable, BsGridFill } from 'react-icons/bs';

const LAYOUT_OPTIONS = Object.freeze({ table: 'table', grid: 'grid' });

function App() {
  return (
    .....
    {users !== null ? (
      <LayoutSwitch defaultLayout={LAYOUT_OPTIONS.grid}>
        <Options>
          <Button
            layoutPreference={LAYOUT_OPTIONS.table}
            title="Table Layout"
          >
            <BsTable />
          </Button>
          <Button
            layoutPreference={LAYOUT_OPTIONS.grid}
            title="Grid Layout"
          >
            <BsGridFill />
          </Button>
          </Options>
          <Content>
            <UsersTable activeLayout={LAYOUT_OPTIONS.table} users={users} />
            <UsersGrid activeLayout={LAYOUT_OPTIONS.grid} users={users} />
          </Content>
        </LayoutSwitch>
      ) : (
    ......
  )
}

```

要保留用户选择的选项，请使用`localStorage`。虽然这超出了本教程的范围，但是您可以根据自己的喜好进行探索和坚持。

暂时就这些了！你可以在我的 [GitHub repo](https://github.com/KRRISH96/react-layout-switch-blog-example/) 中找到完整的代码，并且可以访问[这个演示的最终布局](https://react-layout-switch-example.netlify.app/)来看看用户如何从表格切换到网格布局。

感谢您的阅读。我希望这篇文章对你有所帮助，请与那些可能从中受益的人分享。再见。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)