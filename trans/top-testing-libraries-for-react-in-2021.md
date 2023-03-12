# 2021 年 React 的顶级测试库

> 原文：<https://blog.logrocket.com/top-testing-libraries-for-react-in-2021/>

# 

测试是构建应用程序最关键的步骤之一；它有助于消除有害的错误，并为您的项目提供可信度和质量保证。

React 社区已经开发了大量用于测试 React 应用程序中的组件和功能的库。这篇文章强调了测试 React 应用程序的一些首选。

## 玩笑

Jest 是一个由脸书创建和维护的测试框架，已经被 Airbnb、优步和亚马逊等其他公司广泛采用。React 自带 Jest，因此不需要作为依赖项安装。

### Jest 入门

用 Jest 开始测试有两种方法。首先，对于用`create-react-app`初始化的项目，使用如下所示的节点管理器进行安装:

```
// NPM 
npm install -D react-test-renderer

// Yarn 
yarn add -D react-test-renderer

```

上面的命令将`react-test-renderer`安装到应用程序中，用于快照测试。

接下来，复制下面的命令，使用 Jest 在 React 应用程序中安装 DOM 测试包。

```
// NPM 
npm install -D @testing-library

// Yarn
yarn add -D @testing-library/react

```

对于未使用`create-react-app`初始化的应用程序，您需要遵循以下步骤。

首先，添加以下包作为开发依赖项:

```
npm install --dev jest babel-jest @babel/preset-env @babel/preset-react react-test-renderer

```

接下来，添加巴别塔配置:

```
// babel.config.js
module.exports = {
presets: ['@babel/preset-env', '@babel/preset-react'],
};

```

接下来，将`jest`包含在应用程序的`package.json`中:

```
{
"scripts": {
"test": "jest"
  }
}

```

现在，您将为 React 应用程序的组件编写一个简单的测试:

```
import React from "react";
import { cleanup, render } from "@testing-library/react";
import { FootLabel } from './FootLabel

describe("FootLabel", () =&gt; {
  test("component should display label", () =&gt; {
    const { getByText } = render(&lt;FootLabel label="Test" /&gt;);
    expect(getByText("Test")).toBeTruthy();
  });
});

```

上面的测试使用 Jest 的方法`describe`来表示类似于`FootLabel`的组件，并测试该组件相对于预期输出的样子。

