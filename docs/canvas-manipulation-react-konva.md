# React Konva 画布操作指南

> 原文：<https://blog.logrocket.com/canvas-manipulation-react-konva/>

React Konva 是一个允许我们操纵画布的工具。它让我们可以轻松地创建形状，而无需计算每个点的位置，它有许多内置的形状和动画功能，我们可以使用它们来创建更多的交互式画布。

React Konva 以节点包的形式提供。我们可以通过运行以下命令来安装它:

```
npm install react-konva konva --save
```

## 绘制基本形状

使用 React Konva，我们可以创建一个带有`Stage`组件的画布，其中嵌套了一个或多个`Layer`组件。

在每个`Layer`里面，我们可以放入任何我们想要的形状。React Konva 带有矩形、圆形、椭圆形、直线、图像、文本、星形、标签、SVG 和多边形等形状。

我们可以创建一个画布并添加一个带阴影的矩形，如下所示:

```
import React from "react";
import { Stage, Layer, Rect } from "react-konva";

export default function App() {
  return (
    <Stage width={window.innerWidth} height={window.innerHeight}>
      <Layer>
        <Rect
          x={20}
          y={50}
          width={100}
          height={100}
          fill="red"
          shadowBlur={5}
        />
      </Layer>
    </Stage>
  );
}
```

在上面的代码中，我们引用了创建画布的`Stage`组件。`width`设置为浏览器标签宽度，`height`设置为浏览器标签高度。

然后，我们使用 React Konva 附带的`Rect`组件将矩形放入画布中。`x`和`y`设置左上角的位置；`width`和`height`设置尺寸；并且`fill`设置填充颜色。`shadowBlur`让我们以像素为单位调整阴影的宽度。

相比之下，要用普通 JavaScript 创建一个矩形，我们需要做更多的工作:

```
const canvas = document.querySelector("canvas");
canvas.style.width = '500';
canvas.style.height = '500';
if (canvas.getContext) {
  const ctx = canvas.getContext('2d');
  ctx.rect(20, 50, 100, 100);
  ctx.shadowColor = 'gray';
  ctx.shadowBlur = 10;
  ctx.shadowOffsetX = 10;
  ctx.shadowOffsetY = 10;
  ctx.fillStyle = "red";
  ctx.fill();
}
```

我们必须通过单独设置每个属性来设置阴影的参数，而不仅仅是传入一个道具。此外，canvas 上下文只带有绘制直线和矩形的方法。这意味着，如果没有像 React Konva 这样的库，任何其他形状都很难绘制。

同样，我们可以用`Circle`代替`Rect`来画圆。在这里，`x`和`y`道具是圆心的坐标。我们可以编写以下代码来创建一个带阴影的红色圆圈:

```
import React from "react";
import { Stage, Layer, Circle } from "react-konva";

export default function App() {
  return (
    <Stage width={window.innerWidth} height={window.innerHeight}>
      <Layer>
        <Circle
          x={100}
          y={100}
          width={100}
          height={100}
          fill="red"
          shadowBlur={5}
        />
      </Layer>
    </Stage>
  );
}
```

使用 React Konva 绘制规则多边形同样简单:

```
import React from "react";
import { Stage, Layer, RegularPolygon } from "react-konva";

export default function App() {
  return (
    <Stage width={window.innerWidth} height={window.innerHeight}>
      <Layer>
        <RegularPolygon
          sides={10}
          x={100}
          y={100}
          width={100}
          height={100}
          fill="red"
          shadowBlur={5}
        />
      </Layer>
    </Stage>
  );
}
```

## 绘制自定义形状

我们可以通过使用`Shape`组件来绘制定制的形状。它的`sceneFunc` prop 采用一个函数，将画布`context`对象作为第一个参数，将`shape`方法作为第二个参数。`shape`对象是在`fillStrokeShape`方法中使用的特定于 Konva 的方法。

例如，我们可以画出自己的形状如下:

```
import React from "react";
import { Stage, Layer, Shape } from "react-konva";

export default function App() {
  return (
    <Stage width={window.innerWidth} height={window.innerHeight}>
      <Layer>
        <Shape
          sceneFunc={(context, shape) => {
            context.beginPath();
            context.moveTo(0, 50);
            context.bezierCurveTo(100, 200, 100, 400, 200, 0);
            context.closePath();
            context.fillStrokeShape(shape);
          }}
          fill="#00D2FF"
          stroke="black"
          strokeWidth={4}
        />
      </Layer>
    </Stage>
  );
}
```

道具让我们直接访问画布，我们可以画出任何我们想要的形状。然后，我们在画布上调用`fillStrokeSgape`，将`shape`函数作为回调函数传入，用给定的颜色填充形状并绘制笔画。我们也可以传入其他的道具，比如`fill`和`stroke`来设置填充和描边颜色。

## 事件处理

