# 使用多层结构优化 React 应用程序

> 原文：<https://blog.logrocket.com/optimize-react-apps-using-a-multi-layered-structure/>

React 项目很容易变得一团糟。忘记文件的位置是非常常见的，这可能会导致开发过程中的低效率。那么，如何改进和组织 React 项目呢？通过在多层架构中组织项目的所有文件。

因此，您总是知道如何放置和查找每个文件。这是一个游戏规则的改变。这不仅会使你成为一个更高效的开发人员，而且会使你的项目更易于维护。

此外，通过将项目转化为有组织的架构，您的整个团队也可以从中受益。这是因为不同的人可以在不同的层工作，避免重叠和潜在的开销。

让我们来看看为什么要在 React 中使用多层结构。

## 多层结构如何优化 React 应用

### 重用代码

想象一下，您所有的 React 项目共享相同的多层文件结构，让您最大限度地利用时间。这种共享文件结构允许您像简单的复制粘贴操作一样，将任何类型的文件或代码片段从一个项目导入到另一个项目，而不是将从其他代码库导入的组件或文件应用到您的目标项目。这鼓励代码重用，使你构建应用程序更容易、更省时。

例如，假设你的一个项目中有几个数学问题。通过使用这种文件结构，您不会试图将所有需要的实用函数分散到整个代码库中。

相反，它鼓励你定义一个`math-utils.js`文件，在那里你可以存储你所有的数学工具。此外，通过在同一个文件中外部化用于相同目的的元素，您可以使它们更加可重用。事实上，如果你在未来的项目中面临类似的问题，你可以简单地复制先前定义的`math-utils.js`文件，并将其粘贴到你的`utils`层。

### 避免代码重复

实施多层结构的结果是每个文件、函数或代码都属于特定的位置。这意味着在编写新的东西之前，您应该先查看它假定的文件夹，这不鼓励代码重复和减少构建规模。

一个包含大量多余代码的包显然会比需要的要大。这对于浏览器来说是不好的，浏览器必须下载代码，然后再渲染。

因此，避免代码重复会使您的应用程序更干净、更容易构建，并且在呈现时速度更快。例如，每当您需要检索从特定 API 返回的数据时，您可能会倾向于采用这种方法:

```
function FooComponent1(props) {     
      // ...

      // retrieving data
      fetch(`/api/v1/foo`)
      .then((resp) => resp.json())
      .then(function(data) {
          const results = data.results;
          // handling data ...
        })
        .catch(function(error) {
          console.log(error);
        });

      // component logic ...
}

export default FooComponent1
```

```
function FooComponent2(props) {
      // ... 

      // retrieving data
      fetch(`/api/v1/foo`)
      .then((resp) => resp.json())
      .then(function(data) {
          const results = data.results;
          // handling data in a different way than FooComponent1 ...
        })
        .catch(function(error) {
          console.log(error);
        });

      // different component logic from FooComponent1 ...
}

export default FooComponent2

```

如您所见，`FooComponent1`和`FooComponent2`受到了代码重复问题的影响。特别是，API 端点被复制，迫使您在每次改变时都要更新它。

这不是一个好习惯。相反，您应该将 API 映射到 API 层，如下所示:

```
export const FooAPI = {

    // ...

    // mapping the API of interest
    get: function() {
        return axiosInstance.request({
            method: "GET",
            url: `/api/v1/foo`
        });
    },

    // ...

}

```

然后在需要的地方使用它，以避免代码重复:

```
import React from "react";
import {FooAPI} from "../../api/foo";
//...

function FooComponent1(props) {
      // ... 

      // retrieving data
      FooAPI
      .get()
      .then(function(response) {
          const results = response.data.results;
          // handling data ...
        })
        .catch(function(error) {
          console.log(error);
        });

      // component logic ...
}

export default FooComponent1
```

```
import React from "react";
import {FooAPI} from "../../api/foo";
//...

function FooComponent2(props) {
      // ... 

      // retrieving data
      FooAPI
      .get()
      .then(function(response) {
          const results = response.data.results;
          // handling data in a different way than FooComponent1 ...
        })
        .catch(function(error) {
          console.log(error);
        });

      // different component logic from FooComponent1 ...
}

export default FooComponent2

```

现在，API 端点只保存在一个地方，就像它应该一直保存的那样。

### 利用团队协作

使用众所周知的文件结构意味着开发团队成员都在同一个页面上，并且以相同的方式使用它。

例如，每当一个团队成员映射一个新的 API，或者创建一个新的 utils 文件或者一个新的组件，任何团队成员都可以立即使用它。而且，由于每个文件都逻辑地放在一个特定的文件夹中，每个人都知道如何(以及在哪里)访问，通信开销减少或完全消除，从而简化了公司的开发过程。

## 建造多层结构的潜在副作用

### 创建 npm 模块可能很复杂

这种有组织的文件体系结构有一个潜在的缺点:文件分布在许多文件夹中，这迫使您大量使用相对导入。这是创建 npm 模块时的一个众所周知的问题。

