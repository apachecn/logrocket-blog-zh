# 使用 nice-modal-react - LogRocket 博客改进 React 中的模态管理

> 原文：<https://blog.logrocket.com/improve-modal-management-react-nice-modal-react/>

在信息时代，情态动词的使用可以显著提高网站和网络应用的 UX。我们在任何地方都能看到它们，从像使用模态创建新推文的 Twitter 这样的网站，到在几乎每个企业的后台运行的复杂管理系统。

模态的主要优点是它们独立于活动页面，这意味着它们可以用于添加、更新、删除或查看信息，它们易于打开和关闭，它们不需要更改当前的 URL，并且背景信息通常是完全或部分可见的。

在本教程中，我们将探索 [nice-modal-react](https://github.com/eBay/nice-modal-react) ，这是由[易贝](https://ebay.com)的开发团队为 [React](https://reactjs.org) 创建的一个有用的模态实用程序。在内部测试和使用该工具一年后，他们好心地让公众可以使用它。

我们还将构建一个演示应用程序，以在实践中应用所有已审查的功能。预计我们将能够使用模态来创建新数据，以及编辑和删除现有数据:

![CRUD Notes](img/34138d91e131702ed734be80b76825eb.png)

作为参考，这里是最终项目的[源代码](https://codesandbox.io/s/nice-modal-react-182gc)。

## 为什么要用 nice-modal-react？

[nice-modal-react](https://github.com/eBay/nice-modal-react) 包是一个用[类型脚本](https://www.typescriptlang.org/)编写的零依赖实用程序，它使用上下文来控制整个应用程序中模态的状态。

该工具的主要优点是基于承诺的模态处理。这意味着您可以使用 props 来更新状态，而不是使用 props 来与组件交互。

您可以轻松地在整个应用程序中导入模态`components`或使用组件的特定`id`，因此您根本不必导入组件。

关闭模态独立于代码的其余部分，因此您可以从组件本身关闭组件，无论它显示在应用程序中的什么位置。

理解 [nice-modal-react](https://github.com/eBay/nice-modal-react) 不是模态组件本身是至关重要的。您需要自己创建实际的模型(或者使用 UI 库中的预建组件，如[材质 UI](https://mui.com/) 、[蚂蚁设计](https://ant.design/)或[脉轮](https://chakra-ui.com/))。

## 初始化 React 应用程序

我们将首先使用 [Create React app](https://create-react-app.dev/) 创建一个 React App。在您的终端中运行以下命令:`npx create-react-app crud-notes`。

安装完成后，您将看到在当前工作目录中创建了一个新的项目文件夹。

接下来，通过运行`cd crud-notes`更改目录，然后通过运行`npm start`启动应用程序。该命令应该会打开您的默认浏览器并显示 React 应用程序。

如果没有自动打开，在浏览器的地址栏输入`[http://localhost:3000](http://localhost:3000)`并执行。

回到项目中，导航到`src`文件夹，找到文件`App.js`、`App.css`、`index.js`，并删除其中的内容，因为我们将从头开始编写所有内容。

同样，将`App.css`重命名为`styles.css`并删除`index.css`文件。

在新重命名的`styles.css`文件中，包含以下样式规则:

```
css
@import url("https://fonts.googleapis.com/css2?family=Montserrat&display=swap");

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Montserrat", sans-serif;
}

body {
  padding: 20px;
}

.App {
  max-width: 500px;
  margin: 0 auto;
  text-align: center;
}
```

首先，我们为`margin`、`padding`和`border-box`声明了一些重置规则，因此所有的元素在所有的浏览器中都是同等显示的。我们还确保应用程序使用蒙特塞拉特岛字体。

然后，我们在主体中添加了一些`padding`，将应用包装器设置为不超过`500px`，将它放在视口的中心，并将文本放在视口的中心。

## 设置 nice-modal-react

安装 nice-modal-react 包本身就像运行`npm install @ebay/nice-modal-react`一样简单。它将向您的节点模块添加一个小的(在 gzip 之后大约 2KB)和无依赖性的包。

为了在整个应用程序中使用它，我们将设置一个单独的提供者，该提供者将使用 [React Context](https://reactjs.org/docs/context.html) 来全局控制状态。

为此，打开`index.js`根文件，导入`NiceModal`组件，并将其包在`App`组件周围:

```
javascript
import ReactDOM from "react-dom";
import NiceModal from "@ebay/nice-modal-react";
import App from "./App";

const rootElement = document.getElementById("root");

ReactDOM.render(
  <NiceModal.Provider>
    <App />
  </NiceModal.Provider>,
  rootElement
);
```

在这一点上，我们已经设置了使用 nice-modal-react 的项目，所以我们可以开始为应用程序构建单独的组件。

## 创建组件

首先，我们需要为必要的组件创建单独的文件:`Modal`、`Button`和`Note`。为了保持一切井然有序，我们将创建一个单独的`components`文件夹，并为每个组件创建一个单独的`.js`文件和`.css`文件。

您可以手动创建这些文件，但是我建议使用以下命令来节省时间:

```
bash
mkdir components && cd components && touch Modal.js Modal.css Button.js Button.css Note.js Note.css
```

### 情态的

打开`Modal.js`并包含以下代码:

```
javascript
import { useState } from "react";
import NiceModal, { useModal } from "@ebay/nice-modal-react";
import "./Modal.css";
import Button from "./Button";

const Modal = NiceModal.create(
  ({ title, subtitle, action, bgColor, note = "" }) => {
    const [input, setInput] = useState(note);
    const modal = useModal();
    return (
      <div className="background">
        <div className="modal">
          <h1>{title}</h1>
          <p className="subtitle">{subtitle}</p>
          {action === "Save" && (
            <input
              className="input"
              type="text"
              value={input}
              onChange={(e) => {
                setInput(e.target.value);
              }}
            />
          )}
          <div className="actions">
            <Button
              name={action}
              backgroundColor={bgColor}
              onClick={() => {
                if (action === "Save") {
                  if (input) {
                    modal.resolve(input);
                    modal.remove();
                    console.log("Note saved");
                  } else {
                    console.log("Note is empty");
                  }
                } else {
                  modal.resolve();
                  modal.remove();
                  console.log("Note removed");
                }
              }}
            />
            <Button
              name="Cancel"
              backgroundColor="silver"
              onClick={() => {
                modal.remove();
              }}
            />
          </div>
        </div>
      </div>
    );
  }
);

export default Modal;
```

首先，我们导入了`useState`来跟踪添加和编辑动作的输入状态，以及将成为我们的模型包装器的`NiceModal`组件。我们还为取消操作导入了外部样式表和`Button`组件来关闭模态。

我们使用`NiceModal.create`作为模态包装器。你可以认为这是创建一个基本组件，并将其包装成一个高阶函数。一旦我们将`Modal`组件导入到`App.js`中，它就会收到`title`、`subtitle`、`action`、`bgColor`和`note`道具。

添加和编辑模式将有一个输入字段，用户可以从零开始添加注释标题或编辑现有的注释标题。

输入的状态将存储在状态变量中，并传递给`App.js`使用。我还添加了一个简单的验证，这样用户就不能添加空的注释。

添加和编辑模式将包括保存选项，而删除模式将有一个删除按钮。每个模式的保存/删除旁边都有一个取消按钮，用于关闭该模式。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

打开`Modal.css`并包含以下样式规则:

```
css
.background {
  width: 100vw;
  height: 100vh;
  position: absolute;
  left: 0;
  top: 0;
  display: grid;
  place-items: center;
  background-color: rgba(0, 0, 0, 0.7);
}

.modal {
  padding: 20px;
  width: 300px;
  border-radius: 10px;
  text-align: center;
  background-color: white;
  word-break: break-all;
}

.subtitle {
  margin-bottom: 20px;
}

.input {
  width: 100%;
  height: 25px;
  border: 1px solid silver;
  border-radius: 5px;
  padding: 0px 10px;
}

.actions {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
  margin-top: 20px;
}
```

我们将模态背景设置为填充所有的视口，使用黑色的`background-color`和`0.7`不透明度，并将子元素居中，这将是模态包装器。

对于实际的`modal`，我们设置了`padding`、特定的`width`、`border-radius`，将文本居中，将`background-color`设置为白色，并添加了一个`word-break`来拆分超出包装宽度的单词。

我们在字幕下面设置了一个`margin`，把它和输入区、动作区分开。

`input`将使用整个可用宽度，有一个特定的`height`，一个带圆角的`border`，还有一些左右两侧的`padding`。

`actions`区域将保存几个用于编辑和删除功能的`Button`组件，并被设置为将可用的`width`分成两列，一些`gap`在中间，`margin`在上面。

### 纽扣

打开`Button.js`并包含以下代码:

```
javascript
import "./Button.css";

const Button = ({ name, backgroundColor, onClick }) => {
  return (
    <button className="button" onClick={onClick} style={{ backgroundColor }}>
      {name}
    </button>
  );
};

export default Button;
```

首先，我们导入样式表来样式化组件。然后，我们创建了一个简单的按钮组件，一旦导入并在`App.js`中使用，它将接收`name`、`backgroundColor`和`onClick`道具。

打开`Button.css`文件，包含以下样式规则:

```
css
.button {
  border: none;
  padding: 5px 10px;
  cursor: pointer;
  border-radius: 5px;
  width: 100%;
}
```

我们去掉了默认的按钮`border`，增加了一些`padding`，设置`cursor`为指针，增加了一些`border-radius`为平滑的边角，设置按钮填充可用的`width`。

### 注意

打开`Note.js`文件，包括以下内容:

```
javascript
import "./Note.css";
import Button from "./Button";

const Note = ({ title, onClickEdit, onClickDelete }) => {
  return (
    <div className="note">
      <p>{title}</p>
      <Button name="Edit" backgroundColor="gold" onClick={onClickEdit} />
      <Button name="Delete" backgroundColor="tomato" onClick={onClickDelete} />
    </div>
  );
};

export default Note;
```

我们导入样式表来样式化组件，以及外部的`Button`组件，因此我们可以重用它来编辑和删除功能。

`Note`组件包括笔记的`title`，以及我们在`App.js`中导入和使用`Note`组件时会传入的`Button`组件的`onClickEdit`和`onClickDelete`道具。

打开`Note.css`，包括以下内容:

```
css
.note {
  display: grid;
  grid-template-columns: auto 70px 70px;
  gap: 20px;
  margin: 20px auto;
  text-align: left;
  word-break: break-all;
}

@media screen and (max-width: 400px) {
  .note {
    grid-template-columns: 1fr;
  }
}
```

我们将注释设置为使用三列布局，中间有一个`20px`间隔，而编辑和删除按钮将使用固定的`width`，其余可用的`width`将用于注释`title`。我们还将`margin`设置在顶部，将文本居中放置在左侧，并添加了一个`word-break`以便自动拆分较长的单词。

我们还为响应创建了一些媒体规则。对于屏幕宽度`400px`和更小的，注释将切换到一列布局，这意味着所有包含的元素(标题、编辑按钮和删除按钮)将直接显示在彼此下方。

## 实现逻辑

现在，让我们把所有东西放在一起，为我们的应用程序创建逻辑。打开`App.js`并包含以下代码:

```
javascript
import { useState } from "react";
import NiceModal from "@ebay/nice-modal-react";
import Modal from "../components/Modal";
import Note from "../components/Note";
import Button from "../components/Button";
import "./styles.css";

const noteList = [
  "My awesome third note",
  "My awesome second note",
  "My awesome first note"
];

const getNoteIndex = (e) =>
  Array.from(e.target.parentElement.parentNode.children).indexOf(
    e.target.parentElement
  );

export default function App() {
  const [notes, setNotes] = useState(noteList);

  const showAddModal = () => {
    NiceModal.show(Modal, {
      title: "Add a new note",
      subtitle: "Enter the title",
      action: "Save",
      bgColor: "lime green"
    }).then((note) => {
      setNotes([note, ...notes]);
    });
  };

  const showEditModal = (e) => {
    NiceModal.show(Modal, {
      title: "Edit the note",
      subtitle: "Rename the Title",
      action: "Save",
      bgColor: "gold",
      note: notes[getNoteIndex(e)]
    }).then((note) => {
      const notesArr = [...notes];
      notesArr[getNoteIndex(e)] = note;
      setNotes(notesArr);
    });
  };

  const showDeleteModal = (e) => {
    NiceModal.show(Modal, {
      title: "Confirm Delete",
      subtitle: `The "${notes[getNoteIndex(e)]}" will be permanently removed`,
      action: "Delete",
      bgColor: "tomato",
      note: notes[getNoteIndex(e)]
    }).then(() => {
      const notesArr = [...notes];
      notesArr.splice(getNoteIndex(e), 1);
      setNotes(notesArr);
    });
  };

  return (
    <div className="App">
      <h1>CRUD Notes</h1>
      <p style={{ marginBottom: "20px" }}>Using nice-modal-react</p>
      <Button
        name="Add"
        backgroundColor="lime green"
        onClick={() => {
          showAddModal();
        }}
      />
      <div>
        {notes.map((note, index) => {
          return (
            <Note
              key={index}
              note={note}
              onClickEdit={showEditModal}
              onClickDelete={showDeleteModal}
            />
          );
        })}
      </div>
    </div>
  );
}
```

首先，我们导入了`useState`钩子，以便在使用应用程序时更新 notes 对象时跟踪它。我们还导入了`NiceModal`组件和我们在前一阶段创建的每个单独的组件。

为了设置组件的样式，我们将使用我们创建的外部样式表。

然后我们创建了一个`noteList`数组来保存应用程序的示例注释。我们还创建了`getNoteIndex`函数，这样我们就能够识别用户在列表中点击的特定笔记的索引。

在`App`函数中，我们首先将样本注释列表设置为`notes`变量。然后我们创建了三个不同的函数来处理添加、编辑和删除按钮的点击。

每个函数打开模态，并传入我们在`Modal`组件中定义的必要属性。按下“保存”或“删除”按钮后，笔记列表会相应更新。

最后，我们渲染了应用程序的`title`、`subtitle`，添加了带有必要道具的`Add`按钮，并通过`notes`变量循环显示所有的注释。

一切都是有组织的，模态本身没有单一的状态变量，然而我们成功地处理了三种不同的模态。

至此，您应该有了一个工作演示。让我们来测试一下！

确保您的 React 应用程序仍在终端中运行。如果没有，再次运行`npm start`。现在，打开浏览器并导航至`[http://localhost:3000](http://localhost:3000)`。您应该会看到一个功能齐全的 CRUD Notes 演示应用程序。

![Add and Delete New Note](img/50365d6d42c8207a01cef77502b2159c.png)

## 结论

虽然这可能看起来像一个基本的 notes 应用程序，但我们实现了构建真实 CRUD 应用程序所需的所有功能。我们关注的是行为和状态，所以请确保根据您在项目中的具体需求来调整模态的内容。

此外，可以随意向表单中添加一些高级输入验证，或者编写一些后端程序，这样所有的值都存储在数据库中，并且不会丢失数据。目前，只有空输入的`console.log`语句，数据存储在状态中。

因为它是开源的，所以请查看这个 [GitHub 库](https://github.com/eBay/nice-modal-react),并随时向项目贡献任何想法或功能请求，以使它变得更好！

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)