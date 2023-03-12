# 风格化组件 5.0 的新特性

> 原文：<https://blog.logrocket.com/new-in-styled-components-5-0/>

## 样式组件

[Styled components](http://styled-components.com/) 是一个 CSS-in-JS 样式框架，它使用 JavaScript 中的标记模板文字和 CSS 的强大功能来提供一个平台，允许您编写实际的 CSS 来样式化 react 组件。本质上，样式化组件是用[样式化组件](http://styled-components.com)库编写的易于制作的 react 组件，在这个库中，您可以用 JavaScript 代码中的普通 CSS 来样式化组件。在官方文档页面上，您会看到下面的示例:

```
const Button = styled.a`
 /* This renders the buttons above… Edit me! */
 display: inline-block;
 border-radius: 3px;
 padding: 0.5rem 0;
 margin: 0.5rem 1rem;
 width: 11rem;
 background: transparent;
 color: white;
 border: 2px solid white;
/* The GitHub button is a primary button
 * edit this to target it specifically! */
 ${props => props.primary && css`
 background: white;
 color: palevioletred;
 `}
`
```

我们可以清楚地看到按钮是一个 JavaScript 变量，反勾号中定义的样式是普通的 CSS 样式。我们还看到了普通 CSS 样式的嵌套样式属性。这就是样式化组件在 JavaScript 中呈现 CSS 的方式。

最近，styled-components 团队发布了一个他们命名为 beast mode 的新版本，它的服务器端渲染速度快了 50%，客户端渲染速度快了 20%，包大小小了 19%，支持 RTL，并且没有重大更改！

## React DevTools 中的钩子支持

这个新版本的 styled-components 在性能和内存效率方面有很大的变化。开发工具中的风格化组件现在非常干净，包含的代码也更少了。这是因为针对该版本进行了 React hooks 重构。下面是使用以前版本时 React DevTools 中样式化的`TagLine`组件的样子:

```
<TagLine>
  <StyledComponent forwardedRef={null}>
    <Context.Consumer>
      <Context.Consumer>
        <h2 className=”H2-sc-1izft7s-7”>Hello world</h2>
      </Context.Consumer>
    </Context.Consumer>
  </StyledComponent>
</TagLine>
```

下面你可以看到使用这个新版本时，React DevTools 中相同样式的组件看起来是什么样子:

```
<TagLine>
  <h2 className=”H2-sc-1izft7s-7”>Hello world</h2>
</TagLine>
```

现在组件嵌套明显减少，因此代码块也更加整洁。

## 超级速度

styled-components 的团队一直致力于让性能更好，他们对这一决心的奉献总是产生惊人的结果。从两年前发布的[版本 2 开始，我们已经见证了各种版本在速度上的巨大进步。](https://medium.com/styled-components/announcing-v2-f01ef3766ac2)

这些包括:

这个最新版本包括更小的包大小(16.2kB 对 13.63kB min+gzip)，更快的客户端挂载，更快的动态样式更新，以及更快的服务器端渲染。

有了这个新的更新，一直很快的 styled-components 现在是你能找到的最快的 CSS-in-JS 库之一。

安装深度组件树基准。越低越好。总体速度的新提升是由 styled-component 使用的新核心样式表引擎推动的，它在新版本中进行了重建，非常注重性能和正确性。它已经用很多工具进行了广泛的测试，但是团队仍然在寻找社区成员的反馈。如果你测试新版本有任何问题，你可以[联系这里](https://github.com/styled-components/styled-components/issues/new)。

> 如果你使用 jest 风格的组件，确保[也更新到测试版](https://github.com/styled-components/jest-styled-components/releases/tag/v7.0.0-beta.0)！

## StyleSheetManager 的改进

新版本对 StyleSheetManager 进行了新的改进。StyleSheetManager 现在在版本 5 中能够用插件扩展 stylis，CSS 解析器。

这被证明对很多用例非常有用，对自定义样式的全自动 RTL 的支持是这个新版本中可能实现的事情之一。

## RTL 支持

在样式组件版本 5 中，您现在可以将您的样式从默认的从左到右对流转换为从右到左的方法。

您可以将您的样式从默认的从右向左转换，如下所示:

```
import { StyleSheetManager } from 'styled-components';
import stylisRTLPlugin from 'stylis-rtl';
<StyleSheetManager stylisPlugins={[stylisRTLPlugin]}>
  <App />
</StyleSheetManager>
```

在改进的样式表管理器的巨大帮助下，您会看到仅这个代码块就可以实现转换。

这打开了许多可能性和大量的插件，使得样式化的组件使用起来更加令人兴奋。

## 入门指南

要升级到最新版本，只需在您的终端中运行以下命令。

```
npm install [email protected]
```

要做到这一点，您必须确保您使用的是 React 和 React DOM 的最新版本 16.8，因为它们支持钩子。

## 样式组件项目

Styled-components 是一个非常受欢迎的库。它现在可以被称为一个行业标准的 CSS-in-JS 库，并不断更新，显示了核心团队对项目进展的强烈奉献精神。

styled-components 的团队希望通过参加会议、组织峰会等等来扩展这个项目。大多数核心团队成员来自世界各地，需要支持来继续维护这个令人敬畏的项目。如果你、你的团队或者你的公司使用样式化组件，考虑为样式化组件 [OpenCollective](https://opencollective.com/styled-components) 做贡献，使他们的工作更容易，扩展更快。

## 结论

您已经看到了新样式组件版本 5 中的新特性。Styled-components 作为 CSS-in-JS 框架的业界最爱，最近获得了很高的采用率，你最喜欢的新特性是什么？

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用情况、内存使用情况等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。