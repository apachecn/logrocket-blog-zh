# 从头开始创建 React 应用工具链

> 原文：<https://blog.logrocket.com/creating-a-react-app-toolchain-from-scratch/>

当开始一个新的 React 项目时，有许多选项可用。各有千秋，留下了比较的空间。

有了像 [create-react-app](https://create-react-app.dev/) 、 [Next](https://nextjs.org/) 或 [Gatsby](http://gatsbyjs.org) 这样的工具，你就进入了一个抽象的、高级的、高度自以为是的工具链领域，这无疑会带来一些好处。然而，有些时候[你可能不需要工具链](https://reactjs.org/docs/create-a-new-react-app.html#you-might-not-need-a-toolchain)，这甚至会让你有很多渴望。

你需要明白的一件事是，你可以在你的网站上随意使用 React。这意味着 React 可以应用到你的网站中你觉得需要它的特定部分。这已经在[添加 React 到网站](https://reactjs.org/docs/add-react-to-a-website.html)中讨论过了。

但是，如果您可以为一个周末/宠物项目建立一个新的 React 应用程序工具链，从绝对必要的包开始，并在进行过程中包括更复杂的包，会怎么样呢？

你可以建立一个工具链，包括捆绑、林挺、格式化等等。，没有抽象和固执己见的工具或学习曲线的开销。你也可以学到很多关于事物如何在幕后工作，以及如何把小块的碎片放在一起，创造一些实质性的东西。

## 入门指南

我们将构建一个工具链，包括:

*   捆绑(与包裹一起)
*   林挺和格式(与 ESLint 和更漂亮)
*   Transpiling(与巴别塔)
*   样式(CSS/SCSS/样式组件)
*   数据提取(带提取)

请记住，这并不是对这些工具的深入研究。相反，我们将检查一些必要的细节来帮助您启动和运行。话虽如此，我们还是开始吧。

## 克隆`react-app-toolchain`回购

要开始，您需要在这里克隆 repo [。](https://github.com/adebiyial/react-app-toolchain/)

该文件夹包含开始使用所需的文件夹和文件。

起始文件夹如下所示:

```
- public
  - index.html
- src
  - components
    - Users.js
  - App.js
  - app.css
  - app.scss
- .eslintrc
- .prettierignore
- .prettierrc
- .gitignore
- package.json
```

## 包括反应

现在我们已经创建了必要的文件夹，我们可以包含 React 的两个必要的依赖项:

```
yarn add react react-dom
```

这将更新`package.json`以包含添加的`dependencies`。包的版本可能不同。

```
"dependencies": {
  "react": "^16.13.1",
  "react-dom": "^16.13.1"
}
```

虽然从技术上讲，您可以在这里编写一些 React，但除非我们添加一个 bundler 并将应用程序呈现到 DOM 中，否则这是没有用的。

> 你可以查看`[include-react](https://github.com/adebiyial/react-app-toolchain/tree/include-react)`分支获取最新代码。

## 添加捆扎机

我们将使用的捆扎机是[包裹](https://parceljs.org/getting_started.html)。Parcel 是一个零配置、轻量级、快速的 web 应用程序捆绑器:

```
yarn add parcel-bundler -D
```

此时，您的`package.json`文件(除了依赖版本号)应该看起来像这样:

```
{
  "name": "react-app-toolchain",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "dependencies": {
    "react": "^16.13.1",
    "react-dom": "^16.13.1"
  },
  "devDependencies": {
    "parcel-bundler": "^1.12.4"
  }
}
```

现在，您可以编写一些组件，并使用 Parcel 运行应用程序。

将`public/index.html`文件更新为:

```
<!-- public/index.html -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>React App Toolchain</title>
  </head>
  <body>
    <!-- We'd render the react app in here -->
    <div id="app"></div>
    <!-- We reference the entry point of our React app here -->
    <script src="../src/App.js"></script>
  </body>
</html>
```

和`src/App.js`到:

```
// src/App.js
import React from "react";
import ReactDOM from "react-dom";

function App() {
  return <h1>Hello World!</h1>;
}

ReactDOM.render(<App />, document.getElementById("app"));
```

然后向`package.json`添加一个`start`脚本，指示 parcel 进入您的应用程序:

```
{
  "name": "react-app-toolchain",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "scripts": {
    "start": "parcel public/index.html"
  },
  "dependencies": {
    "react": "^16.13.1",
    "react-dom": "^16.13.1"
  },
  "devDependencies": {
    "parcel-bundler": "^1.12.4"
  }
}
```

现在你可以运行命令`yarn start`并访问在`localhost:1234`运行的应用程序，或者包裹运行应用程序的`port`。

![Text saying 'Hello world!'](img/1aa4705555a57affb3ad416b6ca00b8d.png)

> 你可以查看`[include-bundler](https://github.com/adebiyial/react-app-toolchain/tree/include-bundler)`分支获取最新代码。

## 格式化

为了格式化我们的文件，我们将使用更漂亮的。是一个自以为是的代码格式化程序，使用 HTML、CSS 和 JavaScript 等技术。

```
yarn add prettier -D
```

然后添加两个配置文件:

*   `.prettierrc`将包含期望的更漂亮的规则，并且
*   `.prettierignore`将包含我们不需要`prettier`格式化的文件/目录，例如`node_modules`

将以下规则添加到`.prettierrc`文件中:

```
{
  "arrowParens": "avoid",
  "bracketSpacing": true,
  "jsxBracketSameLine": false,
  "printWidth": 120,
  "proseWrap": "always",
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "all",
  "useTabs": false,
  "jsxSingleQuote": true
}
```

并且到`prettierignore`:

```
node_modules
build
coverage
public
dist
.cache
```

您还需要一个格式化命令。在`package.json`上再加一个`scripts`——这次是`format`。这将`package.json`更新为:

```
{
  "name": "react-app-toolchain",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "scripts": {
    "start": "parcel public/index.html",
    "format": "prettier \"src/**/*.js\" --write"
  },
  "dependencies": {
    "react": "^16.13.1",
    "react-dom": "^16.13.1"
  },
  "devDependencies": {
    "parcel-bundler": "^1.12.4",
    "prettier": "^2.0.5"
  }
}
```

运行`format`脚本指示 Prettier 格式化`src`目录中所有以`js`结尾的文件。

如果您运行命令`yarn format`，那么`App.js`中的双引号现在应该是单引号。省略的地方也应该包括分号，并且应该应用`.prettierrc`中的其他规则。

> 你必须不断运行`yarn format`来格式化你的文件。如果你使用的是 VSCode，你可以安装更漂亮的扩展，然后打开`editor.formatOnSave`设置。当你保存时，Prettier 会自动格式化你的文件——即使没有`format`脚本。
> 
> 你可以查看`[include-formatting](https://github.com/adebiyial/react-app-toolchain/tree/include-formatting)`分支获取最新代码。

## 林挺

你增加了那些更漂亮的规则，但它们没有被强制执行。ESLint 是一个 JavaScript linter，可以帮助你发现代码中的问题。

```
yarn add eslint babel-eslint eslint-plugin-react-hooks eslint-config-prettier eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react -D
```

使用前面的命令，您不仅添加了`eslint`，还添加了如下包:

*   `eslint-plugin-react-hooks`
*   ``eslint-plugin-import``
`*   ``babel-eslint`允许林挺为所有有效的[巴别塔](https://babeljs.io/)编码``*   解决了 ESLint 和 Prettier 之间的冲突*   `eslint-plugin-jsx-a11y`是一个静态的 AST 检查器，用于检查 JSX 元素的可访问性规则，帮助解决图像中不包含替代文本的问题*   `eslint-plugin-react`帮助反应特定的 ESLint 规则``

 ``然后用配置更新`.eslintrc`文件:

```
{
  "extends": [
    "eslint:recommended",
    "plugin:import/errors",
    "plugin:react/recommended",
    "plugin:jsx-a11y/recommended",
    "plugin:react-hooks/recommended",
    "prettier",
    "prettier/react"
  ],
  "plugins": ["react", "import", "jsx-a11y"],
  "parserOptions": {
    "ecmaVersion": 2018,
    "sourceType": "module",
    "ecmaFeatures": {
      "jsx": true
    }
  },
  "env": {
    "es6": true,
    "browser": true,
    "node": true
  },
  "rules": {
    "semi": ["warn", "always"],
    "quotes": ["warn", "single"]
  },
  "settings": {
    "react": {
      "version": "detect"
    }
  }
}
```

这里，`rules`指示 ESLint 在省略分号或不使用单引号时给出警告。`ecmaFeatures`通知 ESLint JSX 已被使用，`env`包含应用程序将运行的环境。

现在你需要一个`lint`脚本来帮助 lint 你的代码。`package.json`脚本被更新为:

```
"scripts": {
  "start": "parcel public/index.html",
  "format": "prettier \"src/**/*.js\" --write",
  "lint": "eslint \"src/**/*.{js,jsx}\""
},
```

现在你可以运行`yarn lint`来格式化`src`中所有以`.js`或`.jsx`扩展名结尾的文件。

> 你必须一直运行`yarn lint`来清理你的文件。如果您使用的是 VSCode，可以安装 ESlint 扩展。这将确保在您创建错误消息时，以及在 ES lint 检测到错误消息时，在您的文件中获得正确的错误消息。

> 你可以查看`[include-linting](https://github.com/adebiyial/react-app-toolchain/tree/include-linting)`分支获取最新代码。

## 式样

谈到造型，有很多选择。让我们回顾一下:

*   半铸钢ˌ钢性铸铁(Cast Semi-Steel)
*   SCSS
*   样式组件

添加 CSS 文件很简单。更新`app.css`和`app.scss`文件:

```
/* src/app.css */
.clr-red {
  color: red;
}
```

```
// src/app.scss
.bg-clr-black {
  background-color: black;
}
```

另外，向`App.js`中的`h1`添加必要的类，并导入`app.css`和`app.scss`:

```
// src/App.js
import React from 'react';
import ReactDOM from 'react-dom';
import './app.css';
import './app.scss';

function App() {
  return <h1 className='clr-red bg-clr-black'>Hello World!</h1>;
}

ReactDOM.render(<App />, document.getElementById('app'));
```

> 因为您使用了 sass，所以 package 会自动安装并将`sass`添加到`package.json`中的依赖项列表中。

如果你想使用 CSS-in-JS 解决方案，比如[风格的组件](https://styled-components.com/)，你必须安装它。

```
yarn add styled-components -D
```

```
// src/App.js
import React from 'react';
import ReactDOM from 'react-dom';
import './app.css';
import './app.scss';
import styled from 'styled-components';

const StyledApp = styled.div`
  text-transform: uppercase;
`;

function App() {
  return (
    <StyledApp>
      <h1 className='clr-red bg-clr-black'>Hello World!</h1>
    </StyledApp>
  );
}

ReactDOM.render(<App />, document.getElementById('app'));
```

> 你可以查看`include-styling`分支获取最新代码。

## 数据提取

获取数据很简单。让我们[取](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)一些数据[在这里](https://randomuser.me/)在`App.js`。

```
// src/App.js
import React, { useState, useEffect } from 'react';
import ReactDOM from 'react-dom';
import './app.css';
import './app.scss';
import styled from 'styled-components';
import Users from './components/Users';

const StyledApp = styled.div`
  text-transform: uppercase;
`;

function App() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetch('https://randomuser.me/api/?format=json&results=10')
      .then(res => res.json())
      .then(res => {
        const results = res.results.map(({ picture, name }) => {
          return {
            thumbnail: picture.thumbnail,
            name: `${name.title} ${name.first} ${name.last}`,
          };
        });

        setUsers([...results]);
      });
  }, []);

  return (
    <StyledApp>
      <h1 className='clr-red bg-clr-black'>Hello World!</h1>
      <Users {...{ users }} />
    </StyledApp>
  );
}

ReactDOM.render(<App />, document.getElementById('app'));
```

您还需要更新`Users.js`:

```
// src/components/Users.js
import React from 'react';
import styled from 'styled-components';
import PropTypes from 'prop-types';

const StyledUsers = styled.div`
  .card {
    display: flex;
    border: 1px solid salmon;
    padding: 20px;
  }

  .photo {
    margin-right: 20px;
    display: flex;
    align-items: flex-start;
  }

  .img {
    border-radius: 50%;
  }
`;

function Users({ users }) {
  return (
    <StyledUsers>
      {users.map((user, index) => (
        <div className='card' key={index}>
          <div className='photo'>
            <img className='img' src={user.thumbnail} alt={user.name} />
          </div>
          <h1 className='name'>{user.name}</h1>
        </div>
      ))}
    </StyledUsers>
  );
}

Users.propTypes = {
  users: PropTypes.array,
};

export default Users;
```

如果你愿意，我们也可以使用 [Axios](https://github.com/axios/axios) 或 Next 的 [SWR](https://swr.vercel.app/) 。

> ESLint 可能会提醒您道具类型。首先，用命令`yarn add prop-types -D`安装它，并相应地更新`Users.js`。

现在应该会显示用户列表:

![List of users.](img/7b0b78fd9fd4170b978bede6479a35a6.png)

> 你可以查看`[include-data-fetching](https://github.com/adebiyial/react-app-toolchain/tree/include-data-fetching)`分支获取最新代码。

最终代码在`[final](https://github.com/adebiyial/react-app-toolchain/tree/final)`分支中。

## 结论

如果你不知道去哪里找，那会有很多构建工具，即使你知道，把它们放在一起运行你的应用程序也会很乏味。尤其是当您必须跟上各种更新和向后依赖时。在这一点上，我猜想你可能在想:为什么要这么麻烦呢？为什么你不能使用一个现成的工具链，这样你就可以专注于真正重要的事情——构建你的应用程序？

公平地说，这是有道理的。

但是了解这些工具的内部工作方式是有力量的，因为这样你就可以确定什么资源对你的开发是重要的，反之亦然。此外，您可以创建一个样板文件，就像 [React 样板文件](https://www.reactboilerplate.com/)一样，以避免重复。您可能还想查看[创建应用](https://createapp.dev)向导，以了解更多或不同的方式来连接这些工具链。

你可以将更多的工具添加到你的工具链中，比如 routing(用 [React Router](https://reactrouter.com/web/guides/quick-start) 和 form management(用 [Formik](https://jaredpalmer.com/formik/) ),但是这些都不是 React 工作所必需的，所以我们没有涉及它们。

你的工具链是你的，你可以随意使用和改变。没什么能阻止你拥有一个。那么，为什么不创造一个呢？

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。``