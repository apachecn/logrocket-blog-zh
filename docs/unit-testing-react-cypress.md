# 用 React 和 Cypress 进行单元测试

> 原文：<https://blog.logrocket.com/unit-testing-react-cypress/>

每个开发人员都想发布一个无 bug 的生产应用程序。为了实现这一点，我们需要考虑如何将测试集成到我们的应用程序中。有许多测试工具、框架和测试类型可供我们使用。

Cypress 是一个现代化的自动化测试套件。这是一个基于 JavaScript 的完全开源的测试框架，由支持 BDD 和 TDD 断言风格的 [Mocha](https://docs.cypress.io/guides/references/bundled-libraries#Mocha) 和 [Chai](https://docs.cypress.io/guides/references/bundled-libraries#Chai) 等库构建。此外，如果您熟悉用 JavaScript 编写测试，使用 Cypress 会很容易。

Cypress 涵盖了编写测试的两种主要风格:端到端测试和单元测试。在本文中，我们将讨论 Cypress 的单元测试。我们将概述单元测试，讨论使用 Cypress 的好处，Cypress 的安装、命令和断言，运行 Cypress，并编写我们的测试。

## 什么是单元测试？

开发人员使用单元测试来验证应用程序是否运行良好。它包括独立检查应用程序的每个模块。

术语“单元”指的是应用程序的单个可测试部分。这个过程帮助测试人员和开发人员捕捉和理解开发环境中的早期错误。这样，员工就有足够的时间来修改任何缺陷代码。

不同类型的测试之间的混淆并不少见。下表涵盖了端到端测试和单元测试之间的主要区别:

| **端到端(E2E)测试** | **单元测试** |
| 用于从头到尾测试整个应用程序。 | 用于测试应用程序的各个单元。 |
| 检查应用程序的行为流。 | 检查装置的功能验证。 |
| 通常是一种昂贵的测试手段。 | 通常是一种成本有效的测试方法。 |
| 大部分是手工完成的。 | 大部分是自动化的。 |
| 测试应用程序的端到端部分。 | 模块单独测试。 |

因为 Cypress 涵盖了前面提到的两个测试领域，所以对于我们今天构建的现代应用程序来说，它是一个极好的测试选择。让我们看看 Cypress 如何帮助我们构建更好的可测试产品。

## 用 Cypress 测试的好处

总的来说，这些是我到目前为止发现的用 Cypress 测试的最大好处:

*   时间旅行调试
*   实时代码重载
*   当前和先前状态差异
*   在运行无头测试或在浏览器中测试之间的选择
*   能够对测试进行截图和录像
*   异步测试
*   浏览器内调试环境

Cypress 测试拍摄快照，并可以提供可读的错误和堆栈跟踪，以便于调试。我们也不需要向我们的测试添加睡眠超时事件，因为它有一个自动等待功能，确保测试在前进之前等待命令和断言。

我发现 Cypress 测试比传统的测试套件更容易出错，速度也更快。Cypress 支持并行执行，导入外部库，操纵对象，存根 API 调用和网络流量控制，[有牛逼的学习文档](https://docs.cypress.io/guides/overview/why-cypress#Setting-up-tests)。

此外，Cypress 可以测试我们应用程序的每一层，包括 UI、数据库、API、前端存储、跨浏览器功能、XHR 请求等等。

## 安装 Cypress

Cypress 运行在一个 Node.js 服务器上，该服务器经常与浏览器(一个测试运行程序)通信，由 Cypress 实现。它同时运行两个 iFrames，其中一个是我们的测试代码。另一个是 iFrame，它查看运行中的测试。

我们将使用以下命令从头开始创建一个 React 和 Vite 项目:

```
npm create [email protected]

```

在我们的应用程序中，让我们通过运行以下命令将 Cypress 添加到我们的 dev 依赖项中:

```
npm i cypress --save-dev

```

请确保您选择了我们可用的单元测试选项。准备就绪后，我们将生成几个文件夹:

```
├── cypress
│   ├── downloads
│   └── component
│   └── fixtures
│   └── support
├── cypress.config.js

```

当我们想要使用正在执行的测试的截图或视频记录时，将会派上用场。

是我们编写单元测试的地方。我们将创建新文件，稍后可以在 iFrame 中选择这些文件。

将加载我们需要测试的所有 JSON 数据。

`support`是我们可以添加自定义命令的地方。我们可以稍后导入它们并在我们的测试中使用它们。

`cypress.config.js`是我们可以从 Cypress 配置中动态修改配置值和环境变量的地方。

一旦测试执行完毕，Cypress 会自动关闭浏览器。我们不需要明确关闭浏览器窗口。

可以在我的 GitHub 链接[这里](https://github.com/ishan-is-me/react_cypress_unit_testing)找到一个完整的工作代码库。

## Cypress 中的命令和断言

命令和断言是 Cypress 中的重要概念，所以在我们继续之前，让我们回顾一下它们。

首先，我们有命令。Cypress 提供了一个 API 和方法来与应用程序的 UI 进行交互。我们可用的命令将有一个内置的方法，我们可以在我们的测试块中调用它。本质上，我们可以模拟用户试图执行用于创建断言的操作。

在下面的示例片段中，我们使用一个`get`命令来获取一个元素。然后我们使用`should`和一个链式断言来断言我们期望从测试中得到的东西。

```
it('sample testing ', () => {
 cy.visit('/')
 cy.get('#name').type('Elon Musk')
 cy.get('#password').type('programming-rocks')
 cy.get('#submit').click()
 cy.get('.messages--error').should('be.visible').and('contain', 'Unrecognized username or password. Forgot your password?')
})

```

断言是我们的测试块的检查点，它确认自动化测试是通过了还是失败了。柏捆柴， [jQuery](https://jquery.com/) ， [Sinon。JS](https://sinonjs.org/) 断言库。他们检查运行测试的期望的应用程序。断言的完整列表可以在这里的[文档中找到。](https://docs.cypress.io/guides/references/assertions)

## 在应用程序中运行 Cypress

如果我们能从命令行界面(CLI)打开 Cypress，那就太方便了。我们可以！为此，我们可以添加:

```
 "scripts": {
   "cypress": "cypress open"
 }

```

这将允许我们打开一个默认的 Cypress iFrame，我们可以在其中选择要执行的测试文件。我们可以用命令`npm run cypress.`来运行它

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 设置我们的示例组件

现在，我们将开始测试我们创建的一个简单的电影列表组件。它接受电影的名称，并在我们单击按钮时将它添加到我们的 UI 中。我们还可以点击电影列表并将其标记为已看过，以及取消所有已看过的电影。

```
import { useState } from 'react';

function Movielist() {
 const [userInput, setUserInput] = useState('');
 const [movieList, setMovieList] = useState('');

 const handleChange = (e) => {...}

 const handleSubmit = (e) => {...}

 const handleClick = (e) => {...}

 const handleSeen = (e) => {...}

 return (
   <div className="wrapper">
     <form onSubmit={handleSubmit}>
       <input value={userInput} type="text" onChange={handleChange} placeholder="Movie wishlist" />
       <button>+Add movies</button>
     </form>
     <div className="movieList">
       {!movieList && <span data-cy='empty' className="empty">No movies here</span>}
       <ul data-cy='movie-list'>
         {movieList && movieList.map(m => {
           return (
             <li onClick={handleClick} className={m.seen ? "strike movie-list" : "movie-list"} id={m.id} key={m.id}>
               <span>
                 {m.movie}
            'X'
               </span>
             </li>
           )
         })}
       </ul>
     </div>
     {movieList.length > 0 && <><button data-cy='clear-movie' className="outline-btn" onClick={handleSeen}>Clear seen movies</button></>}
   </div>
 )
}

export default Movielist

```

有了这个组件，我们可以开始单独测试这个组件了。

## 用 Cypress 编写单元测试

在我们的`component`文件夹中，我们将创建一个名为`Movielist.cy.js`的新文件。这将包含一个简单的测试执行，它访问我们在本地运行的应用程序。

```
import Movielist from '../../src/components/Movielist';

describe('<Movielist>', () => {
 it('Mount the component', () => {
   cy.mount(<Movielist />);
 })
})

```

上面的`import`语句是受支持的，我们也可以从 Cypress 包中为每个受支持的框架挂载函数。如果我们运行命令`npm run cypress`，我们应该看到我们的组件准备好在浏览器中测试。

```
describe('<Movielist>', () => {
 it('Component mounts', () => {
   cy.mount(<Movielist />);
 })
 it('Component mounts', () => {
   cy.mount(<Movielist />);
   cy.get('[data-cy=empty]').contains('No movies here');
 })
})

```

我们需要在我们为测试创建的每个`it()`块中安装我们的组件。为此，我们可以利用提供给我们的`beforeEach()`方法。这样看起来会更干净、更容易:

```
describe('<Movielist>', () => {
 beforeEach(() => {
   cy.mount(<Movielist />);
 });
 it('Check no movies', () => {
   cy.get('[data-cy=empty]').contains('No movies here');
 })
})

```

这看起来肯定比以前好。现在，我们将继续前进，添加一个测试来检查我们是否可以让电影愿望列表工作。

```
describe('<Movielist>', () => {
 beforeEach(() => {
   cy.mount(<Movielist />);
 });

 it('The List of movies appends', () => {
   cy.get('[data-cy=empty]').contains('No movies here');
   const formInput = cy.get('form input');
   formInput.should('have.value', '');
   formInput.type('Monster Inc.')
     .should('have.value', 'Monster Inc.');
   cy.get('form button').click();
   formInput.clear();
   formInput.type('Circle of eight')
     .should('have.value', 'Circle of eight');
   cy.get('form button').click();
   cy.get('[data-cy=movie-list]').children().should('have.length', 2);
 })
 })

```

到目前为止，我们已经通过了我们编写的所有测试，并检查了我们已经实现了组件的预期行为。

现在，我们将测试附加到该组件的最后一项功能，即使用 **Clear movie** 按钮关闭已看过的电影。这听起来类似于我们以前写过的东西，让我们看看它是什么样子的:

```
 it('uncheck movie', () => {
   const lastListitem = cy.get('[data-cy=movie-list]:nth-child(1) li:last-child');
   lastListitem.click();
   lastListitem.should('have.class', 'strike');
   cy.get('[data-cy=clear-movie]').click();
   cy.get('[data-cy=movie-list]').children().should('have.length', 1);
   cy.get('[data-cy=clear-movie]').click();
   cy.get('[data-cy=movie-list]').children().should('have.length', 1);
 })

```

我们检查了是否可以将电影标记为已看过，然后在电影名称中添加一个删除线。我们清空了看过的电影，并测试了界面中可供我们观看的电影数量。我们还测试了我们编写的组件。

拥有充足的代码测试覆盖率总是更好。您可能已经注意到，在 Cypress UI 测试中，它看起来就像一个真实的点击交互和模拟。这也是使用 Cypress 的主要好处之一:可视界面、时间旅行和其他许多好处。

测试应用程序的组件比进行端到端测试要快得多。我们的组件测试执行起来类似于在基于节点的运行器中执行的组件测试，比如 Jest 和 Mocha。

## 结论

我们做到了！我们已经成功地用 Cypress 为我们的应用程序的单个组件编写了一个单元测试。我们现在可以向我们的用户交付完全工作的、无 bug 的代码。

Cypress 是语言不可知的，所以你可以和任何其他编程语言一起使用它，而不仅仅是 JavaScript。这样，我们实现了全自动测试，解决了传统测试工具的痛点。Cypress 是前端开发人员和测试自动化工程师编写自动化 web 测试的绝佳选择。

你可以在他们的[官方文件](https://docs.cypress.io/guides/overview/why-cypress)中了解更多关于赛普拉斯的信息。它们是学习更多关于柏树及其使用方法的极好资源。

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