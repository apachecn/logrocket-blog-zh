# 用漂亮的 Soup - LogRocket 博客构建一个 Python web scraper

> 原文：<https://blog.logrocket.com/build-python-web-scraper-beautiful-soup/>

如果你在技术领域呆上一段时间，你可能会遇到“网络抓取”和“网页抓取器”这两个术语。但是你知道它们是什么吗，它们是如何工作的，或者如何为你自己建立一个？

如果你对这些问题的答案是否定的，请继续阅读，因为我们将在这篇文章中讨论关于网络抓取的所有内容。您还将有机会使用 Python 和漂亮的汤库来构建一个。

## 什么是网页抓取？

网页抓取是指通过使用被认为是网页抓取器的脚本或程序，经由超文本传输协议(HTTP)以自动方式从网站提取和收获数据。

web scraper 是一种能够访问互联网上的资源并提取所需信息的软件应用程序。通常，web 抓取工具可以构建和组织收集的数据，并将其存储在本地以备将来使用。

一些标准的网页抓取工具包括:

你可能想知道为什么有人会对使用 web scraper 感兴趣。以下是一些常见的使用案例:

*   为营销目的挖掘销售线索
*   监控和比较多家商店的产品价格
*   数据分析和学术研究
*   为训练机器学习模型收集数据
*   分析社交媒体档案
*   信息收集和网络安全
*   获取金融数据(股票、加密货币、汇率等。)

## 网络抓取面临的挑战

当你需要数据时，网络抓取听起来像是一个首选的解决方案，但由于多种原因，它并不总是容易设置的。让我们来看看其中的一些。

### 1.每个网站都有不同的结构

人们使用不同的团队、工具、设计和部门来构建网站，使得一个给定网站的一切都与另一个不同。这意味着，如果你为一个网站创建一个 web scraper，你必须构建一个单独的版本来与另一个网站完全兼容——除非它们共享非常相似的内容，或者你的 web scraper 使用了巧妙的启发式方法。

### 2.网站经常改变他们的设计和结构

刮刀的耐用性是一个重要的问题。你可以有一个今天运行良好的网页抓取器，但它似乎会突然崩溃，因为你从中提取数据的网站更新了它的设计和结构。因此，您还必须频繁地修改您的 scraper 逻辑以保持其运行。

### 3.一些网站实施 bot 预防措施

多年来，人们开始滥用他们的权力与网络爬虫来执行恶意活动。网络开发者通过实施防止他们的数据被抓取的措施来报复这一举动。其中一些措施包括:

