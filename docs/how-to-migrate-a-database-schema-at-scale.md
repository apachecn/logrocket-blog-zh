# 如何大规模迁移数据库模式

> 原文：<https://blog.logrocket.com/how-to-migrate-a-database-schema-at-scale/>

任何重要的应用程序都需要一个随时间发展的数据库来支持新特性或解决查询的伸缩问题。在控制台中手动更改数据库模式可能没问题，但前提是您的 web 应用程序非常小，处于测试阶段，受众非常少，或者是一个业余爱好项目。在任何其他情况下，您都应该编写迁移。

## 什么是迁移

迁移是更新数据库模式的小段代码。因为迁移是代码，所以它们可以在不同的环境中以自动化的方式重现，也可以用不同的方式进行测试。通常，您会使用工具来创建和运行迁移。该工具将提供一个命令行界面，用于开始编写新的迁移代码、运行特定的迁移、回滚迁移或在您当前的设置/环境中运行所有待定的迁移。这些工具通常包含在您使用的 ORM 或数据库框架中。例如，你有用于 Ruby on Rails 、 [Sequelize](https://sequelize.org/master/manual/migrations.html) 、 [knex.js](http://knexjs.org/#Migrations) 或 [Prisma/Lift](https://lift.prisma.io/) 的工具。

## 我们应该如何运行这些迁移

在小型应用程序中，您可以决定在运行应用程序之前只运行迁移。我参与过许多项目，这些项目都有这样的 Node.js 脚本:

```
"start": "./run-migrations && node app.js"
```

即使数据库非常大，以至于迁移需要一段时间才能运行，如果您有一个设置，在新部署启动并运行之前，以前的部署一直为请求提供服务，这也是可以的。但是，如果先前的部署不能处理新的数据库更改，该怎么办呢？

例如，如果您重命名了一个列，或者添加了一个在旧代码中会在查询中产生歧义的列，该怎么办？新的部署可能需要一段时间来处理所有的请求，如果代码试图用旧的列名来处理这些请求，将会产生错误。您可以决定暂时关闭服务，然后运行迁移并重新建立服务。

然而，这将会惹恼使用该服务的用户，所以你应该安排一个维护窗口，并通过电子邮件告诉你的用户，或者在你的网站上放一个警告，等等。值得努力吗？有没有更好的办法？

这个问题有一个解决方案，可以让你的服务保持正常运行，而你的用户不会注意到任何事情。这是基于我认为的关于迁移的黄金法则——当迁移一个数据库时，**当前的代码必须与先前的和新的数据库模式都很好地工作**。

我们如何保证这一点？有两种方法:

*   修改数据库模式的部署不能包含对应用程序代码的更改
*   在您的配置项中，在运行迁移后运行测试

通过这种方式，您可以在迁移前后测试您的代码。您在之后测试*，因为您在您的 CI 中显式地这样做，在*之前测试*，因为如果您没有更改任何应用程序代码，这是真的，因为代码没有更改，您也没有更改任何测试，所以当前代码已经在之前的部署中使用旧的数据库模式进行了测试。*

## 回填数据

以这种方式进行迁移还带来了另一个好处。您可以将迁移的运行从部署过程中分离出来，这允许您进行长期运行的迁移。例如，如果您需要回填数据。

如果创建新的列或表来对某些数据或计算进行反规范化，则可能需要迭代所有记录来计算现有记录的值，这意味着某些数据库更改需要模式迁移和部署的多步骤过程。

例如，假设您有一个日期为`created_at`和`sold_at`的商品，您想在您的应用程序中添加一个新的特性，以显示并能够根据商品的库存时间对其进行排序。如果只显示时间量而不排序，您可以在呈现 UI 时进行计算。但是，如果您想按该标准排序，您会希望数据已经在数据库中的新列中进行了计算，并对其进行索引。

因此，首先，您将在迁移中创建一个新列和索引。下面是一个使用 knex 的例子:

```
exports.up = function(knex, Promise) {
  return knex.schema.table('items', function(t) {
    t.integer('time_in_stock')
    t.index(['time_in_stock'])
  })
}

exports.down = function(knex, Promise) {
  return knex.schema.table('items', function(t) {
    t.dropIndex(['time_in_stock'])
    t.dropColumn('time_in_stock')
  })
}
```

然后，您将更改您的应用程序代码，以便在出售新商品时计算时间:

```
function soldItem(item) {
  const now = new Date()
  item.update({
    sold_at: now,
    time_in_stock: now - item.created_at // new calculation
  })
}
```

接下来，您将编写并运行一个迁移来计算在您进行开始计算新列的更改之前现有记录的销售值:

```
exports.up = function(knex, Promise) {
  return knex.raw('UPDATE items SET time_in_stock = UNIX_TIMESTAMP(sold_at) - UNIX_TIMESTAMP(created_at) WHERE time_in_stock IS NULL')
}
```

最后，您将能够添加应用程序代码来显示计算结果，并让您的用户根据它们进行排序。根据您的情况(与销售商品的例子不同),一旦数据库中的所有记录都被回填，您可能还想通过另一次迁移向新列添加一个 NOT NULL 约束。

## 其他示例

让我们看看其他一些需要多步骤过程的例子。

### 删除表或列

首先，您必须停止写入该表或列:

```
item.update({
  a_column: a_value
  // legacy_column: value // Stop writing to the column
})
```

然后，您将能够编写一个迁移，将它从数据库中物理删除:

```
exports.up = function(knex, Promise) {
  return knex.schema.table('items', function(t) {
    t.dropColumn('legacy_column')
  })
}
```

如果您想要删除一个具有 NOT NULL 约束的列，您还需要第一次迁移来删除该约束。否则，你将无法停止给那个专栏写信。

### 将数据从一列或表移动到另一列或表

最简单的例子是在同一个表中将数据从一列移动到另一列。您可能希望这样做，只是因为新的数据类型不同，或者只是名称不同。让我们看看所有涉及的步骤。首先，您需要创建新的列或表:

```
exports.up = function(knex, Promise) {
  return knex.schema.table('items', function(t) {
    t.integer('new_column')
  })
}

exports.down = function(knex, Promise) {
  return knex.schema.table('items', function(t) {
    t.dropColumn('new_column')
  })
}
```

然后，您需要更改应用程序，开始将信息写入这两列:

```
item.update({
  legacy_column: value,
  new_column: value // Start writing to both columns
})
```

然后，您将进行迁移以回填新列，以便旧记录在两列中都有正确的值:

```
exports.up = function(knex, Promise) {
  return knex.raw('UPDATE items SET new_column = legacy_column WHERE new_column IS NULL')
}
```

然后，您将更改应用程序代码，只从新列开始读写:

```
// No longer writing to the legacy column
item.update({ new_column: value })
```

最后，您将能够删除旧的列或表:

```
exports.up = function(knex, Promise) {
  return knex.schema.table('items', function(t) {
    t.dropColumn('legacy_column')
  })
}
```

## 长期运行迁移的注意事项

如果您有大量需要修改或回填的数据，最好不要在一条 SQL 语句中完成，因为您可能会锁定您的表，服务将停止工作或工作不佳。最好查询一些需要更新的记录，然后只在那些记录中运行 SQL 语句。也许做一些类似`UPDATE ... WHERE id IN (…list of ids from previous query)`的事情。您将在一个循环中这样做，首先查询一些记录，然后更新它们。

您将需要抑制更新，因为否则您将在短时间内触发许多写操作，这也可能对您的服务的健全性产生不良影响。

这种实现长期运行迁移的方式还允许您随时停止和恢复迁移。这尤其有用，因为像这样的迁移可能会在某些记录中遇到问题。如果是这种情况，这个策略允许您修复迁移并重新运行它。

根据我的经验，您经常会创建一个新列并希望对其进行回填，但有时您希望用其进行回填的计算会返回 null，这可能是因为非常旧的记录没有进行计算所需的所有信息，并且在更新行时，您会因为 NOT NULL 约束而出错。在这些情况下，您可以默认为一个特定值或删除约束，具体取决于您的情况。

## 结论

在不停机从而不中断用户的情况下发展数据库模式可能需要很多步骤，但我发现这比关闭服务器和争分夺秒地运行更好、压力更小、也更安全。常见的情况是，一些应用程序得到更新，然后需要在开始时进行一些清理，让用户等待，这也是一种糟糕的用户体验。

这些最佳实践不仅适用于 web 应用程序，也适用于具有嵌入式数据库的移动应用程序。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)