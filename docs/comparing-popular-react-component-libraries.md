# 比较流行的 React 组件库

> 原文：<https://blog.logrocket.com/comparing-popular-react-component-libraries/>

React 是一个用于构建用户界面的 JavaScript 库。它有一个了不起的社区，孜孜不倦地开发 UI 组件，帮助加快开发过程，让我们的生活更轻松。

在本文中，我们将比较流行的 React 组件库，并从受欢迎程度、开发人员体验、可读性、文档和软件包大小等方面对每一个进行评估，以帮助您为下一个 React 项目选择正确的库。

## 蚂蚁设计

[Ant Design](https://ant.design/docs/react/introduce) 是一个完全用 TypeScript 构建的 UI 库。它非常适合快速构建 React 应用，因为它有一套高质量的 React 组件，并为浏览器和[服务器端渲染](https://blog.logrocket.com/why-you-should-render-react-on-the-server-side-a50507163b79/)提供了强大的支持。

要查看 Ant Design 的运行情况，请使用以下命令之一安装它。

```
  yarn add antd

```

或者:

```
 npm install antd

```

接下来，添加下面的代码块来创建一个卡组件(我们将为每个库创建一个卡，以便更容易地比较它们)。

```
    import React from 'react';
    import 'antd/dist/antd.css';
    import { Card, Button } from 'antd';

    const AntDesign = () => (
      <Card style={{width: 300}}>
      <h3>Ant Design card</h3>
        <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea</p>
        <Button type="primary">Read more</Button>
      </Card>
    );

    export default AntDesign;

```

正如您在这里看到的，Ant Design 为创建组件提供了一个强大的平台。它们名副其实，可读性很强，元素收到的道具也很清晰。组件也可以定制，以适应我们的设计。

让我们看看 Ant Design 与其他 React 组件库相比如何:

*   **人气**—[GitHub](https://github.com/ant-design/ant-design)上 56.4k 明星， [NPM](https://www.npmjs.com/package/antd) 上每周超过 34.1 万次下载；由阿里巴巴、百度等跨国公司使用
*   **文档** —写得很好，对初学者友好；您可以复制给定组件的源代码，以便在 CodePen、CodeSandbox 或 StackBlitz 上更好地预览它
*   **捆绑** **大小(缩小)** — [antd 2.2mb](https://bundlephobia.com/result?p=antd@3.26.11)

如你所见，Ant Design 非常受欢迎，尤其是在中国，它的文档非常全面。然而，与其他 React 组件库相比，这个包相当大。

## 材料-用户界面

[Material-UI](https://blog.logrocket.com/the-material-ui-grid-system/) 是最流行的 React UI 组件库。它的灵感来自谷歌的材料设计，并由谷歌的材料设计构建而成，具有许多预建的 React 组件，可以帮助您立即创建 React 应用程序。更好的是，它有一些[预置的主题](https://material-ui.com/store/)，你可以用它们来加速你的开发。

要安装 Material-UI，请在您的终端上运行以下两个命令之一。

```
  yarn add @material-ui/core

```

或者:

```
    npm install @material-ui/core

```

接下来，添加下面的黑色代码来创建一个带有材质 UI 的卡片组件。

```
    import React from 'react';
    import { Card, CardActions, CardContent, makeStyles, Button, Typography } from '@material-ui/core';

    const MaterialUI = () => {
      const useStyles = makeStyles({
        root: {
          width: 300,
        },
        title: {
          paddingBottom: '1rem'
        }
      });

      const classes = useStyles();
      return (
    <Card className={classes.root}>
    <CardContent>
    <Typography className={classes.title} variant="h5" component="h1">
    Material UI card
    </Typography>
        <Typography variant="body2" component="p">
        Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea
        </Typography>
      </CardContent>
      <CardActions>
      <Button variant="contained" color="primary">
      Read more
    </Button>
      </CardActions>
    </Card>
    ) };

    export default MaterialUI

```

与 Ant 设计不同，Material-UI 提供了内置的方法来设计组件。`makeStyles()`是有用的，尤其是当你的代码开始变大的时候；它帮助您更快地找到要样式化的元素，并使代码更具可读性。缺点是可读性可能会随着组件的增长而降低。但总的来说，Material-UI 是一个强大的、高度可定制的库。

*   **人气**—[GitHub](https://github.com/mui-org/material-ui)上 54.6k 明星， [NPM](https://www.npmjs.com/package/@material-ui/core) 上每周下载量超 100 万；被 NASA、网飞、亚马逊等公司使用。
*   **文档** —简单易懂，对初学者友好；您可以在文档中找到给定组件的源代码，甚至可以在 CodeSandbox 中编辑它
*   **包大小(缩小)**—[@ material-ui/core 314.5 kb](https://bundlephobia.com/result?p=@material-ui/core@4.9.3)

与 Ant Design 相比，Material UI 的包大小非常小。它也很受欢迎，有一个很棒的生态系统，值得您在下一个 React 项目中考虑。

## 反应引导

React Bootstrap 对于喜欢 Bootstrap 的开发人员来说是梦想成真，因为它将 Bootstrap 的强大和简单带到了 React。它包括大量预构建的纯 React 组件，没有第三方库。一般来说，React Bootstrap 提供了与 Bootstrap 相同的大部分功能。

在终端中运行以下命令之一来安装 React Bootstrap。

```
    yarn add react-bootstrap bootstrap

```

或者:

```
    npm install react-bootstrap bootstrap

```

接下来，像前面一样，添加这个代码块来创建一张卡片:

```
    import React from 'react';

    import 'bootstrap/dist/css/bootstrap.min.css';
    import { Button, Card } from 'react-bootstrap';

    const ReactBootstrap = () => (
    <Card style={{ width: 300 }}>
      <Card.Body>
        <Card.Title>React Bootstrap card</Card.Title>
        <Card.Text>
        Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea
        </Card.Text>
        <Button variant="primary">Read more</Button>
      </Card.Body>
    </Card>
    )

    export default ReactBootstrap;

```

React 引导将引导风格扩展到 React 组件。之前有引导经验会有所帮助，但不是必需的。代码可读性很强，组件名也很容易记住。

现在是成绩单:

*   **人气** — 17k [GitHub](https://github.com/react-bootstrap/react-bootstrap) 明星和在 [NPM](https://www.npmjs.com/package/react-bootstrap) 上每周超过 55.9 万的下载量
*   文档——文档对初学者友好，例子清晰，你可以复制一个组件的源代码
*   **Bundle** **大小(缩小)**—[react-bootstrap 112.9 kb](https://bundlephobia.com/result?p=react-bootstrap@1.0.0-beta.16)；[自举 61.7kB](https://bundlephobia.com/result?p=bootstrap@4.4.1)

React Bootstrap 需要引导库作为依赖项。但是，包的大小很小。如果你喜欢 Bootstrap，React Bootstrap 肯定是你的 React 应用要考虑的东西。

## 蓝图

Blueprint 是一个 UI 库，主要用于桌面应用程序，因为它针对数据密集型界面进行了优化和构建。您仍然可以使用它来构建 web 应用程序，但您不会获得 Blueprint 的全部功能。

通过在终端上运行以下命令之一，安装 Blueprint 并创建卡。

```
    yarn add @blueprintjs/core

```

或者:

```
    npm install @blueprintjs/core

```

要创建一个卡组件，请添加以下代码行。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
    import React from 'react';
    import "@blueprintjs/core/lib/css/blueprint.css"
    import { Button, Card, Classes } from "@blueprintjs/core";

    const Blueprint = () => (
    <Card style={{width: 300}} className={Classes.CARD}>
        <h3>Blueprint card</h3>
        <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea</p>
        <Button intent="primary" text="Read more" className={Classes.BUTTON} />
    </Card>
      );

    export default Blueprint;

```

您仍然可以对 Blueprint 使用相同的组件命名约定。您还可以利用 CSS 实用程序来设计组件的样式，这有助于提高代码的可读性。

现在让我们来分析 Blueprint 的受欢迎程度和代码质量。

*   **人气**——在 [GitHub](https://github.com/palantir/blueprint) 上有 15000 位明星，在 [NPM](https://www.npmjs.com/package/@blueprintjs/core) 上有超过 107000 次下载
*   **文档** —文档是交互式的，你可以从 docs 或 GitHub 中复制给定组件的源代码
*   **捆绑** **大小(缩小)**—[@ blue printjs/core 588.2 kb](https://bundlephobia.com/result?p=@blueprintjs/core@3.23.1)

总的来说，Blueprint 是构建 React 应用程序的一个很好的组件库，尤其是具有复杂、数据密集型界面的桌面应用程序。

## 语义用户界面

与 Blueprint 不同，语义 UI 是为制作 React 应用程序而设计的。它有一些预建的主题，你可以通过安装它或者使用 [Create React App](https://blog.logrocket.com/getting-started-with-create-react-app-d93147444a27/) 来使用。语义 UI 组件也具有很高的响应性，并且它拥有很好的浏览器支持。

让我们通过在终端上运行以下命令之一来创建一个具有语义 UI 的卡片组件。

```
    yarn add semantic-ui-react  semantic-ui-css

```

或者:

```
  npm install semantic-ui-react  semantic-ui-css

```

接下来，添加此代码块来创建一张卡片:

```
    import React from 'react'

    import 'semantic-ui-css/semantic.min.css';
    import { Card, Button } from 'semantic-ui-react'

    const SemanticUI = () => ( 
      <Card style={{width: 300}}>
        <Card.Content>
          <Card.Header>Semantic UI</Card.Header>
          <Card.Description>
          Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea
          </Card.Description>
          <Button primary>Read more</Button>
        </Card.Content>
      </Card>
    )

    export default SemanticUI

```

使用语义 UI 创建组件与在 React Bootstrap 中创建组件非常相似，除了传递属性的方式不同。该组件也是可读和易于理解的。

让我们看看语义 UI 的特性是如何叠加的。

*   **人气**——在[Git](https://github.com/Semantic-Org/Semantic-UI-React)[H](https://github.com/Semantic-Org/Semantic-UI-React)ub 上 10.9k 星，在 [NPM](https://www.npmjs.com/package/semantic-ui-react) 上超过 14.1 万次下载
*   **文档** —文档很棒；语义 UI 提供了构建组件所需的代码，您甚至可以在 CodeSandbox 上预览它
*   **Bundle size(minified)**—[semantic-ui-react 312 kb](https://bundlephobia.com/result?p=semantic-ui-react@0.88.2)； [semantic-ui-css 272.1kB](https://bundlephobia.com/result?p=semantic-ui-css@2.4.1)

语义 UI React 库需要语义 UI CSS 包来正确设置组件的样式。虽然这将在您的项目上增加一些 KB，但是包的大小仍然很小。

## 常绿树

如果你正在寻找一个低层次、极简设计的 React UI 组件库，你会喜欢 Evergreen。它包括几个实用组件，您可以使用它们来构建一个完整的 React 组件，它非常适合企业 web 应用程序，因为它比大多数库更灵活、更具可定制性。

让我们通过在终端中运行以下两个命令之一来安装 Evergreen。

```
    yarn add evergreen-ui

```

或者:

```
    npm install evergreen-ui

```

接下来，创建一个卡组件。

```
    import React from 'react';
    import { Pane, Text, Button, Heading } from 'evergreen-ui'

    const Evergreen = () => (
        <Pane
        display="flex"
        alignItems="center"
        padding={16}
        justifyContent="center"
        flexDirection="column"
        border="default"
        width={300}>
        <Heading is="h1">Evergreen Card</Heading>
        <Text marginY={10}>Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea</Text>
        <Button appearance="primary">Read more</Button>
      </Pane>

    );

    export default Evergreen;

```

如你所见，制作卡片的方法和一些名字已经改变了。这就是常青树的工作方式；它提供了极简的组件，可以扩展以适应您的设计系统。

至于规格:

*   **流行度**——在 [GitHub](https://github.com/segmentio/evergreen) 上有 9.1k 颗星星，在 [NPM](https://www.npmjs.com/package/evergreen-ui) 上有 4000 多次下载，Evergreen 没有像其他组件库那样被广泛使用；由[段](https://segment.com/)开发使用
*   **文档** —文档有很好的解释，很容易找到给定组件的源代码
*   捆绑包大小(缩小)— [evergreen-ui 751.2kB](https://bundlephobia.com/result?p=semantic-ui-react@0.88.2)

归根结底，Evergreen 是一个很好的 React 组件库，具有相对较小的包大小、无数的预构建组件和可以定制以满足您需求的底层设计。

## 反应阱

Reactstrap 类似于 React Bootstrap，只是它不依赖于 Bootstrap 来正常工作。然而，Reactstrap 也引入了引导风格来对组件做出反应。

要查看 Reactstrap 的运行情况，请运行以下命令之一。

```
    yarn add reactstrap

```

或者:

```
    npm install reactstrap

```

创建一个卡组件。

```
    import React from 'react';
    import {
      Card, CardText, CardBody,
      CardTitle, Button
    } from 'reactstrap';

    const Reactstrap = () => (
          <Card style={{width: 300}}>
            <CardBody>
              <CardTitle>Reactstrap Card</CardTitle>
              <CardText>Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea</CardText>
              <Button color="primary">Read more</Button>
            </CardBody>
          </Card>
      );

    export default Reactstrap;

```

语法类似于 React Bootstrap，但是 Reactstrap 使用 camel case 来命名组件，而不是点。代码易于理解和阅读。

*   **人气** — 8.8k [GitHub](https://github.com/reactstrap/reactstrap) 明星， [NPM](https://www.npmjs.com/package/reactstrap) 下载量超过 256k
*   **文档** —文档解释得很清楚，很容易找到给定组件的源代码；还包括一些预构建的主题，您可以使用它们来简化您的开发
*   **包大小(缩小)** — [reactstrap 150.4kB](https://bundlephobia.com/result?p=reactstrap@8.4.1)

Reactstrap 本质上是 React Bootstrap 的替代方案。它们服务于相同的目的，并且它们的束大小相似。

## 温泉 UI

Onsen UI 与我们之前检查过的库有些不同。因为它是以移动为先的设计思想构建的，所以 Onsen UI 主要用于构建跨平台的移动 web 应用程序。

通过运行以下两个命令之一来安装 Onsen UI。

```
    yarn add onsenui react-onsenui

```

或者:

```
    npm install onsenui react-onsenui

```

现在用下面的代码创建一个卡组件。

```
    import React from 'react';
    import 'onsenui/css/onsenui.css';
    import 'onsenui/css/onsen-css-components.css';
    import { Card, Button } from 'react-onsenui';

    const OnsenUI = () => (
      <Card style={{width: 300}}> 
      <h3>Onsen UI card</h3>
        <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea</p>
        <Button modifier="cta">Read more</Button>
      </Card>
    );

    export default OnsenUI;

```

尽管它是移动设计，但代码与我们比较过的大多数组件库有些相似。组件和道具通俗易懂，名副其实。

从受欢迎程度、文档质量和捆绑包大小来看，Onsen UI 怎么样？

*   **人气**——[GitHub](https://github.com/OnsenUI/OnsenUI)上 7.8k 星， [NPM](https://www.npmjs.com/package/react-onsenui) 上 2000 次下载，温泉 UI 并没有被广泛使用
*   **文档** —与其他库相比相对较弱。虽然有一种游乐场，但没有提供代码，并且由于缺少示例，很难理解组件。文档对初学者来说绝对不友好
*   **捆大小(缩小)**—[react-on senui 48kB](https://bundlephobia.com/result?p=onsenui@2.10.10)；[翁塞尼 319.3kB](https://bundlephobia.com/result?p=onsenui@2.10.10)

Onsen UI 可以用于所有类型的 React 应用程序，但当使用移动优先的方法开发 React 应用程序时，它真的会大放异彩。

## 结论

无论您正在从事什么类型的项目，都有许多 UI 组件库可以帮助您快速轻松地制作有用的、功能丰富的 React 应用程序。大多数都是可定制的，包括有用的预建组件。更好的是，一些 UI 库提供了预构建的主题，您可以使用这些主题很快创建一个网站。希望这种比较能够帮助您为下一个 React 项目选择正确的库。

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