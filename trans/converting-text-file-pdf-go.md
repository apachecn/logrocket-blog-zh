# 在 Go - LogRocket 博客中将文本文件转换为 PDF

> 原文：<https://blog.logrocket.com/converting-text-file-pdf-go/>

Golang 是当今发展最快的语言之一。毫无疑问，它的流行带来了许多由 Go 社区开发的软件包，今天我们来看看其中的一个。

在这篇文章中，我们将看看如何使用 Golang 将文本文件转换成 PDF 格式。激动吗？我也是！让我们开始吧。

## 看一看 gofpdf 包

gofpdf 包是一个文档生成器，对文本、绘图和图像提供高级支持。它是由 Jung Kurt 创建的，你可以在这里找到 GitHub 库。存储库目前是归档的和只读的，但是这个包对于我们的用例来说足够稳定。

这个软件包有很多特性，比如页面压缩；剪裁；条形码；包括 JPEG，PNG，GIF，TIFF 和基本的路径唯一的 SVG 图像；文件保护；图表等。除了 Golang 标准库，这个包没有其他依赖项。

## 安装依赖项

我们将安装两个包，在本例中是用于将文本输出到控制台的`fmt`库和用于将文本文件转换为 PDF 格式的`github.com/jung-kurt/gofpdf`包。

创建一个名为`main.go`的文件，并粘贴以下代码:

```
package main

import (
   "fmt"

   "github.com/jung-kurt/gofpdf"
)

```

现在，在您的终端中，导航到包含您的`main.go`文件的目录，并运行命令`go mod init go-pdf`来初始化当前目录中的 Go 模块。

接下来，运行`go mod tidy`下载所有需要的包。

[![Run Go Mod Tidy To Download Required Packages](img/189b0340b53286f4e711206335e63d0e.png)](https://blog.logrocket.com/?attachment_id=103598)

## 创建我们的第一个 PDF 文件

为了创建我们的第一个 PDF，我们将使用以下代码更新`main.go`文件:

```
...
func main() {
   pdf := gofpdf.New("P", "mm", "A4", "")
   pdf.AddPage()
   pdf.SetFont("Arial", "B", 16)
   pdf.MoveTo(0, 10)
   pdf.Cell(1, 1, "Hello world")
   err := pdf.OutputFileAndClose("hello.pdf")
   if err == nil {
       fmt.Println("PDF generated successfully")
   }
}

```

这里，我们用`gofpdf.New`创建一个新的 PDF 对象，将方向设置为纵向，将单位系统设置为毫米，将纸张大小设置为 A4，并将字体目录的选项保留为空字符串。然后，我们将字体设置为 Arial，权重为 bold，字体大小设置为 16。

我们继续创建一个 40 毫米宽、10 毫米高的单元格，并在其中写入文本“Hello world”。然后，我们将它保存到一个名为`"hello.pdf"`的文件中，并写出一条消息，说明 PDF 已成功创建。

在这之后，运行命令`go run main.go`来运行代码，您应该看到在您的当前目录中生成了一个 PDF 文件。

[![Run The Command Go Run Main](img/09e140c5dc295f3111e517a4d3def60b.png)](https://blog.logrocket.com/?attachment_id=103600)

[![PDF File Generated](img/ee11408958ea83895de002889c676e50.png)](https://blog.logrocket.com/?attachment_id=103602)

## 将文本文件转换为 PDF

要将文本文件转换为 PDF，首先我们使用 io/ioutil 库读取文件，并将输出写入 PDF 文件。用以下代码替换您的`main.go`文件中的代码:

```
package main

import (
   "fmt"
   "io/ioutil"

   "github.com/jung-kurt/gofpdf"
)

func main() {
   text, err := ioutil.ReadFile("lorem.txt")
   pdf := gofpdf.New("P", "mm", "A4", "")
   pdf.AddPage()
   pdf.SetFont("Arial", "B", 16)
   pdf.MoveTo(0, 10)
   pdf.Cell(1, 1, "Lorem Ipsum")
   pdf.MoveTo(0, 20)
   pdf.SetFont("Arial", "", 14)
   width, _ := pdf.GetPageSize()
   pdf.MultiCell(width, 10, string(text), "", "", false)
   err = pdf.OutputFileAndClose("hello.pdf")
   if err == nil {
       fmt.Println("PDF generated successfully")
   }
}

```

首先，我们从`lorem.txt`文件中读取文本，然后像以前一样编写文档的标题，只是我们将文本改为`Lorem Ipsum`。我们将光标移动到行首，然后，我们将字体大小设置为 14，字体粗细设置为 normal，并创建一个跨越页面整个宽度的`pdf.Multicell`。

在这里，我们将文本内容写入单元格并保存到一个名为`hello.pdf`的文件中。

创建一个名为`lorem.txt`的文件，并粘贴以下文本:

```
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Donec varius leo quis augue finibus gravida sit amet vitae ligula. Ut sit amet scelerisque eros. Vivamus molestie purus nec orci porta commodo vel ut erat. Sed tincidunt est vitae ligula sodales, pellentesque aliquet libero ullamcorper. Pellentesque pretium lacinia aliquet. Sed at enim ut nunc aliquet fringilla. Morbi rutrum sed velit non volutpat.
Vivamus in venenatis eros. Phasellus purus mauris, mollis in condimentum sed, convallis dapibus neque. Etiam pharetra ut ex eu blandit. Morbi mattis consectetur posuere. Suspendisse tincidunt nunc vitae nibh condimentum lacinia. Suspendisse pulvinar velit a lectus tristique, sed congue nisi mattis. Proin a aliquet mauris, sed rutrum lorem. Morbi nec tellus ac mi ornare blandit eu sit amet velit.

```

现在，保存并运行命令`go run main.go`，应该会在当前目录中生成一个`hello.pdf`文件。

[![Hello.pdf File Generated](img/5dbbce45e00cd93850b0117aff4097f8.png)](https://blog.logrocket.com/?attachment_id=103605)

## 结论

在本文中，我们研究了 gofpdf 库及其用于生成文档的一些特性。我们还看到了使用 gofpdf 库将文本文件转换成 PDF 格式是多么容易。

如果您对高级页面操作感兴趣，Unipdf 是一个 gofpdf 的替代方案。它允许您创建和读取 PDF 文件，压缩和优化 PDF 文件，水印 PDF 文件，处理 PDF 文件，等等。

如果您有兴趣探索关于这个包及其特性的更多信息，请查看 gofpdf [GitHub](https://github.com/jung-kurt/gofpdf) 存储库。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)