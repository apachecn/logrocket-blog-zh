# React Hooks:好的、坏的和丑陋的博客

> 原文：<https://blog.logrocket.com/react-hooks-the-good-the-bad-and-the-ugly/>

随着 [React 16.8](https://blog.logrocket.com/introducing-react-16-8-featuring-official-support-for-hooks/) 的发布，钩子突然出现，其崇高的目标是改变我们编写 React 组件的方式。尘埃落定，钩子无处不在。钩子成功了吗？

最初的营销把钩子作为摆脱类组件的一种方式。类组件的主要问题是可组合性很难。重新共享生命周期事件`componentDidMount`和朋友中包含的逻辑导致了像[高阶组件](https://reactjs.org/docs/higher-order-components.html)和`[renderProps](https://reactjs.org/docs/render-props.html)`这样的模式，它们是具有边缘情况的笨拙模式。钩子最大的优点是它们能够隔离横切关注点，并且是可组合的。

## 好人

钩子做得好的是封装状态和共享逻辑。得益于钩子，`[react-router](https://reactrouter.com/web/guides/quick-start)`和`[react-redux](https://react-redux.js.org/)`等库包拥有更简单、更干净的 API。

下面是一些使用老式 [`connect` API](https://react-redux.js.org/api/connect) 的示例代码。

```
import React from 'react';
import { Dispatch } from 'redux';
import { connect } from 'react-redux';
import { AppStore, User } from '../types';
import { actions } from '../actions/constants';
import { usersSelector } from '../selectors/users';

const mapStateToProps = (state: AppStore) => ({
  users: usersSelector(state)
});

const mapDispatchToProps = (dispatch: Dispatch) => {
  return {
    addItem: (user: User) => dispatch({ type: actions.ADD_USER, payload: user })
  }
}

const UsersContainer: React.FC<{users: User[], addItem: (user: User) => void}> = (props) => {
  return (
    <>
      <h1>HOC connect</h1>
      <div>
        {
          users.map((user) => {
            return (
              <User user={user} key={user.id} dispatchToStore={props.addItem} />
            )
          })
        }
      </div>
    </>
  )
};

export default connect(mapStateToProps, mapDispatchToProps)(UsersContainer);

```

像这样的代码臃肿且重复。输入`mapStateToProps`和`mapDispatchToProps`很烦人。

下面是为了使用钩子而重构的相同代码:

```
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { AppStore, User } from '../types';
import { actions } from '../actions/constants';

export const UsersContainer: React.FC = () => {
  const dispatch = useDispatch();
  const users: User[] = useSelector((state: AppStore) => state.users);

  return (
    <>
      <h1>Hooks</h1>
      {
        users.map((user) => {
          return (
            <User user={user} key={user.id} dispatchToStore={dispatch} />
          )
        })
      }
    </>
  )
};

```

区别是白天和黑夜。钩子提供了一个更简洁的 API。钩子还消除了将所有东西包装在一个组件中的需要，这是另一个巨大的胜利。

## 坏事

### 依赖数组

[`useEffect`钩子](https://blog.logrocket.com/guide-to-react-useeffect-hook/)接受一个函数参数和一个依赖数组作为第二个参数。

```
import React, { useEffect, useState } from 'react';

export function Home() {
  const args = ['a'];
  const [value, setValue] = useState(['b']);

  useEffect(() => {
    setValue(['c']);
  }, [args]);

  console.log('value', value);
}

```

上面的代码将导致`useEffect`钩子无限旋转，因为这个看似无害的赋值:

```
  const args = ['a'];

```

在每次新渲染时，React 将保留上一次渲染的依赖数组的副本。React 会将当前的依赖数组与前一个进行比较。使用 [`Object.is`方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is)比较每个元素，以确定`useEffect`是否应该使用新值再次运行。对象是通过引用而不是通过值进行比较的。变量`args`将在每次重新渲染时成为一个新的对象，并且在内存中有一个与上次不同的地址。

突然间，变量赋值可能会有陷阱。不幸的是，围绕依赖数组有很多很多类似的陷阱。在依赖数组中内联创建一个 arrow 函数会导致同样的命运。

当然，解决方法是使用更多的钩子:

```
import React, { useEffect, useState, useRef } from 'react';

export function Home() {
  const [value, setValue] = useState(['b']);
  const {current:a} = useRef(['a'])
  useEffect(() => {
    setValue(['c']);
  }, [a])
}

```

将标准的 JavaScript 代码打包成过多的`[useRef](https://blog.logrocket.com/usestate-vs-useref/)`、`[useMemo](https://blog.logrocket.com/rethinking-hooks-memoization/)`或`[useCallback](https://blog.logrocket.com/react-usememo-vs-usecallback-a-pragmatic-guide/)`钩子变得令人困惑和尴尬。[eslit-plugin-reactor-hooks 插件](https://www.npmjs.com/package/eslint-plugin-react-hooks)做了一个合理的工作，保持你在直截了当和狭窄，但臭虫并不少见，eslit 插件应该是一个补充，而不是强制性的。

## 丑陋的

我最近发布了一个 react 挂钩[react-aboratable-fetch](https://github.com/dagda1/cuttingedge/blob/main/packages/react-abortable-fetch/)，将`runner`函数包装在`useRef`、`useCallback`或`useMemo`的组合中并不是一个很好的体验:

```
const runner = useCallback(() => {
  task.current = run(function* (scope) {
    counter.current += 1;
    send(start);

    try {
        for (const job of fetchClient.current.jobs) {
          const {
            fetch: {
              request,
              init,
              contentType,
              onQuerySuccess = parentOnQuerySuccess,
              onQueryError = parentOnQueryError,
            },
          } = job;

          timeoutRef.current = timeout ? timeout : undefined;

// etc.
```

产生的依赖数组非常大，并且需要随着代码的改变而保持最新，这很烦人。

```
  }, [
    send,
    timeout,
    onSuccess,
    parentOnQuerySuccess,
    parentOnQueryError,
    retryAttempts,
    fetchType,
    acc,
    retryDelay,
    onError,
    abortable,
    abortController,
  ]);

```

最后，我必须小心地使用`useMemo`来记忆钩子函数的返回值，当然，还要处理另一个依赖数组:

```
const result: QueryResult<R> = useMemo(() => {
    switch (machine.value as FetchStates) {
      case 'READY':
        return {
          state: 'READY',
          run: runner,
          reset: resetable,
          abort: aborter,
          data: undefined,
          error: undefined,
          counter: counter.current,
        };
      case 'LOADING':
        return {
          state: 'LOADING',
          run: runner,
          reset: resetable,
          abort: aborter,
          data: undefined,
          error: undefined,
          counter: counter.current,
        };
      case 'SUCCEEDED':
        return {
          state: 'SUCCEEDED',
          run: runner,
          reset: resetable,
          abort: aborter,
          data: machine.context.data,
          error: undefined,
          counter: counter.current,
        };
      case 'ERROR':
        return {
          state: 'ERROR',
          error: machine.context.error,
          data: undefined,
          run: runner,
          reset: resetable,
          abort: aborter,
          counter: counter.current,
        };
    }
  }, [machine.value, machine.context.data, machine.context.error, runner, resetable, aborter]);
```

### 执行次序

钩子每次需要按照钩子的“[规则”中规定的顺序运行:](https://reactjs.org/docs/hooks-rules.html)

> 不要在循环、条件或嵌套函数中调用钩子。

看起来很奇怪，React 开发人员没有想到会在事件处理程序中执行钩子。

通常的做法是从钩子返回一个函数，这个函数可以不按照钩子的顺序执行:

```
const { run, state } = useFetch(`/api/users/1`, { executeOnMount: false });

return (
  <button
    disabled={state !== 'READY'}
    onClick={() => {
      run();
    }}
  >
    DO IT
  </button>
);

```

## 判决

前面提到的`react-redux`代码的简化是引人注目的，并且导致了优秀的净代码减少。Hooks 比以前的现有产品需要更少的代码，仅此一点就应该让 Hooks 变得显而易见。

钩子的利大于弊，但并不是压倒性的胜利。钩子是一个优雅而聪明的想法，但是在实践中使用起来会很有挑战性。手动管理依赖图并在所有正确的地方记忆可能是大多数问题的根源，这需要重新思考。生成器函数可能更适合这里，因为它们有暂停和恢复执行的漂亮、独特的能力。

闭包是陷阱和陷阱的发源地。陈旧的闭包可以引用不是最新的变量。在使用钩子时，对闭包的了解是入门的一个障碍，为了进行调试，您必须具备这方面的知识。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)