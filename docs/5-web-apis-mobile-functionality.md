# 将移动功能添加到您的项目中的 5 个 web APIs

> 原文：<https://blog.logrocket.com/5-web-apis-mobile-functionality/>

作为开发人员，我们经常使用 API(应用程序编程接口)来轻松实现复杂的功能，或者创建它们来抽象复杂性。API 允许服务之间相互交流，并做一些事情，比如发布推文或显示地图。

为了构建客户端 web 应用程序，我们可以将 web APIs 分为两类:

*   浏览器 API:这些 API 与 JavaScript 接口，允许开发者轻松实现功能。API 包括 DOM、Fetch、音频和视频、WebGL、通知等等
*   第三方 API:你可能已经在你的项目中使用过了。这些是默认情况下没有内置到浏览器中的 API。它们是由谷歌、脸书、特雷罗等公司提供的。允许您通过 JavaScript 访问它们的功能并在您的项目中使用它

让我们来看看一些流行的 Web APIs:

*   地理位置 API:该 API 允许访问以检索主机设备的位置信息
*   文档对象模型 API:DOM 是 HTML 文档的 API，它是 JavaScript 程序和 HTML 文档之间的接口。 [DOM 本身有一个广泛的接口列表](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)，比如`Document`接口、`Window`接口等等
*   历史 API:历史 API 在大多数路由器实现中都是抽象的。该 API 使您能够跟踪和修改浏览器的 URL 和历史数据，以及通过 JavaScript 访问您的浏览历史
*   Canvas API:Canvas API 允许您通过使用一个`<canvas>`元素在页面上显示不同的可视图形，这对 HTML 游戏和图表很有用
*   Web 动画 API:Web 动画 API 支持页面上协调的视觉变化。它结合了 CSS 过渡/动画和基于 JavaScript 的动画的优点。

在本文中，我将探索一些我最喜欢的提供移动友好功能的 API。这可以包括从社交媒体共享和剪贴板操作到联系人、语音和通知功能的任何内容。

## 为您的下一个项目提供 5 个移动功能 web APIs

到目前为止，我们已经讨论了一些通过 JavaScript 库直接或间接使用的常见 API。

在这一节中，我们将探索您的下一个项目可能需要的五个独特的 API。这些 API 非常重要，因为它们为 web 带来了移动原生功能。

### Web 共享 API

这个 API 帮助你在你的网站上实现共享功能。它给人一种移动本地共享的感觉。它可以在设备上共享文本、文件和其他应用程序的链接。

Web 共享 API 可通过`navigator.share`方法访问:

```
if (navigator.share) {
  navigator.share({
    title: 'Logrocket alert here',
    text: 'Check out Logrocket',
    url: '<https://logrocket.com/>',
  })
    .then(() => console.log('Successful share'))
    .catch((error) => console.log('Error sharing', error));
}

```

上面的代码片段演示了如何使用普通 JavaScript 共享文本。需要注意的一件重要事情是，您只能通过`onclick`事件调用这个动作:

```
function Share({ label, text, title }) {
  const shareDetails = { title, text };

  const handleSharing = async () => {
    if (navigator.share) {
      try {
        await navigator.share(shareDetails).then(() => console.log("Sent"));
      } catch (error) {
        console.log(`Oops! I couldn't share to the world because: ${error}`);
      }
    } else {
      // fallback code
      console.log(
        "Web share is currently not supported on this browser. Please provide a callback"
      );
    }
  };
  return (
    <button onClick={handleSharing}>
      <span>{label}</span>
    </button>
  );
}

```

上面的代码片段是一个基本示例，展示了如何使用 API 和 React 在应用程序上实现共享选项。你可以在 CodeSandbox 上查看这个演示。

*注意*，截至今天，Chrome 桌面浏览器不支持 Web Share，但它在 Android 浏览器上可以工作。

```
<template>
  <div id="app">
    <div v-if="webShareApiSupported" class="refer-wrapper">
      <p class="refer-text">
        Share your referal code:
        <span class="theCode">{{ referralCode }}</span> with a friend and earn
        when they sign up
      </p>
      <button @click="shareNow">Share</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      referralCode: "Fss4rsc",
    };
  },
  computed: {
    webShareApiSupported() {
      return navigator.share;
    },
  },
  methods: {
    shareNow() {
      navigator.share({
        title: "Refferal Code",
        text: this.referralCode,
      });
    },
  },
};
</script>

