# 使用 FileReader API 预览 React 中的图像

> 原文：<https://blog.logrocket.com/using-filereader-api-preview-images-react/>

## 介绍

图像在互联网上传输的数据中占很大比例。通常情况下，客户必须将图像文件从他们的设备上传到服务器。为了确保用户上传正确类型、质量和大小的图像文件，大多数 web 应用程序都有预览图像的功能。

在浏览器环境中，客户端可以通过使用`input`元素或[拖放 API](https://blog.logrocket.com/html-5-drag-and-drop-api-a-tutorial/) 浏览文件来启动图像上传。然后，您可以使用`URL` API 或`FileReader` API 来读取图像文件并预览它们。

虽然使用`URL` API 预览图像很简单，但是使用`FileReader` API 可能会令人望而生畏。因此，在本文中，您将学习如何使用`FileReader` API 在 React 应用程序中预览图像。我们将涵盖单一和批量图像预览。

### 内容

## 如何在 React 中浏览图像文件

如果您想在 web 应用程序中添加文件上传功能，那么类型为`file`的`input`元素就派上了用场。它使用户能够从其计算机或移动设备的存储中选择单个或多个文件:

```
<input type="file" accept="image/*" multiple />

```

上面的`input`元素在浏览器中呈现时看起来像一个按钮。点击它将打开操作系统的内置文件选择器对话框。然后，用户可以选择图像文件进行上传。

`input`元素具有用于限制文件类型的`accept`属性。它的值是由逗号分隔的文件类型说明符组成的字符串。在上面的`input`元素中，`accept`属性的值是`image/*`。它使我们能够浏览和上传任何格式的图像。

要上传特定格式的图像文件，您可以限制`accept`属性的值。例如，将其值设置为`image/png`或`.png`仅接受 PNG 图像。

当`multiple`布尔属性设置为`true`时，用户可以选择多个图像文件。另一方面，如果值为`false`，用户只能浏览一个图像文件。值得注意的是，如果一个布尔属性出现在一个元素上，那么这个属性的值是`true`，如果忽略，那么这个属性的值是`false`。

用户完成文件选择后，浏览器发出`change`事件。因此，您应该监听`input`元素上的`change`事件。您可以在 React 中这样做:

```
<form>
  <p>
    <label htmlFor="file">Upload images</label>
    <input
      type="file"
      id="file"
      onChange={changeHandler}
      accept="image/*"
      multiple
    />
  </p>
</form>

```

在`change`事件处理程序中，您可以访问`FileList`对象。它是一个 iterable，其条目是`File`对象。`File`对象包含只读元数据，如文件名、类型和大小:

```
const changeHandler = (e) => {
  const { files } = e.target
  for (let i = 0; i < files.length; i++) {
    const file = files[i]; // OR const file = files.item(i);
  }
}

```

## 对`FileReader` API 的介绍

[`FileReader` API](https://developer.mozilla.org/en-US/docs/Web/API/FileReader) 提供了从 web 应用程序异步读取文件内容的接口。

正如上一节所强调的，您可以使用类型为`file`的`input`元素来浏览用户计算机或移动设备上的文件。以这种方式选择图像文件会返回一个`FileList`对象，其条目是`File`对象。

然后，`FileReader` API 使用`File`对象异步读取用户选择的文件。值得一提的是，您不能使用`FileReader` API 通过文件的路径名从用户的文件系统中读取文件内容。

`FileReader` API 有几个异步实例方法来执行读操作。这些方法包括:

*   `readAsArrayBuffer`
*   `readAsBinaryString`
*   `readAsDataURL`
*   `readAsText`

在本文中，我们将使用`readAsDataURL`方法。`readAsDataURL`方法以 file 对象作为参数，将图像文件作为数据 URL 异步读入内存。

完成`read`操作后，发出`change`事件；

```
const fileReader = new FileReader();

fileReader.onchange = (e) => {
   const { result } = e.target;
}

fileReader.readAsDataURL(fileObject);

```

关于其他`FileReader`实例方法的详细解释，您可以阅读文档。

## 如何在上传之前预览单张图片

在这一节中，我们将看看如何在用`FileReader` API 上传之前预览一张图片。它假设您已经设置了一个 React 项目。

下面的代码显示了如何使用`FileReader` API 在 React 中读取和预览单个图像。我们使用类型为`file`的`input`元素来浏览图像文件。因为我们想要预览一个单独的图像，我省略了`input`元素上的`multiple`布尔属性:

```
import { useEffect, useState } from 'react';

const imageMimeType = /image\/(png|jpg|jpeg)/i;

function App() {
  const [file, setFile] = useState(null);
  const [fileDataURL, setFileDataURL] = useState(null);

  const changeHandler = (e) => {
    const file = e.target.files[0];
    if (!file.type.match(imageMimeType)) {
      alert("Image mime type is not valid");
      return;
    }
    setFile(file);
  }
  useEffect(() => {
    let fileReader, isCancel = false;
    if (file) {
      fileReader = new FileReader();
      fileReader.onload = (e) => {
        const { result } = e.target;
        if (result && !isCancel) {
          setFileDataURL(result)
        }
      }
      fileReader.readAsDataURL(file);
    }
    return () => {
      isCancel = true;
      if (fileReader && fileReader.readyState === 1) {
        fileReader.abort();
      }
    }

  }, [file]);

  return (
    <>
      <form>
        <p>
          <label htmlFor='image'> Browse images  </label>
          <input
            type="file"
            id='image'
            accept='.png, .jpg, .jpeg'
            onChange={changeHandler}
          />
        </p>
        <p>
          <input type="submit" label="Upload" />
        </p>
      </form>
      {fileDataURL ?
        <p className="img-preview-wrapper">
          {
            <img src={fileDataURL} alt="preview" />
          }
        </p> : null}
    </>
  );
}
export default App;

```

如上例所示，您可以监听`input`元素上的`change`事件。客户端完成文件选择后，会调用`change`事件处理程序。您可以访问代表所选文件的`File`对象，并在事件处理程序中更新状态。

由于浏览器中的 HTML 标记是可编辑的，因此在开始读取过程之前，有必要检查所选文件的 MIME 类型。虽然普通用户不太可能编辑网页上的 HTML 元素，但它可以防止任何人轻易破坏你的应用程序。

上传文件后，您必须在服务器端进行类似的检查。此时，您还可以检查所选文件的大小，以确保它没有超过最大限制。

由于读取所选文件是一个副作用，我们使用了`useEffect`钩子。正如上一节所强调的，首先创建一个`FileReader`的实例。`FileReader` API 的`readAsDataURL`方法异步读取文件，完成读取过程后发出`load`事件。

在完成读取过程之前，可以卸载或重新呈现组件。如果读取过程未完成，您需要在卸载前中止。为了防止内存泄漏，React 不允许在卸载组件后进行状态更新。因此，在更新 load 事件处理程序中的状态之前，我们需要检查组件是否仍处于挂载状态。

我们将文件的数据作为 base64 编码的字符串进行访问，并在完成读取过程后更新状态。之后，您可以渲染图像预览。为了简单起见，我没有给上面例子中的`form`元素添加任何样式。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 如何在 React 中上传前预览多个图像

在这一节中，我们将看看如何在用`FileReader` API 上传之前预览多个图像。与上一节一样，它假设您已经设置了一个 React 项目。

阅读和预览多个图像类似于预览单个图像。我们将稍微修改前一节中的代码。要浏览并选择几个图像文件，需要将`input`元素上的`multiple`布尔属性的值设置为`true`。

一个值得注意的区别是，我们在更新状态之前，遍历了`useEffect`钩子中的`FileList`对象，并读取了所有选定文件的内容。我们将每个图像文件的数据 URL 存储在一个数组中，并在读取最后一个文件后更新状态。

下面的代码是对上一个批量预览图像的示例的修改:

```
import { useEffect, useState } from "react";

const imageTypeRegex = /image\/(png|jpg|jpeg)/gm;

function App() {
  const [imageFiles, setImageFiles] = useState([]);
  const [images, setImages] = useState([]);

  const changeHandler = (e) => {
    const { files } = e.target;
    const validImageFiles = [];
    for (let i = 0; i < files.length; i++) {
      const file = files[i];
      if (file.type.match(imageTypeRegex)) {
        validImageFiles.push(file);
      }
    }
    if (validImageFiles.length) {
      setImageFiles(validImageFiles);
      return;
    }
    alert("Selected images are not of valid type!");
  };

  useEffect(() => {
    const images = [], fileReaders = [];
    let isCancel = false;
    if (imageFiles.length) {
      imageFiles.forEach((file) => {
        const fileReader = new FileReader();
        fileReaders.push(fileReader);
        fileReader.onload = (e) => {
          const { result } = e.target;
          if (result) {
            images.push(result)
          }
          if (images.length === imageFiles.length && !isCancel) {
            setImages(images);
          }
        }
        fileReader.readAsDataURL(file);
      })
    };
    return () => {
      isCancel = true;
      fileReaders.forEach(fileReader => {
        if (fileReader.readyState === 1) {
          fileReader.abort()
        }
      })
    }
  }, [imageFiles]);
  return (
    <div className="App">
      <form>
        <p>
          <label htmlFor="file">Upload images</label>
          <input
            type="file"
            id="file"
            onChange={changeHandler}
            accept="image/png, image/jpg, image/jpeg"
            multiple
          />
        </p>
      </form>
      {
        images.length > 0 ?
          <div>
            {
              images.map((image, idx) => {
                return <p key={idx}> <img src={image} alt="" /> </p>
              })
            }
          </div> : null
      }
    </div>
  );
}

export default App;

```

我们将对`FileReader`实例的引用保存在一个数组中，以便在组件重新呈现或卸载时取消`cleanup`函数中的任何文件读取过程，从而避免内存泄漏。

当使用像 [React Router](https://blog.logrocket.com/migrating-react-router-v6-complete-guide/) 这样的路由库时，用户可以导航离开当前页面，并且组件在完成文件读取过程之前被卸载。因此，有必要进行如上所述的清理。

在上面的例子中，我们在一个循环中异步读取文件，然后更新状态。由于文件读取过程的异步性质，不可能知道我们将最后完成哪个文件的读取。因此，我们必须在更新状态之前检查在`load`事件处理程序中读取的文件数量。你可以通过承诺达到同样的效果。

下面的代码显示了对`useEffect`钩子的修改，改为使用承诺。与前面的方法相比，这种方法更简洁，也更容易理解:

```
useEffect(() => {
  const fileReaders = [];
  let isCancel = false;
  if (imageFiles.length) {
    const promises = imageFiles.map(file => {
      return new Promise((resolve, reject) => {
        const fileReader = new FileReader();
        fileReaders.push(fileReader);
        fileReader.onload = (e) => {
          const { result } = e.target;
          if (result) {
            resolve(result);
          }
        }
        fileReader.onabort = () => {
          reject(new Error("File reading aborted"));
        }
        fileReader.onerror = () => {
          reject(new Error("Failed to read file"));
        }
        fileReader.readAsDataURL(file);
      })
    });
    Promise
      .all(promises)
      .then(images => {
        if (!isCancel) {
          setImages(images);
        }
      })
      .catch(reason => {
        console.log(reason);
      });
  };
  return () => {
    isCancel = true;
    fileReaders.forEach(fileReader => {
      if (fileReader.readyState === 1) {
        fileReader.abort()
      }
    })
  }
}, [imageFiles]);

```

## 结论

大多数需要从客户端存储设备上传图像的 web 应用程序还附带了预览图像的功能。除其他原因外，预览图像可确保您的客户上传适当类型、质量和大小的图像文件。

您可以使用类型为`file`的`input`元素或使用拖放界面从客户端设备启动文件上传。选择图像后，您可以使用`URL` API 或`FileReader` API 预览它们。尽管使用`URL` API 可能很简单，但是`FileReader` API 却不简单。

正如本文所强调的，您可以单独或成批预览图像。希望这篇文章能让您对使用`FileReader` API 在 React 中预览图像有所了解。请在下面的评论区告诉我你的想法。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

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