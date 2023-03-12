# 阿波罗客户端 3 的新功能-日志火箭博客

> 原文：<https://blog.logrocket.com/whats-new-in-apollo-client-3/>

7 月中旬发布的 Apollo Client 3 提供了一些新特性，包括包重新排列的变化和更多的缓存特性。让我们来看看如何使用 Apollo Client 3 的最新特性。

## `InMemoryCache`原料药

`InMemoryCache` API 具有扩展的特性。它们包括对象和字段的驱逐、垃圾收集、类型和字段配置以及分页助手。

让我们通过运行以下命令安装`@apollo/client`包及其依赖项来研究这些变化:

```
npm i @apollo/client graphql react
```

我们可以通过编写以下代码将`InMemoryCache`添加到我们的 Apollo 客户端:

```
import { ApolloClient, InMemoryCache, gql } from "@apollo/client";

const cache = new InMemoryCache();

const client = new ApolloClient({
  uri: "https://graphqlzero.almansi.me/api",
  cache
});

client
  .query({
    query: gql`
      {
        user(id: 1) {
          id
          name
        }
      }
    `
  })
  .then(console.log);
```

`client`是用`cache`选项创建的，我们将其设置为`InMemoryCache`；缓存的项目将在内存中。一旦我们完成了这些，我们就可以使用 Apollo Client 3 附带的新的`InMemoryCache`特性。

我们可以通过调用以下命令来驱逐缓存的项目:

```
cache.evict();
```

我们可以有选择地通过写入以下内容来传入缓存对象的 ID:

```
cache.evict({ id: 'user' })
```

我们还可以像这样添加对象的一个`field`属性:

```
cache.evict({ id: 'user', fieldName: 'name'  })
```

`cache.gc`方法让我们对缓存的项目进行垃圾收集。通过从根跟踪到所有子引用，确定该对象是可到达的。未被访问的规范化对象将被删除。

要清除无法访问的缓存项，我们只需调用:

```
cache.gc();
```

垃圾收集也可以配置为保留一些项目。例如，要保留 ID 为`'user'`的对象，我们可以写:

```
cache.retain({ id: 'user' })
```

我们可以配置如何处理悬空引用。当一个对象从缓存中被逐出时，它可能拥有包含其他缓存对象的对象。Apollo 客户机保留了这些引用，因为以后可能还会用到它们。

我们可以通过使用定制的`read`函数来改变这些引用的处理方式。为此，我们将编写:

```
import { ApolloClient, InMemoryCache, gql } from "@apollo/client";

const cache = new InMemoryCache({
  typePolicies: {
    Query: {
      fields: {
        ruler(existingRuler, { canRead, toReference }) {
          return canRead(existingRuler)
            ? existingRuler
            : toReference({
                __typename: "user",
                name: "Apollo"
              });
        }
      }
    },

    user: {
      keyFields: ["name"],
      fields: {
        offspring(existingOffspring, { canRead }) {
          return existingOffspring ? existingOffspring.filter(canRead) : [];
        }
      }
    }
  }
});

const client = new ApolloClient({
  uri: "https://graphqlzero.almansi.me/api",
  cache
});

client
  .query({
    query: gql`
      {
        user(id: 1) {
          id
          name
        }
      }
    `
  })
  .then(console.log);
```

我们将缓存的标尺设置为我们自己的`ruler`函数。我们决定哪些参考文献可以阅读。

如果有一个现有的缓存标尺，那么我们使用它；否则，我们得到带有`toReference`的项目。`offspring`方法返回对象，其中`canRead`返回`true`。这样，我们知道我们可以阅读这些项目。

## 管理本地状态

我们可以在`InMemoryCache`对象中创建自己的本地字段。

例如，我们可以写:

```
import { ApolloClient, InMemoryCache, gql } from "@apollo/client";

const cache = new InMemoryCache({
  typePolicies: {
    User: {
      fields: {
        age: {
          read(_, { variables }) {
            return Math.random() * 100;
          }
        }
      }
    }
  }
});
```

