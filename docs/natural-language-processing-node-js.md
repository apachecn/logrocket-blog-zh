# 使用 Node.js 的自然语言处理

> 原文：<https://blog.logrocket.com/natural-language-processing-node-js/>

***编者按:*** *本文最后一次更新于 2023 年 1 月 4 日，以确保所有信息与 Node.js 的最新版本兼容，并添加关于其他 NLP 库的信息，如 NLP.js 和 Compromise.cool.*

互联网促进了大量非结构化文本数据的永无止境的创建。幸运的是，我们有现代系统可以理解这种数据。

现代计算机系统可以使用一种叫做[自然语言处理(NLP)](https://en.wikipedia.org/wiki/Natural_language_processing) 的底层技术来理解自然语言。

Python 通常是 NLP 的首选语言，因为它有丰富的语言处理包，比如自然语言工具包。然而，JavaScript 正在快速发展，npm 的存在使其开发者能够访问大量的包，包括为不同语言执行 NLP 的包。

在本文中，我们将重点介绍如何使用 Node 开始使用 NLP。我们将使用一个叫做 natural 的 JavaScript 库。通过将自然库添加到我们的项目中，我们的代码将能够解析、解释、操作和理解来自用户输入的自然语言。

本文将仅仅触及 NLP 的表面，但是对于已经在 Python 中使用 NLP 并希望过渡到 Node 以获得相同结果的开发人员来说，它将非常有用。完全的新手也会学到很多关于 NLP 作为一种技术及其在 Node 上的用法。

向前跳:

## 什么是自然语言处理？

自然语言处理技术可以将人类语言作为输入进行处理，并执行以下一项或多项操作:

*   [情绪分析](https://en.wikipedia.org/wiki/Sentiment_analysis)(是正面陈述还是负面陈述？)
*   话题分类(是关于什么的？)
*   根据这一陈述决定应该采取什么行动
*   [意图提取](http://nlp_architect.nervanasys.com/intent.html)(这种说法背后的意图是什么？)

> NLP 是语言学、计算机科学、信息工程和人工智能的一个分支，涉及计算机和人类(自然)语言之间的交互，特别是如何对计算机进行编程以处理和分析大量自然语言数据。

NLP 的重要实现离我们不远了，因为我们的大多数设备都集成了 AI、ML 和 NLP 来增强人机通信。下面是一些常见的自然语言处理的例子。

### 搜索引擎

最有用的技术之一是谷歌搜索引擎。你输入文本，就会收到数百万条相关的回复结果。这是可能的，因为 NLP 技术可以理解输入并执行一系列逻辑操作。这也是为什么谷歌搜索能够理解你的意图，并在你拼写错误时向你建议正确的拼写。

### 智能虚拟助理

Siri、Alexa 和 Google Assistant 等虚拟助手展示了 NLP 实现的高级水平。收到您的口头输入后，它们可以识别意图，执行操作，并以自然语言发回响应。

### 智能聊天机器人

聊天机器人可以分析大量的文本数据，并根据大数据及其检测意图的能力给出不同的响应。这给人一种自然对话而不是与机器对话的总体感觉。

### 垃圾邮件过滤器

您是否注意到电子邮件客户端在过滤收件箱中的垃圾邮件方面不断进步？这是可能的，因为过滤引擎可以理解电子邮件的内容——主要使用[贝叶斯垃圾邮件过滤](https://en.wikipedia.org/wiki/Naive_Bayes_spam_filtering)——并决定它是否是垃圾邮件。

上面的用例表明 AI、ML 和 NLP 已经在 web 上被大量使用。因为人类使用自然语言与网站进行交互，所以我们应该建立具有 NLP 能力的网站。

## 先决条件

1.  Node.js 的基础知识
2.  为运行节点代码而设置的系统

要按照本文编写代码，您需要创建一个`index.js`文件并粘贴您想要尝试的代码片段，然后用 Node 运行该文件。我们开始吧！

## 装置

我们可以通过运行以下命令来安装 natural:

```
npm install natural

```

下一节中以下每个使用示例的源代码都可以在 [GitHub](https://github.com/Jordanirabor/nlp-node-natural-article) 上获得。你可以随意复制它，叉它，或者提交一个问题。

### 使用

让我们学习如何使用 [natural](https://www.npmjs.com/package/natural) 执行一些基本但重要的 NLP 任务。

### 标记化

[记号化](https://en.wikipedia.org/wiki/Natural_language_processing)是将输入的字符或单词分割/拆分成称为“记号”的更小部分的过程标记可以是字符、单词或子单词。标记化是自然语言处理的第一步，需要收集数据并将其分解成机器可以理解的部分。

例如，我们来看文本[字符串](https://en.wikipedia.org/wiki/String_(computer_science)) : `The quick brown fox jumps over the lazy dog`

字符串没有像使用自然语言的人所做的那样，隐式地用空格分割。原始输入(43 个字符)必须使用给定的空格分隔符(即匹配字符串`" "`或正则表达式`/\s{1}/`)显式拆分成 9 个标记。

[natural](https://www.npmjs.com/package/natural) 附带了许多智能记号赋予器算法，可以将文本分解成记号数组。下面的代码片段展示了单词 tokenizer 的用法:

```
// index.js

var natural = require('natural');
var tokenizer = new natural.WordTokenizer();

console.log(tokenizer.tokenize("The quick brown fox jumps over the lazy dog"));

```

使用 Node 运行此命令会产生以下输出:

```
[ 'The',
  'quick',
  'brown',
  'fox',
  'jumps',
  'over',
  'the',
  'lazy',
  'dog' ]

```

## 堵塞物

词干化是将一个单词缩减为其[词干](https://en.wikipedia.org/wiki/Word_stem)(也称为基础或[词根](https://en.wikipedia.org/wiki/Root_(linguistics))形式)的行为。词干是人工智能检索和提取以及语言形态学的一个特征。它被搜索引擎用来索引单词。例如，像 cats、catlike 和 caty 这样的词将被分解到词根 cat。

Natural 目前支持两种词干算法: [Porter](http://tartarus.org/martin/PorterStemmer/index.html) 和 [Lancaster](https://www.scientificpsychic.com/paice/paice.html) (Paice/Husk)。下面是使用波特算法实现词干提取的代码片段:

```
// index.js

const natural = require('natural');

console.log(natural.PorterStemmer.tokenizeAndStem("I can see that we are going to be friends"))

```

从上面的代码中，我们使用 Porter 算法下的`tokenizeAndStem()`方法将字符串分解为单个单词，并将每个单词还原为它们的基本形式。结果是一组词干标记:

```
[ 'go', 'friend' ]

```

> **注意:**，在上面的结果中，算法已经删除了停用词。停用词是自然语言处理前过滤掉的词(如 *be* 、 *an* 、*到*都是停用词)。

## 波特算法与兰卡斯特算法

波特算法是最著名和最古老的词干算法，因为它是最不积极的。词干相当清楚易懂。波特词干分析器是一种后缀剥离算法。本质上，它使用预先定义的原则将单词分解成最基本的形式。Porter stemmer 采用了 50 多条规则，分为五个阶段和几个子步骤，以消除频繁出现的后缀。

这些规则的一些例子是:

*   ATOR-->(操作员-->操作)
*   SSES ->党卫军(压迫->压迫)
*   s-->(规则-->规则)
*   离子->(预测->)预测
*   ING ->(去->去，来->来)

另一方面，兰开斯特是相当积极的，由于其紧凑的截词风格，这使得它令人难以置信的令人费解。因为茎失去了一些相关性，它是最少使用的。超过 100 条规则组成了 Lancaster stemmer，大约是 Porter stemmer 的两倍。作者使用了不同于波特词干规则的符号来定义规则。每个规则由五部分组成，其中两部分是可选的。

这些规则的一些例子是:

*   " nois4j > ":用" j "替换结尾" sion ",并再次应用词干分析器
*   " sei3y > ":如果单词以" ies "结尾，那么用" y "替换最后三个字母，然后再次对缩写形式应用词干分析器
*   “mu*2。”:如果单词以“um”结尾，并且单词完整，则删除最后两个字母并终止

## 测量单词之间的相似性(字符串距离)

Natural 提供了计算字符串距离、[汉明距离](https://en.wikipedia.org/wiki/Hamming_distance)、 [Jaro-Winkler](https://en.wikipedia.org/wiki/Jaro%E2%80%93Winkler_distance) 、 [Levenshtein 距离](https://en.wikipedia.org/wiki/Levenshtein_distance)和[骰子系数](https://en.wikibooks.org/wiki/Algorithm_Implementation/Strings/Dice%27s_coefficient)的四种算法的实现。使用这些算法，我们可以判断两个字符串是否匹配。为了这个项目，我们将使用汉明距离。

汉明距离通过计算不同字符的数量来测量两个等长字符串之间的距离。第三个参数指示是否应该忽略该案例。默认情况下，该算法区分大小写。

下面是一个代码片段，展示了如何使用卷边算法来计算字符串距离:

```
// index.js

var natural = require('natural');

console.log(natural.HammingDistance("karolin", "kathrin", false));
console.log(natural.HammingDistance("karolin", "kerstin", false));
console.log(natural.HammingDistance("short string", "longer string", false));

```

输出:

```
3
3
-1

```

前两个比较返回`3`，因为三个字母不同。最后一个函数返回`-1`，因为被比较的字符串长度不同。

## 分类

文本分类，也称为文本标记，是将文本分类到有组织的组中的过程。也就是说，如果我们有一个新的未知语句，我们的处理系统可以根据它的内容决定它最适合哪个类别。

自动文本分类的一些最常见的用例包括:

*   情感分析
*   话题检测
*   语言检测

natural 目前支持两种分类器:[朴素贝叶斯](https://en.wikipedia.org/wiki/Naive_Bayes_classifier)和[逻辑回归](https://en.wikipedia.org/wiki/Logistic_regression)。以下示例使用了`BayesClassifier`类:

```
// index.js

var natural = require('natural');

var classifier = new natural.BayesClassifier();
classifier.addDocument('i am long qqqq', 'buy');
classifier.addDocument('buy the q\'s', 'buy');
classifier.addDocument('short gold', 'sell');
classifier.addDocument('sell gold', 'sell');
classifier.train();

console.log(classifier.classify('i am short silver'));
console.log(classifier.classify('i am long copper'));

```

在上面的代码中，我们在样本文本上训练了分类器。它将使用合理的默认值对文本进行分词和词干处理。根据示例文本，控制台将记录以下输出:

```
sell
buy

```

## 情感分析

[情感分析](https://en.wikipedia.org/wiki/Sentiment_analysis)，也称为意见挖掘或情感 AI，是 NLP 最常用的应用之一，它从口头或书面语言中识别和提取观点，以确定一个人的情感。

为了评估一条信息是正面的、负面的还是中性的，利用了情感分析。企业使用情感分析来监控品牌认知度和消费者反馈，以了解产品的表现如何，以及需要什么来增加销售额。

Natural 支持这样的算法，即通过将每个单词的极性相加，并用句子的长度将其标准化，来计算每段文本的情感。如果出现否定，结果就是否定的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

下面是它的用法示例:

```
// index.js

var natural = require('natural');
var Analyzer = natural.SentimentAnalyzer;
var stemmer = natural.PorterStemmer;
var analyzer = new Analyzer("English", stemmer, "afinn");

// getSentiment expects an array of strings
console.log(analyzer.getSentiment(["I", "don't", "want", "to", "play", "with", "you"]));

```

该构造函数有三个参数:

*   语言
*   斯特梅尔:为了增加情感分析器的覆盖范围，可以提供一个词干分析器
*   词汇:设置词汇的类型。`"afinn"`、`"senticon"`或`"pattern"`为有效值

运行上面的代码会产生以下输出:

```
0.42857142857142855 // indicates a relatively negative statement

```

## 语音匹配

使用 natural，我们可以使用语音匹配来比较两个拼写不同但发音相似的单词。这里有一个使用`metaphone.compare()`方法的例子:

```
// index.js

var natural = require('natural');
var metaphone = natural.Metaphone;
var soundEx = natural.SoundEx;

var wordA = 'phonetics';
var wordB = 'fonetix';

if (metaphone.compare(wordA, wordB))
    console.log('They sound alike!');

// We can also obtain the raw phonetics of a word using process()
console.log(metaphone.process('phonetics'));

```

我们还使用`process()`获得了一个单词的原始语音。当我们运行上面的代码时，我们得到以下输出:

```
They sound alike!
FNTKS

```

## 拼写检查

当用户通过搜索栏或输入字段向 web 应用程序提供输入时，可能会出现打字错误。Natural 有一个概率拼写检查器，可以使用来自文本语料库的一组标记来建议拼写错误的单词。

为了简单起见，让我们使用两个单词的数组(也称为语料库)来探索一个示例:

```
// index.js

var natural = require('natural');

var corpus = ['something', 'soothing'];
var spellcheck = new natural.Spellcheck(corpus);

console.log(spellcheck.getCorrections('soemthing', 1)); 
console.log(spellcheck.getCorrections('soemthing', 2));

```

它会建议距离输入单词最大编辑距离的更正(按概率降序排列)。一的最大距离将覆盖 80%到 95%的拼写错误。在两个距离之后，它变得非常慢。

我们从运行代码中得到以下输出:

```
[ 'something' ]
[ 'something', 'soothing' ]

```

## 其他 NLP 库

### NLP . JSP

由 AXA 集团创建， [NLP.js](https://github.com/axa-group/nlp.js) 是一个用于 bot 开发的 NLP 包，支持 40 种语言。它提供实体提取、情感分析、自动语言识别和其他功能。它是创建聊天机器人的理想 Node.js 库:

```
const { NlpManager } = require('node-nlp');

const manager = new NlpManager({ languages: ['en'], forceNER: true });

// Adds the utterances and intents for the NLP
manager.addDocument('en', 'bye bye take care', 'greetings.bye');
manager.addDocument('en', 'okay see you later', 'greetings.bye');
manager.addDocument('en', 'hello', 'greetings.hello');
manager.addDocument('en', 'hi', 'greetings.hello');

// Train also the NLG
manager.addAnswer('en', 'greetings.bye', 'Till next time');
manager.addAnswer('en', 'greetings.bye', 'see you soon!');
manager.addAnswer('en', 'greetings.hello', 'Hey there!');
manager.addAnswer('en', 'greetings.hello', 'Greetings!');

// Train and save the model.
(async() => {
    await manager.train();
    manager.save();
    const response = await manager.process('en', 'I should go now');
    console.log(response);
})();

```

### 妥协，酷

Compromise.cool 是一个非常用户友好的轻量级库。通过将文本转换成数据，它可以用来在你的浏览器中运行 NLP，并做出合理的结论。妥协只在英语中起作用。

下面是一个简单的代码片段:

```
import nlp from 'compromise'

var doc = nlp('Sam is coming')
doc.verbs().toNegative()
// 'Sam is not coming'

```

### 眨眼

Wink 为各种任务提供了 NLP 功能，包括增强否定、控制省略、生成单词的语法、词干和语音代码。它提供了一个 API 集合，用于处理字符串，如名称、句子、段落和标记，它们都表示为字符串或单词的数组。它们为许多 ML 应用程序执行必要的预处理，包括分类和语义搜索:

```
// Load wink-nlp-utils
var nlp = require( 'wink-nlp-utils' );

// Extract person's name from a string:
var name = nlp.string.extractPersonsName( 'Dr. Sarah Connor M. Tech., PhD. - AI' );
console.log( name );
// -> 'Sarah Connor'

// Remove stop words:
var t = nlp.tokens.removeWords( [ 'mary', 'had', 'a', 'little', 'lamb' ] );
console.log( t );
// -> [ 'mary', 'little', 'lamb' ]

```

## 结论

下面是到目前为止我们在本文中学到的内容的一个快速总结:

*   计算机系统变得越来越智能，可以使用 NLP 从大量非结构化文本数据中提取意义
*   Python 有丰富的智能包来执行 AI、ML 和 NLP 任务，但 JavaScript 发展非常迅速，其包管理器有大量能够处理自然语言的包
*   Natural 是一个 JavaScript 包，在执行 NLP 操作时非常健壮，并且对于每个任务都有许多算法可供选择

下一节中以下每个使用示例的源代码是 GitHub 上的[。请随意克隆它、派生它或提交一个问题。](https://github.com/Jordanirabor/nlp-node-natural-article)

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.