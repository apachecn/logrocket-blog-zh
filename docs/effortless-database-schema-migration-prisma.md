# 使用 Prisma - LogRocket 博客轻松实现数据库模式迁移

> 原文：<https://blog.logrocket.com/effortless-database-schema-migration-prisma/>

数据库模式迁移是开发人员执行的最频繁和最重要的任务，尤其是随着产品需求的发展。如果这些迁移没有正确执行，可能会产生灾难性的后果，例如数据丢失和模式不一致，从而导致数据库状态不一致。

然而，它们的执行和管理非常繁琐。为了解决这个问题， [Prisma ORM](https://blog.logrocket.com/prisma-2-introduction/) 创建了一个名为 [Prisma Migrate](https://www.prisma.io/docs/concepts/components/prisma-migrate) 的工具，它提供了无缝执行和管理数据库模式迁移的机制。

在本文中，我们将深入探讨如何使用 Prisma ORM 执行数据库模式迁移。我们将从设计一个社交媒体应用模型的基本模式开始，然后对这个模式进行简单和复杂的修改。本质上，我们将看到 Prisma 的内置功能(称为 Prisma Migrate)如何使创建和管理数据库迁移变得尽可能简单。

### 先决条件

考虑到本文后面部分的实践内容，您应该掌握 Node.js 和 JavaScript 的工作知识。你还需要按照官方[网站](https://www.postgresql.org/download/)的指导，在本地安装和设置 PostgreSQL。

### 内容

## Prisma 是什么？

Prisma 是一个 ORM(对象关系映射器),它提供了对原始数据库查询和数据库管理功能的高级抽象。它为执行常见的数据库查询提供了类型安全的 API 和 JavaScript 类。

在本文的上下文中，Prisma 提供了一个健壮的迁移系统，称为 Prisma Migrate，它简化了创建和管理数据库模式迁移的过程。稍后我们将看到如何利用这个迁移系统，但在此之前，让我们从一般数据库的角度快速回顾一下数据库模式迁移。

## 什么是数据库模式迁移？

数据库模式迁移是管理对现有数据库模式所做的增量更改的一种方式，这通常是由于更改需求或修复初始设计错误。关系数据库中的这种更改包括添加或删除列和表，更改某些列的特定数据类型等。

我们通常使用一个迁移文件来执行模式迁移，该文件包含带有必要更改的 SQL 代码。手动管理迁移文件非常具有挑战性，尤其是当数据库包含真实的用户数据时。但是，Prisma 提供了一些机制，通过使用一种称为 Prisma Migrate 的特定功能，使这一过程不像通常那样令人畏惧和危险。

## 设置项目

至此，让我们建立一个项目，进入文章的核心部分。首先，转到合适的目录，在终端中运行以下命令:

```
npm init

```

上面的命令初始化目录并创建一个`package.json`文件。完成后，运行以下命令将 Prisma 包添加为 dev 依赖项:

```
npm install prisma --save-dev

```

然后，我们可以通过在终端中运行另一个命令来初始化 Prisma 项目:

```
npx prisma init

```

上面的命令通过创建一个名为`prisma`的目录和该目录中一个名为`schema.prisma`的文件来初始化一个 Prisma 项目。这个文件是大部分工作将会发生的地方，我们一会儿就会谈到它。

接下来，我们将创建一个简单的社交媒体应用程序的模型架构；该模式将作为进行增量更改的基础，模拟我们在真实场景中通常会遇到的情况。

应该再次提到的是，尽管这个模式非常基础，但它将帮助我们理解如何在 Prisma 中执行模式迁移。也就是说，继续将下面的代码粘贴到`schema.prisma`文件中，我们将在之后浏览它:

```
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url = env("DATABASE_URL")
}

model Users {
  id String @id @db.Uuid @default(uuid())
  fullName String
  email String @unique
  password String
  bio String

  @@map("users")
}

model Posts {
  id String @id @db.Uuid @default(uuid())
  userId String
  postId String
  content String

  @@map("posts")
}

model Followers {
  id String @id @db.Uuid @default(uuid())
  userId String
  followerId String

  @@map("followers")
}

model Likes {
  id String @id @db.Uuid @default(uuid())
  userId String
  postId String

  @@map("likes")
}

model Comments {
  id String @id @db.Uuid @default(uuid())
  userId String
  postId String
  content String

  @@map("comments")
}

```

上面的模式文件包含使用 Prisma 提供的特殊语法设计的应用程序的模式。模型直接映射到底层关系数据库中的表。

这种方法的一个主要优点是 Prisma 将数据建模限制在数据库层，不像其他 ORM 需要在应用程序级别进行某种级别的建模。上面的模式可能存在明显的设计问题，比如表之间没有关系；这是故意的。

在下面几节中，我们将看到如何通过简单的模式更改(添加列、更改数据类型和建立关系)到复杂的模式更改(使用数据库中的现有数据对模式进行更改)来解决这些问题。

要将模式应用于数据库，请在终端目录中运行以下命令:

```
npx prisma migrate dev --name init

```

> 当我们之前运行`npx prisma init`时，Prisma 生成了一个用于管理应用程序环境变量的`.env`文件。这个文件包含一个名为`DATABASE_URL`的环境变量。用 URL 替换其中的值，以连接到本地(或远程)Postgres 数据库连接。作为参考，我连接到我的本地数据库使用这个:
> `postgresql://postgres:[[email protected]](/cdn-cgi/l/email-protection):5433/prisma-migration?schema=public`

当该命令运行完毕时，我们应该能够看到数据库中实际存在的表和列。Prisma 还在与`schema.prisma`文件相同的目录中生成一个名为`migrations`的文件夹。如果我们打开新创建的迁移文件，我们应该能够看到 Prisma 自动生成的原始 SQL 语句。在幕后，Prisma 将`schema.prisma`文件的内容转换为原始 SQL 语句，并将其应用于数据库。

让我们继续对数据库模式进行更改。

## 应用简单的模式更新

接下来，我们将对模式进行相对简单的更改并应用它们。

### 向现有表中添加新列。

假设业务需求已经发生了变化，我们希望我们的应用程序的用户拥有唯一的用户名。要在模式级别实现这一更改，请转到`schema.prisma`文件，并在下面的代码片段中添加突出显示的行，我们将在以下内容之后浏览它:

```
model Users {
  ...
  username String @unique
  ...
}

```

在上面的代码片段中，我们向`Users`表添加了一个新列，并实施了一个惟一的约束，确保多个用户不能有相同的用户名。要应用这一更改，我们需要做的就是告诉 Prisma 将我们的更改与数据库同步；我们可以通过在终端中运行以下代码来轻松做到这一点:

```
npx prisma migrate dev --name added_username_column

```

### 添加关系

现在让我们来看看对我们的模式的一个更复杂的更改。很明显，由于“糟糕的设计”，我们的模式中的表之间没有关系，所以让我们看看如何进行这样的更改。让我们在`Users`表和`Posts`表之间定义一个一对多的关系，这样一个用户可以有很多帖子。更改模式以实现这一点:

```
model Users {
  id String @id @db.Uuid @default(uuid())
  fullName String
  username String @unique
  email String @unique
  password String
  bio String
  posts Posts[]

  @@map("users")
}

model Posts {
  id String @id @db.Uuid @default(uuid())
  postId String
  content String
  user Users @relation(fields: [userId], references: [id])
  userId String // (used in the `@relation` attribute above)

  @@map("posts")
}

```

同样，我们需要做的只是在终端中运行以下命令，Prisma Migrate 会自动将这些更改与数据库同步:

```
npx prisma migrate dev --name connect_users_posts_tables

```

这就是使用 Prisma Migrate 应用模式更新的简单性。然而，有一些重要的事情需要注意。Prisma 应用迁移文件后，不得手动编辑或删除它们。这样做可能导致不一致，迫使 Prisma 给出提示，要求执行数据库重置，这可能导致数据丢失。总之，我们可能会以迁移文件版本之间不匹配的历史记录而告终。另外，`migrate dev`应该只在开发环境中运行，因为如果在生产环境中执行，这可能会导致问题。

## 应用复杂的模式更新

在过去的两节中，我们已经了解了如何对我们的模式进行更改，以及 Prisma 如何轻松地做到这一点。然而，我们只是在数据库中没有任何数据的情况下检查了这一点。当我们有数据时，事情变得有点棘手，因为错误的更改会导致数据库中的数据丢失或数据不一致。在这一节中，我们将简要研究如何在这种情况下将更改应用到我们的模式中。

第一步是向应用程序添加一个或多个用户，手动添加一些用户或运行以下 SQL:

```
INSERT INTO users (id, "fullName", email, password, bio, username)
VALUES ('7f93880a-a8c3-4d4f-b3b3-369aa89a73fa', 'John Doe', '[email protected]', 'johndoe', 'Im a software developer', 'johndoe');

```

完成后，让我们进一步说，我们决定将用户表中的 bio 列重命名为`biography`；我们似乎可以通过简单地改变`prisma.schema`文件中的`User`模型来做到这一点，如下所示:

```
model Users {
  ...
  biography String
  ...
}

```

现在，如果我们尝试使用 Prisma Migrate 提交此更改，我们将得到如下错误:

```
Error: ⚠️ We found changes that cannot be executed: Step 0 Added the required column biography to the users table without a default value. There are 1 rows in this table, it is not possible to execute this step.
You can use prisma migrate dev --create-only to create the migration file, and manually modify it to address the underlying issue(s). Then run prisma migrate dev to apply it and verify it works.

```

这主要是因为我们在`users`表中有一行或多行，Prisma 产生的迁移具有潜在的破坏性。为了防止这种情况，Prisma 提示我们创建迁移文件，而不立即将其同步到数据库。我们可以通过运行以下命令来实现:

```
npx prisma migrate dev --name update-biography --create-only

```

这样，会生成一个迁移文件，但不会立即应用。让我们来看一看；打开迁移文件，它应该如下所示:

```
ALTER TABLE "users" DROP COLUMN "bio",
ADD COLUMN "biography" TEXT NOT NULL;

```

为什么这是一个问题可能是显而易见的——上面的 SQL 删除了`bio`列，然后添加了一个名为`biography`的新列，但是至少有一行在该列上有值，这意味着我们将丢失该列中的所有数据。因此，Prisma 抛出一个警告，并允许我们手动更新文件。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在这种情况下，要解决这个问题，我们可以很容易地将迁移文件中的 SQL 更新或重写为:

```
ALTER TABLE "users" RENAME COLUMN "bio" TO "biography";

```

新的 SQL 语句允许我们在不丢失数据的情况下重命名列。这样，我们需要做的就是告诉 Prisma 通过运行通常的:

```
npx prisma migrate dev

```

瞧，我们更新的模式文件与数据库同步。

## Prisma 迁移的局限性

Prisma Migrate 目前不支持 MongoDB 数据库提供程序；如果您打算使用 MongoDB，这只是一个限制。

在开发环境中，Prisma Migrate 有时会发送重置数据库的提示；不幸的是，这会导致开发环境中的数据丢失。如果我们有种子文件，这不会是一个大问题，因为数据库可以用数据重新播种。需要注意的是，这种重置数据库的提示不会在生产环境中出现。

最后，Prisma Migrate 不允许我们将迁移应用到`schema.prisma`文件中指定的不同数据库提供者，也就是说，如果我们在开发环境中为 PostgreSQL 提供者创建迁移文件，我们就不能将该迁移应用到生产环境中的 MySQL 提供者。

## 结论

在本文中，我们介绍了使用 Prisma Migrate 执行模式迁移的过程。我们通过建模一个简单的社交媒体平台，对模式进行增量更改，并使用 Prisma Migrate 提供的功能来自动创建和应用迁移。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.