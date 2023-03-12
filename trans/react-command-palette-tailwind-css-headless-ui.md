# React 命令面板，带有顺风 CSS 和无头 UI

> 原文：<https://blog.logrocket.com/react-command-palette-tailwind-css-headless-ui/>

作为开发人员，我们经常努力尽可能优化我们的工作流，通过利用像终端这样的工具来节省时间。命令选项板就是这样一种工具，它显示 web 或桌面应用程序中最近的活动，支持快速导航、轻松访问命令和快捷方式等。

为了提高您的生产力水平，命令面板本质上是一个 UI 组件，采用模态的形式。命令选项板在具有许多移动部件的大型复杂应用程序中特别有用，例如，在这些应用程序中，您可能需要多次单击或浏览多个下拉菜单来访问资源。

在本教程中，我们将探索如何使用 [Headless UI Combobox 组件](https://headlessui.dev/react/combobox)和 Tailwind CSS 从头开始构建一个全功能的命令面板。

## 命令选项板的真实用例

作为一名开发人员，您很有可能以前使用过命令面板。最流行的是 [VS C](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette) [ode 命令调板](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette)，但是还有很多其他的例子，包括 GitHub 命令调板，Linear，Figma，Slack， [monkeytype](https://monkeytype.com) 等等。

### GitHub 应用程序

[GitHub 最近发布了一个命令面板](https://docs.github.com/en/get-started/using-github/github-command-palette)功能，在撰写本文时仍处于公开测试阶段。它可以让您快速跳转到不同的页面，搜索命令，并根据您当前的上下文获得建议。您还可以通过跳转到其中一个选项或使用特殊字符来缩小您要查找的资源的范围:

![Github Command Palette](img/778e8b007370e664e6157526474d5547.png)

### 线性应用程序

如果你不熟悉 [Linear](https://linear.app) ，它是一个类似于吉拉和 Asana 的项目管理工具，提供了一个非常棒的用户体验。Linear 有一个非常直观的命令面板，让您可以通过其键盘优先的设计来访问整个应用程序的功能。在本教程中，我们将构建一个类似于线性的命令选项板:

![Linear App Command Palette](img/133f8d2e998579ef6daaafe458f01c8a.png)

## 命令选项板的基本功能

几个现代应用程序正在将命令选项板作为一项功能来实现，但是什么是好的命令选项板组件呢？以下是需要注意的事项的简明列表:

*   打开调色板的简单快捷方式，即`ctrl + k`
*   可以从应用程序的任何地方访问它
*   它有广泛的搜索功能，如模糊搜索
*   命令传达意图，易于理解
*   它提供了从一个地方访问应用程序的每个部分

在下一节中，我们将构建我们自己的组件，它包括上面列出的所有特性。让我们开始吧！

## 构建组件

命令面板实际上并不像看起来那么复杂，任何人都可以快速构建一个命令面板。我为这个教程准备了一个 [starter 项目](https://github.com/Mayowa-Ojo/command-palette/tree/starter-project)，这样你就可以很容易地跟着做了。starter 项目是一个 React 和 Vite SPA，它复制了线性问题页面。

### 设置项目

首先，将存储库克隆到您的本地目录中，安装必要的依赖项，并启动开发服务器。该项目使用 Yarn，但是如果您更喜欢 npm 或 pnPm，您可以在运行`npm install`或`pnpm install`之前删除`yarn.lock`文件:

```
// clone repository
$ git clone https://github.com/Mayowa-Ojo/command-palette
// switch to the 'starter-project' branch
$ git checkout starter-project
// install dependencies
$ yarn
// start dev server
$ yarn dev

```

如果您访问`localhost:3000`，您将看到以下页面:

![Github Repository Clone](img/c535ca14f14c609963a0767340ac3a5b.png)

### `CommandPalette`组件

接下来，我们将构建组件。我们将使用无头 UI `combobox`和`dialog`组件。`combobox`将是我们命令面板的基础组件。它有内置的功能，如焦点管理和键盘交互。我们将使用`dialog`组件在模态中呈现我们的命令面板。

为了设计组件的样式，我们将使用 Tailwind CSS。Tailwind 是一个 CSS 实用程序库，可以让你轻松地在 HTML 或 JSX 文件中添加内嵌样式。starter 项目已经包含了顺风的配置。

按如下方式安装必要的依赖项:

```
$ yarn add @headlessui/react @heroicons/react

```

在`components`文件夹中，创建一个`CommandPalette.jsx`文件，并添加以下代码块:

```
import { Dialog, Combobox } from "@headlessui/react";

export const CommandPalette = ({ commands }) => {
  const [isOpen, setIsOpen] = useState(true);

  return (
    <Dialog
      open={isOpen}
      onClose={setIsOpen}
      className="fixed inset-0 p-4 pt-[15vh] overflow-y-auto"
    >
      <Dialog.Overlay className="fixed inset-0 backdrop-blur-[1px]" />
      <Combobox
         as="div"
         className="bg-accent-dark max-w-2xl mx-auto rounded-lg shadow-2xl relative flex flex-col"
         onChange={(command) => {
            // we have access to the selected command
            // a redirect can happen here or any action can be executed
            setIsOpen(false);
         }}
      >
         <div className="mx-4 mt-4 px-2 h-[25px] text-xs text-slate-100 bg-primary/30 rounded self-start flex items-center flex-shrink-0">
            Issue
         </div>
         <div className="flex items-center text-lg font-medium border-b border-slate-500">
            <Combobox.Input
               className="p-5 text-white placeholder-gray-200 w-full bg-transparent border-0 outline-none"
               placeholder="Type a command or search..."
            />
         </div>
         <Combobox.Options
            className="max-h-72 overflow-y-auto flex flex-col"
            static
         ></Combobox.Options>
      </Combobox>
   </Dialog>
  );
};

```

这里发生了一些事情。首先，我们导入`Dialog`和`Combobox`组件。`Dialog`被呈现为`Combobox`的包装器，我们初始化一个名为`isOpen`的本地状态来控制模态。

我们在`Dialog`组件中呈现一个`Dialog.Overlay`，作为模态的覆盖。你可以随心所欲地设计风格，但是在这里，我们只是使用了`backdrop-blur`。然后，我们渲染`Combobox`组件，并向`onChange`道具传递一个处理函数。每当在`Combobox`中选择一个项目时，就会调用该处理程序。您通常希望导航到一个页面或者在这里执行一个动作，但是现在，我们只是关闭了`Dialog`。

`Combobox.Input`将处理搜索功能，我们将在本节稍后添加。`Combobox.Options`呈现一个`ul`元素，该元素包装了我们将要呈现的结果列表。我们传入一个`static`属性，表明我们想要忽略组件的内部管理状态。

接下来，我们在`App.jsx`文件中渲染我们的`CommandPalette`:

```
const App = () => {
   return (
      <div className="flex w-full bg-primary h-screen max-h-screen min-h-screen overflow-hidden">
         <Drawer teams={teams} />
         <AllIssues issues={issues} />
         <CommandPalette commands={commands}/>
      </div>
   );
};

```

让我们来讨论一下我们的命令面板是如何工作的。我们在`data/seed.json`文件中有一个预定义命令的列表。这些命令将在打开时显示在调色板中，并可以根据搜索查询进行过滤。相当简单，对吧？

### `CommandGroup`组件

`CommandPalette`收到一个`commands`道具，这是我们从`seed.json`导入的命令列表。现在，在`components`文件夹中创建一个`CommandGroup.jsx`文件，并添加以下代码:

```
// CommandGroup.jsx
import React from "react";
import clsx from "clsx";
import { Combobox } from "@headlessui/react";
import { PlusIcon, ArrowSmRightIcon } from "@heroicons/react/solid";
import {
   CogIcon,
   UserCircleIcon,
   FastForwardIcon,
} from "@heroicons/react/outline";
import { ProjectIcon } from "../icons/ProjectIcon";
import { ViewsIcon } from "../icons/ViewsIcon";
import { TemplatesIcon } from "../icons/TemplatesIcon";
import { TeamIcon } from "../icons/TeamIcon";

export const CommandGroup = ({ commands, group }) => {
   return (
      <React.Fragment>
         {/* only show the header when there are commands belonging to this group */}
         {commands.filter((command) => command.group === group).length >= 1 && (
            <div className="flex items-center h-6 flex-shrink-0 bg-accent/50">
               <span className="text-xs text-slate-100 px-3.5">{group}</span>
            </div>
         )}
         {commands
            .filter((command) => command.group === group)
            .map((command, idx) => (
               <Combobox.Option key={idx} value={command}>
                  {({ active }) => (
                     <div
                        className={clsx(
                           "w-full h-[46px] text-white flex items-center hover:bg-primary/40 cursor-default transition-colors duration-100 ease-in",
                           active ? "bg-primary/40" : ""
                        )}
                     >
                        <div className="px-3.5 flex items-center w-full">
                           <div className="mr-3 flex items-center justify-center w-4">
                              {mapCommandGroupToIcon(
                                 command.group.toLowerCase()
                              )}
                           </div>
                           <span className="text-sm text-left flex flex-auto">
                              {command.name}
                           </span>
                           <span className="text-[10px]">{command.shortcut}</span>
                        </div>
                     </div>
                  )}
               </Combobox.Option>
            ))}
      </React.Fragment>
   );
};

```

我们只是使用`CommandGroup`组件来避免一些重复的代码。如果您查看线性命令选项板，您会看到命令是基于上下文分组的。为了实现这一点，我们需要过滤出属于同一个组的命令，并对每个组重复这个逻辑。

`CommandGroup`组件接收两个道具，`commands`和`group`。我们将基于当前组过滤命令，并使用`Combobox.Option`组件呈现它们。使用渲染道具，我们可以获得`active`项并相应地对其进行样式化，从而允许我们在保持代码整洁的同时为`CommandPalette`中的每个组渲染`CommandGroup`。

注意，我们在上面的代码块中有一个`mapCommandGroupToIcon`函数。这是因为每个组都有不同的图标，而函数只是一个帮助器，为当前组呈现正确的图标。现在，在同一个文件中的`CommandGroup`组件下添加函数:

```
const mapCommandGroupToIcon = (group) => {
   switch (group) {
      case "issue":
         return <PlusIcon className="w-4 h-4 text-white"/>;
      case "project":

```

现在，我们需要渲染`CommandPalette`中的`CommandGroup`组件。
按如下方式导入组件:

```
import { CommandGroup } from "./CommandGroup";

```

在每个组的`Combobox.Options`中渲染它:

```
<Combobox.Options
   className="max-h-72 overflow-y-auto flex flex-col"
   static
>
   <CommandGroup commands={commands} group="Issue"/>
   <CommandGroup commands={commands} group="Project"/>
   <CommandGroup commands={commands} group="Views"/>
   <CommandGroup commands={commands} group="Team"/>
   <CommandGroup commands={commands} group="Templates"/>
   <CommandGroup commands={commands} group="Navigation"/>
   <CommandGroup commands={commands} group="Settings"/>
   <CommandGroup commands={commands} group="Account"/>
</Combobox.Options>

```

您应该看到现在正在呈现的命令列表。下一步是连接搜索功能。

### 实现搜索功能

在`CommandPalette.jsx`中创建一个本地状态变量:

```
// CommandPalette.jsx
const [query, setQuery] = useState("");

```

将状态更新处理程序传递给`Combobox.Input`中的`onChange` prop。`query`将随着您在输入框中键入的每个字符而更新:

```
<Combobox.Input
  className="p-5 text-white placeholder-gray-200 w-full bg-transparent border-0 outline-none"
  placeholder="Type a command or search..."
  onChange={(e) => setQuery(e.target.value)}
/>

```

一个好的命令面板的关键属性之一是广泛的搜索功能。我们可以对搜索查询和命令做一个简单的字符串比较，但是这并不考虑拼写错误和上下文。不引入太多复杂性的更好的解决方案是模糊搜索。

为此，我们将使用 [Fuse.js](https://fusejs.io/) 库。 [Fuse.js 是一个强大的轻量级模糊搜索库](https://blog.logrocket.com/fuse-js-dynamic-search-react-app/)，没有依赖性。如果您不熟悉模糊搜索，这是一种字符串匹配技术，它倾向于近似匹配而不是精确匹配，这意味着即使查询中有拼写错误，您也可以获得正确的建议。

首先，安装 Fuse.js 库:

```
$ yarn add fuse.js

```

在`CommandPalette.jsx`中，用命令列表实例化`Fuse`类:

```
// CommandPalette.jsx
const fuse = new Fuse(commands, { includeScore: true, keys: ["name"] });

```

`Fuse`类接受一系列命令和配置选项。`keys`字段是我们注册命令列表中哪些字段将被 Fuse.js 索引的地方。现在，创建一个处理搜索并返回过滤结果的函数:

```
// CommandPalette.jsx
const filteredCommands =
  query === ""
     ? commands
     : fuse.search(query).map((res) => ({ ...res.item }));

```

我们检查`query`是否为空，返回所有命令，如果不是，运行带有查询的`fuse.search`方法。此外，我们正在映射结果以创建一个新对象。这是为了保持一致性，因为 Fuse.js 返回的结果有一些新的字段，不会与我们已经有的结构相匹配。

现在，将`filteredCommands`传递给每个`CommandGroup`组件中的`commands`支柱。它应该类似于下面的代码:

```
// CommandPalette.jsx
<CommandGroup commands={filteredCommands} group="Issue"/>
<CommandGroup commands={filteredCommands} group="Project"/>
<CommandGroup commands={filteredCommands} group="Views"/>
<CommandGroup commands={filteredCommands} group="Team"/>
<CommandGroup commands={filteredCommands} group="Templates"/>
<CommandGroup commands={filteredCommands} group="Navigation"/>
<CommandGroup commands={filteredCommands} group="Settings"/>
<CommandGroup commands={filteredCommands} group="Account"/>

```

尝试在命令选项板中搜索，并查看结果是否被过滤:

![Command Palette Search Filter](img/8ad88a518c5cbdf3af80bafbc2ac4f82.png)

我们有一个全功能的命令面板，但是你可能会注意到它总是打开的。我们需要能够控制它的打开状态。让我们定义一个键盘事件来监听组合键并更新打开状态。将以下代码添加到`CommandPalette.jsx`:

```
// CommandPalette.jsx
useEffect(() => {
  const onKeydown = (e) => {
     if (e.key === "k" && (e.metaKey || e.ctrlKey)) {
        e.preventDefault();
        setIsOpen(true);
     }
  };
  window.addEventListener("keydown", onKeydown);
  return () => {
     window.removeEventListener("keydown", onKeydown);
  };
}, []);

```

当组件被安装时，我们使用一个`useEffect`钩子来注册一个`keydown`键盘事件，当组件被卸载时，我们使用一个清理函数来删除监听器。

在钩子中，我们检查组合键是否匹配`ctrl + k`。如果是，那么打开状态被设置为`true`。您也可以使用不同的组合键，但重要的是不要使用与本机浏览器快捷键冲突的组合键。

就是这样！你可以在[成品项目](https://github.com/Mayowa-Ojo/command-palette/tree/finished-project)分支找到这个项目的成品版本。

## react-command-palette:预构建的组件

我们已经探索了如何从头开始构建命令面板组件。然而，您可能不希望每次需要命令面板时都构建自己的命令面板。这就是预构建组件有用的地方。大多数组件库没有提供命令面板，但是 [react-command-palette](https://github.com/asabaylus/react-command-palette) 是一个编写得很好的组件，可以访问并且与浏览器兼容。

若要使用此组件，请将其作为依赖项安装在项目中:

```
$ yarn add react-command-palette

```

导入组件并将命令列表传递给它，如下所示:

```
import React from "react";
import CommandPalette from 'react-command-palette';

const commands = [{
  name: "Foo",
  command() {}
},{
  name: "Bar",
  command() {}
}]

export default function App() {
  return (
    <div>
      <CommandPalette commands={commands} />
    </div>
  );
}

```

有许多配置选项可以用来定制外观和行为，以满足您的需求。例如，`theme`配置让你从许多内置的主题中选择或者创建你自己的定制主题。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 后续步骤

在本文中，您了解了命令选项板，它们的理想用例，以及好的命令选项板由哪些功能组成。您还以详细的步骤探索了如何使用 Headless UI combobox 组件和 Tailwind CSS 构建一个组件。

如果您只是想在应用程序中快速提供这个特性，那么像 react-command-palette 这样的预构建组件是一个不错的选择。感谢您的阅读，如果您有任何问题，请务必留下评论。

## 你的前端是否占用了用户的 CPU？

随着 web 前端变得越来越复杂，资源贪婪的特性对浏览器的要求越来越高。如果您对监控和跟踪生产环境中所有用户的客户端 CPU 使用、内存使用等感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/css-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/css-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/css-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/css-signup)。