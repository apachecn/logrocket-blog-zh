# 如何用普通 JavaScript 构建文件上传服务

> 原文：<https://blog.logrocket.com/how-to-build-file-upload-service-vanilla-javascript/>

第三方包、编译器和捆绑器是幕后的魔法。由于缺乏时间和巨大的竞争，我们没有足够的担心底层的东西，以了解在这些第三方包的幕后到底发生了什么。

在本文中，我们将从头开始用普通的 JavaScript 构建一个文件上传服务。我们的目标是在没有外部库的情况下构建它，以理解 JavaScript 的一些核心概念。我们将在前端读取用户上传的文件，并将其分块传输到后端，存储在那里。

让我们快速浏览一下我们将要制作的产品:

![JS Multipart Upload](img/e9945fda8b2d889c50b1499f23f10268.png)

让我们开始吃吧。

## 目录

## 设置 Node.js 服务器

我们将利用漂亮的内置 HTTP 包来设置后端服务器。

首先，我们需要为项目创建一个新文件夹。

```
mkdir fileupload-service

```

这样做之后，我们需要创建一个`index.js`文件，作为我们后端服务器的入口点。

```
touch index.js

```

之后，创建 HTTP 服务器。

```
const http = require('http');      // import http module
const server = http.createServer();     // create server

server.listen(8080, () => {
    console.log('Server running on port 8080') // listening on the port
})

```

上面的代码非常简单明了。我们已经创建了一个 HTTP 服务器，运行在端口 8080 上。

## 设置前端

下一步是设置前端。由于我们没有做任何花哨的事情，我们将创建一个带有文件输入和上传按钮的基本 HTML 文件，单击该按钮将启动上传过程。将会有一个微小的状态文本来声明文件上传的状态。

在 vanilla JS 中，要在任何按钮点击上添加动作，我们可以简单地附加一个事件监听器。

```
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>File Uploader</title>
</head>
<body>
    <h2>File Upload Service</h2>
    <input type="file" id="file">
    <button id="upload">Upload</button>
        <small id="status"></small>

    <script>
        const file = document.getElementById('file');
        const upload = document.getElementById('upload');
                const status = document.getElementById('status');
        upload.addEventListener('click', () => {
                console.log('clicked the upload button!');
        })
    </script>
</body>
</html>

```

用户可以选择文件并通过点击上传按钮来上传。很简单！

为了在调用 home route 时提供这个 HTML 文件，我们需要从后端发送这个文件。最简单的方法如下。

```
server.on('request', (req, res) => {

    if(req.url === '/' && req.method === 'GET') {
        return res.end(fs.readFileSync(__dirname + '/index.html'))
    }
})

```

> **注意:**,`server.on('request')`方法用于监听节点后端服务器中的所有 HTTP 请求。

## 读取前端的文件内容

当我们的后端服务器启动并运行时，我们需要一种在前端读取文件的方法。为此，我们将使用`FileReader`对象。它让 web 应用程序异步读取存储在用户计算机上的文件(或原始数据缓冲区)的内容，使用`[File](https://developer.mozilla.org/en-US/docs/Web/API/File)`或`[Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob)`对象来指定要读取的文件或数据。

使用`FileReader`对象在客户端读取文件的语法如下。

```
const fileReader = new FileReader(); // initialize the object  
fileReader.readAsArrayBuffer(file); // read file as array buffer

```

我们可以在输入的`files`字段下访问选择的输入文件。目前，我们只为单个文件上传构建它，但是以后，我们也可以为多个文件上传扩展它。

```
const selectFile = file.files[0]; 

```

为了读取一个文件，`FileReader`提供了一些方法。

1.  `FileReader.readAsArrayBuffer()` —读取文件作为数组缓冲区
2.  `FileReader.readAsBinaryString()` —以原始二进制数据读取文件

3.  `FileReader.readAsDataURL()` —读取文件并将结果作为数据 url 返回

4.  如果我们知道文件的类型是文本，这个方法很有用

对于我们的用例，我们将使用`readAsArrayBuffer`方法以字节为单位读取文件，并通过网络将其传输到后端。

为了在客户端跟踪读取文件，`FileReader`提供了几个事件监听器，如`onload`、`onprogress`等。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们的目标是读取文件，将它分成块，并上传到后端，所以我们将使用`onload`事件，该事件在文件读取完成后触发。

