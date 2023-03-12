# JavaScript 中的自定义事件:完整指南

> 原文：<https://blog.logrocket.com/custom-events-in-javascript-a-complete-guide/>

事件在 web 应用程序中无处不在。从浏览器完成加载和解析 HTML 时立即触发的`DOMContentLoaded`事件，到用户离开网站前触发的`unload`事件，使用 web 应用程序的体验本质上就是一系列事件。对于开发人员来说，这些事件帮助我们确定应用程序中刚刚发生了什么，用户在特定时间的状态是什么，等等。

有时，可用的 JavaScript 事件不能充分或正确地描述应用程序的状态。例如，当用户登录失败并且您希望父组件或元素知道失败时，没有登录失败事件或任何类似事件可供调度。

幸运的是，有一种方法可以为您的应用程序创建 JavaScript 自定义事件，这就是我们将在本教程中讨论的内容。

我们将详细讨论以下内容:

要学习本教程，您应该具备以下基本知识:

*   HTML 和 CSS
*   JavaScript 和 ES6
*   DOM 和 DOM 操作

我们开始吧！

## 如何用 JavaScript 创建自定义事件

可以通过两种方式创建自定义事件:

1.  使用`Event`构造函数
2.  使用`CustomEvent`构造函数

也可以使用`[document.createEvent](https://developer.mozilla.org/en-US/docs/Web/API/Document/createEvent)`创建自定义事件，但是从函数返回的对象所公开的大多数方法都已被否决。

## 使用事件构造函数

可以使用事件构造函数创建自定义事件，如下所示:

```
const myEvent = new Event('myevent', {
  bubbles: true,
  cancelable: true,
  composed: false
})

```

在上面的代码片段中，我们通过将事件名传递给`Event`构造函数，创建了一个事件`myevent`。事件名称不区分大小写，因此`myevent`与`myEvent`和`MyEvent`等相同。

事件构造函数还接受一个对象，该对象指定关于事件的一些重要属性。

### `bubbles`

属性指定事件是否应该向上传播到父元素。将此设置为`true`意味着如果事件在子元素中被调度，父元素可以监听事件并基于此执行操作。这是大多数原生 DOM 事件的行为，但是对于自定义事件，它默认设置为`false`。

如果您只想在特定的元素上调度事件，您可以通过`event.stopPropagation()`停止事件的传播。这应该在监听事件的回调中。稍后将详细介绍。

### `cancelable`

顾名思义，`cancelable`指定事件是否应该是可取消的。

默认情况下，原生 DOM 事件是可取消的，因此您可以对它们调用`event.preventDefault()`，这将阻止事件的默认操作。如果自定义事件的`cancelable`设置为`false`，调用`event.preventDefault()`将不会执行任何操作。

### `composed`

属性指定一个事件是否应该从影子 DOM(使用 web 组件时创建的)冒泡到真正的 DOM。如果`bubbles`被设置为`false`，这个属性的值就无关紧要了，因为你明确地告诉事件不要向上冒泡。但是，如果您想在 web 组件中调度一个定制事件，并在真实 DOM 中的父元素上监听它，那么 composed 属性需要设置为`true`。

使用这种方法的一个缺点是，您不能将数据发送给侦听器。然而，在大多数应用程序中，我们希望能够将数据从事件被发送到侦听器的地方发送出去。为此，我们可以使用`CustomEvent`控制器

也不能使用原生 DOM 事件发送数据。数据只能从事件的目标获取。

## 使用`CustomEvent`构造函数

可以使用`CustomEvent`构造函数创建自定义事件:

```
const myEvent = new CustomEvent("myevent", {
  detail: {},
  bubbles: true,
  cancelable: true,
  composed: false,
});

```

如上所示，通过`CustomEvent`构造函数创建自定义事件类似于使用`Event`构造函数创建事件。唯一的区别在于作为第二个参数传递给构造函数的对象。

当用`Event`构造函数创建事件时，我们受到一个事实的限制，即我们不能通过事件向监听器传递数据。这里，任何需要传递给侦听器的数据都可以在初始化事件时创建的`detail`属性中传递。

## 在 JavaScript 中调度自定义事件

创建事件后，您需要能够调度它们。事件可以被分派给任何扩展了`EventTarget`的对象，它们包括所有的 HTML 元素、文档、窗口等等。

您可以调度自定义事件，如下所示:

```
const myEvent = new CustomEvent("myevent", {
  detail: {},
  bubbles: true,
  cancelable: true,
  composed: false,
});
document.querySelector("#someElement").dispatchEvent(myEvent);

```

要侦听自定义事件，请向要侦听的元素添加一个事件侦听器，就像处理本机 DOM 事件一样。

```
document.querySelector("#someElement").addEventListener("myevent", (event) => {
  console.log("I'm listening on a custom event");
});

```

## JavaScript 自定义事件是如何工作的？

为了展示如何在 JavaScript 应用程序中使用自定义事件，我们将构建一个简单的应用程序，允许用户添加配置文件并自动获取配置文件卡。