```

如果您正在使用 Vue，上面的代码片段显示了 Web Share API 的基本实现。[查看完整演示](https://iu4k3.csb.app)。

### 联系人选取器 API

大多数移动应用程序倾向于请求访问您的联系人或电话簿。这是另一个也可以在网络上使用的移动功能。

假设你正在为一个 fintech web 应用程序实现通话时间充值功能。您可能希望用户选择一个或多个联系人。这可以使用`navigator.contacts`来实现。它接受两个参数:`properties`，一个包含要访问的属性的数组，和`options`:

```
const props = ['name', 'tel',];
const opts = { multiple: true };

async function getContacts() {
  try {
      const contacts = await navigator.contacts.select(props, opts);
      handleResults(contacts);
  } catch (ex) {
      // Handle any errors here.
  }
}

```

如果您正在使用 React，您可以像这样实现联系人选取器功能:

```
export default function Contact({ label }) {
  const properties = ["name", "tel"];
  const options = { multiple: true };

  const handleGetContacts = () => {
    try {
      const contacts = navigator.contacts.select(properties, options);
      return contacts;
    } catch (ex) {
      console.log(ex);
    }
  };
  return (
    <>
      <button onClick={handleGetContacts}>
        <span>{label}</span>
      </button>
    </>
  );
}

```

你可以在 CodeSandbox 上查看 React 联系人选择器演示。

和 Vue 一起工作？你没有被排除在外。这就是您使用 Vue 实现该功能的方式:

```
<template>
  <div id="app">
    <div v-if="contactApiSupported">
      <div class="contact-wrapper">
        <h4>Select Contacts</h4>
        <button @click="pickContact">Select Contact</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  computed: {
    contactApiSupported() {
      return "contacts" in navigator && "ContactsManager" in window;
    },
  },
  methods: {
    pickContact() {
      const properties = ["name", "tel"];
      const options = { multiple: true };
      try {
        const contacts = navigator.contacts.select(properties, options);
        return contacts;
      } catch (ex) {
        console.log(ex);
      }
    },
  },
};
</script>

```

您可以[在代码沙箱](https://nyjuh.csb.app)上查看 Vue 的联系人挑选器演示。

*注意，*这个 API 只能在移动浏览器上使用。

### 剪贴板 API

复制、剪切和粘贴等剪贴板操作是移动应用程序中最常见的功能。剪贴板 API 使 web 用户能够访问系统剪贴板并执行基本的剪贴板操作。

以前，您可以使用 DOM `document.execCommand`与系统剪贴板交互；一些图书馆仍然使用这种方法。然而，现代的异步剪贴板 API 提供了直接读写剪贴板内容的访问。

让我们看看它是如何与 JavaScript 一起工作的。

从剪贴板中读取:

```
navigator.clipboard.readText().then(clipText =>
  document.getElementById("outbox").innerText = clipText);

```

写入剪贴板:

```
function updateClipboard(newClip) {
  navigator.clipboard.writeText(newClip).then(function() {
    /* clipboard successfully set */
  }, function() {
    /* clipboard write failed */
  });
}

```

如果你想用 React 实现剪贴板 API，看看这篇文章。

对于 Vue 开发人员来说，您可以使用 API 实现复制文本，如下所示:

```
<template>
  <div id="app">
    <p>Copy this:</p>
    <input v-model="code" />
    <button v-if="supportCBApi" @click="copyMessage">Copy</button>
    <div v-if="message">{{ message }}</div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      message: "",
      code: "FC Barcelona for ever",
      supportCBApi: false,
    };
  },
  created() {
    if (navigator.clipboard) {
      this.supportCBApi = true;
    }
  },
  methods: {
    copyMessage() {
      navigator.clipboard
        .writeText(this.code)
        .then(() => {
          console.log("Text is on the clipboard.");
          this.message = "Code copied to clipboard.";
        })
        .catch((err) => console.error(err));
    },
  },
};
</script>

```

### 网络语音 API

如今，大多数移动应用程序都集成了语音识别和文本到语音转换功能，以提高可访问性和用户体验。Web 语音 API 为浏览器带来了这些功能。在本文中，我们将只讨论`SpeechRecognition`接口。

使用`SpeechRecognition`界面可以访问语音识别，它使用设备的默认语音识别系统:

```
const SpeechRecognition = SpeechRecognition || webkitSpeechRecognition; 
const recognition = new SpeechRecognition(); //new SpeechRecognition object
recognition.continuous = false; 
recognition.lang = 'en-US';
recognition.interimResults = false; 

