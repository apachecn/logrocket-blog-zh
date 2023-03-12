# MeiliSearch:权威的指南博客

> 原文：<https://blog.logrocket.com/meilisearch-a-definitive-guide/>

MeiliSearch 是一个开源的、超快的、高度相关的搜索引擎。但它不是普通的搜索引擎:MeiliSearch 高度可定制的搜索引擎 API 给了你极大的灵活性。例如，您可以修改排名规则、添加自定义排名规则、配置同义词、过滤掉停用词等等。为了提高搜索能力，MeiliSearch 允许您设置充当索引的方面过滤器。

本教程演示了如何通过 [MeiliSearch JS 客户端](https://github.com/meilisearch/meilisearch-js)与 MeiliSearch 实例进行交互。您可以将这个客户端用于任何需要快速搜索功能的前端项目。

以下是我们将要介绍的内容:

我们将使用 MeiliSearch JavaScript 客户端和直接 API 请求来实现快速操作。事不宜迟，我们开始吧。

## 安装 MeiliSearch

确保您有一个活动的 [Node.js 安装](https://nodejs.org/en/download/)。拥有一个像 [cURL](https://curl.haxx.se/download.html) 这样的工具来发送 API 请求也很有用。

接下来，我们需要一个 MeiliSearch 实例与之交互。我们有几种运行 MeiliSearch 实例的可能性:

1.  使用 [MeiliSearch 沙箱](https://sandbox.meilisearch.com/)创建一个 72 小时的临时实例
2.  使用[数字海洋水滴](https://cloud.digitalocean.com/droplets/new?image=meilisas-meilisearch-10&app=meilisearch&onboarding_origin=marketplace&refcode=7c67bd97e101)部署实例
3.  使用 [Docker](https://github.com/meilisearch/MeiliSearch#docker) 运行 MeiliSearch
4.  或者， [MeiliSearch 文档](https://github.com/meilisearch/MeiliSearch#apt-debian--ubuntu)提供了 Debian、Ubuntu、Linux 和 Mac OS 的安装选项

为了保护我们的实例，我们需要设置一个主密钥来保护 MeiliSearch 实例的 API 端点。默认情况下，Meilisearch 沙盒会为您提供一个。对于选项 2、3 和 4，您必须手动设置一把[主钥匙](https://docs.meilisearch.com/guides/advanced_guides/authentication.html#authentication)。

要验证您的安装，请尝试发送以下请求来列出所有索引。全新安装应该没有索引。因此，响应是一个空数组。

确保用您的 IP 地址替换 IP 地址。对于使用 DigitalOcean 或 MeiliSearch 沙盒的安装，我们不需要添加端口号`:7700`。

```
curl http://127.0.0.1:7700/indexes \
    --header 'X-Meili-API-Key: your-master-key'
// => []

```

接下来，让我们准备项目设置。

## MeiliSearch 项目设置

首先，使用`npm`创建一个新项目:

```
npm init -y

```

接下来，添加`meilisearch-js`依赖项:

```
npm install meilisearch

```

最后，创建一个包含我们所有代码的`index.js`文件。确保在新创建的项目的根目录下创建该文件。

```
touch index.js

```

### 流行博客数据集

在本教程中，我们使用了一个名为“读者参与的互联网新闻数据”的修改数据集。这个数据集已经由 Szymon Janowski 上传到 [Kaggle](https://www.kaggle.com/szymonjanowski/internet-articles-data-with-users-engagement) 。

因为我们不需要这个数据集中的所有属性，所以我们创建了一个适合我们教程的修改过的数据集。您可以在 [GitHub](https://gist.github.com/michielmulders/921b0e1f292519118cfc5ee368f0f663) 上下载数据集，或者使用下面的命令直接下载数据集:

```
curl -L https://gist.githubusercontent.com/michielmulders/921b0e1f292519118cfc5ee368f0f663/raw/c62cef304a50d883ca88bf95b47c809a873ce3ba/blogs.json -o blogs.json

```

博客对象包含以下属性:

```
{
    // `id` is the primary key for indexing objects - always a number
    id: 'string',
    source_name: 'string', // publisher
    author: 'string',
    title: 'string',
    description: 'string', // small excerpt describing the blog
    url: 'string',
    image: 'string', // URL to cover image
    published: 'string', // ISO datetime (2019-09-03T13:00:07Z)

    // number: year of publication extracted from `published` property
    publication_year: 2019, 
    content: 'string' // short excerpt from blog
}

```

## 创建博客索引

为了创建一个`blogs`索引，我们将把我们的`blogs.json`数据上传到这个索引，这样我们可以在以后搜索或修改这个数据。

为了与一个 MeiliSearch 实例交互，我们必须要求在我们的`index.js`文件的顶部有一个`meilisearch`包:

```
const MeiliSearch = require('meilisearch')

```

现在，我们将使用一个允许我们使用`async/await`语法的`main`函数。在本教程中，我们将使用`main`函数来更新代码片段。

在与 MeiliSearch 实例交互之前，我们需要建立一个连接。

```
const main = async () => {
    const client = new MeiliSearch({
        host: 'https://sandbox-pool-bwwv53a-3bsbgmeayb75w.ovh-fr-2.platformsh.site',
        headers: {
            'X-Meili-API-Key': 'your-master-key'
        }
    })
}

main()

```

让我们创建一个索引。`client`对象公开了与我们的 MeiliSearch 实例的 API 交互的所有方法。

```
const main = async () => {
    const client = new MeiliSearch({
        host: 'https://sandbox-pool-bwwv53a-3bsbgmeayb75w.ovh-fr-2.platformsh.site',
        headers: {
            'X-Meili-API-Key': 'your-master-key'
        }
    })

    await client.createIndex('blogs')
}

main()

```

为了创建索引，我们必须执行`index.js`文件:

```
node index.js

```

为了简单起见，我们不会重复所有的代码。

现在，让我们列出所有索引来验证我们是否成功创建了`blogs`索引。

```
const indexes = await client.listIndexes()
console.log(indexes)
/* Output:
[
    {
        name: 'blogs',
        uid: 'blogs',
        createdAt: '2020-12-04T17:27:43.446411126Z',
        updatedAt: '2020-12-04T17:51:52.758550969Z',
        primaryKey: null
    }
]
*/

```

MeiliSearch 还没有为奖品索引设置主键。当我们在下一步添加数据时，MeiliSearch 将[自动选择主键](https://docs.meilisearch.com/guides/main_concepts/documents.html#primary-field)，因为我们的数据集包含一个`id`字段。

看到`blogs`指数了吗？让我们继续前进！

## 在 MeiliSearch 中上传数据集

将大型数据集上传到 MeiliSearch 实例的最快方法是使用 cURL 之类的工具。确保在包含`blogs.json`数据集的目录中执行该命令。注意把数据上传到右边的索引:`/indexes/blogs/`。同样，如果您已经配置了主密钥，请添加主密钥。

```
curl -i -X POST 'https://meilisearch-sandbox.site/indexes/blogs/documents' \
    --header 'content-type: application/json' \
    --header 'X-Meili-API-Key: your-master-key' \
    --data-binary @blogs.json

```

为了验证我们的数据是否已经成功上传，让我们再次列出我们的索引。这一次，主键字段应该列出`id`属性。

```
node index.js

```

[主键](https://docs.meilisearch.com/guides/advanced_guides/authentication.html)设置好了吗？下一步解释如何添加附加文档。

## 使用 MeiliSearch JavaScript 客户端添加文档

向我们的`blogs.json`数据集添加一个新文档怎么样？下面是如何向您的 MeiliSearch 实例添加额外的文档。

在添加文档之前，让我们定义一个由 ABC News 创建的新的虚拟博客。注意，我们定义了一个文档数组。默认情况下，我们可以一次添加多个文档。

```
const documents = [
    {
        id: '201',
        source_name: 'ABC News',
        author: 'Gregorian',
        title:
            '\'This Tender Land\' is an affecting story about growing up',
        description:
            '"This Tender Land" by William Kent Krueger is an affecting story about growing up and overcoming a childhood filled with neglect, abuse and racism during the Depression.',
        url:
            'https://abcnews.go.com/Entertainment/wireStory/tender-land-affecting-story-growing-65359757',
        image: '',
        published: '2019-09-03T15:56:49Z',
        content:
            '"This Tender Land: a Novel" (Atria Books), by William Kent Krueger\r\nStrands of the adventures of Huck Finn and Tom Sawyer on the Mississippi River echo throughout William Kent Krueger\'s lyrical, compassionate "This Tender Land" in which four children try to e… [+2822 chars]'
    }
]

```

接下来，我们需要检索我们的`blogs`索引并调用`addDocuments`方法。这个方法接受我们的`documents`数组。

```
const index = client.getIndex('blogs')
let response = await index.addDocuments(documents)
console.log(response) // => { "updateId": 0 }

```

当文档成功添加后，`addDocuments`函数返回一个带有`updateId`属性的 JSON 对象。新建文档的`updateId`设置为`0`。每当我们对一个特定的文档进行修改时，`updateId`就会增加。这个系统非常类似于跟踪变更的增量版本控制系统。

在下一步中，我们将使用`search`方法来检索我们新添加的文档。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 在 MeiliSearch 中搜索文档

在上一步中，我们添加了一个作者为`Gregorian`的新文档。让我们试着查询这个文档。为此，我们可以使用由`index`对象公开的`search`方法。

```
const index = client.getIndex('blogs')
const search = await index.search('Gregorian')
console.log(search)

/* Output:
{
    hits:
        [{
            id: '201',
            source_name: 'ABC News',
            author: 'Gregorian',
            ...
        }],
    offset: 0,
    limit: 20,
    nbHits: 1,
    exhaustiveNbHits: false,
    processingTimeMs: 4,
    query: 'Gregorian'
}
*/

```

返回的结果包含几个属性:

*   `hits`包含匹配搜索意图的项目
*   `nbHits`表示匹配项目的数量
*   `processingTimeMs`表示检索搜索结果的时间(毫秒)
*   `query`是我们发送给 MeiliSearch 实例的查询

四毫秒——太快了！

## 如何用 MeiliSearch 修改文档

现在让我们更新作者`Gregorian`创建的博客。假设，事实上，我们犯了一个错误:博客不是由`ABC News`而是由`Fox News`发布的。

用 MeiliSearch 更新文档非常简单。我们可以使用原始对象并改变它的一些值。由于 MeiliSearch 已经自动将主键分配给了`id`字段，我们需要将`id`字段与我们的请求一起发送。最重要的是，我们使用相同的`addDocuments`功能来更新文档。

你可以这样做。

```
const original = { "id": "200", "source_name": "ABC News", "author": "The Associated Press", "title": "Sheryl Crow feels 'liberated' by saying goodbye to the album", "description": "Rocker Sheryl Crow says she feels liberated by saying goodbye to the full-length album format, but goes out with a star-studded bang on \"Threads.\"", "url": "https://abcnews.go.com/Entertainment/wireStory/sheryl-crow-feels-liberated-goodbye-album-65358836", "image": "https://s.abcnews.com/images/Entertainment/WireAP_e56806824cfe4f4aa287b73b4b2fcaaf_16x9_992.jpg", "published": "2019-09-03T15:27:46Z", "publication_year": 2019, "content": "Sheryl Crow has a lifetime of stories of hanging out with rock stars, pop stars, athletes, icons and music royalty, some even featured on her new collaborative record, but don't expect her to start revealing any secrets in an autobiography.\r\n\"I mean, there ar… [+4022 chars]" }

const updatedDocument = {
    ...original,
    source_name: 'Fox News'
}

const index = client.getIndex('blogs')
const updatedResponse = await client.getIndex('blogs').addDocuments([updatedDocument])
console.log(updatedResponse) // => { "updateId": 1 }

```

注意，自从我们修改文档以来，`updateId`属性增加了 1。

## 如何修改 MeiliSearch 排名规则

再来看看 MeiliSearch 中更高级的设置:[排名规则](https://docs.meilisearch.com/references/ranking_rules.html)。根据官方文件:

> 排名规则是确保搜索结果相关性的内置规则。MeiliSearch 以默认顺序应用排名规则，该顺序可在设置中更改。您可以添加或删除规则，并更改它们的重要性顺序。

默认情况下，MeiliSearch 采用以下顺序排列规则:

```
1\. typo
2\. words
3\. proximity
4\. attribute
5\. wordsPosition
6\. exactness

```

我们可以通过`getSettings`方法访问排名规则:

```
const index = client.getIndex('blogs')
const settings = await index.getSettings()
console.log(settings)

/* Output:
{
 rankingRules:
   [ 'typo',
     'words',
     'proximity',
     'attribute',
     'wordsPosition',
     'exactness' ],
  distinctAttribute: null,
  searchableAttributes: [ '*' ],
  displayedAttributes: [ '*' ],
  stopWords: [],
  synonyms: {},
  attributesForFaceting: []
}
*/

```

对于这个例子，我们来看看`wordsPosition`和`exactness`。你可以在 [MeiliSearch 文档](https://docs.meilisearch.com/guides/main_concepts/relevancy.html)中找到所有排名规则的解释。

### 将排名规则顺序更改为`wordsPosition`

我们需要更改我们的`blogs`索引的设置，以优先考虑`wordsPosition`，并将`exactness`移到后面。

> 单词位置:根据查询单词在属性中的位置对结果进行排序:首先查找属性中较早包含查询单词的文档。

我们只需将想要更改的属性传递给`updateSettings`函数。因此，我们传递了`rankingRules`属性。

```
const index = client.getIndex('blogs')
await index.updateSettings({
    rankingRules:
        [
            "wordsPosition",
            "typo", 
            "words", 
            "proximity", 
            "attribute",
            "exactness"
        ]
})

```

要查看排名规则的效果，让我们查询单词`cent`。让我们将搜索限制到五个结果。再者，让我们只打印文章的内容。

```
const index = client.getIndex('blogs')
const search = await index.search('cent', { limit: 5 })
search.hits.map(data => console.log(data.content, '\n\n'))

```

这个词以下列方式出现:

*   `center` (x2)
*   `central`
*   `Century`
*   `center-left`

我们也可以通过看`cent`这个词在文中的位置来看`wordsPosition`排名规则的效果。单词`cent`在第一个结果的文本中出现的较早。

```
Walking through the gym of a local community outreach **center** in Choloma, Honduras,

Walking through the gym of a local community outreach **center** in Choloma, Honduras,

The Latest on a boat capsizing along the White River in **central** Indiana (all times local):

Jim Henson has one of the most storied legacies of any 20th **Century** creative.

ROME (Reuters) - Members of the anti-establishment 5-Star Movement backed a proposed coalition with the **center-left** Democratic Party (PD) on Tuesday

```

### 将排名规则顺序更改为`"exactness"`

现在，让我们改变交换排名规则`wordsPosition`和`exactness`来看看区别。

这一次，我们需要更改索引`blogs`的设置，使`exactness`优先于`wordsPosition`。

> 准确性:根据匹配词与查询词的相似性对结果进行排序:查找包含与首先查询的词完全相同的词的文档。

同样，让我们更新`rankingRules`属性:

```
const index = client.getIndex('blogs')
await index.updateSettings({
    rankingRules:
        [
            "exactness",
            "typo", 
            "words", 
            "proximity", 
            "attribute",
            "wordsPosition"
        ]
})

```

要查看排名规则的效果，让我们再次查询单词`cent`:

```
const index = client.getIndex('blogs')
const search = await index.search('cent', { limit: 5 })
search.hits.map(data => console.log(data.content, '\n\n'))

```

这个词以下列方式出现:

*   `cent`->-`5.2 per cent`匹配优先，因为我们的排名规则优先考虑精确的单词匹配
*   `center-left`
*   `center` (x2)
*   `central`

就是这样！

## 结论

在这个 MeiliSearch 教程中，我们演示了如何使用 MeiliSearch 添加、更新和查询文档。除此之外，我们还向您介绍了排名规则的概念。

还有更多的东西需要探索。例如，你可以定义[停用词](https://docs.meilisearch.com/guides/advanced_guides/stop_words.html)来改进你的搜索，设置[同义词](https://docs.meilisearch.com/guides/advanced_guides/synonyms.html#the-multi-way-association)，或者添加[方面过滤器](https://docs.meilisearch.com/guides/advanced_guides/faceted_search.html#setting-up-facets)来索引你的数据。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)