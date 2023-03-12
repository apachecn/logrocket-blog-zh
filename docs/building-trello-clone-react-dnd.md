# 用 React DnD - LogRocket 博客构建一个特雷罗克隆

> 原文：<https://blog.logrocket.com/building-trello-clone-react-dnd/>

使用传统的 JavaScript 构建具有拖放功能的应用程序可能过于复杂。React DnD 是一套实用程序，它简化了应用程序不同部分之间的数据传输，允许您轻松创建具有拖放功能的高性能界面。

React DnD 非常适合 Trello、ProofHub 和 ClickUp 等应用程序，这些应用程序提供了一个用户界面，可以通过拖放将您的项目组织到不同的面板中。在本教程中，我们将通过构建自己的 Trello 克隆来探索 React DnD。本教程中使用的代码可以在 [GitHub](https://github.com/icode247/Trello-Clone-ReactDnD) 上获得。我们开始吧！

## 反应 DnD 的独特之处

[React DnD 包括一系列伟大的功能](https://blog.logrocket.com/using-react-dnd-to-create-a-tower-of-hanoi-game/)，将节省您的开发时间，提高您的应用程序的效率。让我们回顾一下 React DnD 的一些主要福利。

在一种类似于 React Router 和 Flummox 的技术中，React DnD 覆盖了你的组件，将道具注入其中，而不是提供预建的小部件。React DnD 是建立在 React 的声明式渲染理念之上的，因此不会修改 DOM。React DnD 是 Redux 和其他单向数据流架构的优秀补充。

React DnD 默认使用 HTML5 拖放，但是你也可以使用你选择的后端，创建你自己的自定义事件。虽然 HTML5 中的拖放功能有一个有点复杂的 API，有几个浏览器问题，但 React DnD 会自动处理这些问题，让您有更多的时间专注于您的项目，而不是进行故障诊断。

## 用反应 DnD 建造一个特雷罗克隆体

既然我们理解了 React DnD 背后的基本原理，让我们来构建我们的 Trello 克隆！首先，使用`npx create-react-app`命令创建一个新的 React 项目。对于本教程，我们将我们的项目命名为`trello-clone`:

```
npx create-react-app trello-clone

```

等待安装完成，然后将目录切换到新创建的文件夹。接下来，我们需要用下面的命令安装 React DnD 和 HTML 拖放:

```
npm install react-dnd react-dnd-html5-backend react-modal

```

现在，让我们创建应用程序的组件。在`src`目录下，创建一个`component`文件夹。

## 创建一个`Column`组件

首先，我们将创建一个`Column`组件，它将作为我们将在本教程稍后创建的其他组件的包装器。在`Column`中，我们将显示我们卡片的所有内容。创建一个`Column.jsx`文件，并添加下面的 JSX 代码片段:

```
import react from "react";
const Column = ({isOver,children})=>{
    const className = isOver ? "Highlight-region" : ""
    return (
      <div className={`col${className}`}></div>,
        {children}
    );
};
export default Column;

```

上面的代码创建了一个显示我们所有卡片的列。当一个项目被拖动到卡片上时，我们还需要对它应用不同的样式。我们将使用一个三元运算符和一个`className`变量，它将根据卡的状态而变化。

## 创建一个`CardItem`组件

让我们创建另一个名为`ItemCard`的组件来处理我们实际的卡片项目。我们还将在我们的`src/component`文件夹中创建一个`CardItem.jsx`文件。

首先，我们将导入以下内容:

*   钩子:在你的组件中产生副作用
*   钩子:管理应用程序的状态
*   钩子:在我们的组件中持久化对象
*   `Fragment`:使我们能够分组子节点列表
*   `Window`:指的是`Window`组件，我们很快就会创建它
*   `TEMS_TYPE`:指定我们卡上的物品类型

运行以下命令安装上面列出的项目:

```
import react, { userEffect, Fragment, useRef, useState } from "react";
import { useDrag, useDrop } from 'react-dnd';
import Window from './Window';
import ITEM_TYPES from '../data/types'

```

注意，我们还没有创建`data`文件夹或它的文件。我们将在后面的部分中完成这项工作。

接下来，让我们创建我们的`CardItem`组件，它将负责在卡片中移动项目。然后，我们将创建一个`useRef`钩子，并从 React DnD 提供的 React `useDrop`钩子中析构`drop`对象。

我们只需要`drop`对象，所以我们将传递一个`,`作为我们对象的参数。我们接受我们的项目类型，然后将我们的`item`和`monitor`传递给`hover`参数:

```
const cardItem = ({ item, index, moveItem, status }) => {
    const ref = useRef(null)
    const [, drop ] = useDrop({
        accept: ITEM_TYPES,
        hover({ item, monitor }) {
        ......

```

我们还将检查我们拖动的项目是否被放到了另一张卡中。如果物品没有被移动到另一张卡上，我们不需要做任何事情来退回物品:

```
if (!ref.current) {
    return;
}
.....

```

但是，如果我们的物品被拖到另一张卡片上，我们需要将物品移动到那张卡片上:

```
const dragIndex = item.index;
const hoverIndex = item.hover;
if (dragIndex === hoverIndex) {
  return;
}
const hovererdRect = ref.current.getBoundClientRect();
const hoverMiddleY = hovererdRect.bottom - hovererdRect.top / 2;
const mousePostion = monitor.getClientOffest();
const hoverClientY = mousePostion.y - hovererdRect.top;
if (dragIndex < hoverIndex && hoverClientY < hoverMiddleY) {
  return;
}
if (dragIndex > hoverIndex && hoverClientY < hoverMiddleY) {
  return;
}
moveItem(dragIndex, hoverIndex);
item.index = hoverIndex;
......

```

在上面的代码中，我们创建了一个`dragIndex`变量来检查所选项的索引。我们创建了`hoverIndex`变量来指示被移动的项目的索引。当`itemIndex`与`hoverItem`相同时，意味着该项目没有被移动，在这种情况下，我们将什么也不做。

然后，我们找到`hoverdRect`，悬停项矩形位置、`hoverMiddleY`，悬停项中间 y 轴位置、`mousePostion`，悬停项鼠标位置，最后，`overClientY`，悬停项客户端 y 轴位置。

我们进行了一些检查，以确保所选择的项目被正确地移动到目标卡上。然后，我们移动了卡片，并更新了被移动物品的索引。

接下来，我们需要从`useDrag`钩子中析构`isDragging`和`drag`，这将需要一个项目。在这种情况下，我们将使用我们的`item`类型、我们的实际项目和`item`索引。`isDragging`还取了一个`collect`回调函数，给了我们很多 React DnD 提供的道具数据。

collect 回调提供的数据中有`monitor`，它指的是屏幕的副本，让我们知道我们是否正在拖动屏幕:

```
 const [{ isDragging }, drag] = useDrag({
        item: { type: ITEM_TYPES, ...item, index },
        collect: monitor => ({
            isDragging: monitor.isDragging()
        })
});
.......

```

我们将创建一个状态来处理我们的卡片窗口的打开和关闭，用`onOpen`和`onClose`处理函数来改变被拖动的卡片窗口的状态。然后，我们用`drop`钩子包装`drag`钩子，并传入我们的`ref`钩子，帮助我们识别和定位我们正在处理的项目:

```
const [show, setShow] = useState(false);
const onOpen = () => setShow(true);
const onClose = () => setShow(false)
drag(drop(ref))
.......

```

最后，我们将使用`<Fragment>`组件返回分组的 JSX 元素，显示卡片项目数据。当对象处于拖动模式时，我们设置不同的不透明度值和背景颜色。然后，我们用传递给它的`show`值、`item`和`onClose`函数处理程序来呈现我们的`Window`组件:

```
   return (
        <Fragment >
            <div
                ref={ref}
                style={{ opacity: isDragging ? 0 : 1 }}
                className={'item'}
                onClick={onOpen}
            >
                <div className={'color-bar'} style={{ backgroundColor: status.color }}
                ></div>
                <p className={'item-title'}>{item.content}</p>
            </div>
            <Window 
               item = {item}
               onClose = {onclose}
               show = {show}
            />

        </Fragment>
    )
}
export default cardItem

```

## 创建一个`Window`组件

现在我们已经完成了我们的`CardItem`组件，让我们快速创建我们的`Window`组件。在我们的`src/component`文件夹**，**中创建一个`Window.jsx`文件，并向其中添加以下代码片段:

```
import React from "react";
import Modal from "react-modal";
Modal.setAppElement("#app");

```

在上面的代码中，我们从 react-modal 导入 React 和`Modal`。通过使用`Modal.setAppElement`并传递我们的应用程序根元素 ID，我们将`Modal`设置为显示在我们的应用程序上。

接下来，我们创建我们的`Window`功能组件，它给我们三个道具，`show`、`onClose`处理程序和`item`。`show`道具告诉我们是否显示窗口。`onClose`控制`Window`关闭时会发生什么，`Item`道具是`Window`中的实际项目:

```
&gt;const Window = ({ show, onClose, item }) => {
.......

```

然后，我们创建我们的`Modal`，并传入所需的道具、`isOpen`、`onRequestClose`和`overlayClassName`。我们在`Modal`上显示项目的细节，并添加一个按钮来处理`Modal`关闭:

```
 return (
    <Modal
      isOpen={show}
      onRequestClose={onClose}
      className={"modal"}
      overlayClassName={"overlay"}
    >
      <div className={"close-btn-ctn"}>
        <h1 style={{ flex: "1 90%" }}>{item.title}</h1>
        <button className={"close-btn"} onClick={onclose}>
          X
        </button>
      </div>
      <div>
        <h2>Description</h2>
        <p>{item.content}</p>
        <h2>Status</h2>
        <p>
          {item.icon}{" "}
          {`${item.status.charAt(0).toUpperCase()}${item.status.slice(1)}`}
        </p>
      </div>
    </Modal>
  );
};
export default Window;

```

## 创建`DropContainer`组件

我们已经完成了我们的`Window`组件，但是我们需要一个父容器来封装我们的`Column`和`CardItem`组件作为它的子组件，使我们能够指示我们的卡片移动。首先，我们需要在我们的`src/component`文件夹中创建一个`DropContainer.jsx`文件，并将下面的代码片段添加到其中:

```
import React from "react";
import { useDrop } from "react-dnd";
import ITEM_TYPES from "../data/types";
import { statuses } from "../data";

```

上面的代码将导入条目类型，这些条目类型是我们在用作数据库的`data`文件夹中创建的状态，为我们的应用程序存储虚拟数据。

接下来，我们创建我们的`DropContainer`功能组件，它也为我们提供了三个道具，`onDrop`、孩子和状态。我们需要从`useDrop`钩子中析构`isOver`和`drop`，这需要`accept`、`canDrop`、`Drop`和`collect`回调。

然后，我们将`ITEM_TYPES`传递给`accept`回调函数，将`item`和`monitor`传递给`canDrop`回调函数。为了知道我们是否可以通过`canDrop`回调将物品放到给定的卡片上，我们将获取我们的物品索引和状态。

现在，我们可以返回我们的项目及其索引的数组，使我们的项目能够在卡片上向前或向后移动:

```
const DropContainer = ({ onDrop, children, status }) => {
  const [{ isOver }, drop] = useDrop({
    accept: ITEM_TYPES,
    canDrop: (item, monitor) => {
      const itemIndex = statuses.findeIndex((si) => si.status === item.status);
      const statusIndex = statuses.findIndex((si) => si.index === status);
      return [itemIndex + 1, itemIndex - 1, itemIndex].includes(statusIndex);
    },
........

```

在我们的`onDrop`回调中，我们将获取一个`item`和`monitor`，这是我们删除一个项目所需的两个参数。然后，我们调用`onDrop`，传递我们要删除的项目的`item`、`monitor`和`status`。

我们将使用`collect`回调来设置`isOver`道具，这些道具知道物品是否已经被放入卡片中:

```
drop: (item, monitor) => {
      onDrop(item, monitor, status);
    },
    collect: (monitor) => ({
      isOver: monitor.isOver(),
    }),
  });
........

```

最后，我们将返回我们的 HTML，引用`drop`钩子。我们将使用`React.cloneElement`方法将一堆子元素传递给我们的父组件。然后，我们导出我们的`DropContainer`组件:

```
return (
    <div ref={drop} className={"drop-wrapper"}>
      {React.cloneElement(children, { isOver })}
    </div>
  );
};
export default DropContainer;

```

## 创建`Home`组件

现在让我们继续创建我们的主页组件。首先，在我们的`src/component`文件夹中创建一个`Home.jsx`文件，并将下面的代码片段添加到其中:

```
import React, { useState } from "react";
import CardItem from "./CardItems";
import DropContainer from "./DropConatiner";
import Column from "./Column";
import { data, statuses } from "../data";
...........

```

上面的代码将我们的`CardItem`、`DropContainer`和`Column`组件导入到我们的`Home.jsx`组件中。代码还从我们的`data`文件夹中导入`data`和`statuses`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

接下来，我们在`Home`功能组件中创建一个状态，以获取数据中所有项目的数组。我们移动项目的索引，并用`onDrop`处理程序改变它们的状态:

```
const Home = (props) => {
  const [items, setItems] = useState(data);
  const onDrop = (item, monitor, status) => {
    setItems(prevState => {
      const newItems = prevState
        .filter(i => i.id !== item.id)
        .concat({ ...item, status);
      return [...newItems];
    });
  .........
```

我们可以通过创建一个带两个参数`dragIndex`和`hoverIndex`的`moveItem`处理程序来控制项目移动时会发生什么。

`dragIndex`用于被拖动的项目，而`hoverIndex`用于悬停的项目。我们过滤 items 的数组以获得其索引当前在`dragIndex`对象上的项目，并将该项目插入到新卡片中:

```
 const moveItem = (dragIndex, hoverIndex) => {
    const item = items[dragIndex];
    setItems(prevState => {
      const newItems = prevState.filter((i, idx) => idx !== dragIndex);
      newItems.splice(hoverIndex, 0, item);
      return [...newItems];
    });
  };
...........

```

接下来，我们将使用`map`函数遍历所有状态，并返回我们的列及其状态名。我们将传入我们的`DropContainer`，它接受`OnDrop`钩子和状态。现在，我们将显示我们的列，这将只显示每个列中具有状态的项目。然后，我们导出我们的`Home`组件:

```
  return (
    <div className={"row"}>
      {statuses.map(s =&gt; {
        return (
          <div key={s.status} className={"col-wrapper"}>
            <h4 className={"col-header"}>{s.status.toUpperCase}</h4>
            <DropWrapper onDrop={onDrop} status={s.status}>
              <Col>
                {items
                  .filter(i => i.status === s.status)
                  .map((i, idx) => (
                    <Item
                      key={i.id}
                      item={i}
                      index={idx}
                      moveItem={moveItem}
                      status={s}
                    />
                  ))}
              </Col>
            </DropWrapper>
          </div>
        );
      })}
    </div>
  );

```

现在，用下面的 JSX 代码片段更新`/src/App.jsx`文件:

```
........
import Homepage from "./component/Homepage";
import { DndProvider } from "react-dnd";
import { HTML5Backend } from "react-dnd-html5-backend";
const App = () => {
  return (
    <DndProvider backend={HTML5Backend}>
      <div className={"row"}>
        <p className={"page-header"}>Trello Clone Dashboard</p>
      </div>
      <Homepage />
    </DndProvider>
  );
};
........

```

上面的代码用 React `DndProvider`包装了我们的整个应用程序，传递了从 React dnd HTML5 后端导入的`HTML5Backend`。

接下来，在我们的`src`目录文件夹中制作一个`data`文件夹。在数据文件夹中，创建一个`data.js`和一个`types.js`文件。将下面的代码片段添加到`data.js`文件中:

```
const data = [
  {
    id: 1,
    status: "open",
    title: "Available Topic",
    content: "Buiding a REST API with Django",
  },
  {
    id: 2,
    status: "open",
    title: "Sponsored Post",
    content: "How to create a React Chat Application",
  },
  {
    id: 3,
    status: "open",
    title: "Editing",
    content: "Building a Trello clone with React DnD",
  },
  {
    id: 4,
    status: "open",
    title: "Invoicing",
    content: "Inro To Web3 ",
  },
];
const statuses = [
  {
    index: 1,
    status: "open",
    color: "#EB5A46",
  },
  {
    index: 2,
    status: "in progress",
    color: "#00C2E0",
  },
  {
    index: 3,
    status: "in review",
    color: "#C377E0",
  },
  {
    index: 4,
    status: "done",
    color: "#3981DE",
  },
];
export { data, statuses };

```

然后，将下面的代码片段添加到`types.js`文件中:

```
const ITEM_TYPES = 'ITEM'
export default ITEM_TYPES

```

## 设计我们的组件

现在，让我们来设计我们的组件。在您的`src`目录中创建一个`style.css`文件，并将下面的代码片段添加到其中:

```
:root {
    --primary-color: rgb(62, 100, 255);
    --complete-color: #27aa80;
    --text-color: #172b4d;
    --disabled-color: #fad6d6;
    --background-color: #f5eaea;
}
html {
    background: rgb(0,73,191);
    background: linear-gradient(90deg, rgba(0,73,191,1) 0%, rgba(190,190,255,1) 46%, rgba(0,212,255,1) 100%);
}
body {
    color: var(--text-color);
    font-family: -apple-system,BlinkMacSystemFont,Segoe UI,Roboto,Noto Sans,Ubuntu,Droid Sans,Helvetica Neue,sans-serif;
    margin: 0;
    background-image: url("https://trello-backgrounds.s3.amazonaws.com/SharedBackground/2400x1600/ef3a46a026c718b8329c0c34b0a57108/photo-1550592704-6c76defa9985.jpg");
    background-size: cover;
}
a {
    color: unset;
    text-decoration: unset;
    cursor: pointer;
}
p {
    margin: 10px 0;
    overflow-wrap: break-word;
    text-align: left;
}
label {
    font-size: 16px;
    display: block;
}
button, input {
    padding: 4px;
    border: 1px solid var(--disabled-color);
}
button {
    outline: none;
    background: transparent;
    border-radius: 5px;
    color: var(--primary-color);
    transition: all ease 0.8s;
    cursor: pointer;
}
button.active {
    color: var(--primary-color);
}
button.active:after {
    content: "";
    display: block;
    margin: 0 auto;
    width: 50%;
    padding-top: 4px;
    border-bottom: 1px solid var(--primary-color);
}
input:focus {
    outline: none;
}
select {
    outline: none;
    height: 40px;
}
.row {
    display: flex;
    flex-direction: row;
    justify-content: center;
}
.item {
    font-size: 15px;
    margin-bottom: 10px;
    padding: 10px;
    border-radius: 5px;
    z-index: 1;
    background-color: white;
}
.item:hover {
    cursor: pointer;
}
.item-title {
    font-weight: 600;
    font-size: 16px;
}
.item-status {
    text-align: right;
}
.color-bar {
    width: 40px;
    height: 10px;
    border-radius: 5px;
}
.drop-wrapper {
    flex: 1 25%;
    width: 100%;
    height: 100%;
}
.col-wrapper {
    display: flex;
    flex-direction: column;
    margin: 20px;
    padding: 20px;
    background-color: var(--background-color);
    border-radius: 5px;
}
.col-header {
    font-size: 20px;
    font-weight: 600;
    margin-bottom: 20px;
    margin-top: 0;
}
.col {
    min-height: 300px;
    max-width: 300px;
    width: 300px;
}
.highlight-region {
    background-color: yellow;
}
.page-header {
    background-color: #10131470;
    padding: 20px;
    color: white;
    font-size: 20px;
    flex: 1 100%;
    margin-top: 0;
    text-align: left;
    font-weight: bolder;
}
.modal {
    background-color: #F4F5F7;
    border-radius: 2px;
    margin: 48px 0 80px;
    min-height: 450px;
    width: 800px;
    outline: none;
    padding: 20px;
}
.overlay {
    display: flex;
    justify-content: center;
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: rgba(0,0,0,0.5);;
}
.close-btn-ctn {
    display: flex;
}
.close-btn {
    height: 40px;
    width: 35px;
    font-size: 20px;
    color: #031D2C;
    border: none;
    border-radius: 25px;
}
.close-btn:hover {
    background-color: #DCDCDC;
}

```

## 结论

在本教程中，我们学习了如何使用 React DnD 在应用程序中轻松实现拖放功能。为了将我们的知识付诸实践，我们构建了一个 Trello 的克隆，这是一个使用拖放将项目组织成列的 UI 工具。

您可以按照本教程中概述的步骤来构建各种需要拖放的应用程序。我希望你喜欢这个教程！

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