React Konva 形状可以侦听事件，然后相应地对事件做出响应。例如，通过向形状添加可拖动道具，然后为`dragstart`和`dragend`事件附加事件监听器，可以非常容易地使形状可拖动:

```
import React from "react";
import { Stage, Layer, Circle } from "react-konva";

export default function App() {
  const handleDragStart = e => {
    e.target.setAttrs({
      shadowOffset: {
        x: 15,
        y: 15
      },
      scaleX: 1.1,
      scaleY: 1.1
    });
  };
  const handleDragEnd = e => {
    e.target.to({
      duration: 0.5,
      easing: Konva.Easings.ElasticEaseOut,
      scaleX: 1,
      scaleY: 1,
      shadowOffsetX: 5,
      shadowOffsetY: 5
    });
  };

  return (
    <Stage width={window.innerWidth} height={window.innerHeight}>
      <Layer>
        <Circle
          x={100}
          y={100}
          width={100}
          height={100}
          fill="red"
          shadowBlur={5}
          draggable
          onDragStart={handleDragStart}
          onDragEnd={handleDragEnd}
        />
      </Layer>
    </Stage>
  );
}
```

在上面的代码中，我们使用了`handleDragStart`函数将阴影设置为一个新的颜色，偏移为原来的圆形。我们还稍微扩展了形状，以表示它正在被拖动。

我们将`handleDragStart`函数传递给`onDragStart`属性，同样，我们将`handleDragEnd`函数定义给`onDragEnd`属性。在那里，我们通过调用被拖动的形状对象的`to`方法，将被拖动的形状移动到新的位置，这是`e.target`的值。

我们将缓动设置为内置的`Konva.Easings.ElasticEaseOut`值，这使它在被拖放时看起来有弹性。缓动将形状移动的速度作为时间的函数进行更改。没有它，一切都将匀速运动，这既不自然也不有趣。

“减缓”意味着它开始时移动得很快，然后慢下来。

## 添加图像

我们可以用 React Konva 的`Image`组件在画布上添加图像。要添加图像，我们创建一个`window.Image`实例，将图像的 URL 设置为`src`属性，然后将图像对象作为`image`属性的值传递给`Image`组件，如下所示:

```
import React, { useEffect, useState } from "react";
import { Stage, Layer, Image } from "react-konva";

export default function App() {
  const [image, setImage] = useState(new window.Image());

  useEffect(() => {
    const img = new window.Image();
    img.src =
      "https://images.unsplash.com/photo-1531804055935-76f44d7c3621?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=334&q=80";
    setImage(img);
  }, []);

  return (
    <Stage width={window.innerWidth} height={window.innerHeight}>
      <Layer>
        <Image x={100} y={200} image={image} />
      </Layer>
    </Stage>
  );
}
```

在上面的代码中，当我们的应用程序第一次加载时，我们使用了`useEffect`钩子来加载图像，方法是将一个空数组传递给`useEffect`的第二个参数。

添加图像后，我们可以使用 React Konva 的一些内置效果为我们的图像添加一些效果。例如，我们可以使用内置的模糊效果来模糊加载到画布上的图像，如下所示:

```
import React, { useEffect, useState, useRef } from "react";
import { Stage, Layer, Image } from "react-konva";
import Konva from "konva";

export default function App() {
  const [image, setImage] = useState(new window.Image());
  const imageRef = useRef();

  useEffect(() => {
    const img = new window.Image();
    img.crossOrigin = "Anonymous";
    img.src =
      "https://images.unsplash.com/photo-1531804055935-76f44d7c3621?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=334&q=80";
    setImage(img);
  }, []);

  useEffect(() => {
    if (image) {
      imageRef.current.cache();
      imageRef.current.getLayer().batchDraw();
    }
  }, [image]);

  return (
    <Stage width={window.innerWidth} height={window.innerHeight}>
      <Layer>
        <Image
          blurRadius={10}
          filters={[Konva.Filters.Blur]}
          x={100}
          y={200}
          image={image}
          ref={imageRef}
        />
      </Layer>
    </Stage>
  );
}
```

在上面的代码中，我们加载了一个图像，然后将带有`Konva.Filters.Blur`对象的`filters`道具添加到数组中。这是一个数组，这意味着我们可以一次应用多个效果。

我们需要第二个`useEffect`回调来应用过滤器。它观察`image`何时加载，然后用加载时应用的过滤器重画图像。

它引用了我们设置图像的`imageRef`。然后我们调用`imageRef.current.cache();`来缓存原始图像，`imageRef.current.getLayer().batchDraw();`用应用的过滤器重画图像。

## 用`Transformer`调整图像大小

在 React Konva 中，transformer 对象允许用户通过向正在转换的形状添加手柄来调整图像大小，就像我们使用大多数照片编辑应用程序一样。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

添加转换功能有点复杂。我们必须创建一个具有 React Konva 形状的组件，然后使其可拖动。接下来，我们必须添加带有一个函数的`onTransformationEnd`道具，该函数通过用户将形状转换为的因子来缩放形状，并将其设置为宽度和高度。