相对导入容易出错，尤其是当试图封装部分代码库以使其可作为独立模块发布时。一方面，在尝试创建 npm 包时，这无疑是一个额外的挑战。

另一方面，这可以通过将相对进口转变为不可打破的绝对进口来容易地避免，如这里所描述的。这种技术允许你把这个:

```
import UserComponent from "../../components/UserComponent";
import userDefaultImage from "../../../assets/images/user-default-image.png";
import {UserAPI} from "../../apis/user";

```

变成这样:

```
importUserComponentfrom "@components/UserComponent";
import userDefaultImage from "@assets/images/user-default-image.png";
import {UserAPI} from "@apis/user";

```

因此，使用许多相对进口不应该被视为一个真正的问题。

现在，让我们看看如何在 React 和 JavaScript 中为您的项目设计一个高效的多层架构。

## 创建多层架构

首先，我们来看看多层架构的最终结果。请记住，我们架构的每一层都应该放在一个特定的文件夹中。正如所料，强烈建议将这些文件夹命名为架构所包含的层。这样，检索文件变得直观而快速。你也总是知道在哪里放置一个新文件，这是一个很大的优势。

这是最终结构的样子:

![Final Folder Structure](img/32345345e3c006584dfc8009d9c4368d.png)

As you can see, each layer of the architecture is represented by a folder.

现在，让我们深入研究所展示的体系结构包含的每一层。

### 1.API 层

通过利用一个基于承诺的 HTTP 客户端，比如 T2 Axios T3，你可以为你的应用程序所依赖的每个 API 定义一个函数，封装所有需要调用它的逻辑。这样，您就可以区分 API 请求的定义位置和实际使用位置。让我们通过一个简单的示例来看看如何构建这样一个层:

```
const axiosInstance = axios.create({
   baseURL: 'https://yourdomain.org'
});

export const UserAPI = {
    getAll: function() {
        return axiosInstance.request({
            method: "GET",
            url: `/api/v1/users`
        });
    },
    getById: function(userId) {
        return axiosInstance.request({
            method: "GET",
            url: `/api/v1/users/${userId}`
        });
    },
    create: function(user) {
        return axiosInstance.request({
            method: "POST",
            url: `/api/v1/users`,
            data: user
        });
    },
    update: function(userId, user) {
        return axiosInstance.request({
            method: "PUT",
            url: `/api/v1/users/${userId}`,
            data: user,
        });
    },
}

```

现在，您可以导入`UserAPI`对象在您需要的地方进行调用，就像这样:

```
import React, {useEffect, useState} from "react";
import {UserAPI} from "../../api/user";
//...

function UserComponent(props) {
    const { userId } = props;

    // ...

    const [user, setUser] = useState(undefined)

    // ...

    useEffect(() => {
        UserAPI.getById(userId).then(
            function (response) {
                // response handling
                setUser(response.data.user)
            }
        ).catch(function (error) {
            // error handling
        });
    }, []);

    // ...
}

export default UserComponent;

```

