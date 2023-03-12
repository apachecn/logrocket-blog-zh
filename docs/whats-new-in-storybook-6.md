# 故事书 6 中的新内容-日志火箭博客

> 原文：<https://blog.logrocket.com/whats-new-in-storybook-6/>

Storybook 是一组库，它允许我们创建组件，并通过向组件传递各种属性来预览它们。最近发布的故事书 6 包括了许多方便的新功能。事不宜迟，我们来看看新特性以及如何使用它们。

##### 在这里听我们的播客故事集。

零配置设置

## 有了 Storybook 6，我们可以轻松地构建一个故事书:我们所要做的就是在我们的项目上运行`npx sb init`,然后我们就可以添加故事书了。

例如，如果我们想将 Storybook 添加到用 create-react-app 创建的 React 项目中，我们只需使用该命令。但是请注意，`npx sb init`只适用于现有的项目，不能用于空的项目文件夹。

因此，要在 React 项目中使用 Storybook，我们首先运行:

这就创建了`storybook-project` React 项目。然后，我们进入`storybook-project`文件夹，运行`npx sb init`向其中添加故事书。

```
npx create-react-app storybook-project
```

为了将现有的 Storybook 项目升级到最新版本，我们运行`npx sb upgrade`来安装它。然后我们运行`yarn add @storybook/addon-essentials --dev`来安装插件，这些插件呈现我们在组件预览下面看到的内容。

Storybook Essentials 包有一些有用的插件，用于改变我们可以预览组件的视口。它还有一个插件，允许我们使用 JSX 或 MDX 代码记录我们的组件。(MDX 是 Markdown 和 JSX 的混合体。)

其他插件包括:

**actions addon**:让我们记录各种事件发出的事件对象，比如点击、鼠标悬停、键盘事件等。

*   背景插件:让我们在预览组件时将背景设置为我们喜欢的颜色
*   工具栏插件:让我们根据自己的喜好定制故事书屏幕顶部的工具栏
*   Storybook 6 还内置了 TypeScript 支持，因此我们可以立即使用 TypeScript，无需额外配置。

故事的参数

## 在 Storybook 中，参数是我们传递到组件中来改变它的属性。这让我们可以为组件进行预设配置，以便我们可以预览它们。

我们可以在故事文件中设置参数。例如，如果我们有一个 React Storybook 项目，我们可以如下创建我们的组件和故事:

`Button.js`文件有组件文件，`button.css`有`Button`组件的样式。

```
//src/stories/Button.js

import React from 'react';
import PropTypes from 'prop-types';
import './button.css';

export const Button = ({ primary, backgroundColor, size, label, ...props }) => {
  const mode = primary ? 'button-primary' : 'button-secondary';
  return (
    <button
      type="button"
      className={['button', `button-${size}`, mode].join(' ')}
      style={backgroundColor && { backgroundColor }}
      {...props}
    >
      {label}
    </button>
  );
};

Button.propTypes = {
  primary: PropTypes.bool,
  backgroundColor: PropTypes.string,
  size: PropTypes.oneOf(['small', 'medium', 'large']),
  label: PropTypes.string.isRequired,
  onClick: PropTypes.func,
};

Button.defaultProps = {
  backgroundColor: null,
  primary: false,
  size: 'medium',
  onClick: undefined,
};

```

```
//src/stories/button.css

.button {
  font-weight: 700;
  border: 0;
  border-radius: 3em;
  cursor: pointer;
  display: inline-block;
  line-height: 1;
}
.button-primary {
  color: white;
  background-color: #1ea7fd;
}
.button-secondary {
  color: #333;
  background-color: transparent;
}
.button-small {
  font-size: 12px;
  padding: 10px;
}
.button-medium {
  font-size: 14px;
  padding: 11px;
}
.button-large {
  font-size: 16px;
  padding: 12px;
}
```

