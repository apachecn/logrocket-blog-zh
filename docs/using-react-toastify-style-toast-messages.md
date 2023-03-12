# 使用 React-Toastify 来设计你的祝酒辞

> 原文：<https://blog.logrocket.com/using-react-toastify-style-toast-messages/>

前端开发人员使用 HTML、CSS 和 JavaScript 来为用户可以看到并与之交互的网站构建图形界面。目标是建立和设计一个用户可以轻松打开、使用和交互的网站，尤其是在阅读重要信息时。

前端开发的一部分包括网站有多漂亮和美观，也称为 UI。另一个被称为 UX 的方面涉及到使用网站的简单和直接。

有不同的工具可以帮助我们实现伟大的用户界面和 UX。我们可以在前端使用的一个很酷的工具是 React-to-stify。

在本文中，我们将了解 React-Toastify，以及如何使用它在 React 应用程序中创建通知、消息和弹出窗口。我们将涵盖:

按照本教程，您需要在您的计算机上安装 React 和 Node。我们将在下一节讨论如何安装 React-Toastify 包。

## 什么是反应迟钝？

React-Toastify 是可用的顶级 React toast 库之一。该工具允许您轻松地将 toast 通知添加到您的应用程序中，还可以用来设置通知和警报。

### 什么是祝酒通知？

Toast 或 Toastify 通知是向用户显示一些信息的弹出消息。这些信息可能是成功消息、警告、错误等，如下图所示:

![Six Types Of React-Toastify Notifications In Six Locations](img/b8d3e76accbe4af6eb9452dc404906a3.png)