除了根据组件的实际输出测试组件的预期结果，Jest 还执行 [**快照测试**](https://blog.logrocket.com/refactoring-legacy-code-with-jest-snapshots-e290ceccccc3/) [**ing**](https://blog.logrocket.com/refactoring-legacy-code-with-jest-snapshots-e290ceccccc3/) **。**

一个 [s](https://blog.logrocket.com/refactoring-legacy-code-with-jest-snapshots-e290ceccccc3/) [napshot 测试](https://blog.logrocket.com/refactoring-legacy-code-with-jest-snapshots-e290ceccccc3/)确保应用程序的用户界面不会意外改变。Jest 中的快照测试允许用户将一个状态中的组件与它可能采取的任何其他状态进行比较。快照在具有跨组件使用的全局样式的项目中特别有用。

```
import React from "react";
import renderer from "react-test-renderer";
import Navbar from "./Navbar.js"

describe("<Navbar />", () => {
    it('renders correctly across screens', () => {
    const tree = renderer.create().toJSON();
    expect(tree).toMatchSnapshot();
});

```

使用 Jest 的一些优点包括:

*   多样性:Jest 提供快照、异步和并行测试
*   模仿:Jest 提供了模仿 API 函数和第三方库的能力
*   控制:Jest 具有代码和语法报告指南

Jest 的缺点之一是，它需要其他第三方测试框架(如 Enzyme)来提高性能。此外，当用于具有不同类型测试的大型项目时，例如集成测试，它可能会有困难。

您可以查看 Jest [文档](https://jestjs.io/docs/en/getting-started)来了解更多关于 Jest 测试的信息。

## 摩卡

[Mocha](https://blog.logrocket.com/a-quick-and-complete-guide-to-mocha-testing-d0e0ea09f09d/) 是一个运行在 Node.js 上的 JavaScript 框架，用于测试 React 应用。它拥有浏览器支持、异步测试和断言库的使用。使用 Mocha，开发人员可以完全控制在测试应用程序时使用哪些工具和插件。由于它为测试 React 应用程序提供了特定的配置和功能，Mocha 在模拟测试方面表现出色。

### 装置

要使用 Mocha 测试 React 应用程序，首先使用下面的命令将 Mocha 作为开发依赖项安装在 React 项目上:

```
npm i --save-dev mocha 

```

如果您使用 Yarn 作为您的包管理器，那么使用这个命令:

```
yarn add mocha

```

然后更新您的`package.json`中的`test`脚本以包含 Mocha:

```
{
  "scripts": {
    "test": "mocha"
  }
}

```

参见下面的用法:

```
// test/test.js

var modal = require('modal');
  describe('Array', function() {
  describe('#indexOf()', function() {
    it('should return 0 when the value is not present', function() {
      assert.equal([1, 2, 3, 4, 5].indexOf(6), -3);
    });
  });
});

```

上面的测试在数组中搜索一个数字`6`，如果没有找到这个数字的索引，就返回`-3`。

使用摩卡的一些好处包括以下几点:

你可以在 Mocha 的官方文档中读到更多关于它的信息。

## 茉莉

[Jasmine](https://github.com/jasmine/jasmine) 是一个简单的针对浏览器和 Node.js 的 JavaScript 测试框架，Jasmine 遵循行为驱动的开发模式，所以一般在使用前就配置好了。使用 Jasmine，开发人员可以测试不同设备上用户界面的可见性和分辨率。为了在测试 React 应用程序中使用 Jasmine，开发者可以添加其他第三方框架，如[酵素](http://%E2%80%8B%E2%80%8Bhttps://blog.logrocket.com/getting-started-with-enzyme-for-react-a106b58fc53b/)。

### 装置

开发人员经常将 Jasmine 与 Enzyme 一起使用，所以它需要作为配置的一部分进行安装。

```
yarn add -D babel-cli \
            @babel/register \
            babel-preset-react-app \
            cross-env \
            enzyme \
            enzyme-adapter-react-16 \
            jasmine-enzyme \
            jsdom \
            jasmine

```

接下来，使用下面的命令初始化 Jasmine:

```
// For NPM 

npm run jasmine init 

// For Yarn 
yarn run jasmine init 

```

Jasmine 要求将所有配置文件，包括 Babel、Enzyme 和 JSDOM 放在一个`spec`文件夹中。

```
// babel.js
require('@babel/register');

// for typescript
require('@babel/register')({
    "extensions": [".js", ".jsx", ".ts", ".tsx"]
});

// enzyme.js or enzyme.ts 
// be sure your file extension is .ts if your project is a typescript project
import jasmineEnzyme from 'jasmine-enzyme';
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() });

beforeEach(function() {
  jasmineEnzyme();
});

// jsdom.js

import {JSDOM} from 'jsdom';

const dom = new JSDOM('&lt;html&gt;&lt;body&gt;&lt;/body&gt;&lt;/html&gt;');
global.document = dom.window.document;
global.window = dom.window;
global.navigator = dom.window.navigator;

```

接下来，导航到您的项目文件以使用`spec/support/jasmine.json`并编辑配置以使其他第三方库能够使用 Jasmine。

```
// for normal projects
"helpers": [
  "helpers/babel.js",
  "helpers/**/*.js"
],

// for typescript projects
"helpers": [
  "helpers/babel.js",
  "helpers/**/*.{js,ts}"
],

```

Jasmine 的大多数测试架构和助手功能都与 [Jest](https://jestjs.io/) 非常相似。

Jasmine 的一些优点包括:

*   不需要 DOM 来测试
*   可用于前端和后端测试
*   可用于异步功能测试
*   具有自定义的等式检查器断言
*   带有内置的匹配器断言

尽管有好处，Jasmine 并不是 React 应用程序的完美测试框架。它不支持快照测试，并且需要第三方库进行并行化和本地 DOM 操作。

## 酶

Enzyme 是 React 的一个 JavaScript 测试工具，它使得测试 React 组件的输出变得更加容易。Enzyme 由 Airbnb 开发和维护，与其他第三方库(如 Jest 和[柴](https://blog.logrocket.com/unit-testing-node-js-applications-using-mocha-chai-and-sinon/))一起被开发者广泛使用。

要使用 Enzyme，请安装:

```
npm i -D enzyme enzyme-adapter-react-16

```

接下来，在您的`src`目录中创建一个文件`enzyme.js`，并编写下面的代码块

```
import Enzyme, { configure, shallow, mount, render } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() });
export { shallow, mount, render };
export default Enzyme;

```

在这里，您将使用下面的酶对一个组件进行简单的测试:

```
import React from 'react';
// we are importing from our enzyme.js
import { shallow } from './enzyme';
import FootNav from './FootNav';

describe('FootNav', () =&gt; {
  test('renders correct text in component', () =&gt; {
    const wrapper = shallow(&lt;MyComponent label="Contact Fortune" /&gt;);

    expect(wrapper.find('.my-label').get(0).props.children).toEqual('Contact Fortune');
  });
});

```

在上面的测试中，您从 Enzyme 导入了`shallow`和一个`FootNav`组件。接下来，您描述了组件并检查它是否正确呈现并返回了预期的文本，在我们的例子中是`Hello Fortune`。为了完成测试，您需要加入我们的期望，即数组的第一个子元素返回文本，`Hello Fortune`。

酶的优势包括以下几点:

*   支持浅层渲染
*   支持 DOM 渲染的特性
*   支持在浅层渲染中使用反应钩子
*   可以根据测试输出模拟运行时

尽管 Enzyme 是一个强大的测试框架，但它也有许多缺点。首先，Enzyme 是建立在定制的`react-dom`之上的，所以它有时不能支持某些新的 React 特性。另一方面，Enzyme 鼓励使用 ID 和 CSS 选择器，这有时会在重构时破坏代码。

你可以通过阅读官方[文档](https://enzymejs.github.io/enzyme/)来了解更多关于酵素的知识。

## 反应测试库

凭借每月超过 1200 万的下载量， [React 测试库](https://blog.logrocket.com/comparing-react-testing-libraries/)是 React 应用程序最广泛使用的测试框架之一。它由 Kent C. Dodds 创建，目前由开源社区提供支持。React 测试库允许开发人员测试组件以轻松模拟用户行为。此外，它内置了 React DOM 测试实用程序，可以模拟 React 应用程序上的实际用户操作。

要使用 React 测试库，首先使用下面的代码块安装:

```
npm install --save-dev @testing-library/react

```

或者使用纱线:

```
yarn add --dev @testing-library/react

```

## 使用

```
import * as React from 'react'

function Modal({children}) {
  const [showModal, setShowModal] = React.useState(false)
  return (
    <div>
      <label htmlFor="toggle">Hello, This is a modal</label>
      <input
        id="toggle"
        onChange={e => setShowModal(e.target.checked)}
        checked={showModal}
      />
      {showModal ? children : null}
    </div>
  )
}

export default Modal

```

React 测试库同时支持钩子和类，所以无论你使用类还是函数组件，测试都是一致的。

使用 React 测试库的一些优点包括:

*   支持用户行为测试
*   内置 DOM 测试实用程序
*   更容易模拟用户工作流和操作
*   兼容 angular、Vue 等其他 UI 框架。

React 测试库的一些缺点包括缺乏对浅层渲染的支持以及无法访问组件状态。

## **总结**

测试 React 应用程序对于当今的开发非常重要。这些库使得创建更好的 React 应用程序和改善整体用户体验变得前所未有的容易。Jest 之类的库更适合测试大型项目，而 React Testing Library 之类的其他库更适合测试用户行为和支持类或函数组件。

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