除了形状组件之外，我们还必须添加`Transformer`组件作为兄弟组件来添加手柄，以便让用户转换形状。例如，我们编写以下代码来创建两个圆，然后显示手柄，让用户拉伸和收缩形状，如下所示:

```
import React from "react";
import { Stage, Layer, Circle, Transformer } from "react-konva";

const Circ = ({ shapeProps, isSelected, onSelect, onChange }) => {
  const shapeRef = React.useRef();
  const trRef = React.useRef();

  React.useEffect(() => {
    if (isSelected) {
      trRef.current.setNode(shapeRef.current);
      trRef.current.getLayer().batchDraw();
    }
  }, [isSelected]);

  return (
    <React.Fragment>
      <Circle
        onClick={onSelect}
        ref={shapeRef}
        {...shapeProps}
        draggable
        onDragEnd={e => {
          onChange({
            ...shapeProps,
            x: e.target.x(),
            y: e.target.y()
          });
        }}
        onTransformEnd={e => {
          const node = shapeRef.current;
          const scaleX = node.scaleX();
          const scaleY = node.scaleY();

          node.scaleX(1);
          node.scaleY(1);
          onChange({
            ...shapeProps,
            x: node.x(),
            y: node.y(),
            width: Math.max(5, node.width() * scaleX),
            height: Math.max(node.height() * scaleY)
          });
        }}
      />
      {isSelected && (
        <Transformer
          ref={trRef}
          boundBoxFunc={(oldBox, newBox) => {
            if (newBox.width < 5 || newBox.height < 5) {
              return oldBox;
            }
            return newBox;
          }}
        />
      )}
    </React.Fragment>
  );
};

const initialCircles = [
  {
    x: 100,
    y: 100,
    width: 100,
    height: 100,
    fill: "blue",
    id: "circ1"
  },
  {
    x: 150,
    y: 150,
    width: 100,
    height: 100,
    fill: "green",
    id: "circ2"
  }
];

const App = () => {
  const [circles, setCircles] = React.useState(initialCircles);
  const [selectedId, selectShape] = React.useState(null);

  return (
    <Stage
      width={window.innerWidth}
      height={window.innerHeight}
      onMouseDown={e => {
        const clickedOnEmpty = e.target === e.target.getStage();
        if (clickedOnEmpty) {
          selectShape(null);
        }
      }}
    >
      <Layer>
        {circles.map((circ, i) => {
          return (
            <Circ
              key={i}
              shapeProps={circ}
              isSelected={circ.id === selectedId}
              onSelect={() => {
                selectShape(circ.id);
              }}
              onChange={newAttrs => {
                const circs = circles.slice();
                circs[i] = newAttrs;
                setCircles(circs);
              }}
            />
          );
        })}
      </Layer>
    </Stage>
  );
};

export default App;
```

在上面的代码中，我们有形状相同的`Circ`组件、`Circle`和`Transformer`组件，正如我们上面所描述的。

`onDragEnd`处理程序支持拖动，就像我们在前面的拖放示例中所做的那样。`onTransformEnd`处理程序具有在用户完成拖动后将形状调整到新的宽度和高度的功能。

`Transformer`组件有调整圆大小的手柄，当`isSelected`道具设置为`true`时，手柄打开。当我们点击`App`的`onSelect`处理程序中的形状时，我们设置它。`onSelect`在一个圆被点击时运行，然后用于将被点击的圆的`isSelected`设置为`true`。

## 基础动画

我们可以添加一些基本的动画效果，比如通过随机因子缩放尺寸来拖动形状。

例如，我们可以编写以下代码来实现这一点:

```
import React from "react";
import { Stage, Layer, Circle } from "react-konva";

export default function App() {
  const circ = React.useRef();
  const changeSize = () => {
    circ.current.to({
      scaleX: Math.random() + 0.9,
      scaleY: Math.random() + 0.8,
      duration: 0.2
    });
  };

  return (
    <Stage width={window.innerWidth} height={window.innerHeight}>
      <Layer>
        <Circle
          x={100}
          y={100}
          width={100}
          height={100}
          fill="red"
          shadowBlur={5}
          draggable
          ref={circ}
          onDragStart={changeSize}
          onDragEnd={changeSize}
        />
      </Layer>
    </Stage>
  );
}
```

在上面的代码中，我们有`changeSize`函数，当`dragstart`或`dragend`事件被触发时，就会调用这个函数。

注意，我们必须使用 use`current`属性让 DOM 对象调用`to`方法。

## 结论

使用 React Konva，我们可以用比使用普通 JavaScript 少得多的努力来绘制多种形状。我们还可以添加一些功能，让用户轻松地变换形状和拖动形状。这在标准 JavaScript 画布库中是不可用的。

更好的是，我们可以添加`Transformers`让用户像在照片编辑器中一样改变形状的大小。通过内置的`Image`组件和滤镜，添加带效果的图像也很容易。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)