使用以下任意命令[安装 React-Toastify 软件包](https://github.com/fkhadra/react-toastify)。

```
/* NPM */
$ npm install --save react-toastify

/* YARN */
$ yarn add react-toastify

```

安装完成后，您必须将软件包导入到您的组件中，如下所示。React-Toastify 附带了一个 CSS 文件，必须导入该文件该工具才能工作。

```
import { ToastContainer, toast } from 'react-toastify';
import 'react-toastify/dist/ReactToastify.css';

```

## 用 React-Toastify 设计你的祝酒词

让我们启动 React 应用程序，看看如何使用 React-Toastify 来设计我们的祝酒词。

请记住，toast 消息是我们希望在给定时间向用户显示的通知或警报。这些消息可以在用户登录成功、登录错误、错误请求发生、重要信息被点击等情况下显示。

在您的`App.js`文件中，导入`react-toastify`包及其 CSS 文件:

```
import { ToastContainer, toast } from 'react-toastify';
import 'react-toastify/dist/ReactToastify.css';

```

之后，您可以调用`toast`并插入您的`Success Notification !`消息:

```
import React from 'react';
import { ToastContainer, toast } from 'react-toastify';
import 'react-toastify/dist/ReactToastify.css';

function App() {
    const showToastMessage = () => {
        toast.success('Success Notification !', {
            position: toast.POSITION.TOP_RIGHT
        });
    };
    return (
        <div>
            <button onClick={showToastMessage}>Notify</button>
            <ToastContainer />
        </div>
    );
}
export default App;

```

注意我们在上面的代码中也调用了`ToastContainer`。这个容器里有我们的吐司弹出式菜单。没有它，我们的吐司弹出窗口将不会显示。

当我们点击`Notify`按钮时，渲染这段代码将会得到如下结果:

![Example Of React-Toastify Success Notification Shown At Top Right Of Image](img/3026b55fc99f95cba514bd521b4cd64e.png)

### 如何改变祝酒词的位置

默认情况下，所有的祝酒词都位于浏览器的右上角。这个位置可以通过给 toast 分配一个新的位置来改变。反作用力允许六种位置:

*   右上方
*   上死点
*   左上角
*   右下角
*   下死点
*   左下角

根据您想要的祝酒辞位置，您可以在您的`App.js`文件中设置一个位置，如下所示:

```
toast.success('Success Notification !', {
    position: toast.POSITION.TOP_RIGHT
});
toast.success('Success Notification !', {
    position: toast.POSITION.TOP_CENTER
});
toast.success('Success Notification !', {
    position: toast.POSITION.TOP_LEFT
});
toast.success('Success Notification !', {
    position: toast.POSITION.BOTTOM_RIGHT
});
toast.success('Success Notification !', {
    position: toast.POSITION.BOTTOM_LEFT
});
toast.success('Success Notification !', {
    position: toast.POSITION.BOTTOM_CENTER
});

```

![Demonstration Of Six React-Toastify Notification Locations Available For Use With Success Notification In Each Position](img/052e44e97ba6c6a029ca8f612003d28b.png)

### 如何区分 toast 消息类型

与设置 toast 消息的位置类似，您可以让 toast 发射器指定不同类型的 toast 消息，以便用户可以更好地理解所显示的信息。

规范是[改善 React 应用](https://blog.logrocket.com/improve-react-ux-skeleton-ui/)用户体验的一种有用方式。这种技术为每种 toast 消息类型使用不同的颜色，使用户更容易快速理解他们看到的信息。

例如，红色的 toast 消息通常意味着警告或错误消息，就像绿色的消息通常意味着成功响应一样。本质上，您可以专门为错误消息、一般信息、警告等设置一个 toast 变量。

要指定您的 toast 发射器，请在您的`App.js`文件中使用以下任一变量调用您的 toast:

```
toast.success('Success Notification !', {
    position: toast.POSITION.TOP_RIGHT
});
toast.error('Error Notification !', {
    position: toast.POSITION.TOP_CENTER
});
toast.warning('Warning Notification !', {
    position: toast.POSITION.TOP_LEFT
});
toast.info('Information Notification !', {
    position: toast.POSITION.BOTTOM_CENTER
});
toast('Default Notification !', {
    position: toast.POSITION.BOTTOM_LEFT
});
toast('Custom Style Notification with css class!', {
     position: toast.POSITION.BOTTOM_RIGHT,
className: 'foo-bar'
});

```

这样做将很容易告诉用户正在显示什么。

![Six Types Of Toast Notifications, Each Shown At A Different Position On The Screen](img/77e086d826772b26608afc62d9b79698.png)

观察我们创建的最后一个 toast 通知，显示在上图的右下角。我们可以看到我们给它添加了一个`className`，不像其他的。那是一种风俗敬酒。让我们在 React-Toastify 中了解更多关于风俗祝酒的知识。

### 如何设计自定义祝酒辞

自定义祝酒词是一种通知，我们可以根据自己喜欢的样式来设计。这允许我们根据我们想要显示的消息类型，给我们的 toast 消息指定我们喜欢的高度、字体、字体大小、背景等等。

例如，我们可能希望显示与我们的网站或应用程序样式相匹配的 toast。一个定制的祝酒词允许我们这样做。

要在您的`App.js`文件中设计您自己的 toast 消息，首先给它一个`className`，如下所示:

```
toast('This is a custom toast Notification!', {
    position: toast.POSITION.BOTTOM_LEFT,
    className: 'toast-message'
});

```

接下来，根据您的喜好使用`className`在您的 CSS 文件中对其进行样式化:

```
.toast-message {
    background: darkblue;
    color: #fff;
    font-size: 20px;
    width: 34vw;
    padding: 30px 20px;
}

```

使用上面代码块示例中指定的样式，您应该会看到以下结果:

![Example Custom Toast Created With React-Toastify Shown At Bottom Left With Custom Blue Color And Larger Size](img/31cc0828ebeb364790d6f4df17c8a118.png)

### 如何在 React-Toastify 中创建承诺祝酒词

除了这些 toast 变体，React-Toastify 还允许我们创建和显示`Promise`通知。这些 toast 通知在处理任何 API 调用时出现，并在调用完成后返回成功或错误的 toast 消息。

要创建承诺祝酒词，请将以下内容添加到您的`App.js`文件中:

```
const myPromise = new Promise((resolve) =>
    fetch("https://jsonplaceholder.typicode.com/post")
      .then((response) => response.json())
      .then((json) => setTimeout(() => resolve(json), 3000))
  );

  useEffect(() => {
    toast.promise(myPromise, {
      pending: "Promise is pending",
      success: "Promise  Loaded",
      error: "error"
    });
  }, []);

```

在`toast.promise`中，我们设置了`pending`、`success`和`error`消息。执行提取时，将显示`pending`消息。根据结果，随后会显示`success`或`error`消息。

![GIF Showing React-Toastify Promise Notification In Action, Going From Pending With Loading Circle To Success With Green Check Mark](img/0d607886fa2b44d12b2c1f53c823482a.png)

### 了解 React-Toastify v9 中的`useNotificationCenter`更新

`useNotificationCenter`是随着[React-Toastify v 9](https://github.com/fkhadra/react-toastify/releases/tag/v9.0.0)的发布而出现的一个新的重大更新。这是一个在 React-Toastify 上构建通知中心的钩子。

每当您调用任何 toast 变体(如`toast.update`、`toast.promise`、`toast.info`等等)时，toast 通知将被添加到 toast 中心。

`useNotificationCenter`是一个包含所有通知的数组，因此允许我们对它执行一些数组功能，如过滤、排序、映射等。让我们用一个例子来更好地理解这个钩子。

在使用这个钩子之前，先从`react-toastify addons`导入，然后在你的`App.js`文件中的`return`语句之前调用它:

```
import { useNotificationCenter } from 'react-toastify/addons/use-notification-center';

const App = () => {
      const { notifications } = useNotificationCenter();
return ()}

```

`useNotificationCenter`钩子允许我们使用许多函数和值，包括`notifications`、`clear`、`markAllAsRead`、`markAsRead`等等。让我们回顾一下其中几个是什么意思。

`notifications`允许我们访问我们中心的所有通知项目或 toast 消息。在`notifications`数组中的每一个`notificationItem`都包含诸如其`id`、`read`状态(布尔)、`theme`、`isLoading`状态(布尔)以及其他信息的数据。

`clear`功能从通知中心删除所有通知。

`markAllAsRead`将所有通知标记为已读，这意味着它将每个`notificationItem`的`read`布尔值从`false`更改为`true`。相比之下，`markAsRead`只将一个`notificationItem`布尔改为`true`。

## 把你所有的反作用力知识放在一起

现在我们已经理解了`useNotificationCenter`钩子以及 toast 消息的位置、类型和定制，让我们看看如何在我们的应用程序中一起使用它们。

首先，将我们在上一节中讨论过的四个`useNotificationCenter`函数导入到您的`App.js`文件中:

```
import React from 'react';
import { useNotificationCenter } from 'react-toastify/addons/use-notification-center';
import { toast, ToastContainer } from 'react-toastify';
import 'react-toastify/dist/ReactToastify.css';

const App = () => {
const { notifications, clear, markAllAsRead, markAsRead } = useNotificationCenter();
}

```

如上所示，我们还导入了我们的`toast`和`ToastContainer`及其 CSS 文件，因此我们可以调用我们的 toast。继续在`App.js`文件中，让我们创建一个函数来调用我们的 toast 消息:

```
import React from 'react';
import { useNotificationCenter } from 'react-toastify/addons/use-notification-center';
import { toast, ToastContainer } from 'react-toastify';
import 'react-toastify/dist/ReactToastify.css';
const App = () => {
    const { notifications, clear, markAllAsRead, markAsRead } = useNotificationCenter();
    const showToast = () => {
        toast('Hello World', {
            data: {
                title: 'Hello World Again',
                text: 'We are here again with another article'
            }
        });
    };
    return (
        <div>
            <p>{notifications.length}</p>
            <button onClick={showToast}>Click me</button>
            <ToastContainer />
        </div>
    );
};
export default App;

```

请注意，在上面的代码中，我们添加了一个段落标记来显示我们已经调用或添加到通知中心的 toast 消息的数量。

当我们点击我们的按钮来调用我们的吐司，它会自动进入通知中心。按钮上方的数字将会更新，显示我们添加了多少条祝酒词，如下所示:

![Demonstration Of New React-Toastify Version 9 Feature Called useNotificationCenter With Three Notifications At Top Right](img/ac63a523e8b3452a9035dc23c8435362.png)

很酷，对吧？让我们看看这个钩子还能做些什么。如前所述，`useNotificationCenter`钩子允许我们对`notifications`数组执行排序、映射和其他操作。

仍然在您的`App.js`文件中，复制并粘贴下面的代码:

```
import React from 'react';
import { useNotificationCenter } from 'react-toastify/addons/use-notification-center';
import { toast, ToastContainer } from 'react-toastify';
import 'react-toastify/dist/ReactToastify.css';
const Dash = () => {
    const { notifications, clear, markAllAsRead, markAsRead } = useNotificationCenter();
    const showToast = () => {
        toast('Hello World', {
            data: {
                title: 'Hello World Again',
                text: 'We are here again with another article'
            }
        });
    };
    const showSuccessToast = () => {
        toast.success('Hello World', {
            data: {
                title: 'Success toast',
                text: 'This is a success message'
            }
        });
    };
    const showErrorToast = () => {
        toast.error('Hello World', {
            data: {
                title: 'Error toast',
                text: 'This is an error message'
            }
        });
    };
    return (
        <div>
            <p>{notifications.length}</p>
            <button onClick={showToast}>Default</button>
            <button onClick={showSuccessToast}>Success</button>
            <button onClick={showErrorToast}>Error</button>
            <br />
            <br />
            <button onClick={clear}>Clear Notifications</button>
            <button onClick={() => markAllAsRead()}>Mark all as read</button>
            <ul>
                {notifications.map((notification) => (
                    <li
                        onClick={() => markAsRead(notification.id)}
                        key={notification.id}
                        style={
                            notification.read ? (
                                { background: 'green', color: 'silver', padding: '0 20px' }
                            ) : (
                                {
                                    border: '1px solid black',
                                    background: 'navy',
                                    color: '#fff',
                                    marginBottom: 20,
                                    cursor: 'pointer',
                                    padding: '0 20px'
                                }
                            )
                        }
                    >
                        <span>id: {notification.id}</span>
                        <p>title: {notification.data.title}</p>
                        <p>text: {notification.data.text}</p>
                    </li>
                ))}
            </ul>
            <ToastContainer />
        </div>
    );
};
export default Dash;

```

我来解释一下上面的代码。

首先，我们通过我们的通知中心(这是一个通知项数组)进行映射，并获取我们的 toast 消息的`id`、`title`和`text`。

然后，由于有了[反应](https://blog.logrocket.com/a-guide-to-react-onclick-event-handlers-d411943b14dd/) `[onClick](https://blog.logrocket.com/a-guide-to-react-onclick-event-handlers-d411943b14dd/)` [事件处理程序](https://blog.logrocket.com/a-guide-to-react-onclick-event-handlers-d411943b14dd/)，当任何通知项目被点击时，我们可以使用`markAsRead`函数将该项目标记为已读。

我们还指示程序将通知项目的背景颜色从蓝色变为绿色，以区分已读和未读通知。

“全部标记为已读”按钮使用`markAllAsRead`功能将所有通知项的`read`状态更改为`true`。单击此按钮时，所有项目背景将改变颜色。

最后，“清除通知”按钮使用`clear`功能从通知中心删除所有项目。

请记住，您调用的任何 toast 变体——无论是`toast.success`、`toast.error`、`toast.update`还是任何其他类型——都将被添加到通知中心，如下所示:

![Styled Blue And Green React-Toastify Notifications Shown As Popups And In Notification Center](img/3f3b86d065ae0db742f738775fd00909.png)

## 结论

在本教程中，我们学习了如何使用 React-Toastify 来设计我们的 toast 消息。我们还看到，我们可以按照自己的意愿创建自定义的 toast 通知，并设计其样式。

最后，我们看到了如何使用`useNotificationCenter`钩子创建一个很酷的通知中心并显示我们所有的通知。

React-Toastify 是一个有用的 React toast 库，因为它是高度可定制的，并提供了许多 toast 变体。如果你需要更多的功能，还可以使用其他工具，比如在 React 中实现动画祝酒。

感谢阅读。我希望这篇文章对演示如何正确使用 React-Toastify 工具有所帮助。如果你有任何其他问题，请在下面的评论区告诉我。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)