# 用 React 和 Automerge - LogRocket Blog 构建一个 Google Docs 克隆

> 原文：<https://blog.logrocket.com/build-google-docs-clone-react-automerge/>

协作在线文档在各行各业都很常见，它允许具有不同专业知识的人聚集在一起，将他们的工作写在(虚拟)纸上。它们提高参与度，提高生产率，鼓励解决问题，并允许参与者相互学习。

Automerge 是一个流行的库，用于在 [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript) 和 [React](https://reactjs.org/) 生态系统中构建协作应用。它很容易设置和使用类似 JSON 的数据结构，不同的用户可以同时修改和合并该数据结构。

在本教程中，我们将构建一个 [Google Docs](https://docs.google.com) 克隆，用户可以在其中创建、查看、编辑和删除文本文档。编辑器界面将支持[所见即所得](https://en.wikipedia.org/wiki/WYSIWYG)，所有编辑将在应用程序的不同实例之间自动同步。

![Google Docs Clone Output](img/89d3622161ad12225565ad7faf38be99.png)

作为参考，我们将要构建的项目部署在[这里](https://codesandbox.io/s/automerge-google-docs-rgsgcs)。

## 初始化 React 应用程序

首先，打开您的终端并运行命令`npx [[email protected]](/cdn-cgi/l/email-protection) google-docs-clone`。该命令将创建一个全功能的 React 应用程序。

然后通过`cd google-docs-clone`在新创建的项目文件夹中更改您的工作目录，并运行`npm start`来启动 React developer 服务器。

现在，打开您的网络浏览器，导航到 [http://localhost:3000](http://localhost:3000) 查看您的应用。它应该是这样的:

![Localhost React Output](img/9db321a8214c84a056c6e2239d35cbbf.png)

切换回代码编辑器，删除`src`文件夹中的所有文件，因为我们将从头开始构建所有内容。

## 安装依赖项

为了安装所有必需的依赖项，在终端中运行以下命令:`npm install automerge react-quill react-router-dom uuid localforage`。

*   [automerge](https://github.com/automerge/automerge) 包将为应用程序提供核心功能，允许创建、编辑、删除和同步所有文档的数据
*   [react-quill](https://github.com/zenoamaro/react-quill) 将用于 WYSIWYG 编辑器来编辑每个文档的内容。它将允许用户创建诸如标题、段落、列表和链接等元素
*   [react-router-dom](https://v5.reactrouter.com/web/guides/quick-start) 将提供主仪表板和单个文档之间的路由，允许用户打开它们并切换回主页
*   [uuid](https://www.npmjs.com/package/uuid) 包将为每个文档生成唯一的指示符，这些指示符将被传递给 URL 以使它们是唯一的
*   [local feed](https://github.com/localForage/localForage)包将用于在用户机器上存储创建的数据。你也可以使用本地的[本地存储](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)，但是这个包会简化与它的交互

## 创建基础

我们首先需要创建将呈现我们的应用程序的主文件，并定义一些将在整个应用程序中使用的全局样式规则。

在`src`文件夹中，创建一个新文件`index.js`并包含以下代码:

```
import ReactDOM from "react-dom/client";
import { BrowserRouter } from "react-router-dom";

import App from "./App";
import  "./styles.css";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);

```

我们导入了`ReactDOM`并创建了根元素，稍后我们用它来呈现应用程序。为了用`react-router-dom`，我们先导入，然后把整个 app 都包在里面。

创建一个新文件`styles.css`，并包含以下样式规则:

```
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  background-color: #eeeeee;
  min-height: 100vh;
}

.wrapper {
  font-family: sans-serif;
}

.ql-editor {
  min-height: calc(100vh - 100px);
  background-color: white;
}

```

我们首先删除了所有关于边距、填充和框大小的默认样式，这样我们的应用程序的布局在不同的浏览器上看起来是一样的。然后，我们将主体背景设置为非常浅的灰色，并确保它至少是视口的高度。

我们还将应用程序设置为使用 sans-serif 字体，并为编辑器窗口设置自定义规则。我们确保编辑器总是填充视口的高度，并将文本的背景颜色设置为白色，以便更好地与文本形成对比。

## 创建组件

在终端中运行以下命令:

```
cd src && mkdir components && cd components && touch ContentWrapper.js ContentWrapper.module.css Header.js Header.module.css DocumentCard.js DocumentCard.module.css AddButton.js AddButton.module.css

```

这将为应用程序创建所有必要的组件。

打开`ContentWrapper.js`并包含以下代码:

```
lang=javascript
import styles from "./ContentWrapper.module.css";

export default function ({ children }) {
  return <div className={styles.wrapper}>{children}</div>;
}

```

这将是主仪表板上所有文档卡片的包装组件。一旦实现了 app 逻辑，我们就会传递子组件。

打开`ContentWrapper.module.css`并包含以下样式规则:

```
.wrapper {
  max-width: 1200px;
  margin: 20px auto;
  padding: 0 20px;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
  gap: 20px;
}

```

我们确保包装宽度是有限的，居中，并在顶部和底部增加了一些余量。我们还在包装器的两侧添加了一些填充，这样内容在较小的屏幕上看起来也很好。

然后，我们将包装器设置为使用网格布局系统，其中每一列都不超过一定的宽度，并且响应不同的屏幕。为了改善布局，我们还在列之间添加了一些间隙。

打开`Header.js`并包含以下代码:

```
lang=javascript
import styles from "./Header.module.css";

export default function ({ onClick }) {
  return (
    <div className={styles.wrapper}>
      <p className={styles.title} onClick={onClick}>
        Docs
      </p>
    </div>
  );
}

```

header 组件将显示 title 元素并接收`onClick` prop，这将把用户从应用程序的任何状态带到主仪表板。

打开`Header.module.css`并包含以下样式规则:

```
.wrapper {
  background-color: #4480f7;
  padding: 20px;
  color: white;
}

.title {
  cursor: pointer;
}

```

我们将背景颜色设置为蓝色，添加一些填充，并将文本颜色设置为白色。为了改善 UX，我们将光标设置为当悬停在标题的 title 元素上时变为指针。

打开`DocumentCard.js`并包含以下代码:

```
import styles from "./DocumentCard.module.css";

export default function ({ text, onClick, deleteHandler }) {
  const createTitle = (text) => {
    if (text.replace(/<\/?[^>]+(>|$)/g, "")) {
      let parser = new DOMParser();
      const doc = parser.parseFromString(text, "text/html");
      const title =
        doc.body.childNodes[0].lastChild.innerHTML ||
        doc.body.childNodes[0].innerHTML;
      return title.length > 10 ? `${title.slice(0, 10)}...` : title;
    }
    return "Untitled doc";
  };
  return (
    <div className={styles.wrapper} onClick={onClick}>
      <div
        className={styles.preview}
        dangerouslySetInnerHTML={{ __html: text }}
      ></div>
      <div className={styles.footer}>
        <div className={styles.title}>{createTitle(text)}</div>
        <div className={styles.delete} onClick={deleteHandler}>
          <span role="img" aria-label="bin">
            ❌
          </span>
        </div>
      </div>
    </div>
  );
}

```

文件卡将由两个主要部分组成；预览区域和页脚。

预览区域将接收到`text` prop，这将是一串原始的 HTML 代码，我们将使用`dangerouslySetInnerHTML`来生成它的预览。

页脚将包含卡片的标题，该标题将从`text`属性中的第一个节点生成，并使用`createTitle`函数限制为 10 个字符。它还将包括删除按钮，这将允许用户删除卡与`deleteHandler`道具。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

卡片还会收到`onClick`道具，道具会打开卡片并显示编辑器。

打开`DocumentCard.module.css`并包含以下样式规则:

```
.wrapper {
  background-color: white;
  padding: 10px;
  border: 1px solid rgb(223, 223, 223);
  border-radius: 5px;
}

.wrapper:hover {
  border: 1px solid #4480f7;
  cursor: pointer;
}

.preview {
  height: 200px;
  overflow: hidden;
  font-size: 50%;
  word-wrap: break-word;
}

.footer {
  display: grid;
  grid-template-columns: auto 20px;
  min-height: 40px;
  border-top: 1px solid rgb(223, 223, 223);
  padding-top: 10px;
}

.title {
  color: #494949;
  font-weight: bold;
}

.delete {
  font-size: 12px;
}

```

我们将卡片的主包装设为白色，添加一些填充，设置一个灰色边框，并为一些平滑的边缘添加一个边框半径。我们还确保卡片将边框颜色更改为蓝色，并将光标更改为悬停时的指针。

对于预览块，我们定义了一个特定的高度，通过将其正常大小减半来确保包含更多的文本，并确保较长的单词被拆分。

对于页脚区域，我们设置了一个特定的高度，在顶部添加了一些边距和填充，并使用网格布局将宽度分成两列。

第一列将包括标题，标题将使用深灰色并加粗。第二列将包括删除按钮，我们减小了它的字体大小。

打开`AddButton.js`并包含以下代码:

```
import styles from "./AddButton.module.css";

export default function AddButton({ onClick }) {
  return (
    <div className={styles.wrapper} onClick={onClick}>
      <p className={styles.sign}>+</p>
    </div>
  );
}

```

add 按钮将包含加号并接收`onClick`属性，这将允许用户在单击它时创建一个新文档。

打开`AddButton.module.css`并包含以下样式规则:

```
.wrapper {
  display: grid;
  place-items: center;
  height: 60px;
  width: 60px;
  border-radius: 50%;
  background-color: #4480f7;
  position: fixed;
  bottom: 20px;
  right: 20px;
}

.wrapper:hover {
  cursor: pointer;
  box-shadow: rgba(0, 0, 0, 0.1) 0px 20px 25px -5px,
    rgba(0, 0, 0, 0.04) 0px 10px 10px -5px;
}

.sign {
  font-size: 28px;
  color: white;
}

```

我们使用网格布局，将包装器的内容居中，将元素设置为使用特定的宽度和高度，将其设置为圆形，设置蓝色背景色，并确保它总是显示在屏幕的右下角。

为了改善 UX，我们还将光标设置为指针，并在悬停时显示一些方框阴影。为了改进 UI，我们还增加了加号的字体大小，并以白色显示。

## 实现逻辑

在`src`文件夹中，创建一个新文件`App.js`，并包含以下代码:

```
import { useState, useEffect, useCallback, useMemo } from "react";
import { useNavigate } from "react-router-dom";

import * as Automerge from "automerge";
import localforage from "localforage";
import Header from "./components/Header";
import ContentWrapper from "./components/ContentWrapper";
import DocumentCard from "./components/DocumentCard";
import AddButton from "./components/AddButton";

import ReactQuill from "react-quill";
import "react-quill/dist/quill.snow.css";

import { v4 as uuidv4 } from "uuid";

let doc = Automerge.init();

export default function App() {
  const navigate = useNavigate();

  const [editorVisible, setEditorVisible] = useState(false);
  const [editorValue, setEditorValue] = useState("");

  let docId = window.location.pathname.split("/").pop();
  let channel = useMemo(() => {
    return new BroadcastChannel(docId);
  }, [docId]);

  const initDocuments = useCallback(() => {
    if (localforage.getItem("automerge-data") && !docId) {
      setEditorVisible(false);
      async function getItem() {
        return await localforage.getItem("automerge-data");
      }

      getItem()
        .then((item) => {
          if (item) {
            doc = Automerge.load(item);
            navigate(`/`);
          }
        })
        .catch((err) => console.log(err));
    }
  }, [navigate, docId]);

  useEffect(() => {
    initDocuments();
  }, [initDocuments]);

  const addDocument = () => {
    const id = uuidv4();
    let newDoc = Automerge.change(doc, (doc) => {
      setEditorValue("");
      if (!doc.documents) doc.documents = [];
      doc.documents.push({
        id,
        text: editorValue,
        done: false
      });
      navigate(`/${id}`);
    });

    let binary = Automerge.save(newDoc);
    localforage.clear();
    localforage
      .setItem("automerge-data", binary)
      .catch((err) => console.log(err));
    doc = newDoc;
  };

  const loadDocument = useCallback(() => {
    if (docId) {
      setEditorVisible(true);
      async function getItem() {
        return await localforage.getItem("automerge-data");
      }

      getItem()
        .then((item) => {
          if (item) {
            doc = Automerge.load(item);

            const itemIndex = doc.documents.findIndex(
              (item) => item.id === docId
            );
            if (itemIndex !== -1) {
              setEditorValue(doc.documents[itemIndex].text);
            } else {
              navigate("/");
              setEditorVisible(false);
            }
          }
        })
        .catch((err) => console.log(err));
    }
  }, [docId, navigate]);

  useEffect(() => {
    loadDocument();
  }, [loadDocument]);

  const updateDocument = useCallback(() => {
    if (Object.keys(doc).length !== 0) {
      const itemIndex = doc.documents.findIndex((item) => item.id === docId);

      if (itemIndex !== -1) {
        let newDoc = Automerge.change(doc, (doc) => {
          doc.documents[itemIndex].text = editorValue;
        });

        let binary = Automerge.save(newDoc);
        localforage
          .setItem("automerge-data", binary)
          .catch((err) => console.log(err));
        doc = newDoc;
        channel.postMessage(binary);
      }
    }
  }, [docId, editorValue, channel]);

  useEffect(() => {
    updateDocument();
  }, [updateDocument]);

  const deleteDocument = (docId) => {
    if (Object.keys(doc).length !== 0) {
      const itemIndex = doc.documents.findIndex((item) => item.id === docId);

      if (itemIndex !== -1) {
        let newDoc = Automerge.change(doc, (doc) => {
          doc.documents.splice(itemIndex, 1);
        });

        let binary = Automerge.save(newDoc);
        localforage
          .setItem("automerge-data", binary)
          .catch((err) => console.log(err));
        doc = newDoc;
        channel.postMessage(binary);
      }
      navigate("/");
    }
  };

  const syncDocument = useCallback(() => {
    channel.onmessage = (ev) => {
      let newDoc = Automerge.merge(doc, Automerge.load(ev.data));
      doc = newDoc;
    };
  }, [channel]);

  useEffect(() => {
    syncDocument();
  }, [syncDocument]);

  return (
    <div className="wrapper">
      <Header
        onClick={() => {
          setEditorVisible(false);
          navigate("/");
        }}
      />
      {!editorVisible ? (
        <ContentWrapper>
          {Object.keys(doc).length !== 0 &&
            doc.documents.map((document, index) => {
              return (
                <DocumentCard
                  key={index}
                  text={document.text}
                  onClick={() => {
                    setEditorVisible(true);
                    navigate(`/${document.id}`);
                  }}
                  deleteHandler={(e) => {
                    e.stopPropagation();
                    deleteDocument(document.id);
                  }}
                />
              );
            })}
          <AddButton
            onClick={() => {
              setEditorVisible(true);
              addDocument();
            }}
          />
        </ContentWrapper>
      ) : (
        <ReactQuill
          theme="snow"
          value={editorValue}
          onChange={setEditorValue}
        />
      )}
    </div>
  );
}

```

首先，我们导入了所有必要的 React 挂钩来跟踪应用程序状态，并在执行某些操作时执行副作用，我们安装的所有依赖项实例以及我们在上一步中创建的所有组件。

然后，我们初始化了 Automerge 实例。我们还创建了`editorVisible`和`editorState`来跟踪编辑器的存在及其内容，并创建了几个函数来为应用程序提供创建、读取、更新、删除和同步功能:

`initDocuments()`在初始启动时或 URL 更改为主仪表板时从 local feed 获取文档，以使用`automerge.load()`刷新内容

按下添加按钮后,`addDocument()`启动。它使用`automerge.change()`在数组对象中推送一个新文档，并使用`automerge.save()`保存它。

`loadDocument()`用于在使用`automerge.load()`在所见即所得编辑器中打开特定文档后获取该文档的信息。

每次用户在编辑器中进行任何编辑时，都会使用`updateDocument()`。文档首先用`automerge.change()`编辑，然后用`automerge.save()`保存。

按下删除图标后，启动`deleteDocument()`；用`automerge.change()`从文档数组中删除文档，用`automerge.save()`保存。

`syncDocument()`使用`automerge.merge()`功能同步来自应用程序其他实例的数据，并更新当前文档数组。

最后，我们渲染了所有导入的组件，并传递了所需的道具

## 测试应用程序

首先，检查你的应用是否还在 [http://localhost:3000](http://localhost:3000) 上运行。如果不是，请在您的终端中再次运行`npm start`。

点击右下角的**添加**按钮，创建一个新文档。您将被带到编辑器来创建内容。请注意，该文档收到了一个惟一的 ID。

![App Test Final](img/46a8dc7533be0d2c9e27c48c26d3a111.png)

现在在你的浏览器上打开一个新的标签页。请注意，您在前一个选项卡中创建的所有文档都已经在那里了。打开它们中的任何一个并进行一些更改。

切换回另一个标签，打开您在前一个标签中编辑的文档，您所做的更改现在应该会自动同步。

![App Test Thesis](img/af4d32203ab810990a64e49b6bff8256.png)

此外，如果有人试图在 URL 中使用错误的 ID 进行记录，应用程序会自动将用户重定向到主仪表板。

## 结论

在本教程中，我们学习了如何实现文本文档的创建、读取、更新和删除功能。我们还使文档协同工作，这意味着所有的更改都会自动与应用程序的其他实例同步。

请随意[派生项目](https://codesandbox.io/s/automerge-google-docs-rgsgcs)并添加额外的特性来满足您的特定需求。例如，您可以添加云解决方案来存储数据，并为应用程序添加身份验证，以便您可以在线使用应用程序，并仅邀请特定用户使用它。

下一次你必须提出一个协作文档解决方案时，你将知道实现它所需的技术和工具。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)