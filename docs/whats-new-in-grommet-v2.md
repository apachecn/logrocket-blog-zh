# Grommet v2 - LogRocket 博客中的新内容

> 原文：<https://blog.logrocket.com/whats-new-in-grommet-v2/>

## 介绍

[Grommet](https://v2.grommet.io/) 是一个基于 React 的库，它为构建具有极简外观的用户界面提供了端到端的解决方案。它提供了高水平的定制和响应能力，同时不影响可访问性。

用他们自己的话来说，正如在[官方登陆页面](https://v2.grommet.io)上所述，索姆是:

> “部分设计系统，部分框架，一切都棒极了”

在成功运行了 Grommet 的[第一版](https://v1.grommet.io)之后，v2 发布了一个更干净的 UI，改变了技术依赖性，更改了组件及其用法。在本文中，我们将首先简要了解“索环”的工作方式，然后深入探讨从第一个版本到第二个版本的变化。稍后，我们将了解一些索环器件及其使用案例。

## 索环设计语言

![Grommet](img/f5cfff0490c75712a4a77a1c2e48051b.png)

Grommet 的整体美学不同于其他框架，如谷歌的[材料设计](https://material.io/design)或阿里巴巴的[蚂蚁设计](https://ant.design/)，因为它看起来更简约。有很多空白，元素之间的对比度更高，调色板也明显更小。

此外，传递给组件的 props 是以这样一种方式设计的，即甚至不需要显式地指定一行 CSS。例如，任何`Box`内部的填充可以通过使用`pad`支柱来处理，边距可以通过`margin`支柱来控制。类似地，为了处理可访问性，我们只需要传递一个`a11yTitle` prop，提供给它的值将被像屏幕阅读器这样的工具使用。记住这一点，现在让我们看看使用 Grommet 建立我们自己的存储库所需的步骤，以便尝试其中的一些特性。

## Grommet v2 入门

有两种方法可以让我们开始使用索环。一个是直接的方法，而另一个是跟随风格的指南，慢慢地达到期望的结果。

作为第一种方法的一部分，我们只是克隆了 grommet-sample [存储库](https://github.com/grommet/grommet-sample)并在安装完依赖项后运行它。这些是步骤。

克隆存储库:

```
git clone https://github.com/grommet/grommet-sample.git

```

`cd`进入目录:

```
cd grommet-sample

```

安装模块:

```
npm install

```

启动开发服务器:

```
npm install

```

我们应该能够看到 *Todo 应用程序*在我们的本地环境中运行。

如果你更喜欢从 CRA (create-react-app)项目开始，并朝着一个成熟的索环库努力，这个[文档](https://github.com/grommet/grommet-starter-new-app)解释了一步一步的过程，以便手动定制由 CRA 创建的支持索环的库。

## Grommet v2 有什么变化？

如果我们从零开始一个新项目，并从头开始使用 Grommet v2，上面提到的那些方法工作得很好。但是，如果我们最初在 v1 项目上工作，并且想要转移到 v2，有一些额外的步骤要遵循，因为 v2 中引入的一些变化本质上是破坏性的。

## `Grommet`组件

`App`组件是 Grommet v1 的主要`Grommet`组件，这意味着整个应用程序将被包装在一个`App`组件中，如下所示:

```
import App from 'grommet/components/App';

<App>
  {application}
</App>

```

但是，从索环 v2 开始，该组件将由`Grommet`组件替换，因为`App`组件不再出现在 v2 中:

```
import { Grommet } from 'grommet';

<Grommet>
  {application}
</Grommet>

```

## 通过`Grommet`组件支持主题

![Grommet Theme Support](img/db6967e7f9cbf8bac28a8317de7f1fa7.png)

通过可以提供给`Grommet`组件的`theme`属性，可以在 Grommet v2 中将主题应用于整个应用程序。为`theme`道具提供的值必须是有效的主题，如上面截图中显示的`grommet-theme-v1`。为了将主题应用到我们的索环应用程序中，我们编写了如下内容:

```
import { Grommet } from 'grommet';
import { v1 } from 'grommet-theme-v1';

<Grommet theme={v1}>
// The app goes here
</Grommet>

```

其他一些流行主题的例子有`grommet-theme-hp`、`grommet-theme-aruba`和`grommet-theme-nineties`。

## `Box`组件

Grommet 中的`Box`组件相当于 HTML 中的通用组件`div`。`Box`组件用于放置和对齐其中的元素。v2 中 Box 组件发生的一些最显著的变化是:

*   它不再接受`onClick`属性，因为它被要求在任何需要控制点击行为的地方使用`Button`
*   它不再接受`size`道具，因为它被建议使用`width`和`height`来代替

## `List`组件

索环 v1 中的`List`组件被用作包装几个`ListItem`组件的容器。列表看起来像这样:

```
<List>
  <ListItem justify='between' separator='horizontal'>
    <span>Alan</span><span className='secondary'>happy</span>
  </ListItem>
  <ListItem justify='between' separator='horizontal'>
    <span>Chris</span><span className='secondary'>cool</span>
  </ListItem>
  <ListItem justify='between' separator='horizontal'>
    <span>Eric</span><span className='secondary'>odd</span>
  </ListItem>
  ...
</List>

```

上面的代码显示了这个列表:

![Displayed List](img/3ae6175ff5a2c87e594619048313332f.png)

然而，现在可以在 v2 库中使用“不太冗长”的方法使用以下代码获得相同的结果:

```
<List
  primaryKey="name"
  secondaryKey="mood"
  data={[
    { name: 'Alan', mood: 'happy' },
    { name: 'Bryan', mood: 'cool' },
    { name: 'Chris', mood: 'odd' },
  ]}
/>

```

## 删除的组件

一些组件如`Header`、`Footer`、`Article`、`Section`、`Hero`、`Title`、`Quote`、`Card`等。作为护孔环 v1 的一部分被完全移除，并且不再作为独立部件存在。然而，它们中的大多数都可以通过`Box`组件使用，只需给它传递一个适当的标签。例如，为了使用`Header`组件，我们应该这样做:

```
import { Box } from 'grommet';

<Box tag="header">
{/% Stuff inside the Header %/}
</Box>

```

当`Box`组件作为大多数被删除组件的替换时，被删除的`Title`组件可以通过使用`Text`组件并传递如下适当的道具来重新生成:

```
<Text size="large" weight="bold">
  This is as good as a title!
</Text>

```

## 从 v1 到 v2 的技术变化

那是关于组件级别的变化。在技术层面上，Grommet v2 处理某些方面的方式也有一些核心变化。

使用 [babel-grommet-plugin](https://github.com/grommet/babel-plugin-grommet) 可以净化 Grommet v2 中的导入。这意味着，以这种方式从库中导入的任何组件:

```
import { Grommet, Button } from 'grommet';

```

会自动转换为:

```
import Button from 'grommet/components/button';

```

如果 [react-intl](https://www.npmjs.com/package/react-intl) 在最初的 Grommet v1 项目中使用，它需要单独安装，因为 Grommet v2 默认不包括它。

如果最初的 v1 项目使用由 Grommet 提供的 [REST](https://v1.grommet.io/docs/rest/) 实用程序进行 Rest 调用，以创建请求参数或查询参数或设置头，所有这些都需要使用 fetch 库来完成，因为 Rest 实用程序没有打包为 v2 库的一部分。

## 索环 v2 中的组件

随着所有设置的完成和 Grommet v2 的完美运行，我们现在可以深入了解该库提供的一些新组件，这些组件不同于 Material 或 Ant 等其他框架中提供的组件。

## `Stack`组件

![Stack Component](img/6197f5f8991b7fa2970ebda80952cbd7.png)

`Stack` [组件](https://v2.grommet.io/stack)相当独特。它让我们将 UI 元素重叠在彼此之上，可以用来创建我们通常在通知图标中看到的 UX 操作类型的调用。以下是如何在通知图标顶部显示通知计数的示例:

```
import { Stack } from 'grommet';
import { Notification, MailOption } from 'grommet-icons';

<Stack anchor='top-right'>
  <Notification size='large' />
  <Box
    background='brand'
    pad={{ horizontal: 'xsmall' }}
    round
      >
    <Text>8</Text>
  </Box>
</Stack>

```

注意传递给`Stack`组件的`anchor`属性。它决定图标顶部通知编号的锚点。

## `Markdown`组件

![Markdown Formatting](img/06a522c49f5bba18b479eedc3b3f47c4.png)

Grommet 本身支持一个`Markdown` [组件](https://v2.grommet.io/markdown)，这意味着如果我们希望显示一些*，就不需要额外的 markdown 解析器。我们页面上的 md* 内容。我们只需要从库中导入组件，并将所有的 markdown 文本包装在组件中，剩下的工作由 Grommet 完成。下面是它的代码:

```
<Box tag='header' direction='row' pad='medium'>
  <Text size='large' weight='bold'>Markdown Component</Text>
</Box>
<Box pad='medium'>
  <Markdown>
    This is some **markdown**. Grommet is *parsing* the markdown for us! Here is some `inline code` too :)
  </Markdown>
</Box>

```

这是它被渲染成的样子:

![Markdown Component](img/62f1a46e38b85fe2fa8543682e26d73f.png)

## `RangeSelector`组件

这是一个有趣的组件，不同于其他框架提供的组件。它允许我们通过调整范围的起点和终点来选择范围。下面是实现选择器的代码，它再次使用了`Stack`组件:

```
<Stack>
  <Box direction='row' justify='between'>
    {[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10].map(value => (
      <Box key={value} pad='small' border={false}>
        <Text style={{ fontFamily: 'monospace' }}>
          {value}
        </Text>
      </Box>
    ))}
  </Box>
  <RangeSelector
    direction='horizontal'
    invert={false}
    min={0}
    max={10}
    size='full'
    round='small'
    values={this.state.values}
    onChange={values => this.setState({ values })}
  />
</Stack>

```

它在用户界面上显示:

![Rendered Range Selector](img/41a76f650b47e3b113baff8b8847d1d9.png)

## `FileInput`组件

另一个对`Grommet`库非常独特的组件是这个`FileInput`组件。该组件放在 UI 上时，会在页面上呈现一个按钮。单击该按钮时，会打开操作系统的本地文件选择器。选择的文件被传递给`onChange` 监听器。以下是设置组件的代码:

```
<FileInput
  name='file'
  onChange={(event) => {
    const fileList = event.target.files;
    fileList.forEach((f, i) => {
      console.log('file with index: ', f, i);
      // do whatever is supposed to be done with the files
    });
  }}
/>

```

下面是它在用户界面上的呈现方式:

![File Input Component](img/82c7b12d49a72fe697f773b883ce2196.png)

不过，请注意，该组件目前只是索环[稳定](https://github.com/grommet/grommet/wiki/What-is-grommet-stable-and-how-to-use-it%3F)版本的一部分，将在下一个发布版本中与库一起发布。

## `WorldMap`组件

尽管索门环库提供了几种可视化模式，如[图表](https://v2.grommet.io/chart)、[数据图表](https://v2.grommet.io/datachart)、[仪表](https://v2.grommet.io/meter)，但有一个非常突出，那就是[世界地图](https://v2.grommet.io/worldmap)组件。这是一个低分辨率的基于点的地图，其中可以根据点所属的洲以及纬度和经度值为点分配不同的颜色。下面是一些代码，它们将亚洲大陆标为灰色，而将班加罗尔标为红色:

```
<WorldMap
  color='neutral-1'
  continents={[
    {
      name: 'Asia',
      color: 'light-5',
      onClick: () => { console.log('clicked on Asia'); },
    },
  ]}
  onSelectPlace={(lat, lon) => {}}
  places={[
    {
      name: 'Bangalore',
      location: [12, 77],
      color: 'status-critical',
      onClick: () => { console.log('clicked on Bangalore'); },
    },
  ]}
  selectColor='accent-2'
/>

```

渲染时，看起来像这样:

![World Map Component](img/90975a5caf3a8ac4089605d3d8dd27d5.png)

这些只是索环提供的一些有趣的组件。[组件](https://v2.grommet.io/components)页面列出了所有组件。

## 结论

有了如此详尽的组件列表，人们总会找到一个符合需求的组件。此外，与其他 UI 库相比，Grommet 为桌面带来了如此新鲜的美感。

全面的[故事书](https://storybook.grommet.io/?path=/story/all--all)文档页面使它成为一个更加有利可图的选择。如果您正在寻找一个优雅、功能丰富而又简约的框架，专门用于管理仪表板界面，那么 Grommet v2 值得一试。

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