你可能想知道，为什么我们不使用`onprogress`方法来制作一个完全流式文件上传的应用程序？但是`onprogress`方法的问题是它不告诉新的读取块，它告诉直到现在的完整数据读取。所以，我们使用了`onload`方法。

一旦文件被完全读取，我们就将它分成小块，并将其传输到后端。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>File Uploader</title>
</head>

<body>
    <h2>File Upload Service</h2>

    <input type="file" id="file">
    <button id="upload">Upload</button>
        <small id="status"></small>

    <script>

        const file = document.getElementById('file');
        const upload = document.getElementById('upload');
                const status = document.getElementById(status);

        upload.addEventListener('click', () => {
                        // set status to uploading
                        status.innerHTML = ‘uploading…’;

            const fileReader = new FileReader();
            fileReader.readAsArrayBuffer(file.files[0]);

            fileReader.onload = (event) => {
                console.log('Complete File read successfully!')
            }
        });
    </script>
</body>
</html>

```

你可能已经注意到我们正在使用一个`<small>`标签，当我们开始上传时它会变成`uploading...`，一旦文件成功上传到后台，它就会变成`uploaded!!!`！

## 将文件分割成块并传输到后端

有时，文件可能很大，因此一次发送完整的文件并不是一个好的做法。一些代理服务器如 Nginx 可能会阻止它，因为它似乎是恶意的。

因此，我们将把这个文件分割成大约 5000 字节的块，然后一个接一个地发送到后端。

如果我们仔细观察`event`参数，我们会发现，一旦它读取了文件，我们就可以在`event.target.result`字段中访问作为数组缓冲区的文件内容。

我们将把这个文件的数组缓冲区分成 5000 字节的块。

```
// file content 
const content = event.target.result;
// fix chunk size
const CHUNK_SIZE = 5000;
// total chunks
const totalChunks = event.target.result.byteLength / CHUNK_SIZE;

// loop over each chunk
for (let chunk = 0; chunk < totalChunks + 1; chunk++) {
    // prepare the chunk
    let CHUNK = content.slice(chunk * CHUNK_SIZE, (chunk + 1) * CHUNK_SIZE)

        // todo - send it to the backend
}

```

现在，我们需要将这些块发送到后端。为了攻击后端服务器，我的老朋友`fetch`来帮忙了。

在我们将块发送到后端之前，我们需要确保我们按顺序进行，否则文件将会损坏。

第二件事是在上传时使用`async await`,因为我们不想后端服务器被请求淹没。

```
fileReader.onload = async (event) => {

    const content = event.target.result;
    const CHUNK_SIZE = 1000;
    const totalChunks = event.target.result.byteLength / CHUNK_SIZE;

    // generate a file name
    const fileName = Math.random().toString(36).slice(-6) + file.files[0].name;

    for (let chunk = 0; chunk < totalChunks + 1; chunk++) {
        let CHUNK = content.slice(chunk * CHUNK_SIZE, (chunk + 1) * CHUNK_SIZE)

        await fetch('/upload?fileName=' + fileName, {
                'method' : 'POST',
                'headers' : {
                    'content-type' : "application/octet-stream",
                    'content-length' : CHUNK.length,
                },
                'body': CHUNK
        })
    }
        status.innerHTML = ‘uploaded!!!’;
}

```

如您所见，我们已经添加了文件名作为查询参数，您可能想知道为什么我们也要发送文件名。看，所有对后端服务器的 API 调用都是无状态的，所以要将内容附加到一个文件中，我们需要有一个惟一的标识符，这就是我们例子中的文件名。

因为用户可能希望上传具有相同文件名的文件，以确保后端工作正常，所以我们需要一个惟一的标识符。为此，我们使用这个漂亮的一行程序:

```
Math.random().toString(36).slice(-6)
```

理想情况下，我们不应该发送任何自定义头，因为大多数代理如 Nginx 或 HAProxy 可能会阻止它。

## 接收块并将它们存储在服务器上

因为我们已经完全设置好了前端，所以下一步是监听文件块并将它们写入服务器。

为了从请求的查询参数中提取文件名，我们使用下面这段代码。

```
const query = new URLSearchParams(req.url);
const fileName = query.get(‘/upload?fileName’);

