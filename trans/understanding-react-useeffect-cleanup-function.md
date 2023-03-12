# 了解 React 的使用效果清理功能

> 原文：<https://blog.logrocket.com/understanding-react-useeffect-cleanup-function/>

React 的`useEffect`清理功能通过清理效果来避免应用程序出现不必要的行为，如内存泄漏。这样做，我们可以优化应用程序的性能。

要开始这篇文章，您应该对什么是`useEffect`有一个基本的了解，包括使用它来获取 API。本文将解释`useEffect`钩子的清理功能，希望在本文结束时，您能够轻松地使用清理功能。

## 什么是`useEffect`清理功能？

顾名思义，`useEffect`清理是 `[useEffect](https://blog.logrocket.com/guide-to-react-useeffect-hook/)` [钩子](https://blog.logrocket.com/guide-to-react-useeffect-hook/)中的一个函数[，它允许我们在组件卸载之前整理代码。当我们的代码运行并为每一次渲染重新运行时，`useEffect`也会使用清理功能清理自身。](https://blog.logrocket.com/guide-to-react-useeffect-hook/)

钩子的构建方式是我们可以在其中返回一个函数，这个返回函数就是清理发生的地方。清理功能可以防止内存泄漏，并删除一些不必要和不想要的行为。

请注意，您也不更新返回函数内部的状态:

```
useEffect(() => {
        effect
        return () => {
            cleanup
        }
    }, [input])

```

## 为什么`useEffect`清理功能有用？

如前所述，`useEffect`清理功能帮助开发人员清理影响，防止不必要的行为和优化应用程序的性能。

然而，值得注意的是,`useEffect`清理函数不仅在我们的组件想要卸载时运行，它还在下一个预定效果执行之前运行。

事实上，在我们的效果执行之后，下一个预定的效果通常是基于`dependency(array)`:

```
// The dependency is an array
useEffect( callback, dependency )

```

因此，当我们的效果依赖于我们的道具时，或者当我们设置了一些持续的东西时，我们就有理由调用清理函数。

让我们看看这个场景:假设我们通过一个用户的`id`获取一个特定用户，在获取完成之前，我们改变主意，试图获取另一个用户。在这一点上，prop，或者在这种情况下是`id`，在先前的获取请求仍在进行的同时进行更新。

然后，我们有必要使用 cleanup 函数中止获取，这样我们就不会将我们的应用程序暴露给内存泄漏。

## 我们什么时候应该使用`useEffect`清理？

假设我们有一个获取和呈现数据的 React 组件。如果我们的组件在我们的承诺解决之前被卸载，`useEffect`将尝试更新状态(在一个卸载的组件上)并发送如下所示的错误:

![Warning Error](img/8ae386aa2835405a5932bffc897e0bf4.png)

为了修复这个错误，我们使用清理功能来解决它。

根据 React 的官方文档，“React 在组件卸载时执行清理。然而…效果会在每次渲染时运行，而不是只运行一次。这就是为什么 React 还会在下次运行效果之前清除上一次渲染的效果。”

清理通常用于取消所有订阅和取消获取请求。现在，让我们写一些代码，看看我们如何能够完成这些取消。

### 清除订阅

要开始清理订阅，我们必须首先取消订阅，因为我们不想让我们的应用程序暴露于内存泄漏，我们希望优化我们的应用程序。

为了在组件卸载之前取消订阅，我们将变量`isApiSubscribed`设置为`true`，然后当我们想要卸载时，可以将其设置为`false`:

```
useEffect(() => {
    // set our variable to true
    let isApiSubscribed = true;
    axios.get(API).then((response) => {
        if (isApiSubscribed) {
            // handle success
        }
    });
    return () => {
        // cancel the subscription
        isApiSubscribed = false;
    };
}, []);

```

在上面的代码中，我们将变量`isApiSubscribed`设置为`true`，然后使用它作为条件来处理我们的成功请求。然而，当我们卸载组件时，我们将变量`isApiSubscribed`设置为`false`。

### 取消提取请求

取消获取请求调用有不同的方法:要么我们使用`[AbortController](https://blog.logrocket.com/axios-or-fetch-api/)`要么我们[使用 Axios 的取消令牌](https://github.com/axios/axios#cancellation)。

要使用`AbortController`，我们必须使用`AbortController()`构造函数创建一个控制器。然后，当我们的获取请求启动时，我们将`AbortSignal`作为请求的`option`对象中的一个选项传递。

这将控制器和信号与获取请求相关联，并允许我们随时使用`AbortController.abort()`取消它:

```
>useEffect(() => {
    const controller = new AbortController();
    const signal = controller.signal;

        fetch(API, {
            signal: signal
        })
        .then((response) => response.json())
        .then((response) => {
            // handle success
        });
    return () => {
        // cancel the request before component unmounts
        controller.abort();
    };
}, []);

```

我们可以更进一步，在 catch 中添加一个错误条件，这样我们的 fetch 请求就不会在我们中止时抛出错误。发生此错误的原因是，在卸载时，我们仍然试图在处理错误时更新状态。

我们能做的就是写一个条件，知道会得到什么样的错误；如果我们得到一个中止错误，那么我们不想更新状态:

```
useEffect(() => {
  const controller = new AbortController();
  const signal = controller.signal;

   fetch(API, {
      signal: signal
    })
    .then((response) => response.json())
    .then((response) => {
      // handle success
      console.log(response);
    })
    .catch((err) => {
      if (err.name === 'AbortError') {
        console.log('successfully aborted');
      } else {
        // handle error
      }
    });
  return () => {
    // cancel the request before component unmounts
    controller.abort();
  };
}, []);

```

现在，即使我们不耐烦了，在请求解决之前导航到另一个页面，我们也不会再收到那个错误，因为请求会在组件卸载之前中止。如果我们得到一个中止错误，状态也不会更新。

那么，让我们看看如何使用 Axios 的取消选项，Axios 取消令牌，

我们首先将来自 Axios 的`CancelToken.source()`存储在一个名为 source 的常量中，将令牌作为 Axios 选项传递，然后随时用`source.cancel()`取消请求:

```
useEffect(() => {
  const CancelToken = axios.CancelToken;
  const source = CancelToken.source();
  axios
    .get(API, {
      cancelToken: source.token
    })
    .catch((err) => {
      if (axios.isCancel(err)) {
        console.log('successfully aborted');
      } else {
        // handle error
      }
    });
  return () => {
    // cancel the request before component unmounts
    source.cancel();
  };
}, []);

```

就像我们对`AbortController`中的`AbortError`所做的一样，Axios 给了我们一个叫做`isCancel`的方法，允许我们检查错误的原因，并知道如何处理我们的错误。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

如果请求因为 Axios 源中止或取消而失败，那么我们不希望更新状态。

## 如何使用`useEffect`清理功能

让我们看一个例子，上面的错误什么时候会发生，以及当它发生时如何使用清理功能。让我们首先创建两个文件:`Post`和`App`。继续编写以下代码:

```
// Post component

import React, { useState, useEffect } from "react";
export default function Post() {
  const [posts, setPosts] = useState([]);
  const [error, setError] = useState(null);
  useEffect(() => {
    const controller = new AbortController();
    const signal = controller.signal;
    fetch("https://jsonplaceholder.typicode.com/posts", { signal: signal })
      .then((res) => res.json())
      .then((res) => setPosts(res))
      .catch((err) => setError(err));
  }, []);
  return (
    <div>
      {!error ? (
        posts.map((post) => (
          <ul key={post.id}>
            <li>{post.title}</li>
          </ul>
        ))
      ) : (
        <p>{error}</p>
      )}
    </div>
  );
}

```

这是一个简单的 post 组件，在每次渲染时获取 post 并处理获取错误。

这里，我们将 post 组件导入到主组件中，并在单击按钮时显示帖子。该按钮显示和隐藏文章，也就是说，它装载和卸载我们的文章组件:

```
// App component

import React, { useState } from "react";
import Post from "./Post";
const App = () => {
  const [show, setShow] = useState(false);
  const showPost = () => {
    // toggles posts onclick of button
    setShow(!show);
  };
  return (
    <div>
      <button onClick={showPost}>Show Posts</button>
      {show && <Post />}
    </div>
  );
};
export default App;

```

现在，单击按钮，在文章呈现之前，再次单击按钮(在另一个场景中，它可能在文章呈现之前导航到另一个页面)，我们在控制台中得到一个错误。

这是因为 React 的`useEffect`仍在运行，并试图在后台获取 API。当获取完 API 后，它会尝试更新状态，但这一次是在一个卸载的组件上，因此它会抛出以下错误:

![Error Message From Updating The State Of An Unmounted Component](img/d8dcf1f858431e2b9ca1d348863b7e9a.png)

现在，要清除这个错误并停止内存泄漏，我们必须使用上述任何一种解决方案来实现清理功能。在本文中，我们将使用`AbortController`:

```
// Post component

import React, { useState, useEffect } from "react";
export default function Post() {
  const [posts, setPosts] = useState([]);
  const [error, setError] = useState(null);
  useEffect(() => {
    const controller = new AbortController();
    const signal = controller.signal;
    fetch("https://jsonplaceholder.typicode.com/posts", { signal: signal })
      .then((res) => res.json())
      .then((res) => setPosts(res))
      .catch((err) => {
        setError(err);
      });
    return () => controller.abort();
  }, []);
  return (
    <div>
      {!error ? (
        posts.map((post) => (
          <ul key={post.id}>
            <li>{post.title}</li>
          </ul>
        ))
      ) : (
        <p>{error}</p>
      )}
    </div>
  );
}

```

我们仍然可以在控制台中看到，即使在中止了 cleanup 函数中的信号之后，卸载还是会抛出一个错误。正如我们前面讨论的，当我们中止 fetch 调用时，就会发生这个错误。

`useEffect`在 catch 块中捕获获取错误，然后尝试更新错误状态，然后抛出一个错误。为了停止更新，我们可以使用一个`if else`条件并检查我们得到的错误类型。

如果是中止错误，那么我们不需要更新状态，否则我们处理错误:

```
// Post component

import React, { useState, useEffect } from "react";

export default function Post() {
  const [posts, setPosts] = useState([]);
  const [error, setError] = useState(null);
  useEffect(() => {
    const controller = new AbortController();
    const signal = controller.signal;

      fetch("https://jsonplaceholder.typicode.com/posts", { signal: signal })
      .then((res) => res.json())
      .then((res) => setPosts(res))
      .catch((err) => {
        if (err.name === "AbortError") {
          console.log("successfully aborted");
        } else {
          setError(err);
        }
      });
    return () => controller.abort();
  }, []);
  return (
    <div>
      {!error ? (
        posts.map((post) => (
          <ul key={post.id}>
            <li>{post.title}</li>
          </ul>
        ))
      ) : (
        <p>{error}</p>
      )}
    </div>
  );
}

```

注意，我们应该只在使用 fetch 时使用`err.name === "AbortError"`，在使用 Axios 时使用`axios.isCancel()`方法[。](https://blog.logrocket.com/how-to-make-http-requests-like-a-pro-with-axios/)

就这样，我们完成了！

## **结论**

有两种副作用:不需要清理的副作用和需要清理的副作用，就像我们上面看到的例子。我们学习何时以及如何使用 [`useEffect`钩子](https://blog.logrocket.com/guide-to-react-useeffect-hook/)的清理功能来防止内存泄漏和优化应用程序是非常重要的。

我希望这篇文章对你有所帮助，现在你可以正确使用清理功能了。

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