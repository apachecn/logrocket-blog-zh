# 在 Redux - LogRocket 博客中跟踪行为状态的方法

> 原文：<https://blog.logrocket.com/tracking-action-status-in-redux/>

在从事了相当多的 [React](https://blog.logrocket.com/debug-react-applications-with-the-new-react-devtools/) 和 Redux 应用程序之后，我不禁注意到，许多人很难向用户指出一个给定的动作正在发生。

让我们考虑下面的例子:

```
class RegisterForm extends React.Component {
 state = {
   isLoading: false
 }

 async handleSubmit(event) {
   event.preventDefault();

   this.setState({
     isLoading: true,
   });

   try {
     const result = await post('localhost:8000/api/users', {
       data: {},
     });
   } catch (error) {
     // do something with the error
   }

   // do something with the result
   this.setState({ isLoading: false });
 }

 render() {
   return (
     <form onSubmit={this.handleSubmit.bind(this)} >
       <input type="text" />
       <button type="submit">Submit</button>
       {this.state.isLoading && <p>Spinner!</p>}
     </form>
   );
 }
}
```

这里我们有一个简化的 React 注册表单，当用户点击提交按钮时，它应该显示一个加载指示器——比如一个微调按钮。嗯，我们可以简单地在组件内部发出请求，并使用`setState`来跟踪它的状态，这样就很好了。

然而，这种解决方案有两个问题。首先，请求及其逻辑是在组件内部定义的；如果我们想在应用程序的其他地方使用相同的功能，我们需要重复同样的代码。

第二，如果我们想在组件外部显示微调器会怎么样？我们如何将那个组件的状态提升几个组件呢？

这就是 Redux 帮助我们的地方。

通过在我们的应用程序中到处都有一个不可变的全局状态，我们可以在状态中保存动作的状态，并在任何地方都可以使用它——因此，指示器可以在任何地方显示。让我们来看看 Redux 中常见的异步动作流。

## 我们不只是写 Redux，我们也谈论它。现在听着:

或者以后订阅

### 通常的异步动作流

## Redux 中的动作是对象，因此是同步调度的。但是多亏了各种中间件，我们可以用异步的方式调度它们。

有许多库允许我们异步分派动作——仅举几个例子，比如 [redux-thunk](https://github.com/reduxjs/redux-thunk) 、 [redux-saga](https://github.com/redux-saga/redux-saga) 和 [redux-observable](https://github.com/redux-observable/redux-observable) 。

通常的流程是这样的:首先，我们分派应该启动事情的动作(通常动作的类型以一个`_REQUEST`后缀结尾，例如`GET_USER_REQUEST`)。

然后，在我们州的某个地方，我们记下该操作是未决的，就像这样:

或者:

```
{
  isLoading: true
}
```

注意:我更喜欢 pending 这个名字，因为它并不意味着这个动作一定会加载一些东西。

```
{
  pending: true
}
```

> 然后，一旦动作完成，我们将根据结果调度以下动作之一:`GET_USER_SUCCESS`或`GET_USER_FAILURE`。

这两个动作都会将`pending`值设置为`false`并保存(在状态中的某个位置)错误或结果。

存储待定指标的最简单解决方案

## 处理动作加载状态的一种常见方法是创建以下形状的状态:

在这里我们可以看到，我们有一个用户部分，其中存储了所有与用户相关的数据。

```
{
  user: {
    isLoading: true,
    user: {
      ...
    }
    token: '...'
  }
}
```

这种解决方案只适用于最基本的应用程序，原因如下:`isLoading`到底告诉了我们什么？有许多操作可能被认为是与用户相关的，例如注册、登录和更新；使用这种解决方案，我们无法区分它们。

每个动作本身

## 处理动作挂起状态的一个更好的方法是为每个动作创建一个单独的对象。

这里有一个例子:

这样，我们可以在整个应用程序中跟踪给定动作的状态，或者在特定动作发生时识别它们。这允许我们在应用程序的多个地方显示`register`动作的状态。

```
{
  user: {
    register: {
      pending: false,
      error: null,
    },
    login: {
      pending: false,
      error: null,
    },
  }
}
```

虽然这样的状态更容易管理，但是这个解决方案仍然需要为每个动作编写大量的样板代码。让我们考虑一种不同的方法，为待定指标创建一个单独的缩减器。

创建单独的减速器

## 在 Redux 中，每个被调度的动作执行所有的 reducer，不管给定的 reducer 是否应该处理它。

通过创建一个独立的 reducer 来保存未决状态，我们可以使用`SUCCESS`和`FAILURE`动作来保存状态其他部分的错误和结果。

创建减速器

### 由于 reducer 将在每个动作上执行，我们应该过滤掉我们不感兴趣的:类型不以`_REQUEST`、`_SUCCESS`或`_FAILURE`结尾的动作。

由于我们的惯例是命名类似于`GET_USERS_REQUEST`的动作，我们可以创建一个名为`getActionName`的函数，在这个函数中，我们在`_`字符处拆分名称，删除最后一部分(`REQUEST`、`SUCCESS`或`FAILURE`，并用`_`连接其余部分。

如果`actionType`不是字符串，比如常用的`Symbol`，我们返回`null`以避免错误。

```
function getActionName(actionType) {
 if (typeof actionType !== 'string') {
   return null;
 }

 return actionType
   .split("_")
   .slice(0, -1)
   .join("_");
}
```

这样，我们就把`GET_USERS_REQUEST`变成了`GET_USERS`，这样就有了一个名字，我们可以用它来保存状态中的挂起状态。

了解 LogRocket 如何跟踪
Redux 操作&状态

#### [](https://docs.logrocket.com/reference#redux-logging)

这是减速器的代码:

首先，我们检查动作的类型是否以`_REQUEST`结尾。如果确实如此，我们在状态中创建一个新的条目，用动作的名称作为键，用`{ pending: true }`作为值。

```
const pendingReducer = (state = {}, action) => {
 const { type } = action;
 const actionName = getActionName(type);

 if (!actionName) {
   return {
     ...state,
   }
 }

 if (type.endsWith("_REQUEST")) {
   return {
     ...state,
     [actionName]: {
       pending: true
     }
   };
 }

 if (type.endsWith("_SUCCESS") || type.endsWith("_FAILURE")) {
   return {
     ...state,
     [actionName]: {
       pending: false
     }
   };
 }

 return {
   ...state
 };
};
```

然后，如果动作的类型以`_SUCCESS` 或`_FAILURE`结束，我们做同样的事情，但是这次我们将`{ pending: false }`设置为一个值。

现在，如果我们需要一个用户缩减器，我们可以这样创建它:

现在，我们不必担心在每个动作上设置`pending: true`，然后在成功/失败时将其设置回`false`。

```
const userReducer = (state = initialUserState, action) => {
if (action.type === GET_USERS_SUCCESS) {
  return {
    ...state,
    user: action.payload.user,
  };
}
if (action.type === GET_USERS_FAILURE) {
  return {
    ...state,
    user: null,
  };
}
return { ...state };
};
```

注意:我们这里没有错误处理，但是它也可以在一个单独的 reducer 中完成。

> 这里有一个现场演示供您使用:

摘要

为每个动作分配自己的状态来跟踪状态是一个依赖于大量样板代码的可伸缩解决方案。通过创建一个单独的 reducer 来处理管理状态的逻辑，我们可以减少冗余代码的数量，但是反过来，我们失去了定义一些更准确地跟踪特定动作状态所需的附加字段的灵活性。

## 使用 LogRocket 消除传统反应错误报告的噪音

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

## 自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

automatically aggregates client side errors, React error boundaries, Redux state, slow component load times, JS exceptions, frontend performance metrics, and user interactions. Then LogRocket uses machine learning to notify you of the most impactful problems affecting the most users and provides the context you need to fix it.

Focus on the React bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/react-signup-issue-free).