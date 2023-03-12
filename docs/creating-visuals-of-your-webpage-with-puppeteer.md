# 使用木偶师博客创建网页的视觉效果

> 原文：<https://blog.logrocket.com/creating-visuals-of-your-webpage-with-puppeteer/>

Puppeteer 是由 Google 构建的 Node.js 模块，用于在节点环境中模拟 Chrome 浏览器或 Chrome。

> 来自 Puppeteer API 文档:Puppeteer 是一个节点库，它提供了一个高级 API 来控制 Chrome 或 DevTools 协议上的 Chrome。

因此，基本上，Puppeteer 是一个在 Node.js 上运行的浏览器。它包含模拟浏览器的 API。这些 API 使您能够执行不同的操作，例如:

*   从网页生成 PDF
*   从网页生成屏幕截图
*   测试 Chrome 扩展
*   [这么多更](https://github.com/puppeteer/puppeteer)

在这篇文章中，我们将学习如何使用木偶师从一个网站的网址生成截图。

## 创建视觉

使用木偶节点模块创建网页的视觉效果非常容易。

首先，我们安装木偶节点模块:

```
npm i puppeteer
```

然后，我们将创建我们的。js 文件并需要“木偶师”库。

```
const puppeteer = require("puppeteer")
```

现在，创建一个浏览器上下文和一个新页面:

```
const puppeteer = require("puppeteer")
const browser = await puppeteer.launch({ headless: true })
const page = await browser.newPage()
```

注意:木偶库是基于承诺的。这意味着它的 API 主要返回 promise 对象。

通过`_const browser = await puppeteer.launch({ headless: true })_`，我们在木偶师类实例 Puppeteer 中使用`launch` API 创建了一个新的浏览器实例。这实际上启动了一个 Chromium 实例。浏览器是浏览器类的一个实例。

设置`{ headless: true }`意味着浏览器将是 Chromium 的一个无头实例。

注意，启动返回一个承诺，它解析为一个浏览器实例。在这里，那将是`browser`。

```
_const page = await browser.newPage()_
```

浏览器可以容纳如此多的页面。因此，`Browser`中的这个`newPage()`方法在默认浏览器上下文中创建了一个新页面。页面是页面类的对象。

现在，使用`page`对象，我们将加载或导航到我们想要截屏的网页:

```
await page.goto('https://medium.com')>/pre>
```

在这里，我们正在加载中型主页。当浏览器触发`load`事件时，方法`goto`将解析，表示页面已经成功加载。现在，随着页面`medium.com`的加载，我们可以截图了。

```
const screenShot = await page.screenshot({
    path: "./",
    type: "png",
    fullPage: true
})
```

page 对象的截图方法完成了所有的工作。它获取当前页面的截图。

截图方法采用了一些配置:

`_path_`:表示我们想要保存图像的文件路径。在这里，我们将保存在当前的工作目录。如果没有路径，图像将不会保存到磁盘。

`_type_`:表示使用 png 或 jpeg 的图像编码类型。

`_fullPage_`:这将使屏幕截图完全滚动到页面大小。

还有其他设置，包括:

`_quality_`:图像的质量，在 0-100 之间。不适用于 png 图像。

`_omitBackground_`:隐藏默认白色背景，允许捕捉透明截图。

`_encoding_`:图像的编码可以是 base64，也可以是二进制。默认为二进制。

截图方法返回一个承诺，它将根据设置中的`encoding`属性值解析为 buffer 或 base64。因此，在我们自己的例子中，屏幕截图方法将返回一个承诺，该承诺将解析为二进制。截图变量将保存介质 frontpage 的二进制图像。

至此，我们的代码完成了:

```
// screenshot.js
const puppeteer = require("puppeteer")

( async function() {

    const browser = await puppeteer.launch({ headless: true })

    const page = await browser.newPage()

    await page.goto('https://medium.com')

    const screenShot = await page.screenshot({
        path: "./",
        type: "png",
        fullPage: true
    })
}()
)
```

我们可以在 Node.js 环境中运行该文件:

```
node screenshot.js
```

这将生成一个媒体截图，并保存在我们当前的目录。

您可以将`[https://medium.com](https://medium.com)`替换为您想要捕捉其屏幕截图的网页。

## 设置视口大小

如果在使用 puppeter 截图时没有指定`fullPage`选项，puppeter 将模拟一个默认大小设置为 800×600 的浏览器窗口。

我们可以使用 page 类中的`setViewport` API 来改变屏幕截图的大小。

```
await page.setViewport({
    width: 1200,
    height: 1500
})
```

这将生成一个宽度为 1200px，高度为 1500px 的网页截图。

## 设置屏幕截图的特定区域

我们可以剪切页面的一个区域，并对其进行截图。也就是说，我们可以对网页上的特定区域进行截图。

这是通过将一个`clip`对象传递给`page.screenshot(...)`方法来实现的。

```
const screenShot = await page.screenshot({
        path: "./",
        type: "png",
        clip: {
            ...            
        }
    })
```

`clip`对象有以下字段:

*   `_x_`:剪辑区网页 x 轴的左上方
*   `_y_`:剪辑区网页 y 轴的左上方
*   `_width_`:剪裁区域的宽度
*   `_height_`:裁剪区域的高度

```
const screenShot = await page.screenshot({
        path: "./",
        type: "png",
        clip: {
            x: 0,
            y: 0,
            width: 360,
            height: 400
        }
    })
```

上面的代码将从 0，0 => x，y 轴开始，向右移动 300 像素，向下移动 400 像素，截取网页的截图。

```
const screenShot = await page.screenshot({
        path: "./",
        type: "png",
        clip: {
            x: 50,
            y: 60,
            width: 360,
            height: 400
        }
    })
```

这将对页面上的 50，60 => x，y 轴进行三角剖分，并向右移动 360px，向下移动 400px，然后对裁剪区域进行截图。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 背景

我们可以通过传递`omitBackground`布尔选项来省略截图中木偶师给我们的默认白色背景。

```
const screenShot = await page.screenshot({
        ...,
        omitBackground: true
    })
```

这将采取透明背景的网页截图。

## 等待页面加载

对于我们当前的加载，当加载事件被触发时，页面被认为是完全加载的。当页面成功加载时，将触发 load 事件。

除了 load 事件之外，Puppeteer 还为我们提供了更多选项来指示导航何时完成。

*   `_domcontentloaded_`:这将告诉木偶师在`DOMContentLoaded`事件被触发时触发页面加载完成。
*   这将确保当至少 500 毫秒内没有超过 0 个网络连接时，木偶师告诉我们页面加载完成。

*   这将确保当至少 500 毫秒内没有超过 2 个网络连接时，Puppeteer 告诉我们页面加载完成。

默认的页面加载事件是加载。我们可以在`page.goto(...)`调用中设置以上选项。

为了设置`domcontentload`页面加载事件，我们这样做:

```
await .goto( "https://medium.com",{
    waitUntil: 'domcontentloaded'
});
```

页面加载事件在传递给`page.goto(...)`的选项的`waitUntil`字段中设置。这里，当`DOMContentLoaded`事件被触发时，页面加载将被视为完成。

```
await .goto( "https://medium.com",{
    waitUnitl: 'networkidle0'
});
```

这里，当至少 500 毫秒没有网络连接时，页面加载将被视为完成。

```
await .goto( "https://medium.com",{
    waitUnitl: 'networkidle2'
});
```

这里，当至少 500 毫秒内没有超过 2 个网络连接时，页面加载将被视为完成。

## 横向模式

为了在横向模式下截取网页的截图，我们将把`isLandscape`选项传递给`page.setViewport(...)`调用。

```
page.setViewport({
    ...,
    isLandscape: true
})
```

这将采取横向模式的网页截图。

## 让它成为一种服务

我们可以扩展这个实现，把它变成一个服务，就像在线网页变成图像服务一样。

这项服务将让用户对他们的网页或任何选定的网页进行截图。

我们将在 Node.js 中实现这个服务。

以下是后端代码:

```
/** require dependencies */
const express = require("express")
const cors = require('cors')
const bodyParser = require('body-parser')
const helmet = require('helmet')
const path = require('path')
const puppeteer = require("puppeteer")

const app = express()

let port = process.env.PORT || 5000

/** set up middlewares */
app.use(cors())
app.use(bodyParser.json({limit: '50mb'}))
app.use(helmet())

app.get("/", (request, response) => {
    response.sendFile(path.join(__dirname, 'index.html'));
});

app.get("/style.css", (request, response) => {
    response.sendFile(path.join(__dirname, 'style.css'));
});

app.get('*', function(req, res) {
  res.sendFile(path.resolve(__dirname, 'index.html'));
});

app.use('/static',express.static(path.join(__dirname,'static')))
app.use('/assets',express.static(path.join(__dirname,'assets')))

app.post('/api/screenshot', (req, res, next) => {
    const { url } = req.body

    var screenshot = takeScreenshot(url)
    res.send({result: screenshot })
    next()    
})

async function takeScreenshot(url) {
    const browser = await puppeteer.launch({ 
       headless: true,
       args: ['--no-sandbox'] 
     });
    const page = await browser.newPage();
    await page.goto(url, { waitUntil: 'networkidle0' });
    const screenShot = await page.screenshot({
        path: "./",
        type: "png",
        fullPage: true
    })

    await browser.close();
    return screenshot;
}

/** start server */
app.listen(port, () => {
    console.log(`Server started at port: ${port}`);
});
```

`takeScreenshot`函数是主代码。我们将从请求体中获取想要截图的网页的 URL，并将其传递给`takeScreenshot`函数。创建了一个木偶浏览器实例，我们将导航到 URL。然后对页面进行截屏，并将截屏返回给用户。

让我们看看前端代码:

```
<title>Webpage to Image</title>

<style>
</style>

<body>
    <header>
        <div class="title-bar"><h2>Webpage to Image</h2></div>
    </header>
    <div class="container">
        <div class="info close" id="info">
            <h3>Info</h3>
        </div>
        <div class="wrapper">
            <div class="div-input" style="">
                <input id="webpageUrl" type="text" placeholder="Type your webpage URL" />
            </div>
            <div class="div-button">
                <button id="webpageButton" onclick="return generateImage(event)">
                    Generate Image
                </button>
            </div>                
        </div>
    </div>
</body>
<script src="./axios/axios.min.js"></script>
<script>

    webpageUrl.addEventListener("keydown", (evt) => {
        if(evt.key == "Enter")
            generateImage(evt)
    })

    function generateImage(evt) {
        evt.preventDefault()

        showLoading(true)
        disableButton(true)

        if(webpageUrl.value.length === 0) {
            showLoading(false)
            info.innerHTML = `
                <h3>Error</h3>
                Please, Type in the webpage URL.
            `
            info.classList.add("info-danger")
            info.classList.remove("close")

            setTimeout(() => {
                info.classList.add("close")
                info.classList.remove("info-danger")
            }, 3000)
        } else
            axios.post("api/screenshot", { url: webpageUrl.value } ).then( res => {
                showLoading(false)
                const { result } = res.data
                const blob = new Blob([result], {type: 'image/png'})
                const link = document.createElement('a')
                link.href = window.URL.createObjectURL(blob)
                link.download = `your-file-name.png`
                link.click()
            }).catch(err => {
                showLoading(false)

                // err

                info.innerHTML = `
                    <h3>Error</h3>
                    ${err}
                `
                info.classList.add("info-danger")
                info.classList.remove("close")
                setTimeout(() => {
                    info.classList.add("close")
                    info.classList.remove("info-danger")
                }, 3000)
            })

        disableButton(false)
    }

    function showLoading(show) {
        if(show == true) {
            webpageButton.innerHTML = "wait..."
        } else {
            webpageButton.innerHTML = "Generate Image"
        }
    }

    function disableButton(disable) {
        if(disable == true) {
            webpageButton.setAttribute("disable", true)
        } else {
            webpageButton.removeAttribute("disable")
        }
    }
</script>
...
```

我们有输入和按钮。我们在输入中输入想要截图的网页。点击后，按钮将调用“API/截图”处的后端 API，并随其一起发送输入框中的网页 URL。“API/截图”处的后端代码将生成截图并将其发送回用户。Axios get 调用中的解析函数将运行，我们将以编程方式将生成的图像下载到我们的存储中。

很简单。

除此之外，您还可以添加更多功能:

*   要生成的图像类型
*   批量图像处理(在这里，用户可以添加一组网页，服务将在批量处理中生成它们的图像)
*   屏幕截图大小
*   要截图的网页区域
*   背景透明度
*   等等

您可以根据 Puppeteer APIs 中的不同选项来设置选项。

天空是极限。

## 结论

毫无疑问，木偶师非常棒。

它有如此多的 API，你可以利用它来制作一些很酷的东西。在此找到它们:

> 无头 Chrome Node.js API。在 GitHub 上创建一个帐户，为木偶师/木偶师的发展做出贡献。

如果你对此有任何问题，请随时评论，发电子邮件或给我发短信。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.