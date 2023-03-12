# PostgreSQL v14 中 JSON 的新特性

> 原文：<https://blog.logrocket.com/whats-new-json-postgresql-v14/>

PostgreSQL v14 最近发布了一些重大改进。其中一些改进包括针对繁重工作负载性能的增强、对分布式工作负载的改进支持以及安全性增强。

最激动人心的版本之一是增强了对 JSON 的支持，这将是这篇博文的重点。

在我们深入研究这些改进之前，让我们先了解一下 JSON 在 PostgreSQL 中是如何使用的。这将有助于我们更好地理解这些改进。

## PostgreSQL 中 JSON 的简史

JSON 最初是在 Postgres 的 v9.2 版本中引入的。虽然这是一个非常令人兴奋的发展，但它的实施并不完美。Postgres 基本上验证了要存储的 JSON 是有效的 JSON，并将其存储为文本字符串。

版本 9.4 中发布的 JSONB 类型带来了重大改进。这通常被称为“更好的 JSON”是有道理的。JSONB 以分解的二进制格式存储，这在存储时增加了一点转换开销，但在操作和查询 JSON 时非常有效。JSONB 还支持数据的索引。大多数人更喜欢在 Postgres 中使用 JSONB 而不是 JSON。

在 Postgres v12 中，增加了 JSONPath 来提高查询 JSON 数据的效率。

这就把我们带到了现在。现在让我们考虑一下 v14 对 JSON 的改进。

## v14 的 JSON 便利性

Postgres v14 允许您以常规方式访问和操作 JSON 数据。让我们用一些例子来解释这一点。

假设我们有一个博客文章的表，表中的数据列存储在 JSON 中。在本文中，我们将对所有示例使用 JSONB 类型:

```
CREATE TABLE blogs (
  id serial,
  data JSONB
)

```

我们还将一些测试值插入其中:

```
INSERT INTO 
  blogs (data)
VALUES 
 ('{"title": "blog one", "author": {"first_name": "Ada", "last_name": "Love"}}' ),
 ('{"title": "blog two", "author": {"first_name": "Star", "last_name": "Work"}}' );

```

这将产生下表:

```
SELECT *
 FROM blogs;

 id |                                     data                                     
----+------------------------------------------------------------------------------
  1 | {"title": "blog one", "author": {"last_name": "Love", "first_name": "Ada"}}
  2 | {"title": "blog two", "author": {"last_name": "Work", "first_name": "Star"}}
(2 rows)

```

让我们看看 v14 的改进。

### 使用下标访问 JSON 数据

在 Postgres 13 和更早的版本中，如果您想要查找作者名字为“Ada”的所有博客的标题，您可以执行以下操作:

```
SELECT data -> 'title' as title
FROM blogs 
WHERE data -> 'author' ->> 'first_name' = 'Ada' ;

   title    
------------
 "blog one"
(1 row)

```

请注意我们用来获取这些数据的操作符:

*   `->`用于获取从零开始索引的 JSON 数组元素或 JSON 对象字段的 by 键
*   `->>`用于获取 JSON 数组元素或 JSON 对象字段作为文本

虽然这种方法有效，但是记住这种语法并不容易。这是因为语法不同于访问 JSON 数据的传统方式。如果我们可以像我们习惯的那样使用下标访问 Postgres 中存储的 JSON 数据，会怎么样？这就是 Postgres v14 带给我们的。

让我们尝试重新提取上面得到的数据，但这次是 Postgres v14 方式，使用下标:

```
SELECT data['title'] as title
FROM blogs 
WHERE data['author']['first_name'] = '"Ada"';

   title    
------------
 "blog one"
(1 row)

```

注意，当使用下标进行比较时，必须使用 JSON 字符串。

### 使用订阅更新 json

通过订阅，更新 Postgres 中存储的 JSON 数据也变得更加容易。为了在 v13 和更早版本中更新 JSON，我们需要使用带有以下签名的`jsonb_set`函数:

```
 jsonb_set (target jsonb, path text[], new_value jsonb [, create_if_missing boolean ])

```

在这段代码中:
–`target`是要更新的 JSONB 列
–`path`表示要更新哪个 JSON 键
–`new_value`是要更新的项目的新值
–`create_if_missing`是一个选项参数，指定如果路径指定的键不存在，是否应该创建键/值

现在，让我们使用这个函数来更新上面例子中的数据列。例如，如果我们想用`id 1`更新博客作者的姓氏，我们这样做:

```
UPDATE blogs
SET data = jsonb_set(data, '{author, last_name}', '"Sarah"', false)
WHERE id = 1;

```

这将导致:

```
SELECT * FROM blogs;

 id |                                     data                                     
----+------------------------------------------------------------------------------
  2 | {"title": "blog two", "author": {"last_name": "Work", "first_name": "Star"}}
  1 | {"title": "blog one", "author": {"last_name": "Sarah", "first_name": "Ada"}}
(2 rows)

```

在 Postgres v14 中，我们不需要使用`jsonb_set`函数来更新 JSONB 数据。我们可以这样做:

```
UPDATE blogs                                                                       SET data['author']['first_name'] = '"Sarah"'                                       WHERE id = 2; 
//id is different in this case it updates a different row

```

这将导致:

```
select * from blogs;                                                                
id  |                                  data                                      
----+---------------------------------------------------------------------------------
  1 | {"title": "blog one", "author": {"last_name": "Sarah", "first_name": "Ada"}}
  2 | {"title": "blog two", "author": {"last_name": "Work", "first_name": "Sarah"}}
(2 rows)

```

第二行被更新。

#### 使用订阅更新 JSON 时需要注意的重要事项

通过订阅使用 JSON 赋值处理一些边缘情况与`jsonb_set`不同。让我们考虑其中的一些:

*   如果正在更新的 JSON 键值是`null`，那么通过下标的赋值将表现为键值是一个空对象或数组

因此，在上面的示例中，如果我们尝试用标签更新一行，而这些标签并不存在于任何一行中，如下所示:

```
UPDATE blogs SET
data['tags'] =' ["postgresql"] '
WHERE id = 1;

```

我们得到这样的结果:

```
SELECT *
FROM blogs
WHERE id = 1;
  id | data ----+-----------------------------------------------------------------------------------------------------
  1 | {"tags": ["postgresql"], "title": "blog one", "author": {"last*name": "Sarah", "first*name": "Ada"}}
(1 row)
```

`tags`总是被添加到行中。没有选项可以阻止它添加一个不存在的列，如`jsonb_set create_optional`参数。

*   如果为数组指定了一个索引，而数组包含的元素太少，则追加`null`直到到达该索引

因此，如果我们尝试用一个比数组的当前长度更长的索引来更新我们在上一个示例中添加的 tags 字段，如下所示:

```
UPDATE blogs                                     SET
data['tags'][4] =' "javascript" '              WHERE
id = 1;
```

我们得到这样的结果:

```
SELECT * 
FROM blogs 
WHERE id = 1;

 id |                                    data         
----+-------------------------------------------------------------------------------------------------------------------------------------
  1 | {"tags": ["postgresql", null, null, null, "javascript"], "title": "blog one", "author": {"last_name": "Love", "first_name": "Ada"}}
(1 row)

```

请注意，添加`null`直到达到指定的索引。

*   如果将一个 JSON 值赋给一个不存在的下标路径，并且要遍历的最后一个现有元素是一个对象或数组，那么将创建嵌套的数组或对象。然而，就像上面的例子一样，`null`将被添加，直到达到指定的索引，并且创建的对象或数组被放置

所以在我们的例子中，如果我们做了以下事情:

```
UPDATE blogs
SET data['otherdata'][3]['address'] =' "New York" '                              
WHERE id = 2;

```

我们得到以下结果:

```
 id |                data                                                         ----+--------------------------------------------------------------------------------------------------------------------------------------
  2 | {"title": "blog two", "author": {"last_name": "Work", "first_name": "Star"}, "otherdata": [null, null, null, {"address": "New York"}]}
(1 row)

```

您可以看到对象已创建。但是，`null`会被追加，直到达到索引。

## 结论

看到 Postgres 对 JSON 的支持在过去的几年里有了巨大的改进，真的很令人兴奋。在本文中，我们看到了 Postgres v14 中添加的 JSON 订阅是如何使访问和更新 JSON 变得容易的。我们还考虑了在使用下标时给 JSON 赋值时需要注意的一些问题。

这是否意味着订阅正在完全取代旧的访问 JSON 数据的方式？不一定。例如，我们看到，通过更新 JSON 值，如果列不存在，就无法阻止创建它。`jsonb_set`函数给了我们这个选项。此外，在使用订阅访问 JSON 数据时，并不总是利用索引。然而，对于不复杂的查询，使用订阅是很好的。

谁知道新版 Postgres 会带来哪些改进？使用下标时更好地利用索引？通过点符号访问 JSON？只有未来能告诉我们。

查看 JSON 订阅的上的[官方文档了解更多信息。](https://www.postgresql.org/docs/14/datatype-json.html#JSONB-SUBSCRIPTING)

您还可以在此找到 Postgres v14 的其他版本:

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)