我们创建了一个名为`age`的本地字段。这样，我们可以在查询中包含字段，如`loading`州和`networkStatus`。`variables`从查询中得到字段。它也有缓存数据。

它只是一个返回随机数的 getter:

```
import { ApolloClient, InMemoryCache, gql } from "@apollo/client";

const cache = new InMemoryCache({
  typePolicies: {
    User: {
      fields: {
        age: {
          read(_, { variables }) {
            return Math.random() * 100;
          }
        }
      }
    }
  }
});

const client = new ApolloClient({
  uri: "https://graphqlzero.almansi.me/api",
  cache
});

client
  .query({
    query: gql`
      {
        user(id: 1) {
          id
          name
          age @client
        }
      }
    `
  })
  .then(console.log);
```

我们用`age @client`得到`age`字段。`@client`关键字将本地字段与从 API 中检索的字段区分开来。

反应变量是 Apollo 客户端 3.0 以后的新特性。为了创建一个，我们使用来自`@apollo/client`包的`makeVar`方法。例如，要创建一个`children`反应变量，我们可以写:

```
import { makeVar } from "@apollo/client";

const children = makeVar(["jane", "mary"]);
```

它返回一个具有反应变量的值的函数。要调用它并获取值，我们可以写:

```
console.log(children());
```

控制台日志应如下所示:

```
["jane", "mary"]
```

反应变量对于在 Apollo 客户端缓存之外存储本地状态非常有用。这不同于本地状态和缓存项，后者是从缓存中检索的。修改反应变量会自动触发依赖于该变量的所有活动查询的更新。

我们也可以用反应变量存储局部状态。为此，我们可以写:

```
import { ApolloClient, InMemoryCache, gql, makeVar } from "@apollo/client";

const age = makeVar(Math.random() * 100);

const cache = new InMemoryCache({
  typePolicies: {
    User: {
      fields: {
        age: {
          read(_, { variables }) {
            return age();
          }
        }
      }
    }
  }
});

const client = new ApolloClient({
  uri: "https://graphqlzero.almansi.me/api",
  cache
});

client
  .query({
    query: gql`
      {
        user(id: 1) {
          id
          name
          age @client
        }
      }
    `
  })
  .then(console.log);
```

上面，我们创建了`age`反应变量，通过在`read`方法中返回它，我们将它读入本地状态。然后我们可以像查询其他州一样查询`age`。现在，每当我们的查询发生变化时，我们都会看到返回一个新的值`age`。

要更新反应变量，我们只需传入一个新值，如下所示:

```
import { makeVar } from "@apollo/client";

const age = makeVar(Math.random() * 100);

console.log(age());

age(Math.random() * 100);

console.log(age());
```

我们向由`makeVar`返回的函数传递一个新值来更新该值。现在，两个控制台日志应该显示不同的值。

## 缓存字段策略

我们可以定义自己的缓存字段策略，这样我们就可以用不同于 API 中的方式读取它们。

例如，我们可以写:

```
import { ApolloClient, InMemoryCache, gql } from "@apollo/client";

const cache = new InMemoryCache({
  typePolicies: {
    User: {
      fields: {
        name: {
          read(name) {
            return name.toUpperCase();
          }
        }
      }
    }
  }
});

const client = new ApolloClient({
  uri: "https://graphqlzero.almansi.me/api",
  cache
});

client
  .query({
    query: gql`
      {
        user(id: 1) {
          id
          name
        }
      }
    `
  })
  .then(console.log);
```

我们为`User`类型创建了一个类型策略。`fields`有我们希望在读取时修改的字段，我们希望`name`的值是大写的。

所以我们让`name`的`read`方法返回大写的名字。现在，`then`方法中的`console.log`调用应该具有包含大写字母`user.name`的`data`字段。

我们可以将它用于许多其他应用程序，比如设置默认字段值、转换列表、更改字段值、分页等等。

## 结论

Apollo Client 3 对缓存进行了许多更改，包括清除缓存数据的能力。我们还可以添加本地字段，并用缓存策略改变普通字段的检索方式。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)