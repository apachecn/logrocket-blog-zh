# 使用 Fabric.js 和 React - LogRocket 博客构建室内地图

> 原文：<https://blog.logrocket.com/build-indoor-maps-fabric-js-react/>

***编者按**:这篇文章最后一次更新是在 2022 年 10 月 31 日，包括了更多关于 Fabric.js 和使用哪个 React 映射库的信息。*

室内制图是一个新概念，它使用数字 2D 或 3D 地图来可视化室内场地或地理数据。通过在数字地图上显示地点、人员和资产，您可以使用导航功能重新创建室内位置，从而支持许多业务用例，提高工作流程和效率。

例如，室内绘图可以提供对访问者行为的更深入的见解，提高管理者快速、轻松地发现和识别资产的能力。然后，管理人员可以选择利用这些知识进行重组，以提高运营效率。

为了构建室内地图，开发人员可以使用 [Fabric.js](http://fabricjs.com/) 和 React 来掌握网格系统的基本功能，缩放、平移和注释。在本文中，我们将介绍如何在组件的`render`方法中使用 Fabric.js。

要阅读本文，您需要掌握基本的 React、HTML、CSS 和 JavaScript 知识。您还需要一个带有 ID 的 canvas 元素和返回`fabric.Canvas`对象的函数。最后，您需要对如何使用 npm 有一个基本的了解。你可以在 GitHub 上找到这个项目的[完整代码。](https://github.com/martinwairegi/ReactIndoorMapping)

我们开始吧！

![GIF of Fabric.js Indoor Mapping](img/6d4ee19d67c9cf8565d69c6bec47419d.png)

## 目录

## Fabric.js 是什么？

Fabric.js 是一个强大的开源 JavaScript 画布库，它提供了一个交互式平台来使用 React。Fabric.js 允许您在画布上创建各种对象和形状，从简单的几何形状到更复杂的形状。fabric-covered canvas 管理所有关联的 fabric 对象，并可用作`canvas`元素的包装器。它接受元素的 id 并返回一个 fabric 实例。

使用 Fabric.js，您可以处理图像和动画，允许您拖动、缩放和旋转图像。您也可以将形状和对象组合在一起进行操作。Fabric.js 甚至提供了将画布序列化为 [SVG](https://blog.logrocket.com/svg-vs-canvas/) 或 JSON 并重用它的功能。

作为开源软件，Fabric.js 可以在一个协作的公共环境中创建。开源软件是这种合作的一个众所周知的例子，因为任何有能力的用户都可以在线参与其开发，潜在贡献者的数量增加了一倍。公众对软件的信心是通过研究代码来增强的。

## 如何在 React 中使用 Fabric.js

启动一个新的 React 项目的最快方法是克隆现有的 [GitHub 项目](https://github.com/martinwairegi/ReactIndoorMapping)或者使用 [Create React App](https://create-react-app.dev/) 。

首先，在安装 npm 后启动终端并输入命令`npm install --save fabricjs-react fabric react react-dom`。这将安装 Fabric.js、React 和 ReactDOM，因为它们是此模块的对等依赖项。

然后，安装[indorjs 库](https://www.npmjs.com/package/indoorjs)在本地构建并启动应用程序:

```
npm install indoorjs
npm start
```

因为这个项目使用 Node.js v16+，所以 Node.js v17+可能会出错。为了避免这种情况，请尝试降级到 Node.js v16。否则，使用 [nvm](https://github.com/nvm-sh/nvm) 在 Node.js 版本之间切换，如图[此处](https://learn.microsoft.com/en-us/windows/dev-environment/javascript/nodejs-on-windows)。

## 在画布上填充对象

要在 Fabric.js 画布上创建对象，请先创建`Canvas`类，然后再向其中填充所需的对象。使用`createElement`函数将画布上传到文档及其容器中。现在，创建将在画布上填充的不同对象，如下所示。使用必要的函数填充它们:

```
import Base from '../core/Base';
import { Arrow } from './Arrow';

const Modes = {
  SELECT: 'select',
  DRAWING: 'drawing',
  ARROW: 'arrow',
  TEXT: 'text'
};

export class Canvas extends Base {
  constructor(container, options) {
    super(options);

    this.container = container;

    const canvas = document.createElement('canvas');
    this.container.appendChild(canvas);
    canvas.setAttribute('id', 'indoorjs-canvas');

    canvas.width = this.width || this.container.clientWidth;
    canvas.height = this.height || this.container.clientHeight;

    this.currentColor = this.currentColor || 'black';
    this.fontFamily = this.fontFamily || 'Roboto';

    this.canvas = new fabric.Canvas(canvas, {
      freeDrawingCursor: 'none',
      freeDrawingLineWidth: this.lineWidth
    });
    this.arrows = [];

    this.setLineWidth(this.lineWidth || 10);
    this.addCursor();
    this.addListeners();

    this.setModeAsArrow();
  }

  setModeAsDrawing() {
    this.mode = Modes.DRAWING;
    this.canvas.isDrawingMode = true;
    this.canvas.selection = false;
    this.onModeChanged();
  }

  isDrawingMode() {
    return this.mode === Modes.DRAWING;
  }

 setModeAsSelect() {
    this.mode = Modes.SELECT;
    this.canvas.isDrawingMode = false;
    this.canvas.selection = true;
    this.onModeChanged();
  }

  isSelectMode() {
    return this.mode === Modes.SELECT;
  }

  setModeAsArrow() {
    this.mode = Modes.ARROW;
    this.canvas.isDrawingMode = false;
    this.canvas.selection = false;
    this.onModeChanged();
  }

  isArrowMode() {
    return this.mode === Modes.ARROW;
  }

  setModeAsText() {
    this.mode = Modes.TEXT;
    this.canvas.isDrawingMode = false;
    this.canvas.selection = false;
    this.onModeChanged();
  }

```

## 在 React 中创建对象的渐变

由于渐变对于测量画布上的对象至关重要，因此使用 measurement 类来实现 x 轴和 y 轴。下面的代码显示了如何使用 x 和 y 轴以及`onMouseMove`函数来创建对象的渐变:

```
import Measurer from './Measurer';

class Measurement {
  constructor(map) {
    this.map = map;
    this.measurer = null;
  }

  onMouseMove(e) {
    const point = {
      x: e.absolutePointer.x,
      y: e.absolutePointer.y,
    };

    if (this.measurer &amp;&amp; !this.measurer.completed) {
     this.measurer.setEnd(point);
      this.map.canvas.requestRenderAll();
    }
  }

  onClick(e) {
    const point = {
      x: e.absolutePointer.x,
      y: e.absolutePointer.y,
    };
    if (!this.measurer) {
      this.measurer = new Measurer({
        start: point,
        end: point,
       map: this.map,
      });

      // this.map.canvas.add(this.measurer);
    } else if (!this.measurer.completed) {
      this.measurer.setEnd(point);
      this.measurer.complete();
    }
  }
}

export default Measurement;

```

## 用 Fabric.js 构建网格系统

从几何图形中导入`alpha`、`grid-style`、`Axis`和`Point`。在进行下一步之前，在`Grid`类中创建画布的构造函数。使用`getCenterCoords`功能获得不同形状的坐标、宽度、高度和状态。

重新评估具有 x 轴和 y 轴的线，以计算渲染器的选项并重新计算其状态。然后，获取带有计算参数的`state`对象，准备进行渲染。最后，计算一下`real offset/range`:

```
import alpha from '../lib/color-alpha';
import Base from '../core/Base';
import {
  clamp, almost, len, parseUnit, toPx, isObj
} from '../lib/mumath/index';
import gridStyle from './gridStyle';
import Axis from './Axis';
import { Point } from '../geometry/Point';

// constructor
class Grid extends Base {
  constructor(canvas, opts) {
    super(opts);
    this.canvas = canvas;
    this.context = this.canvas.getContext('2d');
    this.state = {};
    this.setDefaults();
    this.update(opts);
  }

  render() {
    this.draw();
    return this;
  }

  getCenterCoords() {
    let state = this.state.x;
    let [width, height] = state.shape;
    let axisCoords = state.opposite.coordinate.getCoords(
      [state.coordinate.axisOrigin],
      state.opposite
    );
    const y = pt + axisCoords[1] * (height - pt - pb);
    state = this.state.y;
    [width, height] = state.shape;
    [pt, pr, pb, pl] = state.padding;
    axisCoords = state.opposite.coordinate.getCoords([state.coordinate.axisOrigin], state.opposite);
    const x = pl + axisCoords[0] * (width - pr - pl);
    return { x, y };
  }

  setSize(width, height) {
    this.setWidth(width);
    this.setHeight(height);
  }

  setWidth(width) {
    this.canvas.width = width;
  }

  setHeight(height) {
    this.canvas.height = height;
  }

  update(opts) {
    if (!opts) opts = {};
    const shape = [this.canvas.width, this.canvas.height];

    // recalc state
    this.state.x = this.calcCoordinate(this.axisX, shape, this);
    this.state.y = this.calcCoordinate(this.axisY, shape, this);
    this.state.x.opposite = this.state.y;
    this.state.y.opposite = this.state.x;
    this.emit('update', opts);
    return this;
  }

  // re-evaluate lines,
  update2(center) {
    const shape = [this.canvas.width, this.canvas.height];
    Object.assign(this.center, center);
    // recalc state
    this.state.x = this.calcCoordinate(this.axisX, shape, this);
    this.state.y = this.calcCoordinate(this.axisY, shape, this);
    this.state.x.opposite = this.state.y;
    this.state.y.opposite = this.state.x;
    this.emit('update', center);

    this.axisX.offset = center.x;
    this.axisX.zoom = 1 / center.zoom;

    this.axisY.offset = center.y;
    this.axisY.zoom = 1 / center.zoom;
  }

  calcCoordinate(coord, shape) {
    const state = {
      coordinate: coord,
      shape,
      grid: this
    };
    // calculate real offset/range
    state.range = coord.getRange(state);
    state.offset = clamp(
      Math.max(coord.min, -Number.MAX_VALUE + 1),
      Math.min(coord.max, Number.MAX_VALUE) - state.range
    );

```

## 在 Fabric.js 中实现缩放和平移

因为在前面的代码中有一些缩放功能，我们将在网格中实现缩放和平移功能。`stub`方法使用可见范围参数、标签、线条和轴参数来返回由轴重新定义的值的坐标。

现在，用重要变量如`height`、`width`、`minimum`和`maximum`来声明`Zoom`函数。在这一点上，声明 pan 及其特性是至关重要的。最后，要在缩放和平移后将屏幕返回到默认功能，请使用如下所示的`reset`功能:

```
setZoom(zoom) {
    const { width, height } = this.canvas;
    this.zoom = clamp(zoom, this.minZoom, this.maxZoom);
    this.dx = 0;
    this.dy = 0;
    this.x = width / 2.0;
    this.y = height / 2.0;
    this.update();
    process.nextTick(() =&gt; {
      this.update();
    });
  }
    if (layer.shape.keepOnZoom) {
      const scale = 1.0 / this.zoom;
      layer.shape.set('scaleX', scale);
      layer.shape.set('scaleY', scale);
      layer.shape.setCoords();
      this.emit(`${layer.class}scaling`, layer);
    }
  this.zoom = Math.min(scaleX, scaleY);

    this.canvas.setZoom(this.zoom);

    this.canvas.absolutePan({
      x: this.originX + this.center.x * this.zoom,
      y: this.originY - this.center.y * this.zoom
    });

reset() {
    const { width, height } = this.canvas;
    this.zoom = this._options.zoom || 1;
    this.center = new Point();
    this.originX = -this.canvas.width / 2;
    this.originY = -this.canvas.height / 2;
    this.canvas.absolutePan({
      x: this.originX,
      y: this.originY
    });
 const objects = canvas.getObjects();
    let hasKeepZoom = false;
    for (let i = 0; i &lt; objects.length; i += 1) {
      const object = objects[i];
      if (object.keepOnZoom) {
        object.set('scaleX', 1.0 / this.zoom);
        object.set('scaleY', 1.0 / this.zoom);
        object.setCoords();
        hasKeepZoom = true;
  this.emit(`${object.class}scaling`, object);
      }
    }
    if (hasKeepZoom) canvas.requestRenderAll();
  }

  panzoom(e) {
    // enable interactions
    const { width, height } = this.canvas;

    const prevZoom = 1 / this.zoom;
    let curZoom = prevZoom * (1 - zoom);
    curZoom = clamp(curZoom, this.minZoom, this.maxZoom);

    // pan
    const oX = 0.5;
    const oY = 0.5;
    if (this.isGrabMode() || e.isRight) {
      x -= prevZoom * e.dx;
      y += prevZoom * e.dy;
      this.setCursor('grab');
    } else {
      this.setCursor('pointer');
    }

    if (this.zoomEnabled) {
      x -= width * (curZoom - prevZoom) * tx;
      y -= height * (curZoom - prevZoom) * ty;
    }
    this.center.setX(x);
    this.center.setY(y);
    this.zoom = 1 / curZoom;
    this.dx = e.dx;
    this.dy = e.dy;
    this.x = e.x0;
    this.y = e.y0;
    this.isRight = e.isRight;
    this.update();
  }

```

## 缩放画布

为了缩放画布，我们首先初始化用于相对于原始画布尺寸转换对象尺寸的`scaleX`和`scaleY`值。我们使用一个数学函数来缩放对象和画布。画布根据用户的输入以及用户使用滚轮放大和缩小对象来改变大小。画布根据高度和宽度进行放大和缩小，允许用户查看整个画布。

## 为我们的 React 应用添加注释

注释是指标记文本或图像。当默认标签选项不符合我们的需要时，我们可以使用注释来改进分类法。我们首先将图像注释工具导入到组件中来注释我们的代码。要使用嵌套的对象数组，标签必须以标签或标注的坐标开始。

最后，我们将 hashmap 标签或注释转换为线条和颜色，使它们在应用程序运行时可见:

```
    let labels;
    if (coord.labels === true) labels = state.lines;
    else if (coord.labels instanceof Function) {
      labels = coord.labels(state);
    } else if (Array.isArray(coord.labels)) {
      labels = coord.labels;
    } else if (isObj(coord.labels)) {
      labels = coord.labels;
    } else {
      labels = Array(state.lines.length).fill(null);
    }
    state.labels = labels;
    // convert hashmap labels to lines
    if (isObj(ticks)) {
      state.ticks = Array(lines.length).fill(0);
    }
    if (isObj(labels)) {
      state.labels = Array(lines.length).fill(null);
    }
    if (isObj(ticks)) {
      // eslint-disable-next-line
      Object.keys(ticks).forEach((value, tick) =&gt; {
        state.ticks.push(tick);
       state.lines.push(parseFloat(value));
      state.lineColors.push(null);
       state.labels.push(null);
      });
    }

    if (isObj(labels)) {
      Object.keys(labels).forEach((label, value) =&gt; {
        state.labels.push(label);
        state.lines.push(parseFloat(value));
        state.lineColors.push(null);
        state.ticks.push(null);
      });
    }

    return state;
  }

```

## React 室内制图的 Fabric.js 替代方案

虽然我们已经讨论了 Fabric.js，但是还有其他映射方式。我们来看几个。

### 地图谈判

通过倾斜和旋转 2D 地图， [Maptalks](https://maptalks.org) 灵活的开源库混合了 2D 和 3D 地图。任何首选技术，如 CSS3、Canvas 和 WebGL，都可以增强 Maptalks。勇往直前，创造你自己的梦想；很简单，也很好玩。使用 Maptalks 和插件，你可以用画布 2D 渲染 10，000 个几何图形。

### 开放层

OpenLayers 是一个开源、高性能的 JavaScript 框架，可以构建使用各种地图服务的交互式地图。您可以从地图服务选择中为地图图层源选择切片图层或矢量图层。

由于该工具是开箱即用的移动工具，因此适用于跨平台和浏览器创建地图。使用 CSS，你可以改变你的地图的外观。

### 拉斐尔. js

一个名为[raphal . js](https://dmitrybaranovskiy.github.io/raphael/)的小 JavaScript 包旨在简化网络上矢量图形的使用。在这个库的帮助下，您可以快速轻松地创建自己独特的图表或图像裁剪和旋转小部件。

拉斐尔使用 VML 和 W3C 推荐标准来制作图形。因此，您创建的每个可视对象也成为一个 DOM 对象，使您能够在以后附加或更改 JavaScript 事件处理程序。Raphaë的任务是提供一个适配器，使创建跨浏览器兼容的矢量图形变得更加容易。

### Kartograph.js

Kartograph 是一个创建 SVG 地图的框架，不需要任何额外的地图服务。是一个 JavaScript 库，允许你使用 Kartograph SVG 地图创建交互式地图。它建立在拉斐尔和 [jQuery](https://blog.logrocket.com/the-history-and-legacy-of-jquery/) 之上，并优雅地降级到 IE7 及以上。

Kartograph 缺乏现成的地图集合，但它支持任何 SVG 地图，并附带一个名为 [Kartograph.py](https://kartograph.org/about/kartograph.py/) 的地图创建工具。这个框架提供了流畅的映射体验。库文档和 API 参考(集成到一个页面中)使在项目中设置交互式地图变得简单而愉快。然而，Kartograph 并不是没有依赖性的；它需要 Raphaë l.js 用于绘图和 jQuery。

### 传单

传单是一个免费的开源 JavaScript 库，用于制作动态的、移动友好的地图。这是一个紧凑的应用程序，支持所有主要的浏览器和操作系统，并有大量的功能，插件和直观的 API。这里有几个[传单的例子](https://leafletjs.com/examples.html)。

由于其核心库的合理大小，当加载时间或大小受到限制时，Leaflet 对于移动应用程序或其他环境来说是一个极好的选择。此外，传单有一个巨大的插件选择，所以你可以添加几乎任何功能，将与一个更广泛的映射库。

### 纸张. js

[Paper.js](http://paperjs.org) 是一个开源的基于画布的矢量图形编程环境。除了为创建和修改矢量图形和贝塞尔曲线提供强大的工具集合之外，它还提供了一个干净的、设计良好的编程接口。它还为矢量图形提供了一个场景图/文档对象模型。Paper.js 建立在 [Scriptographer](https://scriptographer.org) 之上，这是一个为 Adobe Illustrator 编写脚本的环境。

### p5.js

软件素描本的比喻为 [p5.js](https://p5js.org) 库的全面素描功能奠定了基础。但是，此工具并不限制您使用简单的草图曲面；相反，把你的整个浏览器页面想象成你的草图！

通过使用 p5.js 附加库，您可以与 HTML5 组件进行交互，包括文本、输入、视频、网络摄像头和声音。

如果你已经熟悉 JavaScript 或[处理](https://processing.org)的基础知识，那么 p5.js 可能是一个不错的选择，因为它是这两种语言的持续开发解释。

## 应该使用哪个 React 映射库？

与上面描述的其他库相比，Fabric.js 是最好的 React 映射库，因为它的画布使我们能够制作一些真正令人难以置信的图像。

遗憾的是，大多数其他映射库都非常基础。如果我们只想在画布上快速画出一些简单的形状，然后继续前进，这是一回事。然而，当需要任何交互、图片更改或更复杂形状的草图时，该场景会发生巨大变化。Fabric.js 旨在解决这个问题。

原生画布技术仅提供不加区别地改变整个画布位图的简单图形指令。它类似于用刷子在画布上绘画，同时几乎不受控制地在顶部涂抹越来越多的油。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

Fabric.js 没有在这样的基础上工作，而是在原生方法之上提供了一个简单而有效的对象模型。它处理画布状态和渲染，并支持与对象的直接交互。

最后，Fabric.js 是一个更强大的 JavaScript 框架，它简化了 HTML5 canvas 的工作，尽管大多数 React 映射库都是完全开源的。fabric.js 提供了交互性层、canvas 缺少的对象模型和大量其他基本实用工具。

## 结论

Fabric.js 是撰写本文时市场上最好的绘图库之一。在本文中，我们学习了如何使用 Fabric.js 将复杂的库包装到 React 的不受控制的组件中。希望 Fabric.js 也能实现其他组件。

我们还回顾了 Fabric.js 的几种映射替代方案:Maptalks、OpenLayers、Raphaë l.js、Kartograph.js、great、Paper.js 和 p5.js。我过去曾非常成功地使用 Fabric.js，尽管在撰写本文时它还处于开发阶段。感谢阅读！

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