```
//src/stories/Button.stories.js

import React from 'react';

import { Button } from './Button';

export default {
  title: 'Example/Button',
  component: Button,
  argTypes: {
    backgroundColor: { control: 'color' },
  },
};

const Template = (args) => <Button {...args} />;

export const Primary = Template.bind({});
Primary.args = {
  primary: true,
  label: 'Button',
};

export const Secondary = Template.bind({});
Secondary.args = {
  label: 'Button',
};

export const Large = Template.bind({});
Large.args = {
  size: 'large',
  label: 'Button',
};

export const Small = Template.bind({});
Small.args = {
  size: 'small',
  label: 'Button',
};
```

`Button`组件需要几个道具:

属性让我们可以设置类来以不同的方式设计按钮

*   `backgroundColor`设置背景颜色
*   `size`设置尺寸
*   `label`设置按钮文本
*   其余的道具被传递到`button`元素中。

在此之下，我们添加了一些适当的类型验证，这样我们就可以正确地设置我们的参数，并让 Storybook 为这些参数选择控件。`primary`是一个布尔值，所以它将显示为一个复选框按钮。`backgroundColor`是字符串。

`size`可以是三个值中的一个，所以 Storybook 会自动为它创建一个下拉列表，让我们选择值。`label`是一个字符串属性，所以它将显示为一个文本输入。输入控件位于组件预览下方故事书屏幕的**控件**选项卡中。

参数设置在`Button.stories.js`文件中，该文件是一个包含故事的文件。故事书将挑选任何以`stories.js`或`stories.ts`结尾的文件作为故事文件。

属性让我们设置对参数的控制。在我们的示例中，我们将`backgroundColor`道具设置为由`'color'`控件控制，该控件是颜色选择器。

在那下面，我们有我们的故事代码。我们用我们的`Template`函数从`Button`组件创建一个模板。它接受我们传入的参数，并将它们全部传递给`Button`。

然后，我们调用`Template.bind`,通过将`args`属性设置为带有道具的对象，让我们将参数作为道具传递给`Button`。

更多来自 LogRocket 的精彩文章:

* * *

### 返回一个故事对象，我们可以用参数来配置它。这是一种设置我们想要在故事中预览的道具的便捷方式。

* * *

实时编辑用户界面组件

## **控件**选项卡包含了我们可以用来设置组件道具的所有表单控件。Storybook 选择道具并根据道具类型显示控件。

此外，我们可以在 stories 文件中随意设置表单控件类型，就像我们在前面章节的示例中的`argTypes`属性中看到的那样。这样，我们可以在故事书屏幕上设置道具，并在**画布**标签中查看输出结果。

用颜色选择器改变`backgroundColor`道具的值。`primary`道具被换成了一个切换按钮，让我们将其设置为`true`或`false`。而`size`道具是用下拉菜单控制的，因为它只能是三个值中的一个。

Storybook 会自动完成这项工作，除非我们自己改变控件类型。这是一个非常有用的特性，它让我们无需更改任何代码就可以更改我们的组件。

组合多本故事书

## Storybook 6 引入了通过在另一个项目中引用不同的 Storybook 项目来组合多个 Storybook 项目的能力。

我们可以通过在`.storybook/main.js`文件中添加以下代码来做到这一点:

这让我们可以在一个项目中加载多个故事书项目的故事。现在，如果我们运行`npm run storybook`，我们将在左侧栏看到两个项目中显示的所有故事书故事。

```
module.exports = {
  //...
  refs: {
    react: {
      title: "React",
      url: 'http://localhost:6007'
    },
    angular: {
      title: "Angular",
      url: 'http://localhost:6008'
    }
  }
}
```

在左侧边栏中显示了`title`值，并且`url`具有到达 Storybook 项目的 URL。

结论

## 故事书 6 附带了许多有用的新功能。如果您有一个 Storybook 支持的项目，现在可以用一个命令在现有项目中设置 Storybook。我们可以使用 args 来预设故事中的道具，并轻松预览它们，我们可以通过最小的配置从另一个故事书项目引用另一个故事书项目。

使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

## [LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

Focus on the bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/signup-issue-free).