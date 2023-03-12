# 使用 React 和 Ant Design 实现数据可视化

> 原文：<https://blog.logrocket.com/data-visualization-with-react-ant-design/>

数据可视化是用图表、图片等表示信息的过程。有许多 JavaScript 库可以帮助表示数据，其中最流行的是 [React 和 D3.js](https://blog.logrocket.com/data-visualization-in-react-using-react-d3-c35835af16d0/) 。这些库非常强大，被一些人认为是最好的数据可视化框架——但是 D3 语法可能会令人困惑。

在本文中，我们将带您踏上使用 React 和 Ant Design 的数据可视化之旅。Ant Design 是作为一个 React UI 库实现的，它是一个高质量组件的集合，用于构建丰富的交互式用户界面。

为了正确地阅读本文，读者应该具备以下方面的中级知识:

*   Java Script 语言
*   反应
*   节点. js
*   像 Visual Studio 代码等代码编辑器。

在本文中，我们旨在向您展示以下内容:

让我们继续吧！

## 设置环境

首先，我们将使用节点包管理器(npm)创建一个 React 应用程序。为此，我们需要遵循以下步骤。

首先，[下载 Node.js](https://nodejs.org/en/download/) ，如果你还没有的话。成功安装节点后，我们将在 Visual Studio 代码中打开终端，创建 React 文件夹。请遵循以下步骤:

*   `cd`到我们的首选目录
*   在我们的终端中键入下面的脚本。注意！`my-apps`是我们的 React 文件夹名称

```
npx create-react-app my-apps
```

安装完节点模块后，在终端上键入`cd my-apps`，通过输入命令`npm start`启动我们的 React 应用程序。

在实现上述内容后，我们的 React 应用程序应该如下所示:

![Our sample React app](img/9032f13c846c9e413b641e39c9392f6d.png)

## 安装 Ant Design

在创建我们的 React 文件夹后，在某些情况下，模块中缺少 Ant 设计。添加 Ant 设计最简单的方法是在我们的终端中键入`npm install`命令来安装所有必要的模块。

如果我们有一个现有的节点模块文件夹，我们将输入`yarn add antd`将 Ant 设计模块添加到我们的节点模块文件夹中。然后，我们需要使用以下命令将 Ant 设计图表库添加到我们的`@antdesign`文件夹中:

```
yarn add @ant-design/charts

```

是一个库，它将帮助我们开发分析所需的图表。由于我们正在处理流程图，我们还需要导入流程图库:

```
yarn add @ant-design/flowchart

```

和`react-dom and react`一起，蚂蚁设计的流程图也依赖于`antd icons, @ant-design/icons, and lodash`。为此，我们需要将这两个包安装到我们的应用程序中:

```
yarn add lodash
yarn add @ant-design/icons

```

完成所有这些后，我们的项目现在应该可以顺利运行了。这是我们的应用程序文件夹结构的图像:

![The recommended app folder structure](img/61b96c0f7d897af322df6d75b45444f2.png)

我们的`package.json`文件通常应该包含以下文件:

`"dependencies": { "@ant-design/icons": "^4.6.0", "antd": "^4.6.3", "lodash": "^4.17.20", "react": ">=16.8.4", "react-dom": ">=16.8.4" }`

## 使用流程图的数据表示

接下来，我们将在我们的`src`文件夹中创建一个名为`components`的子文件夹。这个文件夹将包含一个用于流程图的 JavaScript 文件。我们可以使用`import`或`require`语句来开始。

首先，我们将通过将下面的代码添加到我们的`App.js`文件来导入必要的 CSS 文件:

```
import "@ant - design/flowchart/dist/index.css";

```

注意！如果不导入 CSS，流程图小部件将会不成比例和不负责任。

![Flowchart behavior without Ant Design's CSS flowchart file](img/4fc6aed95c0d5ccb155f3105b613a647.png)

Flowchart behavior without Ant Design’s CSS flowchart file

现在，我们可以将流程图(和其他图表)导入到`App.js`文件中。

对于我们的流程图，我们将在一个名为`flowchart.js`的新文件中创建一个 React 组件。这个文件将存放我们的流程图组件，而流程图组件将保存我们代码的数据集。

```
import React from 'react';
import ReactDOM from 'react-dom';
import { Flowchart } from '@ant-design/charts';
const SampleFlowchart = () => {
 return (
  <>

  </>
 );
};
export default SampleFlowchart;

```

在上面的代码片段中，我们从 Ant 设计图表库中导入了`react`、`react-dom`和`Flowchart`。接下来，我们使用 React 的功能组件呈现机制，创建我们的`SampleFlowChart`组件并将其导出为默认组件。

```
<div style={{ height: 600 }}>
  <Flowchart
    onSave={(d) => {
      console.log(d, JSON.stringify(d));
    }}
    toolbarPanelProps={{
      position: {
        top: 0,
        left: 0,
        right: 0,
      },
    }}
    scaleToolbarPanelProps={{
      layout: 'horizontal',
      position: {
        right: 0,
        top: -40,
      },
      style: {
        background: 'transparent',
      },
    }}
    canvasProps={{
      position: {
        top: 40,
        left: 0,
        right: 0,
        bottom: 0,
      },
    }}
    nodePanelProps={{
      position: { width: 160, top: 40, bottom: 0, left: 0 },
    }}
    detailPanelProps={{
      position: { width: 200, top: 40, bottom: 0, right: 0 },
    }}
  />
</div>

```

插入上面的代码将显示我们的流程图仪表板，其中填充了必要的小部件，以便为任何应用程序流创建流程图。您可以决定使用这些结构中的任何一种，包括平行四边形、椭圆形，甚至菱形。

![The available widgets you can use in your diagrams](img/a6033a2262032c6aaa094727076c3c41.png)

由于 Antd 是基于中文的框架，所以界面上的文本是中文的。不要惊慌！有一种方法可以绕过它。您将在浏览器中安装[谷歌翻译免费插件扩展](https://chrome.google.com/webstore/detail/google-translate/aapbdbdomjkkjkaonfhkkikfgjllcleb?hl=en)。指定谷歌翻译网站，你应该准备好了。

![Translate the flowchart UI from Chinese to English](img/c0e0d0fa47abf728af6527891e7038c6.png)

### 解释代码片段

流程图小部件创建显示在浏览器上的面板仪表板:

```
onSave={(d) => {
    console.log(d, JSON.stringify(d));
}}

```

每当我们保存更新时，都会将我们设计生成的数据集记录到控制台。这些数据集可与任何人共享，以重新生成流程图。

```
toolbarPanelProps={{
          position: {
            top: 0,
            left: 0,
            right: 0,
  },
}}

```

上面的代码片段设置了界面工具栏的位置。通过改变这些值，我们可以改变工具栏的位置。

![Our UI before we set the toolbar position](img/3f9d253a8937db09749326b28a57e28c.png)

![Our UI after we set the toolbar position](img/b87b9ebe917c954e26aa8830afe30a39.png)

```
scaleToolbarPanelProps={{
          layout: 'horizontal',
          position: {
            right: 0,
            top: -40,
          },
          style: {
            background: 'transparent',
          },
        }}

```

以上是屏幕右上方的“适合屏幕”、“全屏”和“放大/缩小”按钮的位置。更改这些值可以指定我们希望每个按钮在仪表板上的位置。

![Before we position the buttons](img/0c154f55b3b87265ff77f3ca3dfe1f86.png)

![After we position the button](img/d7a8761607292dc0a3f4663e4eae16e0.png)

```
canvasProps={{
          position: {
            top: 40,
            left: 0,
            right: 0,
            bottom: 0,
          },
        }}

```

这些道具处理中央画布在屏幕上的定位，您可以从侧边栏拖放小部件来显示它们。

```
nodePanelProps={{
          position: { width: 160, top: 40, bottom: 0, left: 0 },
        }}
        detailPanelProps={{
          position: { width: 200, top: 40, bottom: 0, right: 0 },
        }}

```

这些道具处理屏幕两侧的面板。`nodepanelprops`处理左侧面板中的节点。另一方面，右侧面板显示了突出显示的小部件的详细信息。

![The nodePanelProps handle the nodes on the left panel](img/3e5a355214cb1e9278a0573a17e8a575.png)

![The right panel displays the highlighted widget](img/f976d7cc921661c72fb128b1f2d9875f.png)

流程图小部件有一个默认的`data`属性。这个`data`属性接受对象的值，每个对象有两个属性:节点和边。这两个属性都是应用程序中所有节点的数组。

然后从`onSave`函数调用中获得的数据显示在界面上。

因此，最终代码将是:

```
import React from 'react';
import ReactDOM from 'react-dom';
import { Flowchart } from '@ant-design/charts';

const SampleFlowchart = () => {
  return (
    <div style={{ height: 600 }}>
      <Flowchart
        onSave={(d) => {
          console.log(d, JSON.stringify(d));
        }}
        toolbarPanelProps={{
          position: {
            top: 0,
            left: 0,
            right: 0,
          },
        }}
        scaleToolbarPanelProps={{
          layout: 'horizontal',
          position: {
            right: 0,
            top: -40,
          },
          style: {
            background: 'transparent',
          },
        }}
        canvasProps={{
          position: {
            top: 40,
            left: 0,
            right: 0,
            bottom: 0,
          },
        }}
        nodePanelProps={{
          position: { width: 160, top: 40, bottom: 0, left: 0 },
        }}
        detailPanelProps={{
          position: { width: 200, top: 40, bottom: 0, right: 0 },
        }}
      />
    </div>
  );
};
export default SampleFlowchart;

```

![Our flowchart canvas](img/955d3c3c0bab56f62ed0d92f8cc782f8.png)

![Our final product, a basic flowchart](img/94c3d3fb825cc91fc75b40cba3b36fdc.png)

## 使用多行图表的数据可视化

对于我们的多行图表，我们将创建一个新文件来作为组件存放它。我们把这个文件命名为`multiline.js`。

下面是我们将粘贴到该文件中的完整代码:

```
import React, { useState, useEffect } from 'react';
import ReactDOM from 'react-dom';
import { Line } from '@ant-design/charts';

const SampleMultiLine = () => {
  const data = [
    {
      "year": "2010",
      "value": 3107,
      "category": "Cars"
    },
    {
      "year": "2010",
      "value": 3812,
      "category": "Buses"
    },
    {
      "year": "2010",
      "value": 1696,
      "category": "Bikes"
    },
    {
      "year": "2010",
      "value": 446,
      "category": "train"
    },
    {
      "year": "2010",
      "value": 67,
      "category": "Walk"
    },
    {
      "year": "2011",
      "value": 3134,
      "category": "Cars"
    },
    {
      "year": "2011",
      "value": 4055,
      "category": "Buses"
    },
    {
      "year": "2011",
      "value": 1756,
      "category": "Bikes"
    },
    {
      "year": "2011",
      "value": 494,
      "category": "train"
    },
    {
      "year": "2011",
      "value": 64,
      "category": "Walk"
    },
    {
      "year": "2012",
      "value": 3200,
      "category": "Cars"
    },
    {
      "year": "2012",
      "value": 4106,
      "category": "Buses"
    },
    {
      "year": "2012",
      "value": 1783,
      "category": "Bikes"
    },
    {
      "year": "2012",
      "value": 519,
      "category": "train"
    },
    {
      "year": "2012",
      "value": 65,
      "category": "Walk"
    },
    {
      "year": "2013",
      "value": 3220,
      "category": "Cars"
    },
    {
      "year": "2013",
      "value": 4126,
      "category": "Buses"
    },
    {
      "year": "2013",
      "value": 1806,
      "category": "Bikes"
    },
    {
      "year": "2013",
      "value": 554,
      "category": "train"
    },
    {
      "year": "2013",
      "value": 68,
      "category": "Walk"
    },
    {
      "year": "2014",
      "value": 3280,
      "category": "Cars"
    },
    {
      "year": "2014",
      "value": 4117,
      "category": "Buses"
    },
    {
      "year": "2014",
      "value": 1823,
      "category": "Bikes"
    },
    {
      "year": "2014",
      "value": 568,
      "category": "train"
    },
    {
      "year": "2014",
      "value": 68,
      "category": "Walk"
    }
  ];

  const config = {
    data,
    xField: 'year',
    yField: 'value',
    seriesField: 'category',
    xAxis: {
      type: 'time',
    },
    yAxis: {
      label: {
        formatter: (v) => `${v}`.replace(/\d{1,3}(?=(\d{3})+$)/g, (s) => `${s},`),
      },
    },
  };
  return <Line {...config} />;
};
export default SampleMultiLine;

```

### 解释代码片段

和我们前面的例子一样，上面的代码导入了库提供的`react`、`react-dom`和 Ant 设计折线图。

我们将分别解释下面的代码片段，因为每一个都是我们项目的焦点。

片段 1:

```
const SampleMultiLine = () => {
  return (
      <>
      </>
  );
};
export default SampleMultiLine;

```

片段 2:

```
const config = { data, xField: 'year', yField: 'value', seriesField: 'category', xAxis: { type: 'time', }, yAxis: { label: { formatter: (v) => ${v}.replace(/\d{1,3}(?=(\d{3})+$)/g, (s) => ${s},), }, }, };

```

每一个都描述了我们的多线图的各种配置。这里有两件重要的事情需要注意:

*   配置变量是一个对象，它在一个`data`属性中接受数据集
*   另一方面，`xField`属性接收我们的数据集中的键值，我们希望在 x 轴上显示这些键值

因此，对于数据集中属性`category`的每个不同/唯一的值，图表将创建一条新的线来表示它。

对于配置变量的`xAxis`和`yAxis`属性，我们指定一个字符串来标识所表示的值的类型。

一旦实现了上述内容，我们的多行图表应该显示如下:

![Our final multiline chart](img/8b7b790653ee8df67d027c6f241a61ae.png)

## 使用堆叠条形图的数据可视化

堆积条形图是一种分段图，用于比较整体的各个部分。对于我们的堆积条形图，我们将创建一个名为`stackedbar.js`的 JavaScript 文件，并添加以下代码:

```
import React, { useState, useEffect } from 'react';
import ReactDOM from 'react-dom';
import { Bar } from '@ant-design/charts';
const DemoBar = () => {
  const data = [
    {
      instrument: 'drums',
      value: 5,
      type: 'boys',
    },
    {
      instrument: 'bass',
      value: 9,
      type: 'boys',
    },
    {
      instrument: 'ukelele',
      value: 2,
      type: 'boys',
    },
    {
      instrument: 'cymbals',
      value: 3,
      type: 'boys',
    },
    {
      instrument: 'lead',
      value: 7,
      type: 'boys',
    },
    {
      instrument: 'keyboard',
      value: 3,
      type: 'boys',
    },
    {
      instrument: 'violin',
      value: 4,
      type: 'boys',
    },
    {
      instrument: 'cello',
      value: 6,
      type: 'boys',
    },
    {
      instrument: 'accordion',
      value: 4,
      type: 'boys',
    },
    {
      instrument: 'drums',
      value: 9,
      type: 'girls',
    },
    {
      instrument: 'bass',
      value: 3,
      type: 'girls',
    },
    {
      instrument: 'ukelele',
      value: 6,
      type: 'girls',
    },
    {
      instrument: 'cymbals',
      value: 7,
      type: 'girls',
    },
    {
      instrument: 'lead',
      value: 4.9,
      type: 'girls',
    },
    {
      instrument: 'keyboard',
      value: 6,
      type: 'girls',
    },
    {
      instrument: 'violin',
      value: 7,
      type: 'girls',
    },
    {
      instrument: 'cello',
      value: 9,
      type: 'girls',
    },
    {
      instrument: 'accordion',
      value: 13,
      type: 'girls',
    },
  ];
  const config = {
    data: data.reverse(),
    isStack: true,
    xField: 'value',
    yField: 'instrument' 
  };
  return <Bar {...config} />;
};
export default DemoBar;

```

上面的数据集显示了有多少学生演奏列出的每种乐器，我们的图表将显示有多少女孩和男孩演奏每种乐器。

堆积条形图中的主要代码关键字与我们在上面的其他图表中解释的相同。因此，没有必要深究它。

![Our final stacked bar chart](img/009206c2634ef3d270780b2f009a4675.png)

## 结论

在本教程中，我们向您展示了 Ant 设计在数据表示方面的多功能性。这里的是完整代码片段的链接。快乐视觉化！

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)