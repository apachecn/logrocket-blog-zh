# 探索 SQL 和 elastic 使用开放的发行版搜索引擎博客

> 原文：<https://blog.logrocket.com/exploring-sql-elasticsearch-open-distro/>

## 介绍

Open Distro 是一个开源的企业级弹性搜索发行版，具有许多高级功能，包括强大的安全选项、警报、集成事件监控、性能分析和更多功能。它还为已经熟悉 SQL 的开发人员提供了一种选择，让他们可以快速进入编写 Elasticsearch 查询的状态。

[Open Distro](https://opendistro.github.io/for-elasticsearch-docs/#why-use-open-distro-for-elasticsearch) 是开源版 Elasticsearch (ES)和 Kibana 的 Apache 2.0 授权扩展。它目前由 AWS 开发和维护，作为其开源工作的一部分。这变得很有必要，因为需要持续支持新的和高级的 ES 特性，使之成为真正的开源软件。

Open Distro 允许您使用 SQL 与集群进行交互，并执行通常必须使用 Elasticsearch 领域特定语言(DSL)才能执行的操作。这个特性最初是基于 [elasticsearch-sql](https://github.com/NLPchina/elasticsearch-sql) 插件，现在逐渐被淘汰或弃用。

在本教程中，我们将重点介绍如何在这个开源工具的帮助下使用 SQL 查询 Elasticsearch 数据。我们将涵盖:

*   开放式发行版的安装和入门
*   使用 REST API 将数据接收到我们的 ES 集群中
*   带 Elasticsearch 的 SQL
    *   端点和操作
    *   功能和支持的协议
    *   探索附加插件设置和选项

### 先决条件

为了轻松地学习本教程，建议您具备 Elasticsearch 的基础知识，并使用 DSL 编写基本的 ES 查询。这很重要，这样我们在用 SQL 编写类似的查询时就有了一个参考点。

但是，如果您已经熟悉基本的 SQL 命令，那么这应该不会太难。作为使用 Elasticsearch 的初级读本，我们可以在 Node.js 中查看这篇关于[理解 Elasticsearch 查询构建器的精彩教程。](https://blog.logrocket.com/elasticsearch-query-body-builder-node-js/)

由于我们对开放发行版及其 SQL 特性感兴趣，我们将把开放发行版插件[作为独立插件安装在我们的机器](https://opendistro.github.io/for-elasticsearch-docs/docs/install/plugins/)上，就像我们可以在兼容的集群上安装和使用任何其他 ES 插件一样。

> **注意**:ES 和 Kibana 的开放发行版可以通过 [Docker 镜像](https://opendistro.github.io/for-elasticsearch-docs/docs/install/docker/),%20%5BRPM%5D(https://opendistro.github.io/for-elasticsearch-docs/docs/install/rpm/),%20%5BHelm%5D(https://opendistro.github.io/for-elasticsearch-docs/docs/install/helm/)等获得。更多细节在下一节。

## 第一步:开始

为了开始使用 ElasticSearch 的开放发行版，让我们从安装和配置它开始。不过，值得注意的是，有多种安装和开始使用 Open Distro 的选项。

正如我们前面提到的，我们将安装独立的插件并在我们的机器上开发。注意，如果我们已经使用 AWS 并打算拥有这个特性，我们可以利用云托管版本作为服务提供。

> **注意**:关于安装和配置该工具的其他可用选项的更多信息，我们可以查看文档中关于安装和配置的[部分](https://opendistro.github.io/for-elasticsearch-docs/docs/install/)。

让我们确保我们的机器上有一个兼容的 ES 版本。关于插件与 ES 版本兼容性的细节可以在文档中找到[。](https://opendistro.github.io/for-elasticsearch-docs/docs/install/plugins/#plugin-compatibility)

这里，我们将使用 Elasticsearch 版本`7.1.1`和 Open Distro plugin 版本`1.1.0.0`作为 SQL 特性，因为我们现在只对此感兴趣。要安装插件，我们可以在终端/命令提示符下运行以下命令:

```
sudo bin/elasticsearch-plugin install https://d3g5vo6xdbdb9a.cloudfront.net/downloads/elasticsearch-plugins/opendistro-sql/opendistro_sql-1.1.0.0.zip
```

在运行这个命令之前，我们应该确保我们的机器上安装了 Elasticsearch 版本`7.1.1`。对于 macOS，我们可以运行以下命令来下载 gzipped 文件夹:

```
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.1.1-darwin-x86_64.tar.gz
```

下载完成后，我们可以运行这个命令来解压缩 gzipped 文件夹:

```
tar -xzvf elasticsearch-7.1.1-darwin-x86_64.tar.gz
```

然后，我们可以导航到该文件夹来测试我们的安装。我们可以在下面运行这个命令:

```
cd elasticsearch-7.1.1  // navigate into our elasticsearch downloaded binaries
./bin/elasticsearch // test your elastic search installation by starting it up
ls // list all files and folders inside the distribution
```

插件安装后的输出应该类似于下图:

```
-> Downloading https://d3g5vo6xdbdb9a.cloudfront.net/downloads/elasticsearch-plugins/opendistro-sql/opendistro_sql-1.1.0.0.zip
[=================================================] 100%   
-> Installed opendistro_sql
```

> **注意**:我们可以通过在 ES 文件夹中运行以下命令来确认我们的插件安装成功:
> 
> ```
> sudo bin/elasticsearch-plugin list
> ```

现在我们已经完成了为开发设置`open_distro_sql`的工作，让我们看看如何使用一个简单的 Node.js 脚本将数据接收到 ES 集群中。之后，我们可以开始使用 SQL 语句来编写查询。

## 使用 REST API 将数据接收到我们的 ES 集群中

从我们之前关于使用 Elasticsearch 的教程中，我们可以看到关于如何使用 Node.js 将数据接收到 ES 集群中的章节[。作为参考，下面是一个做同样事情的类似脚本:](https://github.com/firebase007/Elastic_search_query_builder_tutorial/blob/master/app/utility.js)

```
'use strict'

const fs = require('fs'); // Node JS file system module 
require('dotenv').config()  // for environment variables
const { Client } = require('@elastic/elasticsearch'). // ES client
const client = new Client({ node: 'http://localhost:9200' }). // ES client connection

// import json from file to ingest to our cluster
const data = JSON.parse(fs.readFileSync(__dirname + '/demo.json'));

const index= process.env.ES_INDEX; // ES index
const type = process.env.ES_TYPE;  // ES type

const writeDataToES = async (index, data) => {
    for (let i = 0; i < data.length; i++ ) {
      await client.create({
        refresh: true,
        index: index,
        id: i,
        body: data[i]
      }, (error, res) => {
        if (error) {
          console.error("Failed to import data to ES", error);
          return;
        }
        else {
          console.log("Successfully imported data", res);
        }
      });
    }
};

writeDataToES(index, data);

//to run the script, create a new index.js file and copy the above code snippet
//then run <node index.js> from the terminal...
```

注意，我们可以给 ES 索引起任何我们选择的名字。此外，确保我们已经安装了脚本成功运行所需的所有导入的依赖项。

使用上述命令运行脚本后，我们可以通过运行以下命令来确认数据已成功写入集群:

```
http://127.0.0.1:9200/index_name/_search?pretty
```

> **注意**:在本教程中，我们的查询将针对这个相同的 JSON 数据运行[这里](https://github.com/firebase007/Elastic_search_query_builder_tutorial/blob/master/app/dataToEs.json)。

## 带 Elasticsearch 的 SQL

### 端点和操作

如前所述，Open Distro for Elasticsearch SQL 允许我们用 SQL 而不是 DSL 编写查询。为了使用这个特性，我们需要做的就是将我们所有的请求发送到这个→ `[http://<host:port>/_opendistro/_sql](http://<host:port>/_opendistro/_sql)` URI 或端点。

尽管对此端点的所有调用都支持请求参数或请求正文，但通常推荐使用后者来调用此 URI。

> **注意** : `POST`请求是推荐的方法，因为内容长度没有限制。此外，当调用额外的功能时，其他参数也可以传递给插件，例如`prepared statements`。

此外，`explain`端点经常用于查询翻译和故障排除。终点包括:

*   `GET`–在这里，我们可以发送常规的`HTTP GET`请求，在 URL 参数中嵌入我们的查询。下面是一个例子:

    ```
    curl -H 'Content-Type: application/json' -X GET localhost:9200/_*opendistro/_*sql?sql=SELECT * FROM cars
    ```

*   `POST`–我们也可以在请求正文中发送带有我们的查询的`HTTP POST`请求，如下所示:

    ```
    curl -H 'Content-Type: application/json' -X POST localhost:9200/_*opendistro/_*sql -d '{"query" : "SELECT * FROM cars"}'
    ```

*   `explain`–为了翻译查询，我们可以使用`explain`端点。输出通常是 JSON 格式的 ES DSL。

> **注意**:我们还可以复制并粘贴到控制台，在我们的 ES 集群上运行它。让我们看下面的例子:
> 
> ```
> curl -H 'Content-Type: application/json' -X POST localhost:9200/_opendistro/_sql/_explain -d '{"query" : "SELECT Origin,Horsepower FROM cars WHERE Cylinders > 4 LIMIT 5"}'
> ```
> 
> 结果是:
> 
> ```
> {
>         "from": 0,
>         "size": 5,
>         "query": {
>                 "bool": {
>                         "filter": [{
>                                 "bool": {
>                                         "must": [{
>                                                 "range": {
>                                                         "Cylinders": {
>                                                           "from": 4,
>                                                           "to": null,
>                                                           "include_lower": false,
>                                                           "include_upper": true,
>                                                           "boost": 1.0
>                                                     }
>                                               }
>                                         }],
>                                         "adjust_pure_negative": true,
>                                         "boost": 1.0
>                                 }
>                         }],
>                         "adjust_pure_negative": true,
>                         "boost": 1.0
>                 }
>         },
>         "_source": {
>                 "includes": ["Origin", "Horsepower"],
>                 "excludes": []
>         }
> }
> ```

顺便说一句，注意本地 SQL 查询支持的特性或命令也受 Open Distro for SQL 支持。它们包括`SQL Select`、`Delete`、`Where`、`Order By`、`Group By`、`Inner Join`、`Show`、[等等](https://github.com/opendistro-for-elasticsearch/sql#sql-features)。

例如，使用`WHERE`条件语句就像这样简单:

```
curl -XPOST localhost:9200/_opendistro/_sql -d '{"query": "SELECT * FROM cars WHERE ['Acceleration']='20' LIMIT 5"}' -H 'Content-Type: application/json'
```

> **注意**:`WHERE`子句表示我们希望过滤数据，只包含指定或给定条件为真的行。

下面是上面查询的结果:

```
{
        "took": 3,
        "timed_out": false,
        "_shards": {
                "total": 1,
                "successful": 1,
                "skipped": 0,
                "failed": 0
        },
        "hits": {
                "total": {
                        "value": 8,
                        "relation": "eq"
                },
                "max_score": 0.0,
                "hits": [{
                        "_index": "cars",
                        "_type": "_doc",
                        "_id": "39",
                        "_score": 0.0,
                        "_source": {
                                "Name": "volkswagen super beetle 117",
                                "Miles_per_Gallon": null,
                                "Cylinders": 4,
                                "Displacement": 97,
                                "Horsepower": 48,
                                "Weight_in_lbs": 1978,
                                "Acceleration": 20,
                                "Year": "1971-01-01",
                                "Origin": "Europe"
                        }
                }, {
                        "_index": "cars",
                        "_type": "_doc",
                        "_id": "382",
                        "_score": 0.0,
                        "_source": {
                                "Name": "amc concord dl",
                                "Miles_per_Gallon": 23,
                                "Cylinders": 4,
                                "Displacement": 151,
                                "Horsepower": null,
                                "Weight_in_lbs": 3035,
                                "Acceleration": 20.5,
                                "Year": "1982-01-01",
                                "Origin": "USA"
                        }
                }, {
                        "_index": "cars",
                        "_type": "_doc",
                        "_id": "63",
                        "_score": 0.0,
                        "_source": {
                                "Name": "plymouth cricket",
                                "Miles_per_Gallon": 26,
                                "Cylinders": 4,
                                "Displacement": 91,
                                "Horsepower": 70,
                                "Weight_in_lbs": 1955,
                                "Acceleration": 20.5,
                                "Year": "1971-01-01",
                                "Origin": "USA"
                        }
                }, {
                        "_index": "cars",
                        "_type": "_doc",
                        "_id": "359",
                        "_score": 0.0,
                        "_source": {
                                "Name": "ford escort 2h",
                                "Miles_per_Gallon": 29.9,
                                "Cylinders": 4,
                                "Displacement": 98,
                                "Horsepower": 65,
                                "Weight_in_lbs": 2380,
                                "Acceleration": 20.7,
                                "Year": "1982-01-01",
                                "Origin": "USA"
                        }
                }, {
                        "_index": "cars",
                        "_type": "_doc",
                        "_id": "25",
                        "_score": 0.0,
                        "_source": {
                                "Name": "volkswagen 1131 deluxe sedan",
                                "Miles_per_Gallon": 26,
                                "Cylinders": 4,
                                "Displacement": 97,
                                "Horsepower": 46,
                                "Weight_in_lbs": 1835,
                                "Acceleration": 20.5,
                                "Year": "1970-01-01",
                                "Origin": "Europe"
                        }
                }]
        }
}
```

> **注意**:在本教程中，我们也使用`curl`命令行来发出我们所有的请求。我们可以通过运行这个命令:`brew install curl`在装有家酿的 macOS 上安装 curl。

同样对于 DSL，我们也可以利用 SQL 特性来处理`aggregation`、`queries`、`join`、`show`和[等等](https://github.com/opendistro-for-elasticsearch/sql#sql-usage)。

## 查询我们的集群

### 声明

*   **`WHERE`**–该子句对数据集进行过滤，只返回给定条件语句为真的行。在 Github repo 上的[这里可以找到一个针对`WHERE`子句的示例查询和结果。](https://github.com/firebase007/Elasticsearch-Open-Distro/tree/master/queries/conditions/where)
*   **`ORDER BY`**–该子句允许我们按照特定的列(在本例中我们使用的是`_id`列)按数字或字母顺序对数据进行排序。这里，我们是按照数字和升序来做的。在 Github repo 上可以找到`ORDER BY`条款的示例查询和结果[。](https://github.com/firebase007/Elasticsearch-Open-Distro/tree/master/queries/statements/orderBy)
*   **`GROUP BY`**–该子句用于聚合函数，例如`range`函数，以便在单个桶中有一组具有相似属性的数据。在 Github repo 上可以找到`GROUP BY`条款的示例查询和结果[。](https://github.com/firebase007/Elasticsearch-Open-Distro/tree/master/queries/statements/groupBy)

### 聚集

*   **`COUNT()`**——从 GitHub 上的[示例查询中我们可以看到，`COUNT()`函数将一列的名称——在本例中为`Miles_per_Gallon` —作为参数，并计算不属于`NULL`的行数。](https://github.com/firebase007/Elasticsearch-Open-Distro/tree/master/queries/aggregations/count)
*   **`MIN()`**—`MIN()`函数将一列的名称作为参数，在[我们的示例查询](https://github.com/firebase007/Elasticsearch-Open-Distro/tree/master/queries/aggregations/min)中为`Horsepower`，并返回该列中的最小值。
*   **`SHOW`**–返回与索引名模式匹配的索引和映射的聚合函数。注意，对于通配符，我们可以使用`*`或`%`命令。可以在 GitHub 上的[处找到`SHOW`命令的示例查询和结果。](https://github.com/firebase007/Elasticsearch-Open-Distro/tree/master/queries/aggregations/show)
*   **`AVG()`**–这是一个聚合函数，返回数值列的平均值。在[我们的样本查询](https://github.com/firebase007/Elasticsearch-Open-Distro/tree/master/queries/aggregations/avg)，那就是`Weight_in_lbs`。

### 功能

*   **`DATE_FORMAT()`**–函数`DATE_FORMAT`将时间戳列作为参数，其中包含我们要格式化的字段，并返回该列的格式化响应。这里可以在 GitHub repo 中找到`DATE_FORMAT`函数的示例查询和结果[。](https://github.com/firebase007/Elasticsearch-Open-Distro/tree/master/queries/functions/dateFormat)
*   **`FLOOR()`**–此函数将指定数字列的数值四舍五入到最接近的整数。请注意，它可以与其他聚合函数结合使用。在[我们的示例查询和结果](https://github.com/firebase007/Elasticsearch-Open-Distro/tree/master/queries/functions/floor)中，我们将它与`AS`组合在一起。

### 情况

*   **`BETWEEN`**–用于过滤掉一定范围内的数据。请注意，条件可以是数字、文本或日期。在我们的示例查询中，我们使用了数字。
*   `**COUNT(DISTINCT** … )`–仅用于选择一列的唯一值。这个命令的示例查询和结果可以在 GitHub repo 中找到[。](https://github.com/firebase007/Elasticsearch-Open-Distro/tree/master/queries/conditions/countDistinct)
*   **`IN`**–该子句基于公共列中的多个值返回结果。这里可以在 GitHub repo 中找到`IN`子句的示例查询和结果[。](https://github.com/firebase007/Elasticsearch-Open-Distro/tree/master/queries/conditions/in)

## 功能和支持的协议

对于协议，SQL 插件为不同的目的提供了多种响应格式，而请求格式对所有的都是一样的。其中，JDBC 格式被广泛使用，因为它提供了模式信息和额外的功能，如分页。除了 JDBC 驱动程序之外，各种客户端也可以从详细且格式良好的响应中受益。

例如,`HTTP POST`请求的主体可以在我们的 SQL 查询中多带几个其他字段:

```
curl -H 'Content-Type: application/json' -X POST localhost:9200/_opendistro/_sql -d '{"query" : "SELECT Origin, Horsepower, Miles_per_Gallon FROM cars LIMIT 5","filter" : {"range" :{"Weight_in_lbs" : {"lt" : 4000}}}}'
```

结果是:

```
{
        "took": 6,
        "timed_out": false,
        "_shards": {
                "total": 1,
                "successful": 1,
                "skipped": 0,
                "failed": 0
        },
        "hits": {
                "total": {
                        "value": 339,
                        "relation": "eq"
                },
                "max_score": 0.0,
                "hits": [{
                        "_index": "cars",
                        "_type": "_doc",
                        "_id": "182",
                        "_score": 0.0,
                        "_source": {
                                "Origin": "Europe",
                                "Horsepower": 70,
                                "Miles_per_Gallon": 29
                        }
                }, {
                        "_index": "cars",
                        "_type": "_doc",
                        "_id": "128",
                        "_score": 0.0,
                        "_source": {
                                "Origin": "USA",
                                "Horsepower": 150,
                                "Miles_per_Gallon": 15
                        }
                }, {
                        "_index": "cars",
                        "_type": "_doc",
                        "_id": "156",
                        "_score": 0.0,
                        "_source": {
                                "Origin": "Japan",
                                "Horsepower": 97,
                                "Miles_per_Gallon": 24
                        }
                }, {
                        "_index": "cars",
                        "_type": "_doc",
                        "_id": "170",
                        "_score": 0.0,
                        "_source": {
                                "Origin": "USA",
                                "Horsepower": 95,
                                "Miles_per_Gallon": 18
                        }
                }, {
                        "_index": "cars",
                        "_type": "_doc",
                        "_id": "57",
                        "_score": 0.0,
                        "_source": {
                                "Origin": "Europe",
                                "Horsepower": 90,
                                "Miles_per_Gallon": 28
                        }
                }]
        }
}
```

> **注意**:我们可以利用`filter`命令直接给 ES DSL 添加更多的条件。这个额外字段特性的示例查询和结果可以在 GitHub repo 这里的中找到[。](https://github.com/firebase007/Elasticsearch-Open-Distro/blob/master/queries/extraFields.js)

## 探索附加插件设置和选项

当 Elasticsearch 启动时，SQL 插件将在 ES 集群设置中注册一些设置。这些设置中的大部分都可以动态改变，因为这将使我们能够控制我们安装的插件的行为，而不需要改变我们的集群配置。

它们包括:

**1。)`opendistro.sql.enabled`**–我们可以启用或禁用 SQL 插件来拒绝所有到来的请求。请注意以下几点:

*   默认值为 true
*   此设置是节点范围的
*   该设置可以动态更新

我们可以用新值更新设置，如下所示:

```
curl -H 'Content-Type: application/json' -X PUT localhost:9200/_cluster/settings -d '{"transient" : {"opendistro.sql.enabled" : false}}'
```

结果如下:

```
{
        "acknowledged": true,
        "persistent": {},
        "transient": {
                "opendistro": {
                        "sql": {
                                "enabled": "false"
                        }
                }
        }
}
```

**2。)**`**opendistro.sql.query.analysis.enabled**`–我们还可以启用或禁用查询分析器来绕过严格的语法和语义分析。请注意以下几点:

*   默认值为 true
*   此设置是节点范围的
*   该设置只能动态更新

我们可以用一个新值来更新这个设置，如下所示:

```
curl -H 'Content-Type: application/json' -X PUT localhost:9200/_cluster/settings -d '{"transient" : {"opendistro.sql.query.analysis.enabled" : false}}'
```

结果:

```
{
        "acknowledged": true,
        "persistent": {},
        "transient": {
                "opendistro": {
                        "sql": {
                                "query": {
                                        "analysis": {
                                                "enabled": "false"
                                        }
                                }
                        }
                }
        }
}
```

### 插件监控

通过利用`/stats`端点，我们可以在特定的持续时间内收集插件的指标。请注意，目前仅支持每个节点级别的统计信息。本质上，我们只获得能够访问的节点的指标。让我们看看下面的例子——

```
curl -H 'Content-Type: application/json' -X GET localhost:9200/_opendistro/_sql/stats
```

结果:

```
{
        "failed_request_count_cb": 0,  
        "failed_request_count_cuserr": 0,
        "circuit_breaker": 0,
        "request_total": 12,
        "request_count": 0,
        "failed_request_count_syserr": 0
}
```

## 结论

从我们之前的教程中，我们了解到 Elasticsearch 是一个完全分布式的搜索和分析引擎。它也是无模式的，因为它支持结构化和非结构化数据。有了 Open Distro，已经熟悉 SQL 的用户现在可以与 Elasticsearch 集群交互，并执行与 DSL 类似的操作。

在本教程中，我们主要介绍了 SQL 特性，它提供了一种与其他 SQL 兼容系统集成的简单方法。但这只是冰山一角——要了解更多本教程中没有涉及的其他特性，请不要犹豫，查看 [awesome 文档](https://opendistro.github.io/for-elasticsearch-docs/),了解更多 Open Distro for ES 的其他功能。

最后，本教程的资源库可以在 Github 上的[处找到。](https://github.com/firebase007/Elasticsearch-Open-Distro)

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)