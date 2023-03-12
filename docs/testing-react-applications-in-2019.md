# 2019 年测试 React 应用

> 原文：<https://blog.logrocket.com/testing-react-applications-in-2019/>

测试是软件开发中一个非常有争议的概念。虽然很难让每个人都同意进行测试的最佳方式或最佳工具，甚至是测试的优先级别，但我们都同意这是任何产品的一个非常重要的方面，并且应该这样对待。

在本帖中，我们将仔细看看测试 React 应用程序的一些最佳方法。我们将在这里解释的概念显然适用于其他 JavaScript 框架，如 Vue，甚至其他语言。然而，为了精确起见，我们将在 [React](https://reactjs.org) 中进行演示。

在我们开始之前，值得注意的是这篇文章并不是对测试的全面介绍。它对您应该在 React 中进行测试的方式有更大的启发(如果您还没有的话)。

## 先决条件

在我们进一步讨论之前，本文假设:

*   [Node.js ≥v6](https://nodejs.org/) 安装在您的机器上
*   您的机器上安装了 npm
*   您机器上安装的 React 版本 16.8 或更高版本
*   安装在您机器上的 [Create-react-app](https://www.npmjs.com/package/create-react-app)
*   你对[反应](https://reactjs.org)有一个基本的了解

## 一般测试概念

如果你对[测试](https://blog.logrocket.com/experimental-node-js-testing-the-new-performance-hooks-31fcdd2a747e/)的概念完全陌生，可以这样想——测试是一种让你自动化应用程序活动的方式，而不必手动检查组件中的每个功能是否做了它应该做的事情。当然，这并不是测试的全部，但是它给了你一个开始的总体思路。

测试同样有助于代码审核。如果您有多个参与者在同一个项目上工作，测试可以帮助您为代码库的各个部分指定确切的功能。因此，检测系统中的问题并提供修复变得非常容易。

## JavaScript 测试框架

迄今为止，Jest 仍然是最受欢迎的 JavaScript 框架，在 Github 上拥有超过 27k 颗星。它由脸书建造，并继续由脸书的 Jest 团队维护和支持。Jest 是 React 推荐的零配置 javascript 测试框架，非常容易使用。它在 2019 年获得了超过 900 名贡献者的 JavaScript 社区的认可。

其他受欢迎的替代品是摩卡咖啡和 T2 茉莉。Mocha 号称是使用最多的 JavaScript 测试框架。它在 [Github](https://github.com/mochajs/mocha) 上有超过 18k 颗星星。除了庞大的生态系统，Mocha 还提供了完善的选项和大量的文档。它也非常灵活，可以进行很多扩展。

另一方面，Jasmine 已经被证明是官方推荐的测试框架。它在 Github 上有超过 [14k 颗星星，也是拥有最多资源和社区支持的最古老的测试框架之一。甚至 Jest 也是建立在 Jasmine 之上的。](https://github.com/jasmine/jasmine)

考虑到这些框架，值得注意的是没有明确的“最佳”框架。从长远来看，一切都取决于什么对你最有利。在本帖中，我们将使用 Jest 进行演示。

## 配置 Jest

默认情况下，create-react-app 带有这些配置。然而，为了灵活性和完整性，我们演示了如何为客户端手动配置带有 [webpack](https://webpack.js.org/) 的 [Jest](https://jestjs.io/) 。

步骤 1:在您的项目目录
上运行`npm install --save-dev jest`步骤 2:在您的应用程序中的`package.json`文件中添加一个测试脚本:

```
"script":{
  "test": "jest"
}
```

第 3 步:接下来，我们必须设置`.babelrc.js`文件，因为我们在`package.json`中有一个指向它的预置。Jest 会自动提取该文件，并将其应用到我们所有的测试中

```
const isTest = String(process.env.NODE_ENV ) === 'test'
module.export = {
  presets: [['env', {modules: isTest ? 'commonjs' : false}], 'react'],
  plugins: [
  'syntax-dynamic-import',
  'transform-object-rest-spread',
],
}
```

有了这个， [babel](https://blog.logrocket.com/choosing-between-babel-and-typescript-4ed1ad563e41/) 现在可以识别出我们正在通过测试，然后将我们所有的 ESmodules 移植到 CommonJS。

## 测试 React 应用

有许多方法可以测试 React 应用程序。我们将会看到其中的一些。

### 单元测试反应组件

单元测试包括独立测试软件的单个单元/组件，以验证其正确性。现在，我们如何在 React 应用程序中实现这一点呢？如果我们在一个`login.js`文件中有一个登录组件，如下所示:

```
function Login({ onSubmit }) {
  return (
    <div>
      <Form
        onSubmit={e => {
          e.preventDefault()
          const { username, password } = e.target.elements
          onSubmit({
            username: username.value,
            password: password.value,
          })
        }}
      >
        <label style={{ justifySelf: 'right' }} htmlFor="username-input">
          Username
        </label>
        <Input
          id="username-input"
          placeholder="Username..."
          name="username"
          style={{ flex: 1 }}
        />
        <label style={{ justifySelf: 'right' }} id="password-input">
          Password
        </label>
        <Input
          placeholder="Password..."
          type="password"
          name="password"
          aria-labelledby="password-input"
        />
      </Form>
    </div>
  )
}
```

上面的代码是一个简单的登录组件，我们将在一个`login.test.js`文件中进行测试。

```
import React from 'react'
import ReactDOM from 'react-dom'
import Login from '../login'
  test('calls onSubmit with the username and password when submitted',() => {
    const handleSubmit = jest.fn()
    const container = document.createElement('div')
    const form = container.querySelector('form')
    const {username, password} = form.element 
    username.value = 'Kenny'
    passwords.value = 'pineapples'

    form.dispatchEvent(new window.event('submit'))
      expect(handleSubmit).toHaveBeenCalledTimes(1)
      expect(handleSubmit).toHaveBeenCalledWith({
        username: username.value,
        password: password.value, 
      })
  ReactDOM.render(<Login onSubmit = {handleSubmit} />, container)
  })
```

测试寻找一个`div`并将它传递给一个容器变量。然后从这个容器变量中，我们通过调用它的`querySelector('form')`来创建一个表单。

接下来，我们使用对象析构从`form.element`中获取字段。因为在提交事件上调用了`dispatchEvent()`，我们可以测试表单要做什么，或者当提交事件被触发时它应该有什么值。这表明事件应该触发一次，并且在触发时应该有用户名和密码。

```
form.dispatchEvent(new window.event('submit'))
  expect(handleSubmit).toHaveBeenCalledTimes(1)
  expect(handleSubmit).toHaveBeenCalledWith({
  username: username.value,
  password: password.value, 
})

```

当然，我们可以用`npm run test`来运行测试。

### 快照测试

以前，我们能够测试一个特定的组件，以确保它们像预期的那样运行，但有一件事我们还没有做，那就是测试用户界面的结构。我们可以通过快照测试做到这一点。考虑下面的例子:

```
render(){
  <div>
    <p> Current count: {this.state.count}</p>
    <button className = 'increment'
      onClick ={this.increment}>
        + 
    </button>
    <button className = 'decrement'
      onClick ={this.decrement}>
        -
    </button>
  </div>
}
```

想象一下，如果一个组件有一个特定的格式，比如一个递增按钮在一个递减按钮之前，当这是真的时候测试通过。如果设计者改变了这种格式，实际上也就改变了呈现给 DOM 的格式。那么我们如何避免对 DOM 的渲染函数的意外更改呢？

快照测试帮助您在给定的时间获取组件的快照，并存储它之前在 DOM 上呈现的内容。因此，当您运行组件的测试时，Jest 会让您知道您渲染的内容是否与它已经有的快照不同。您可以接受更改，也可以收到更改通知。

为了执行这个测试，我们将使用`react-test-renderer`表单，它将给出我们在特定时间的测试的 JSON 表示。然后，我们将使用 Jest 存储该数据:

```
import React form 'react'
import Counter from './counter'
import {shallow} from 'enzyme'
import renderer from 'react-test-renderer'

describe('Counter component', () => {
it('matches the snapshot', () => {
  const tree = renderer.create(< Counter/>).toJson()
expect(tree).toMatchSnapshot()
})
it('start with a count of 0', () => {
  const wrapper =shallow(<Counter/>)
  const text = wwrapper.find('p').text()
  expect(tesxt).toEqual('Current count: 0')
})
it('can increment the count when the button is clicked', ()=>{
const wrapper = shallow(<Counter/>)
}
```

首先，我们得到一个将存储在 Jest 中的`counter`组件的 JSON 表示。`expect``()`方法将树作为一个参数，这就是导致与下一次重新渲染进行比较的原因。

### 集成测试

如前所述，集成测试是将单个单元作为一个组进行组合和测试。例如，如果我们有两个功能在一个上下文中一起工作，我们将使用一个集成测试来确保它们能够正确地相互交互。让我们考虑最简单的用例——在一个组件中将两个数字相加。

```
export const add = (x,y)=> x + y

export const total = (Tax,price) => {
  return "$" + add(Tax, price)
}
```

然后在`app.test.js`中我们做:

```
import {add,total} from './App' 

test('add', () => {
  expect(add(1,2)).toBe(3)
})

test('total', () =>{
  expect(total(5,20)).toBe($25);
})
```

## 推荐的测试工具

### 反应测试库

我个人认为这是测试 React 组件的一个很好的工具。它从用户的角度解决测试问题。它也非常有用，因为它处理特定的元素标签，而不是 UI 的组成。为了演示这个库是如何工作的，让我们重构之前使用这个库编写的单元测试。

```
import React from 'react'
import ReactDOM from 'react-dom'
import {render,simulate} from 'react-testing-library'
import Login from '../login'

test('calls onSubmit with the username and password when submitted',() => {
const fakeuser = generate.loginForm
const handleSubmit = jest.fn()
const {container,getByLabelText, getByText} = render(<login onSubmit= {handleSubmit}/>)
const usernameNode = getByLabelText('username')
const passwordNode= getByLabelText('password')
const formNode = container.querySelector('form')
const submitButtonNode = getByText('submit')

```

在上面的例子中，我们通过获取与元素相关联的名称来更加关注测试元素，而不是被 UI 所困扰。这是使用该文库优于其他替代物如 enzyme 和 cypress 的主要优势。

## 结论

在这篇文章中，我们看了测试 React 应用程序的各种方法以及测试的重要性。我希望这篇文章能帮助你理解测试在 React 中的重要性，并向你展示如何进行测试。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)