通过使用`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)`技术，您可以在同一个地方收集所有的 API 请求定义。但是我们不要忘记目标。这篇文章并不是要展示你可以用 API 层实现什么，你可以跟随[这个](https://codeburst.io/avoiding-code-duplication-by-adding-an-api-requests-definition-layer-in-javascript-6e5d7b409896)和[这个](https://codeburst.io/automatically-canceling-previous-requests-in-javascript-606943e2834)做进一步的阅读。

![API Layer Folder](img/4b9f5ae06c839be0b9d9d7c858ae3cbc.png)

Organizing all the files that belong to the API layer in the same folder.

相反，我们将关注这样一个事实，即使用这种方法，您将能够为 API 请求定义一个完全专用的层。这将把处理 API 请求的所有逻辑保存在一个地方，而不是在每次需要时复制整个逻辑来调用 API。

### 2.资产层

您的 React 项目可能依赖于严格来说不属于您的代码库的文件，例如多媒体文件。拥有一个存储它们的地方是最佳实践，因为你应该清楚地将它们从你的代码中分离出来。请注意，这种层可以根据类型或扩展组织在子文件夹中，以保持其有序性:

![Sample Asset Layer](img/7891e37bc418dd986e0057c12752e5e9.png)

A sample asset layer organized by type.

此外，这是一个很好的地方，可以存储采用国际化框架的国际应用程序所需的所有翻译文件，比如`[i18next](https://www.i18next.com/)`。

![All Translation Files](img/b34ac835cc858185830d0e39e7f97956.png)

Keeping all the `i18next` translation files in the translations folder.

### 3.组件层

这是你应该放置所有组件的地方。每个组件应该有一个文件夹，以与组件本身相同的方式命名。这样的文件夹通常只包含两个文件:`index.js`和`index.css`。它们分别表示定义 React 组件的位置和包含其样式的位置。

![Sample Component Layer](img/4ffdcc92a923a0c67689537fe416ca13.png)

A sample component layer in action.

因为您的组件可能很大，所以您应该考虑用嵌套结构来组织它们。找到一个好的标准将它们分配到子文件夹中并不总是一件简单的任务。

同时，请记住，您可以随时更改它，按照您的意愿重新安排组件的存储结构。

### 4.恒定层

一种常见的做法是在单个`constans.js`文件中定义应用程序中使用的所有常量。在一个小项目中，这种方法可能是最好的解决方案。不可避免的是，随着你的文件变得越来越大，它会慢慢变得一团糟。

这就是为什么你应该在多个文件中分割你的常量。例如，在一个国际应用程序中，您可以考虑将所有由`[i18next](https://www.npmjs.com/package/i18next)`使用的自定义键存储在一个特定的文件中。

这样，所有与 i18n 相关的常量都在同一个文件中，使它们更容易管理。同样，在其他情况下，您可以遵循后一种方法，如下例所示:

![Splitting All Application Constants](img/6ea363339ccf0e19a6288316e25df75d.png)

Splitting all application constants into four different files.

### 5.冗余层

这是一个可选层，只有当您的应用程序使用 [Redux](https://redux.js.org/) 时，您才应该定义它。正如[官方文档](https://redux.js.org/introduction/getting-started)中所述，Redux 是 JavaScript 应用程序的可预测状态容器。如果你曾经使用过 Redux，你应该知道它需要多少文件。

迷失在众多的[Redux Redux reducer](https://redux.js.org/tutorials/fundamentals/part-3-state-actions-reducers#writing-reducers)和 [Redux actions](https://redux.js.org/tutorials/fundamentals/part-3-state-actions-reducers#designing-actions) 中是很常见的，尤其是后者。另外，定义动作涉及样板代码。这就是为什么推荐使用一个库，比如`[redux-actions](https://www.npmjs.com/package/redux-actions)`，来管理你所有的动作。然后，通过按如下方式排列文件，您将能够简单地获得一个有组织的结构:

![Sample Redux Layer](img/1081d82ed76234005c2913c81c90125b.png)

A sample Redux layer composed by two folders and one file.

如上所示，您可以将所有 Redux reducers 放在`reducers`文件夹中，将 Redux 操作放在`actions`文件夹中，并定义一个顶层的`index.js`文件，其中包含您的[存储和持久逻辑](https://redux.js.org/tutorials/fundamentals/part-4-store)。如果需要，您甚至可以将包含在上述两个文件夹中的文件组织到子文件夹中。

### 6.路线层

您的项目可能已经将应用程序覆盖的所有路线存储在一个`routes.js`文件中。定义一个包含所有路线的文件，将它们收集在一个地方，并在实际使用时将它们分开。

同样，对于小型项目，这种方法可能就足够了。问题在于您无法预测您的应用程序将增长到多大。这就是为什么您应该定义一个包含所有路由文件和逻辑的层。将`routes.js`文件分割成多个文件的一个技巧是为应用程序使用的每个级别路由路径创建一个文件。

![Sample Route Layer](img/a85c061ccc5b353a2280b7a79de8da9b.png)

A sample route layer made up of four files.

### 7.效用层

这是存储您的整个代码库所依赖的所有自定义实用函数的地方。虽然您可能将函数存储在一个单独的`utilis.js`文件中，但是随着项目规模的增长，您可能会被迫将它分成多个文件。

这就是为什么有一个专门的分层可能变得不可避免。不要等待这种情况的发生，先下手为强，建立一个新的层，如下所示:

![Splitting Starting File](img/38204ec6c0bbbf26ed13f1445227b9a5.png)

Splitting a starting `utils.js` file into two different files.

### 8.视图层

这是顶层，在这里你将存储你的应用程序所包含的所有页面。请注意，这是唯一可能需要从前面提到的任何其他层导入文件的层。

如果您的项目有大量的页面，您应该将它们组织到子文件夹中。实现这一点的一个好标准是复制路由结构，就像在 PHP 项目中一样。例如，共享`[https://yourdomain.dom/users/](https://yourdomain.dom/users/)`基路径的所有页面都应该放在`Users`文件夹中。

![Sample View Layer Structure](img/f4c4cd6c1f2a1b4fa99f4c49d0908b3b.png)

A sample view layer structure with the criterion presented in the example.

## 多层结构中的剩余文件

请记住，这样的架构可能不足以涵盖构成代码库的所有文件。这就是为什么总会有额外的文件，你可能不知道放在哪里。

遵循这样一个有组织的结构可以让你把它们减少到几个地方。一个很好的经验法则是，如果您的文件少于三个，您应该将它们放在项目的顶层文件夹中，或者您认为它们属于的位置。否则，您可以随时设计满足您需求的新图层。

## 结论

在本文中，我们研究了如何(以及为什么应该)组织一个 React 项目，以使其更加高效。通过将文件放在代表多层体系结构的文件夹中，您可以将项目变成更有组织的结构。

使用这种方法，每个文件都有自己的位置。这将使您和您的团队能够设置架构标准，使您的整个代码库更加健壮和可维护，并简化您的开发过程。

感谢阅读！我希望这篇文章对你有所帮助。如果有任何问题、意见或建议，请随时联系我。

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