recognition.onstart = function() {
    console.log("Speak into the microphone");
};

recognition.onspeechend = function() {
    // when user is done speaking
    recognition.stop();
}

// This runs when the speech recognition service returns result
recognition.onresult = function(event) {
    var transcript = event.results[0][0].transcript;
    var confidence = event.results[0][0].confidence;
};

// start recognition
recognition.start();

```

来源: [MDN 语音识别](https://developer.mozilla.org/en-US/docs/Web/API/SpeechRecognition)
我们来过一遍上面的代码片段。

首先，我们通过分配`new SpeechRecognition`来创建一个语音识别对象。`SpeechRecognition`对象有一些属性，例如:

*   `recognition.continuous`:当语音识别开始时，收听单个结果(单词或短语)。如果设置为`true`,`speechRecognition`服务会一直监听，除非你停止它
*   `recognition.lang`:用户的语言偏好
*   `recognition.interimResults`:设置为`true`时，返回中期结果和最终结果

此外，为了让我们的语音识别服务工作，我们需要为`onstart`、`onspeechend`和`onresult`等事件提供回调。

*   `recognition.onstart`:当用户触发该事件时，语音识别服务启动
*   `recognition.onspeechend`:这将停止语音识别服务的运行
*   `recognition.onresult`:一旦收到成功的结果，就触发该事件

如果你想在 React 中实现这一点，请阅读本教程，它向你展示了如何使用 React 语音识别钩子来进行语音辅助。

### 通知 API

Web 通知 API 通常与 Web 推送 API 互换，但它们是不同的。通知 API 的目标是向用户显示信息，而推送 API 允许服务人员处理来自服务器的推送消息，即使设备处于非活动状态。

现在，博客和网络应用程序广泛使用这种方式来通知用户服务的变化或更新。此 API 的一个常见用例是当您的应用程序是 PWA(渐进式 web 应用程序)时，您需要用户刷新浏览器以获得应用程序的新更新。

为了创建通知，JavaScript 有一个`Notification`构造函数:

```
const message = 'Refresh to get new features';
var notification = new Notification('Savings PWA app', { body: text });

```

您可以用您想要的 web 框架来实现这个 API。

## Web APIs 在未来应该会得到广泛的支持

到目前为止，我们已经讨论了为 web 带来本地移动感觉的 API。所有这些 API 的一个相似之处是它们得到了流行的现代浏览器的广泛支持。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在这一节中，我将重点介绍三种 API，它们在未来应该会得到浏览器的广泛支持。

### 屏幕唤醒锁定 API

大多数应用程序需要访问设备的电源状态。如果你已经注意到，如果你的屏幕被锁定，像 YouTube 这样的移动应用程序会暂停；Spotify 等其他一些应用即使锁屏也会继续播放。

在 web 上，屏幕唤醒锁定 API 允许开发人员在 web 应用程序运行时控制设备的电源状态。然而，Firefox、Safari 和 Opera Mini 浏览器尚不支持它。

![Screen Wake Lock API](img/907059d9464cb1dbcf42b1cfb90f3132.png)

### WebXR 设备 API

由于像口袋妖怪 Go 和谷歌翻译这样的游戏，混合现实现在变得越来越受欢迎。随着支持 XR 的设备范围不断增加，WebXR 设备 API 使开发人员能够为 web 构建令人敬畏的混合现实应用程序。

Android、Opera、Safari、Firefox 等浏览器仍然不支持这个 API。

![WebXR Device API](img/513232296801ae60ffb37aa8267900ef.png)

### Web 发票 API

在移动设备上，NFC 帮助用户进行安全交易，并与一定半径内的其他设备进行连接。

在网络上，当网站离用户很近时，网络 NFC 将允许网站读写 NFC 标签。目前为止，只有 Android 版的 Chrome 支持它。

![Web NFC API](img/e7ed5e675e3108886c32a9400a3b59ce.png)

## 结论

在本文中，我们讨论了将移动功能添加到项目中的 Web APIs，以及将来应该会得到广泛支持的一些其他 API。

为 web 构建意味着为能够访问 web 浏览器的所有类型的用户和设备构建。这就是为什么模仿移动功能的 API 成为 web 开发人员的必需品。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)