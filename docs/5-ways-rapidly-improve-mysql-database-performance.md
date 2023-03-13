# 快速提高 MySQL 数据库性能的 5 种方法

> 原文：<https://blog.logrocket.com/5-ways-rapidly-improve-mysql-database-performance/>

每个使用 MySQL 的开发人员都知道 RDBMS 对他们的项目有多重要。数据库管理系统可以支持各种项目，从游戏论坛到医疗保健解决方案。根据 DatabaseJournal 进行的[研究，它占据了数据库市场份额的近一半，即 44%。](https://www.databasejournal.com/mysql/postgresql-vs-mysql-which-is-best/)

提高 MySQL 的性能也是每个 DBA 在职业生涯的某个阶段都在努力解决的问题。别担心，我们是来帮忙的。在本文中，我们将介绍五种快速提高 MySQL 数据库性能的方法。我们开始吧！

## MySQL 和您的应用程序

在尝试提高 MySQL 应用程序的性能之前，首先要考虑的事情之一是支持您的应用程序的[基础设施。没有一个系统是在不了解它是建立在什么基础上的情况下被改进的。出于这个原因，我们需要后退一步，检查一下支持 MySQL 的服务器。](https://blog.logrocket.com/build-rest-api-node-express-mysql/)

我们首先要检查两件基本的事情，从服务器上安装的内存量开始。您可以通过发出一个`free`命令来观察这一点。其次，您可以发出`df"`命令来观察服务器上的硬盘空间量。

记住这些事情，你就可以连接 MySQL 了。首先，您需要使用`SHOW PROCESSLIST`命令检查是否有任何不必要的查询正在运行。您应该会收到如下所示的输出:

![Running Queries List Mysql](img/0b05ad3bd068ef2740cce3efcf6baa9a.png)

如果您看到任何您不认识的长时间运行的查询，终止它们是一个好主意；长时间运行的查询可能会成为其他查询的障碍。然而，您可能看不到任何可疑的查询。即使您这样做了，终止一两个缓慢的查询也不会迅速提高数据库性能。

为了提高查询速度，您需要对查询和 MySQL 的一般工作原理有所了解。

## 总体提高数据库性能

在介绍有助于提高 MySQL 数据库性能的具体措施之前，您需要了解数据库性能的基础知识。

就数据库性能改进而言，人们通常谈论的是改进 CRUD、`Create`、`Read`、`Update`和`Delete`查询的性能。在 MySQL 中，这些查询跨越了`INSERT`、`SELECT`、`UPDATE`和`DELETE`查询。

MySQL 中的所有查询都依赖于与 MySQL 相关的一个核心文件`my.cnf`中定义的设置。在`my.cnf`中定义的所有设置都对查询性能有直接影响。

通常可以通过从插入数据的表中删除索引来提高`INSERT`查询的性能。特定表上的索引越多，`INSERT`就越难继续。

为了提高查询性能，我们通常使用索引。为了提高查询性能，我们分批执行更新，这意味着我们执行许多较小的更新，而不是一个大的更新。

为了提高`DELETE`查询的性能，我们将`DELETE`查询切换到`TRUNCATE`。`TRUNCATE`删除表格中的所有行。这样的查询通常比使用`DELETE`删除行要快得多，因为`TRUNCATE`为 MySQL 提供了更少的开销。

当试图理解为什么一个基于 MySQL 的数据库在性能方面表现不佳时，上面给出的建议无疑是一个很好的起点。

然而，为了理解上述假设背后的原因，我们需要更深入地探究。我建议备份你的数据库，然后回到这个博客。现在，我们将回顾有助于快速提高数据库性能的五种方法。

## #1:管理`my.cnf`文件

当试图提高 MySQL 查询性能时，首先要仔细研究的是`my.cnf`文件，它保存了 MySQL 运行所需的所有参数。如果您使用的是 Linux，您可以在以下目录中找到`my.cnf`文件:

*   `/var/lib/mysql/my.cnf`
*   `/etc/my.cnf`
*   `/etc/mysql/my.cnf`
*   `/usr/etc/my.cnf`

如果您使用的是 Windows，您可以在`/bin/mysql/mysql *.*.*`目录中找到该文件。

`mysql *.``*.*``*`指你的 MySQL 版本。打开文件，查找 InnoDB 周围的参数:

![My CNF Parameters Example](img/f88d83ec57a50cbc65732c8392630b1c.png)

所有这些参数都与 MySQL 中的一个主要存储引擎 InnoDB 相关。您可以使用其他存储引擎，但是由于 InnoDB 是 MySQL 提供的默认存储引擎，我们建议您使用它。

### `my.cnf`参数

让我们回顾一下参数。`innodb-buffer-pool-size`参数定义缓冲池的大小，该缓冲池用于缓存与 InnoDB 表相关的数据。`innodb-data-file-path`参数指定了存储`ibdata1`文件的路径。`ibdata1`是与 InnoDB 相关的主文件，存储所有必需的数据。

`innodb-default-row-format`指定 InnoDB 表格中的行格式。这些可以是固定的，也可以是动态的。`innodb-doublewrite`指定是否启用 InnoDB 中的双写机制。

`innodb-flush-log-at-trx-commit`指定当事务提交和完成时，如何将数据刷新到日志文件。`innodb-flush-method`参数定义了将数据刷新到日志文件的方法。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 设置`my.cnf`参数

还记得您是如何计算出基础架构中可用的 RAM 和硬盘空间的吗？现在是时候利用这些细节来获得最佳性能了。我们将如下设置参数。

`innodb-buffer-pool-size`参数应设置为可用 RAM 的 50%到 60%。它越大，缓存的数据就越多，因此插入数据的速度就越快。

增加`innodb-data-file-path`变量的大小，以便它能够容纳 MySQL 中的所有数据。我们建议将该参数设置为 5-10 GB。

如果参数不存在，包括一个`innodb-file-per-table`参数并将其设置为`one`。`innodb-file-per-table`参数将帮助 MySQL 理解它需要将所有表存储为单独的文件，从而使缓冲池的大小显著变小。缓冲池将只保存元数据。

我们建议保留`innodb-flush-log-at-trx-commit`参数的默认值。默认值保证了 ACID 合规性，但是，如果您想要更快的写入性能，您也可以考虑将该值更改为`0`或`2`。请记住，ACID(保证数据完整性的属性)将会作为一个结果被放弃。

保持冲洗方法不变。由于 Linux 内核避免了操作系统缓存，因此`O_DIRECT`刷新方法保证了导入数据时更快的性能。

即使服务器的 RAM 和存储空间有限，执行上述步骤也能保证更快的性能。

## #2 和#3:检查 MySQL 存储引擎和模式设计

除了摆弄`my.cnf`文件，我们还应该检查我们使用的存储引擎以及它们的设计方式。如果你用的是 MySQL，就用 InnoDB。如果你使用的是 [Percona 服务器](https://www.percona.com/software/mysql-database/percona-server)，使用 [Percona XtraDB](https://www.percona.com/software/mysql-database/percona-xtradb-cluster) 。

### InnoDB 参数

在撰写本文时，InnoDB 是唯一支持 ACID 属性的存储引擎。这些属性保证了数据的完整性，即使在停电或任何类似的中断事件。如前所述，酸可以通过将`innodb-flush-log-at-trx-commit`参数设置为`0`或`2`来换取速度。

InnoDB 提供了多个参数，可以用来快速提高查询性能和其他操作，包括`innodb-buffer-pool-size`和`innodb-log-file-size`。

将缓冲池大小设置为基础设施中可用 RAM 的 60 %,将日志文件大小设置为分配给缓冲池的值的大约四分之一。MySQL 在 InnoDB 中恢复数据时会扫描日志文件。大小越大，恢复过程的速度越快。

InnoDB 和 XtraDB 都支持行级锁定。简单地说，行级锁定指的是只锁定对直接受事务影响的行的访问。与表级锁定相比，它有一个显著的优点；开发人员可以在更新数据时继续使用行。

如果您的用例不需要这种方法，您应该避免使用除 InnoDB 之外的任何其他存储引擎。MyISAM 不可靠，其他存储引擎只能在特定的情况下使用。有关更多信息，[请参考 MySQL 文档](https://dev.mysql.com/doc/refman/8.0/en/storage-engines.html)。

如果您正在处理的数据超过 1000 万行，则您的所有表格都将被规范化。

### 索引查询

您运行`SELECT`查询的表中至少有一些列被索引。为了获得最佳结果，要么索引所有跟在`WHERE`子句后面的列，要么索引第一列以节省空间。这种方法将提高读取数据的查询的性能，因为索引将让 MySQL 知道如何快速找到具有特定值的列。

### 数据类型和整数

了解数据类型和字符集非常重要。为了在磁盘上占用更少的空间，你应该使用`CHAR`(字符)或`VARCHAR`(可变字符)数据类型，而不是`TEXT– CHAR`和`VARCHAR`数据类型。整数也一样。如有必要，考虑使用`SMALLINT`代替`INT`，以节省硬盘空间。

正确指定数据类型的长度。在处理大数据时，考虑将大小指定为 50，而不是最大值 255。这种方法将节省大量的磁盘空间。

确保您的表不存储任何不必要的数据。存储的数据越少，需要读取和更新的数据就越少。

## #4 和#5:索引和分区

除了上述因素之外，索引和分区也非常重要。索引帮助我们快速找到具有特定值的行，而分区充当表中的表，以进一步提高性能。

这两种方法都伴随着对`INSERT`、`UPDATE`和`DELETE`查询的开销，因为插入或更新的数据需要在索引或分区内部更新。

然而，这两种方法都有好处；它们都加快了读取操作。分区使查询更快，因为它们可以将表分割成以某个字符开始的更小的表，只通过它们运行查询。另一方面，索引的工作是使带有`WHERE`子句的`SELECT`查询更快。

### 索引的最佳实践

索引和分区都有多种不同的类型。出于本文的目的，我们不会全部讨论它们，但是对于索引，请记住以下几点。

最常见的索引类型，B 树索引，在与带有包含等号`=`的操作符的查询结合使用时非常有用。

覆盖索引覆盖特定查询使用的所有列。例如，列`a1`、`a2`和`a3`的覆盖索引将满足以下查询:

```
SELECT * FROM demo WHERE a1 = 'Demo' AND a2 = 'Demo 2' AND a3 = 'Demo 3';

```

哈希索引只在 MySQL 内部特定的存储引擎和特定的搜索操作符中起作用，包括`=`和`<=>`。

### MySQL 中的分区

对于分区，请记住它们也有多种风格。

通过`RANGE`进行分区，我们可以对给定范围内的值进行分区。当按字符或数字分割大型表时，这种类型的分区特别有用。

通过`HASH`进行分区，根据列数将表分成多个表。例如，`PARTITION BY HASH(id) PARTITIONS 8;`会在数据库级别将表分成多个不同的表，总共有八个分区。

所有类型的分区都可以在 MySQL 文档中找到。分区通常是在创建表时定义的，在许多情况下，它看起来像下面这样:

```
CREATE TABLE table_name (
[column_details]
) [partitioning_details];
Partitioning by range, for example, would look like this:
CREATE TABLE table_name (
`demo_column` VARCHAR(255) NOT NULL DEFAULT ''
) PARTITION BY RANGE (column) (
PARTITION p1 VALUES LESS THAN (100),
PARTITION p2 VALUES LESS THAN (200)
);

```

其他类型的分区看起来与上面定义的分区非常相似。然而，由`RANGE`划分的分区被替换为`LIST`、`HASH`或其他类型。

分区还有另一个非常重要的好处。它允许用户删除存储在单个分区中的所有数据；会成功的。

## 高级操作:提示和技巧

索引和分区都将极大地有助于改进读操作，但是我们还需要记住一些其他的事情。

只有在使用 MyISAM 存储引擎时，查询才会很快。更快的`COUNT(*)`查询是 MyISAM 存储引擎的唯一优势，因为它将行数存储在其元数据中。没有其他存储引擎能做到这一点。

对于使用通配符的更快的`SELECT`查询，只在搜索查询的末尾使用通配符。查询应该类似于下面的代码；请记住，字符串的开头没有通配符符号:

```
SELECT * FROM demo_table WHERE column LIKE 'string%';

```

字符串开头的通配符告诉 MySQL 应该搜索开头的任何内容`%`，这会降低查询速度。

索引帮助我们确保一列中的每个条目都是唯一的。如果不是这样，MySQL 就会出错。

如果我们想在插入数据或执行其他操作时忽略错误，关键字`IGNORE`很有用。只需在语句中指定`IGNORE`，然后照常进行:

```
INSERT IGNORE INTO demo_table (c1) VALUES ('Demo');

```

`LOAD DATA INFILE and SELECT … INTO OUTFILE`比发出`INSERT`查询和定期备份数据要快得多。这种查询避免了运行`INSERT`查询时存在的大量开销。[参考 MySQL 文档](https://dev.mysql.com/doc/refman/8.0/en/load-data.html)了解更多信息。

当我们搜索任何带有`@`符号的内容时，旧版本的 MySQL 无法处理更大数据集上的`FULLTEXT`索引。那是 MySQL 内部的一个 bug，`BUG#104263`。这种方法会导致查询超时。

避免在运行大数据集的表上发出`ALTER`查询。由于`ALTER`的内部工作方式，它迫使 MySQL 创建一个新表，然后将数据插入其中，进行必要的更改，并用副本交换原始表。就大数据集而言，这种方法通常需要很长时间，所以请记住这一点。

有时，使用`DEFAULT`关键字一次为许多行设置默认值会很有帮助。想象一下，创建一个表，然后向其中插入十亿行。当使用`DEFAULT`关键字时，将使用特定的关键字预填充行，从而避免了如上所述的可能有问题的`ALTER`查询的需要。按如下方式定义列:

```
`column_name` VARCHAR(255) NOT NULL DEFAULT 'value';

```

希望本文中的建议能够帮助您提高 MySQL 数据库的性能。然而，和所有事情一样，请注意它也有不好的一面。

## 每种方法的缺点

使用上述方法提高 MySQL 性能可能会带来以下缺点。

检查`my.cnf`需要一些 Linux 内部知识，并且在许多情况下，需要一个相当强大的服务器。如果你的内存被限制在 256MB，或者你总共只有 2GB 的磁盘空间，你就不能提高太多的性能。

熟悉`my.cnf`和存储引擎并修改它们的设置通常需要对 MySQL 领域有深入的了解。人们需要确切地知道每个被修改的参数是做什么的，它们的合适值是什么，等等。

Windows 用户很轻松，因为`my.ini`(相当于`my.cnf`)本身为他们提供了许多注释，但是 Linux 用户通常必须自己定义许多设置。

数据类型和字符集的主要缺点是每个字符都需要磁盘上的空间，并且有些字符集对存储空间有不同的要求。如果我们处理大型数据集，每个字符四个字节或八个字节肯定会有所不同，所以这也是需要考虑的事情。更多信息请参考 MySQL 文档。

索引和分区通常会加速`SELECT`操作，代价是减慢其他所有操作，包括`INSERT`、`UPDATE`和`DELETE`，因为所有这些查询都必须插入、更新或删除索引和分区中的数据。

## 结论

在本文中，我们讨论了快速提高 MySQL 数据库性能的五种方法。每种方法都有其独特的优点和缺点，适用于不同的场景。然而，是否利大于弊由你来决定。

请[熟悉您选择的](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html)存储引擎的相关文档。使用 InnoDB 或 XtraDB 作为您的存储引擎，尽最大努力规范化您正在使用的表，避免为您的数据类型使用不必要的大小，并索引您的列以加快`SELECT`查询。

索引和分区用于加速`SELECT`查询，代价是减慢`INSERT`、`UPDATE`和`DELETE`。这两种方法都有多种类型，如果使用得当，会非常有用。

与往常一样，在尝试使用本文中描述的一种或多种方法来提高 MySQL 应用程序的性能之前，请确保评估所有可用的选项，在测试任何东西之前进行备份，并首先在本地环境中尝试所有修改。明智地进行修改，并始终记住，一个地方的性能提高很可能意味着另一个地方的性能下降。

我希望你喜欢这篇文章。如果您有任何问题，请务必留下评论，祝您编码愉快。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)