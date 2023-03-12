# 用 Node.js - LogRocket Blog 构建情感分析应用

> 原文：<https://blog.logrocket.com/sentiment-analysis-node-js/>

在本文中，我们将使用 Node.js 构建一个情感分析应用程序，该应用程序分析来自用户评论的文本数据，并使用自然语言处理(NLP)来确定用户的情感。

我们最终的应用程序将如下所示:

![Sentiment Analysis Application Preview](img/0447a3aa4df94165a45f41491ab8062a.png)

在开始之前，我们先了解一下情感分析和自然语言处理是什么意思。

## 什么是情感分析？

情感分析是分析文本数据并得出其情感基调的过程。例如，将客户对产品的评价分为满意、不满意或中立。为了自动化这一过程，我们将使用自然语言处理，这是人工智能的一个分支。

## 什么是自然语言处理？

与编程语言不同，自然语言通常是模糊的，并且不是为计算机理解而设计的——因此需要一种处理自然语言的技术，以便从中获得有意义和可操作的数据。SAS 简洁地说:

> 自然语言处理是人工智能的一个分支，它赋予计算机解释、理解和操纵人类语言的能力。

## 安装

让我们从使用快速框架构建一个新的 Node.js 应用程序[开始。我们将使用`express-generator` CLI 工具生成一个支架应用程序。](https://blog.logrocket.com/forget-express-js-opt-for-these-alternatives-instead/)

首先，我们将通过在终端上运行以下命令来确保安装了 Node:

```
node --version
```

如果返回错误信息，点击[此处](https://nodejs.org/en/download/)查看节点安装说明。安装节点后，让我们在终端上运行以下命令:

```
npm install -g express-generator
```

`express-generator`是我们将用来搭建一个新的节点应用程序的。为此，我们将运行:

```
express node_nlp --no-view
```

要启动我们的应用程序，让我们导航到新的应用程序目录并运行`npm start`:

```
cd node_nlp
npm start
```

在我们新生成的应用程序目录中，让我们导航到`./package.json`。我们需要设置 nodemon 来帮助我们在保存新更改时自动重启应用程序。在您的终端上，运行:

```
npm install --save nodemon
```

接下来，我们将添加一个新脚本，通过 nodemon 启动我们的应用程序。在`package.json`中的`scripts`下，添加以下代码:

```
"dev": "nodemon ./bin/www"
```

接下来，我们可以通过在终端上运行以下命令来启动我们的应用程序:

```
npm run dev
```

现在我们已经成功地设置了我们的应用程序，让我们使用 NLP 来实现我们的情感分析功能。

我们将从安装 [Natural](https://www.npmjs.com/package/natural) 开始，这是一个 Node.js 包，支持我们将在项目中使用的大多数 NLP 算法。让我们在终端上运行以下命令:

```
npm install --save natural
```

接下来，在我们的`routes`目录中，我们将创建一个新文件，并将其命名为`nlp.js`。这是我们将为`API`放置 NLP 相关路线的地方。在我们的新文件`./routes/nlp.js`中，让我们导入以下包:

```
const express = require('express');
const natural = require('natural');
```

在此之后，我们将创建一个新的路线，并给它路径`s-analyzer`。当用户将带有产品评论的`POST`请求发送到我们的 route 时，他们应该会收到包含情感分析的响应。

为了创建我们的新路线，让我们修改我们的`./routes/nlp.js`文件:

```
const express = require('express');
const natural = require('natural');

const router = express.Router();

router.post('/s-analyzer', function(req, res, next) {
  const { review } = req.body;
});
```

注意，我们已经析构了用户的评论，因为我们将从我们的`request.body`对象中期待它。

## 数据预处理

我们从用户那里获得的原始数据通常充满了大量的噪声，并且可能包含许多错误，因此需要将其转换为我们的 NLP 算法可以理解/使用的格式。这一步被称为数据预处理。

### 将缩写转换成标准词典

为了保持文本数据的统一结构，我们需要转换缩写(例如， *I'm，you 're，*等)。)到他们的标准词库(即*我是，你是，*等。).为此，让我们通过在终端上运行以下命令来安装包 [apos-to-lex-form](https://www.npmjs.com/package/apos-to-lex-form) :

```
npm install --save apos-to-lex-form
```

接下来，我们将它导入到我们的`/routes/nlp.js`文件中，并用于我们的数据转换:

```
const express = require('express');
const aposToLexForm = require('apos-to-lex-form');
const natural = require('natural');

const router = express.Router();

router.post('/s-analyzer', function(req, res, next) {
  const { review } = req.body;
  const lexedReview = aposToLexForm(review);
});
```

### 将我们的文本数据转换成小写

在我们的情感分析过程中，我们希望所有的数据都采用统一的格式。这一步确保我们的算法将*好的*和*好的*视为相同的单词。我们将通过使用 JavaScript 的默认函数`toLowerCase()`来实现这一点:

```
...

const router = express.Router();

router.post('/s-analyzer', function(req, res, next) {
  const { review } = req.body;
  const lexedReview = aposToLexForm(review);
  const casedReview = lexedReview.toLowerCase();
});
```

### 删除非字母和特殊字符

为了提高我们对用户情感分类的准确性，我们将删除特殊字符和数字符号，因为它们对情感没有贡献。这个过程将确保我们的文本数据只剩下字母字符。

让我们使用 JavaScript 的默认`replace()`函数来实现这一点:

```
...

const router = express.Router();

router.post('/s-analyzer', function(req, res, next) {
  const { review } = req.body;
  const lexedReview = aposToLexForm(review);
  const casedReview = lexedReview.toLowerCase();
  const alphaOnlyReview = casedReview.replace(/[^a-zA-Z\s]+/g, '');
});
```

### 标记化

这是将一篇文章分割成单个有意义的单元的过程。我们可以把一个单词想象成一个句子的标记，一个句子想象成一个段落的标记。

下一步，我们将使用进口天然包装中的`WordTokenizer`:

```
...

const router = express.Router();

router.post('/s-analyzer', function(req, res, next) {
  const { review } = req.body;
  const lexedReview = aposToLexForm(review);
  const casedReview = lexedReview.toLowerCase();
  const alphaOnlyReview = casedReview.replace(/[^a-zA-Z\s]+/g, '');

  const { WordTokenizer } = natural;
  const tokenizer = new WordTokenizer();
  const tokenizedReview = tokenizer.tokenize(alphaOnlyReview);
});
```

### 纠正拼写错误的单词

由于产品评论将由我们的用户手动编写，因此很有可能出现印刷错误。在将我们的数据传递给我们的情感分析算法之前，让我们使用[拼写纠正器](https://www.npmjs.com/package/spelling-corrector)包来纠正拼写错误的单词，这样，如果我们的用户错误地输入了 *lov* ，正确的拼写 *love* 将被传递给我们的算法。

让我们从使用以下命令安装它开始:

```
npm install --save spelling-corrector
```

接下来，我们将把下面突出显示的行添加到我们的`./routes/nlp.js`文件中:

```
...
const SpellCorrector = require('spelling-corrector');

const router = express.Router();

const spellCorrector = new SpellCorrector();
spellCorrector.loadDictionary();

router.post('/s-analyzer', function(req, res, next) {
  const { review } = req.body;
  const lexedReview = aposToLexForm(review);
  const casedReview = lexedReview.toLowerCase();
  const alphaOnlyReview = casedReview.replace(/[^a-zA-Z\s]+/g, '');

  const { WordTokenizer } = natural;
  const tokenizer = new WordTokenizer();
  const tokenizedReview = tokenizer.tokenize(alphaOnlyReview);

  tokenizedReview.forEach((word, index) => {
    tokenizedReview[index] = spellCorrector.correct(word);
  })
});
```

### 删除停用词

停用词一般是一种语言中最常见的词，在处理前被过滤掉。一些停用词的例子包括*但是*、*一个*、*或*，以及*什么*。由于这些词对用户的情绪没有影响，删除它们将有助于我们专注于重要的关键词。

为此，我们将使用[停用词](https://www.npmjs.com/package/stopword)包。让我们通过在终端上运行以下命令来安装它:

```
npm install --save stopword
```

接下来，我们将把下面突出显示的行添加到我们的`./routes/nlp.js`文件中:

```
...
const SW = require('stopword');

const router = express.Router();

const spellCorrector = new SpellCorrector();
spellCorrector.loadDictionary();

router.post('/s-analyzer', function(req, res, next) {
  const { review } = req.body;
  const lexedReview = aposToLexForm(review);
  const casedReview = lexedReview.toLowerCase();
  const alphaOnlyReview = casedReview.replace(/[^a-zA-Z\s]+/g, '');

  const { WordTokenizer } = natural;
  const tokenizer = new WordTokenizer();
  const tokenizedReview = tokenizer.tokenize(alphaOnlyReview);

  tokenizedReview.forEach((word, index) => {
    tokenizedReview[index] = spellCorrector.correct(word);
  })
  const filteredReview = SW.removeStopwords(tokenizedReview);
});
```

### 堵塞物

这是 NLP 中的单词规范化过程，用于将派生或变形的单词转换为它们的基本或根形式。例如，词干分析器算法可以将单词“giving”、“given”和“giver”简化为它们的词根“give”。

对于我们的应用程序，我们不会单独执行这个过程，因为自然库中的`SentimentAnalyzer`为我们提供了在调用它时提供词干分析器作为参数的选项。在分析过程中，单个单词将被转换成它们的词根形式。

## 基于自然库的情感分析

现在我们有了想要的文本数据，我们可以使用 Natural 的`SentimentAnalyzer`来分析用户的评论。

自然库中的情感分析算法基于一个为单词分配极性的词汇表。比如“好”这个词的极性是`3`，而“坏”的极性是`-3`。该算法通过对一段文本中每个单词的极性求和，并用句子的长度进行标准化，来进行情感计算。

这就是为什么预处理和去除数据中的所有噪声是获得更准确结果的必要步骤。如果我们的算法返回负值，文本的情感被认为是负面的，如果它返回正值，则被认为是正面的，如果它返回`0`，则被认为是中性的。

`SentimentAnalyzer`构造函数有三个参数:

*   文本数据的语言
*   去梗器
*   词汇表(目前支持 AFINN、Senticon 和 Pattern)

这里有一个来自自然图书馆的官方情感分析文档的链接。

为了在我们的应用程序中使用该算法，让我们将下面突出显示的代码添加到我们的`./routes/nlp.js`文件中:

```
...

router.post('/s-analyzer', function(req, res, next) {
  const { review } = req.body;
  const lexedReview = aposToLexForm(review);
  const casedReview = lexedReview.toLowerCase();
  const alphaOnlyReview = casedReview.replace(/[^a-zA-Z\s]+/g, '');

  const { WordTokenizer } = natural;
  const tokenizer = new WordTokenizer();
  const tokenizedReview = tokenizer.tokenize(alphaOnlyReview);

  tokenizedReview.forEach((word, index) => {
    tokenizedReview[index] = spellCorrector.correct(word);
  })
  const filteredReview = SW.removeStopwords(tokenizedReview);

  const { SentimentAnalyzer, PorterStemmer } = natural;
  const analyzer = new SentimentAnalyzer('English', PorterStemmer, 'afinn');
  const analysis = analyzer.getSentiment(filteredReview);

  res.status(200).json({ analysis });
});

module.exports = router;
```

在我们新添加的代码行中，我们从自然库中析构了`SentimentAnalyzer`和`PorterStemmer`方法，然后创建了一个新变量`analyzer`，并将我们的情感分析结果赋给它。

注意，在`SentimentAnalyzer`构造函数中，我们提供了参数`English`(因为这是我们希望用户使用的语言)`PorterStemmer`(我们为分析选择的词干分析器的类型)，以及`afinn`(我们分析的词汇类型)。

## 连接我们的 NLP 路由到我们的服务器

设置好我们的`sentiment analysis`路线后，下一步将是将其连接到我们的 Express 服务器。要做到这一点，我们将把`nlp router`导入到我们的`./app.js`文件中，并把它作为一条路由添加到`/api/nlp`路径中。

让我们将下面突出显示的行添加到我们的`./app.js`文件中:

```
var express = require('express');
var path = require('path');
var cookieParser = require('cookie-parser');
var logger = require('morgan');

var indexRouter = require('./routes/index');
var nlpRouter = require('./routes/nlp');

var app = express();

app.use(logger('dev'));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));

app.use('/', indexRouter);
app.use('/api/nlp', nlpRouter);

module.exports = app;
```

## 与我们的前端合作

现在我们已经设置好了路由，让我们将它与应用程序的前端连接起来。我们将添加一个收集用户评论的简单表单和一个用于进行 API 调用的 JavaScript 函数。

让我们修改我们的`./public/index.html file`,如下所示:

```
<html>

<head>
  <title>Sentiment Analyzer</title>
  <link rel="stylesheet" href="/stylesheets/style.css">
</head>

<body>
  <h1 id="title">Please write a review for this product:</h1>
  <form id="reviewForm">
    <textarea id="review" rows="4" cols="50"></textarea>
  </form>

  <div id="emojiSection"></div>

  <script type="text/javascript" src="./javascripts/index.js"></script>
</body>

</html>
```

接下来，在`/public/javascripts`文件夹中，让我们创建一个新文件`index.js`，并将以下代码行粘贴到其中:

```
const submitReview = (e) => {
  e.preventDefault();
  const review = document.getElementById('review').value;
  const options = {
    method: 'POST',
    body: JSON.stringify({ review }),
    headers: new Headers({ 'Content-Type': 'application/json' })
  }

  const emojiSection = document.getElementById('emojiSection');
  const title = document.getElementById('title');
  const outline = document.querySelector(':focus');

  fetch('/api/nlp/s-analyzer', options)
    .then(res => res.json())
    .then (({ analysis }) => {
      if (analysis < 0) {
        emojiSection.innerHTML = '<img src="https://img.icons8.com/emoji/96/000000/angry-face.png">';
        title.style.color = 'red';
        outline.style.borderColor = 'red';
      };
      if (analysis === 0) {
        emojiSection.innerHTML = '<img src="https://img.icons8.com/officel/80/000000/neutral-emoticon.png">';
        title.style.color = '#00367c';
        outline.style.borderColor = '#00367c';
      }
      if (analysis > 0) {
        emojiSection.innerHTML = '<img src="https://img.icons8.com/color/96/000000/happy.png">';
        title.style.color = 'green';
        outline.style.borderColor = 'green'
      }
    })
    .catch(err => {
      emojiSection.innerHTML = 'There was an error processing your request!'
    })
}

document.getElementById('review').addEventListener('keyup', submitReview);
document.getElementById('reviewForm').addEventListener('submit', submitReview);
```

注意，我们正在为在`index.html`文件中创建的`emojiSection` `div`渲染表情符号。我们还根据从 API 接收到的情感值改变了应用程序的颜色:小于`0`的结果被认为是负面的，大于`0`的结果被认为是正面的，等于`0`的结果是中性的。

现在，当我们启动应用程序并导航到`[http://localhost:3000/](http://localhost:3000/)`时，它应该能够根据我们的表单输入来计算产品评论的情感分析，就像下面的演示一样:

![Sentiment Analysis Application Preview](img/0447a3aa4df94165a45f41491ab8062a.png)

## 结论

在本文中，我们介绍了使用 Node.js 进行自然语言处理的基础知识，并构建了一个情感分析应用程序，该应用程序根据用户评论中收到的文本数据计算用户的情感。

下面是我们的演示应用程序的 GitHub repo 的链接:[node _ NLP _ sensition _ analysis](https://github.com/ebenezerdon/node_nlp_sentiment_analysis)。如果你在 Node.js 的 NLP 上需要任何进一步的帮助，请随时通过 [Twitter](https://twitter.com/ebenezerDN) 联系我

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.