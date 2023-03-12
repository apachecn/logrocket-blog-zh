# 如何为使用 JavaScript 和 TypeScript 的代码库构建 React 组件

> 原文：<https://blog.logrocket.com/how-to-build-react-components-for-codebases-that-use-javascript-and-typescript/>

TypeScript 最近越来越受欢迎，因为它通过在运行代码之前捕捉错误并提出修复建议来帮助改善开发体验。出于这个原因，TypeScript 在防止任何运行时错误和减少 bug 方面大有作为。

TypeScript 通过扩展 JavaScript 来添加类型并在开发期间执行类型检查，因此，所有类型错误都必须在运行代码之前修复，类似于 Elm 等语言。

TypeScript 相当不错，在某些情况下，您可能希望将它插入到已经利用 JavaScript 的代码库中，这可以与您的 JavaScript 代码一起使用，或者逐渐将您的代码库迁移到 JavaScript。在本文中，我们将了解如何将 TypeScript 与 React 和 built 组件结合使用，这些组件在 TypeScript 和 JavaScript 文件中都可用。为此，我们将构建一个简单的显示组件，它接收一些数据并显示给用户。

## 创建应用程序

为了节省我们开发人员环境的配置时间，我们将使用 [create-react-app](https://create-react-app.dev/docs/getting-started/) 来引导我们的应用程序，幸运的是，它还有一个[类型脚本模板](https://create-react-app.dev/docs/adding-typescript/)，我们可以使用它来快速启动并运行。

如果您没有安装 create-react-app，请运行以下命令进行安装:

```
npm install -g create-react-app
```

安装 create-react-app 后，通过运行以下命令创建您的应用程序:

```
yarn create react-app my-app --template typescript
```

进入应用程序的目录，在终端中使用`yarn start`命令运行。

我们将在应用程序中使用 [react-router](https://reacttraining.com/react-router/web/guides/quick-start) 进行一些最少的路由，因此我们需要安装一些依赖项:

```
yarn add react-router-dom @types/react-router-dom
```

这将安装两个包，`react-router-dom`为 react-router 提供 DOM 绑定，而`@types/react-router-dom`包含了`react-router-dom`的类型定义。

现在我们有了应用程序设置的样板文件，在您的`src`文件夹中创建两个新目录，`pages`将保存我们将在应用程序中拥有的两个简单页面，`DisplayData`将存放我们的组件。

## 路由和页面

每个页面都将使用我们将要创建的相同显示组件，其中一个页面用 JavaScript 实现，另一个用 TypeScript 实现。

在`pages`文件夹中，创建两个文件,`WithTS.tsx`和`WithoutTS.jsx`,这两个文件都将包含一些功能组件，它们只是将一些数据传递到我们的可重用显示组件中。我们的 JavaScript 页面将包含一个名为`DisplayWithoutTS`的组件，我们将把它放在`WithoutTS.jsx`中，它看起来像这样:

```
import React from 'react'
import { DisplayData } from '../DisplayData'
const DisplayWithoutTS = () => {
    const info = {
        name: 'Anakin Skywalker',
        alias: [ 'Darth Vader', 'The Chosen One'],
        powers: ['Force Push', 'Force Pull', 'Force Vision'],
        rating: 10
      }
    return <DisplayData data={info}/>
}
export { DisplayWithoutTS }
```

我们的 TypeScript 实现是一个`DisplayWithTS`组件，它将位于`WithTs.tsx`文件中，并包含以下代码:

```
import React from 'react'
import { DisplayData } from '../DisplayData'
const DisplayWithTS = () => {
    const info = {
        name: 'Sheev Palpatine',
        alias: [ 'Chancellor Palpatine', 'Emperor Palpatine', 'Darth Sidious'],
        powers: ['Force Push', 'Force Pull', 'Force Vision', 'Force Lightning', 'Sith Storm', 'Essense Transfer'],
        rating: 10
      }
    return <DisplayData data={info}/>
}
export { DisplayWithTS }
```

现在我们已经建立了基础页面，下一步是建立路由。由于我们只有两条路线，我们将保持简单并编辑我们的`App.tsx`文件:

```
import React from 'react';
import './App.css';
import { BrowserRouter, Route, Switch } from 'react-router-dom'
import  { DisplayWithoutTS } from './pages/WithoutTS'
import { DisplayWithTS } from './pages/WithTS'

function App() {
  return (
    <div className="App">
      <div>
        <a href="/">With JS</a>
        <br/>
        <a href="/withts">With TS</a>
      </div>
      <br/><br/>
      <BrowserRouter>
        <Switch>
          <Route exact path='/' component={DisplayWithoutTS}/>
          <Route exact path='/withts' component={DisplayWithTS}/>
          <Route exact path='*' >
            <div>
              404 Page Not Found
            </div>
          </Route>
        </Switch>
      </BrowserRouter>
    </div>
  );
}
export default App;
```

在`BrowserRouter`组件之前我们有两个链接，这将让我们在应用程序的两个页面之间切换。我们使用`Switch`来处理路由，并通过将`404`错误与`*`路径匹配来显示所有未考虑路径的错误消息。

## 构建我们的可重用组件

在设置我们的 TypeScript 兼容性之前，我们需要首先构建我们的组件。在`DisplayData`目录下，创建两个文件，`DisplayData.jsx`和`index.ts`。

让我们在`DisplayData.jsx`中创建我们的显示组件:

```
import React from 'react'
import {shape, string, number, arrayOf} from 'prop-types'

const DisplayData = ({data}) => {
    return (
        <div>
            <div>Name: {data.name}</div>
            <div>Alias: {data.alias.join(', ')}</div>
            <div>Powers: {data.powers.join(', ')}</div>
            <div>Rating: {data.rating}</div>
        </div>
    )
}
DisplayData.propTypes = {
    data: shape({
        name: string,
        alias: arrayOf(string),
        powers: arrayOf(string),
        rating: number,
    })
}

DisplayData.defaultProps = {
    data: {
        name: '',
        alias: [],
        powers: [],
        rating: null,
    }
}
export { DisplayData }
```

我们的组件接受一个数据属性，用它来填充一个虚构角色的信息并显示给用户。我们用 prop-types 执行 prop 验证，在这种情况下，我们的组件接收一个单独的 prop，它是一个对象。我们使用`shape`来描述正确验证中对象类型。然后，我们继续填充对象内容的类型，例如分别用于文本字符串和数值的`string`和`number`。对于以数组形式出现的数据，我们使用`arrayOf`来定义数组的内容，这很好，因为它比简单地使用`array`类型声明数据提供了更多的特异性。

然后我们将这个组件导出到`index.ts`中，使它可以在其他文件中使用。

## 添加 TypeScript 兼容性

既然我们已经设置了组件并使其工作，我们需要添加一些 TypeScript 配置，以确保当在 TypeScript 文件(扩展名为`.ts`或`.tsx`的文件)中使用类型检查时，类型检查由 TypeScript 处理。为了利用 TypeScript 的类型检查功能，我们需要熟悉[类型](https://www.typescriptlang.org/docs/handbook/basic-types.html)和[接口](https://www.typescriptlang.org/docs/handbook/interfaces.html)的概念，您可以查看[这篇涵盖这两者的伟大文章](https://blog.logrocket.com/types-vs-interfaces-in-typescript/)来帮助您快速上手。类型基本上指定了我们传入的各种数据的格式。接口允许我们塑造这些值，并充当命名这些类型的角色，并且是在您的代码中定义契约以及与项目外部的代码定义契约的强大方法。我们还需要熟悉[模块](https://www.typescriptlang.org/docs/handbook/modules.html)，这些模块允许我们将添加的 TypeScript 封装到现有的 React 组件中，并允许它在 TypeScript 文件中使用时执行类型检查。

将 TypeScript 合并到组件中就像将下面的代码添加到组件文件夹中的`index.ts`文件中一样简单:

```
interface DisplayDataProps {
    data : {
        name: String,
        alias: String[],
        powers: String[],
        rating: Number,
    }
}
declare module '.'{
    export const DisplayData: React.FC<DisplayDataProps>
}
```

我们将接口声明为`DisplayDataProps`，接口中的类型定义与组件中的属性类型验证非常相似，但是更加简洁。对于对象类型，我们简单地按照对象的结构来映射它。其他基本类型的定义与`prop-types`相同，唯一的区别是类型名是大写的，即`string`将是`String`而`number`将是`Number`等等。

你可能还会注意到我们对`alias`和`powers`的类型定义，它们都是字符串数组，与我们用`prop-types`验证的略有不同，而不是`arrayOf(string)`，我们用`String[]`来声明字符串数组。这不仅比`prop-types`实现更简洁，而且当值未定义时，它还会自动返回一个空列表，这很方便，因为它为组件提供了自动默认属性。

一旦定义了我们的接口，我们需要将组件声明为 TypeScript 模块，以便在组件被导入到`.ts`或`.tsx`文件时通知 TypeScript 使用定义的接口。我们使用`React.FC` ( `React.FunctionalComponent`全称)在声明的模块中定义了我们的`DisplayData`组件，以使其返回类型更加明确，并为静态属性(如`displayName`、`propTypes`和`defaultProps`)提供类型检查和自动完成功能。你可以在[React TypeScript cheat sheet](https://github.com/typescript-cheatsheets/react-typescript-cheatsheet)上找到关于这个的更多信息。

就这样，我们准备好了！由于我们已经做了所有的前期设置，我们所要做的就是重新运行我们的应用程序。在路由之间切换应该显示由两个页面提供给应用程序的数据，如下面的演示所示。

您可以进行类型验证，以确保一切都按照预期进行，更改其中任何一项都会破坏应用程序。

## 结论

现在，您已经具备了在包含 JavaScript 或 TypeScript 文件的代码库中使用组件所需的条件。这在很多情况下都会派上用场，无论是迁移代码库还是构建兼容的组件库。TypeScript 的类型检查在开发中非常方便，这无疑是 TypeScript 值得学习的地方。如果你想看看这个应用程序的代码，你可以看看这个 [GitHub repo](https://github.com/austinroy/react-ts-component-demo/blob/master/README.md) 。

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

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.