这是我们完成后页面的样子:

![Profile Card](img/079fdd0894ce0fa3ab640cd33ba4d1d1.png)

### 构建用户界面

创建一个文件夹，随意命名，并在文件夹中创建一个`index.html`文件。

将以下内容添加到`index.html`:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Custom Events Application</title>
    <link rel="stylesheet" href="style.css" />
  </head>
  <body>
    <h1>Profile Card</h1>
    <main>
      <form class="form">
        <h2>Enter Profile Details</h2>
        <section>
          Drag an Image into this section or
          <label>
            select an image
            <input type="file" id="file-input" accept="image/*" />
          </label>
        </section>
        <div class="form-group">
          <label for="name"> Enter Name </label>
          <input type="text" name="name" id="name" autofocus />
        </div>
        <div class="form-group">
          <label for="occupation"> Enter Occupation </label>
          <input type="text" name="occupation" id="occupation" />
        </div>
      </form>

      <section class="profile-card">
        <div class="img-container">
          <img src="http://via.placeholder.com/200" alt="" />
        </div>
        <span class="name">No Name Entered</span>
        <span class="occupation">No Occupation Entered</span>
      </section>
    </main>
    <script src="index.js"></script>
  </body>
</html>

```

这里，我们正在为页面添加标记。

该页面有两个部分。第一部分是一个表单，它允许用户执行以下操作:

*   通过拖放或手动选择图像文件来上传图像
*   输入一个名称
*   从事一种职业

从表单中获取的数据将显示在第二部分，这是个人资料卡。第二部分只包含一些占位符文本和图像。从表单接收的数据将覆盖内容占位符数据。

创建一个`style.css`文件，并用以下内容填充它:

```
* {
  box-sizing: border-box;
}
h1 {
  text-align: center;
}
main {
  display: flex;
  margin-top: 50px;
  justify-content: space-evenly;
}
.form {
  flex-basis: 500px;
  border: solid 1px #cccccc;
  padding: 10px 50px;
  box-shadow: 0 0 3px #cccccc;
  border-radius: 5px;
}
.form section {
  border: dashed 2px #aaaaaa;
  border-radius: 5px;
  box-shadow: 0 0 3px #aaaaaa;
  transition: all 0.2s;
  margin-bottom: 30px;
  padding: 50px;
  font-size: 1.1rem;
}
.form section:hover {
  box-shadow: 0 0 8px #aaaaaa;
  border-color: #888888;
}
.form section label {
  text-decoration: underline #000000;
  cursor: pointer;
}
.form-group {
  margin-bottom: 25px;
}
.form-group label {
  display: block;
  margin-bottom: 10px;
}
.form-group input {
  width: 100%;
  padding: 10px;
  border-radius: 5px;
  border: solid 1px #cccccc;
  box-shadow: 0 0 2px #cccccc;
}
#file-input {
  display: none;
}
.profile-card {
  flex-basis: 300px;
  border: solid 2px #cccccc;
  border-radius: 5px;
  box-shadow: 0 0 5px #cccccc;
  padding: 40px 35px;
  align-self: center;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.img-container {
  margin-bottom: 50px;
}
.img-container img {
  border-radius: 50%;
  width: 200px;
  height: 200px;
}
.profile-card .name {
  margin-bottom: 10px;
  font-size: 1.5rem;
}
.profile-card .occupation {
  font-size: 1.2rem;
}

```

最后，创建一个`index.js`文件，这样您就可以向应用程序添加功能。

## JavaScript 拖放

我们将添加到应用程序的第一个功能是上传图像的能力。为此，我们将支持拖放和手动上传。

将以下内容添加到 JavaScript 文件中:

```
const section = document.querySelector(".form section");

section.addEventListener("dragover", handleDragOver);
section.addEventListener("dragenter", handleDragEnter);
section.addEventListener("drop", handleDrop);

/**
 * @param {DragEvent} event
 */
function handleDragOver(event) {
  // Only allow files to be dropped here.
  if (!event.dataTransfer.types.includes("Files")) {
    return;
  }
  event.preventDefault();
  // Specify Drop Effect.
  event.dataTransfer.dropEffect = "copy";
}

/**
 * @param {DragEvent} event
 */
function handleDragEnter(event) {
  // Only allow files to be dropped here.
  if (!event.dataTransfer.types.includes("Files")) {
    return;
  }
  event.preventDefault();
}

/**
 * @param {DragEvent} event
 */
function handleDrop(event) {
  event.preventDefault();
  // Get the first item here since we only want one image
  const file = event.dataTransfer.files[0];
  // Check that file is an image.
  if (!file.type.startsWith("image/")) {
    alert("Only image files are allowed.");
    return;
  }
  handleFileUpload(file);
}

```

这里，我们从 DOM 中选择部分。这允许我们监听允许拖放操作所需的适当事件，即`dragover`、`dragenter`和`drop`。

要更深入地了解，请查看我们关于 HTML 拖放 API 的[综合教程。](https://blog.logrocket.com/html-5-drag-and-drop-api-a-tutorial/)

在`handleDragOver`函数中，我们确保被拖动的项目是一个文件，并将拖放效果设置为`copy`。`handleDragEnter`也执行类似的功能，确保我们只处理文件的拖动。

实际的功能发生在文件被删除时，我们使用`handleDrop`来处理它。首先，我们阻止浏览器的默认操作，即在提交文件之前打开文件。

我们验证该文件是一个图像。如果不是，我们会发送一条错误消息让用户知道我们只接受图像文件。如果验证通过，我们将继续在`handleFileUpload`函数中处理该文件，我们接下来将创建它/

用以下内容更新`index.js`:

```
/**
 * @param {File} file
 */
function handleFileUpload(file) {
  const fileReader = new FileReader();
  fileReader.addEventListener("load", (event) => {
    // Dispatch an event to the profile card containing the updated profile.
    dispatchCardEvent({
      image: event.target.result,
    });
  });
  fileReader.readAsDataURL(file);
}

const profileCard = document.querySelector(".profile-card");
const CARD_UPDATE_EVENT_NAME = "cardupdate";

function dispatchCardEvent(data) {
  profileCard.dispatchEvent(
    new CustomEvent(CARD_UPDATE_EVENT_NAME, {
      detail: data,
    })
  );
}

```

`handleFileUpload`函数接受一个文件作为参数，并尝试使用文件读取器将该文件作为数据 URL 读取。

`FileReader`构造函数从`EventTarget`扩展而来，使我们能够监听事件。load 事件在图像加载后被触发——在我们的例子中，作为一个数据 URL。

您也可以加载其他格式的图像。如果你想了解更多关于文件阅读器的知识，MDN 有一个关于文件阅读器 API 的很好的文档。

一旦图像被加载，我们需要在 profile 卡中显示它。为此，我们将向配置文件卡分派一个自定义事件`cardupdate`。`dispatchCardEvent`处理事件的创建和分发到配置文件卡。

如果您还记得上一节，自定义事件有一个`detail`属性，可以用来传递数据。在本例中，我们传递一个包含图像 URL 的对象，该对象是从文件读取器获得的。

接下来，我们需要 profile 卡来监听卡的更新，并相应地更新 DOM。

```
profileCard.addEventListener(CARD_UPDATE_EVENT_NAME, handleCardUpdate);
/**
 * @param {CustomEvent} event
 */
function handleCardUpdate(event) {
  const { image } = event.detail;
  if (image) {
    profileCard.querySelector("img").src = image;
  }
}

```

如上所示，您只需像平常一样添加事件监听器，并在事件被触发时调用`handleCardUpdate`函数。

## 如何在 JavaScript 中使用对象析构

`handleCardUpdate`接收事件作为参数。使用对象析构，你可以从`event.detail`获得`image`属性。接下来，将 profile card 中图像的`src`属性设置为从事件中获取的图像 URL。

要允许用户通过输入字段上传图像:

```
const fileInput = document.querySelector("#file-input");

fileInput.addEventListener("change", (event) => {
  handleFileUpload(event.target.files[0]);
});

```

当用户选择一个图像时，文件输入将触发 change 事件。我们可以处理第一个图像的上传，因为我们只需要一个图像的个人资料卡。

现在我们不需要做任何新的事情，因为我们已经开发了所有的功能，增加了对拖放的支持。

下一个要添加的功能是更新姓名和职业:

```
const nameInput = document.querySelector("#name");
const occupationInput = document.querySelector("#occupation");

occupationInput.addEventListener("change", (event) => {
  dispatchCardEvent({
    occupation: event.target.value,
  });
});
nameInput.addEventListener("change", (event) => {
  dispatchCardEvent({
    name: event.target.value,
  });
});

```

为此，我们监听 change 事件并调度 card update 事件，但是这次使用不同的数据。我们需要更新处理程序，以便能够处理更多的图像。

```
/**
 * @param {CustomEvent} event
 */
function handleCardUpdate(event) {
  const { image, name, occupation } = event.detail;
  if (image) {
    profileCard.querySelector("img").src = image;
  }
  if (name) {
    profileCard.querySelector("span.name").textContent = name;
  }
  if (occupation) {
    profileCard.querySelector("span.occupation").textContent = occupation;
  }
} 
```

更新`handleCardUpdate`函数，看起来像上面的代码片段。这里，我们再次使用对象析构来从`event.detail`获取图像、姓名和职业。获得数据后，我们将它们显示在 profile 卡上。

## 结论

当您从被调度的事件(自定义和本机 DOM 事件)的角度考虑代码时，有时会更容易理解。如果使用得当，JavaScript 自定义事件可以增强应用程序的用户体验。因此，它包含在一些顶级 JavaScript 框架中也就不足为奇了，比如 Vue.js(在 Vue 中，您使用`$emit`分派自定义事件)。

本教程中使用的演示代码可以在 [GitHub](https://github.com/jameesjohn/custom-event-demo) 上获得。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.