*   提交表单时添加验证码
*   使用 Cloudflare 授权访问者
*   验证访问者的用户代理
*   拒绝代理请求
*   [节流网络资源](https://blog.logrocket.com/throttling-data-requests-with-react-hooks/)
*   IP 地址安全列表/阻止列表

### 4.限速技术会干扰刮削

简而言之，[速率限制](https://blog.logrocket.com/rate-limiting-node-js/)是一种通过为系统操作设置使用上限来控制系统处理多少流量的技术。在这种情况下，该操作允许访问者访问网站上的内容。

当你试图从多个网页中抓取大量数据时，速率限制变得很麻烦。

### 5.动态网站更难刮

动态网站使用脚本在网站上生成内容。通常，它从外部数据源获取数据，并用它来预填充页面。

如果您的 web scraper 向网页发出 GET 请求并抓取返回的数据，它将不会按预期运行，因为它没有在网站上运行脚本。这里的解决方案是使用 Selenium 这样的工具来启动浏览器实例并执行所需的脚本。

## 基本概念

在我们深入讨论这个例子之前，让我们确保我们已经正确地设置并理解了一些关于 web 抓取的基本概念。

要跟随并理解本教程，您需要以下内容:

*   HTML 和 Python 的工作知识
*   计算机上安装的 Python 3.6 或更高版本
*   Python 开发环境(例如，文本编辑器、IDE)
*   [美汤≥4.0](https://pypi.org/project/beautifulsoup4/)

首先，安装[美汤](https://pypi.org/project/beautifulsoup4/%20target=)，这是一个 Python 库，为你提供了从 HTML 和 XML 文档中提取数据的简单方法。

在您的终端中，键入以下内容:

```
pip install beautifulsoup4
```

## 使用 Beautiful Soup 解析 HTML 文档

让我们探索一段使用漂亮的 Soup 来解析和导航 HTML 文档的 Python 代码:

```
from bs4 import BeautifulSoup

# define a HTML document
html = "<!DOCTYPE html><html><head><title>This is the title of a website</title></head><body><h1 id='heading-1'>This is the main heading</h1><h2 id='heading-2'>This is a subheading</h2><p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.</p><ul><li class='list-item'>First</li><li class='list-item'>Second</li><li class='list-item'>Third</li></ul></body></html>"

# parse the HTML content with Beautiful Soup
soup = BeautifulSoup(html, "html.parser")

# print the HTML in a beautiful form
print(soup.prettify())
```

我们将漂亮的 Soup 库导入到一个脚本中，并在上面的代码中从我们的 HTML 文档创建了一个`BeautifulSoup`对象。然后，我们使用`prettify()`方法以适当缩进的形式显示 HTML 内容。下面是输出:
![The Beautiful Soup object we created in HTML form](img/e02be43b71db97ab48938607f97d0cb2.png)

## 通过标签名提取 HTML 元素

接下来，让我们提取文档中的一些 HTML 标签。Beautiful Soup 提供了几个方法，可以让你提取元素。

让我们看一个例子:

```
# getting the title element of the HTML
print(soup.title)

# getting the first h1 element in the HTML
print(soup.h1)
```

及其输出:
![Extract the HTML elements by tag names](img/854f8b0f0e13a6cedce6e6370e34eed5.png)

Beautiful Soup 提供了一个`find()`方法，允许您用特定的标准提取元素。让我们看看如何使用它:

```
# getting the first h2 element in the HTML
print(soup.find("h2"))

# getting the first p element in the HTML
print(soup.find("p"))
```

以及输出的样子:
![The output for extracting HTML elements with specific criteria](img/d645220c9b5660ae464223945ade3801.png)

Beautiful Soup 还提供了一个`find_all()`方法来提取每个带有特定标签的元素作为列表，而不是只获取第一个出现的元素。让我们看看它的用法:

```
# getting all the li elements in the HTML
print(soup.find_all("li"))
```

![The output when you extract elements by specific tags as a list](img/c5bd73402aa764ec5e0fb7c5283a6464.png)

## 按 id 提取 HTML 元素

您可能希望提取附加了特定 ID 的 HTML 元素。`find()`方法允许您提供一个 ID 来过滤搜索结果。

让我们看看如何使用它:

```
# getting the h1 element with the heading-1 id
print(soup.find("h1", id="heading-1"))

# getting the h2 element with the heading-2 id
print(soup.find("h2", {"id": "heading-2"}))
```

而下面是输出:
![The output when extracting HTML elements by their IDs](img/2b24de02a48f6d438b0dda0a69490682.png)

## 提取 HTML 元素及其类

Beautiful Soup 还允许您通过向`find()`和`find_all()`方法提供适当的参数来过滤它们的搜索结果，从而提取特定类的 HTML 元素。让我们看看它的用法:

```
# getting the first li element with the list-item class
print(soup.find("li", {"class": "list-item"}))

# getting all the li elements with the list-item class
print(soup.find_all("li", {"class": "list-item"}))
```

![Extracting HTML elements by their class](img/8f1eb941e2f862232b3722b0e71ca3a9.png)

## 访问元素的属性和内容

您可能希望检索提取的元素的属性值和内容。

幸运的是，Beautiful Soup 提供了实现这一点的功能。让我们看一些例子:

```
# define a HTML document
html = "<a id='homepage' class='hyperlink' href='https://google.com'>Google</a>"

# parse the HTML content with Beautiful Soup
soup = BeautifulSoup(html, "html.parser")

# extract the a element in the HTML
element = soup.find("a")

# extract the element id
print("ID:", element["id"])

# extract the element class
print("class:", element["class"])

# extract the element href
print("href:", element["href"])

# extract the text contained in the element
print("text:", element.text)
print("text:", element.get_text())
```

![Access the element's attributes and contents](img/7638613cd29eb70fdedbf539ec597e9c.png)

## 让我们建立一个网络刮刀

既然我们已经用 Python 和 Beautiful Soup 介绍了 web 抓取的基础知识，那么让我们构建一个脚本来抓取并显示来自 [CoinGecko](https://www.coingecko.com/) 的加密货币信息。

### 步骤 1:安装依赖项

您需要为 Python 安装 [Requests](https://pypi.org/project/requests/) 库来扩展脚本的功能，以便非常容易地发送 HTTP/1.1 请求。

在您的终端中，键入以下内容:

```
pip install requests
```

### 步骤 2:提取 CoinGecko HTML 数据

现在，我们将检索 CoinGecko 的 HTML 内容，用漂亮的 Soup 解析和提取所需的信息。创建一个名为`scraper.py`的文件，并将下面的代码保存在其中:

```
import requests

def fetch_coingecko_html():
    # make a request to the target website
    r = requests.get("https://www.coingecko.com")
    if r.status_code == 200:
        # if the request is successful return the HTML content
        return r.text
    else:
        # throw an exception if an error occurred
        raise Exception("an error occurred while fetching coingecko html")
```

### 第三步:研究 CoinGecko 网站的结构

记住:我们强调了每个网站都有不同的结构，所以我们需要在构建 web scraper 之前研究 CoinGecko 是如何构造和构建的。

在你的浏览器中打开`[https://coingecko.com](https://coingecko.com)`，这样我们就可以看到我们正在抓取的网站(下面的截图来自我的火狐浏览器):
![The CoinGecko website in a Firefox browser](img/2c32e85abab8fc7f30f4d2132e01ac42.png)

由于我们要抓取加密货币信息，打开`Web Developer Toolbox`中的`Inspector`标签，从信息表中查看任意加密货币元素的源代码:
![Bitcoin's price according to CoinGecko](img/c586e39abe55a3b48b39ef6a3cdaf503.png)

![The source code of the Bitcoin element](img/568c6005138e9ebe5b90847c240177a7.png)

从上面的源代码中，我们可以注意到我们正在检查的 HTML 标签的以下内容:

*   每个加密货币元素都存储在一个包含在具有`coin-table`类的`div`标签中的`tr`标签中
*   加密货币名称存储在带有`coin-name`类的`td`标签中
*   价格存储在带有`td-price`和`price`类的`td`标签中
*   价格变化存储在带有`td-change1h`、`td-change24h`和`td-change7d`类的`td`标签中
*   交易量和市值存储在带有`td-liquidity_score`和`td-market_cap`类的`td`标签中

## 第四步:用美汤提取数据

既然我们已经研究了 CoinGecko 网站的结构，那我们就用美汤来提取我们需要的数据吧。

向`scraper.py`文件添加新功能:

```
from bs4 import BeautifulSoup

def extract_crypto_info(html):
    # parse the HTML content with Beautiful Soup
    soup = BeautifulSoup(html, "html.parser")

    # find all the cryptocurrency elements
    coin_table = soup.find("div", {"class": "coin-table"})
    crypto_elements = coin_table.find_all("tr")[1:]

    # iterate through our cryptocurrency elements
    cryptos = []
    for crypto in crypto_elements:
        # extract the information needed using our observations
        cryptos.append({
            "name": crypto.find("td", {"class": "coin-name"})["data-sort"],
            "price": crypto.find("td", {"class": "td-price"}).text.strip(),
            "change_1h": crypto.find("td", {"class": "td-change1h"}).text.strip(),
            "change_24h": crypto.find("td", {"class": "td-change24h"}).text.strip(),
            "change_7d": crypto.find("td", {"class": "td-change7d"}).text.strip(),
            "volume": crypto.find("td", {"class": "td-liquidity_score"}).text.strip(),
            "market_cap": crypto.find("td", {"class": "td-market_cap"}).text.strip()
        })

    return cryptos
```

这里，我们创建了一个`extract_crypto_info()`函数，从 CoinGecko 的 HTML 内容中提取所有加密货币信息。我们使用 Beautiful Soup 中的`find()`、`find_all()`和`.text`方法来导航 CoinGecko 的数据并提取我们需要的内容。

## 第 5 步:显示提取的数据

让我们使用上面创建的函数来完成我们的刮刀，并在终端中显示加密货币信息。将以下代码添加到`scraper.py`文件中:

```
# fetch CoinGecko's HTML content
html = fetch_coingecko_html()

# extract our data from the HTML document
cryptos = extract_crypto_info(html)

# display the scraper results
for crypto in cryptos:
    print(crypto, "\n")
```

一旦你运行它，你会看到下面的内容:
![The display of the extracted data](img/36fd65fe2ed329579a6640e9d4586622.png)

您还可以决定将结果保存在本地的 JSON 文件中:

```
import json

# save the results locally in JSON
with open("coingecko.json", "w") as f:
    f.write(json.dumps(cryptos, indent=2))
```

![The same extracted data displayed in JSON format](img/bae506a74f6a4a5b52d9814ca484711d.png)

## 结论

在本文中，您了解了网页抓取和网页抓取器，它们的用途，与网页抓取相关的挑战，以及如何使用漂亮的汤库。我们还探索了多个实现代码片段，并用 Python 和 Beautiful Soup 构建了一个 web scraper 来从 [CoinGecko](https://www.coingecko.com/) 中检索加密货币信息。

加密货币 web scraper 的源代码作为 [GitHub Gist](https://gist.github.com/LordGhostX/bf24894108de79888d14994d76b6a34d) 提供。你可以去官方的[Beautiful Soup documentation](https://beautiful-soup-4.readthedocs.io/en/latest/#)去探索它提供的更多功能，并用从本教程中获得的知识来建造令人惊奇的东西。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)