```

因此，我们的最终代码看起来像这样:

```
server.on('request', (req, res) => {

if(req.url === '/' && req.method == 'GET') {
    return res.end(fs.readFileSync(__dirname + '/index.html'))
}

if(req.url=== '/upload' && req.method == 'POST') {
    const query = new URLSearchParams(req.url);
        const fileName = query.get(‘/upload?fileName’);

    req.on('data', chunk => {
        fs.appendFileSync(fileName, chunk); // append to a file on the disk
    })

    return res.end('Yay! File is uploaded.')
}
})
```

## 上传多个文件

到目前为止，我们已经用 vanilla JS 构建了一个漂亮的单个文件上传应用程序。现在，我们的下一个目标是扩展我们当前的实现，以支持多文件上传。

让我们开始吧。

如果我们仔细观察，我们会发现后端足够智能，可以顺利地处理多个文件上传，因为它有一个非常简单的工作:获取一个块，并将其附加到请求中收到的相应文件名。它完全独立于从前端上传了多少文件。

所以，让我们利用它，提高我们对它的应用。

在 UI 上接受多个文件选择的第一步是修改文件输入。目前，它默认接受单个文件输入。要接受多个文件，我们在输入中使用`multiple`选项:

```
<input type="file" id="files" multiple>

```

现在，我们已经设置好在文件输入中接受多个文件。如果你错过了，我们也已经更新了从`file`到`files`的文件输入的`id`。

我们知道所有的输入文件现在都可以通过`files.files`数组访问。因此，我们的想法非常简单:我们将遍历所选文件的数组，将它一个接一个地分成块，并将其传输到后端服务器并存储在那里:

```
for(let fileIndex=0;fileIndex<files.files.length;fileIndex++) {
    const file = files.files[fileIndex];

    // divide the file into chunks and upload it to the backend
}

```

我们的好朋友`for`循环使得检查每个文件并上传到后端变得非常简单。

为了跟踪文件上传状态，我们维护一个变量，该变量在每次文件上传时更新。

因此，我们的文件上传脚本如下所示:

```
        const files = document.getElementById('files');
        const upload = document.getElementById('upload');
        const status = document.getElementById('status');

        upload.addEventListener('click', () => {

            // set loading status
            status.innerHTML = 'uploading...';
            let fileUploaded = 0;

            for(let fileIndex = 0; fileIndex < files.files.length; fileIndex++) {
                const file = files.files[fileIndex];

                const fileReader = new FileReader();

                fileReader.readAsArrayBuffer(file);

                fileReader.onload = async (event) => {
                    const content = event.target.result;
                    const CHUNK_SIZE = 1000;
                    const totalChunks = event.target.result.byteLength / CHUNK_SIZE;

                    const fileName = Math.random().toString(36).slice(-6) + file.name;

                    for (let chunk = 0; chunk < totalChunks + 1; chunk++) {
                        let CHUNK = content.slice(chunk * CHUNK_SIZE, (chunk + 1) * CHUNK_SIZE)

                        await fetch('/upload?fileName=' + fileName, {
                            'method' : 'POST',
                            'headers' : {
                                'content-type' : "application/octet-stream",
                                'content-length' : CHUNK.length
                            },
                            'body' : CHUNK
                        })
                    }           
                    fileUploaded += 1;        

                    status.innerHTML = `file ${fileUploaded} of ${files.files.length} uploaded!!!`;
                }
            }

        })

```

我不确定您是否是通过查看我们的实现想到这一点的，但是我们也实现了多个文件并行上传。如果您仔细查看“网络”选项卡，您会发现文件块是以并行方式上传的，但没错，文件本身是以串行方式上传的。

![JS Multipart Upload with Parallel Files](img/5800dd9c6fe950e5ae87522aeb1c19b6.png)

因为我们没有等待前一个文件完全上传，所以所有的文件都是并行上传的。因为我们的后端是无状态的，所以这个功能非常好。

如果你热衷于探索代码库的 GitHub 库，[你可以在这里找到它。](https://github.com/Pankajtanwarbanna/vanilla-js-file-upload)

## 结论

我们学习了如何用 vanilla JS 构建文件上传服务。显然，这不是最有效的实现，但是它足以让您对一些核心概念有一个大致的了解。

我们可以扩展它，在上传时有一个进度条，在失败时重试块上传，上传多个文件，一次上传多个块，等等。

我是推特上活跃的第二号人物，很想听听你的想法。如果你对我的其他文章感兴趣，[你可以在这里找到](https://www.pankajtanwar.in/blogs)。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.