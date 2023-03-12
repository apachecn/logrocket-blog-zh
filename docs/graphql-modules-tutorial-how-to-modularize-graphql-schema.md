# GraphQL 模块教程:如何模块化 GraphQL 模式

> 原文：<https://blog.logrocket.com/graphql-modules-tutorial-how-to-modularize-graphql-schema/>

在本教程中，我们将向您展示如何使用 GraphQL 模块将您的 GraphQL 应用程序分解成简单的、可重用的、基于功能的模块。

我们将讨论以下内容:

## 什么是 GraphQL 模块？

GraphQL 模块是一组库和指南，旨在帮助你创建可重用、可维护、可测试和可扩展的模块。GraphQL 模块背后的基本概念是将您的 GraphQL 服务器分成更小的、独立的、基于功能的模块。

GraphQL 模块帮助您在 GraphQL 中实现[关注点分离](https://deviq.com/principles/separation-of-concerns) [设计模式](https://deviq.com/principles/separation-of-concerns)，使您能够编写简单的模块，只做他们需要做的事情。这使得您的代码更容易编写、测试和维护。

GraphQL 服务器的初始基本实现的结构如下所示:

![GraphQL Server Basic Implementation Example](img/5fd636680ade7a71015c2311fce72761.png)

Basic GraphQL implementation

随着您的 GraphQL 应用程序的增长，您的模式和解析器的复杂性也会增加，这会使模式维护变得困难。为了组织您的代码，您需要将您的模式类型及其相关的解析器分离到多个文件或模块中，并在模块下添加与应用程序特定部分相关的任何内容。graphql-modules 库有助于促进这个渐进的过程。

使用`graphql-modules`，您的应用程序将如下构建:

![GraphQL-Modules Library App Structure](img/9b98f44349345a2aba096d249251d50f.png)

## 为什么要使用 GraphQL 模块？

大多数使用 GraphQL 模块的开发人员选择该工具集是因为它具有可重用的模块、可伸缩的结构、清晰的发展路径和可测试性。让我们仔细看看为什么开发人员转向 GraphQL 模块来为他们的 GraphQL 应用程序编写可重用、可扩展的模块。

*   **可重用模块**:模块由它们的 GraphQL 模式定义成更小的部分，您可以在以后移动和重用这些部分
*   **可扩展的结构**:每个模块都有一个明确定义的边界，这使得管理多个团队和功能、多个微服务和服务器变得更加容易。模块可以使用自定义消息相互通信
*   **清晰的增长路径**:通过将特性分成模块，很容易看到您的应用程序如何从非常简单、快速的单文件模块增长到可扩展的多文件、团队、回购和服务器模块
*   测试性:测试小块代码比测试大块代码更容易。GraphQL 模块为测试和模拟您的应用程序提供了丰富的工具集

## GraphQL 模块如何工作

我们将使用一个简单的库应用程序来展示如何将 GraphQL 应用程序分解成模块。应用程序将有`Book,` `Author`和`Genre`模块，每个模块将由类型定义和解析器函数组成。

### 创建 GraphQL 模块

要开始使用`graphql-modules`，你只需要安装它的软件包和`graphql`:

```
// NPM
npm install --save graphql graphql-modules

//Yarn 

yarn add graphql graphql-modules

```

要创建一个模块，使用`createModule`:

```
import { createModule } from 'graphql-modules';

export const myModule = createModule({
  id: 'my-module',
  dirname: __dirname,
  typeDefs: [
    `type Query {
      hello: String!
    }`,
  ],
  resolvers: {
    Query: {
      hello: () =&gt; 'world',
    },
  },
});

```

给模块添加一个惟一的`id`是很重要的，因为它将帮助您定位类型定义中的问题。虽然`dirname`是可选的，但是它使得在异常发生时匹配正确的文件变得更加简单。

### 类型定义和解析器

对于定义模式，GraphQL 模块使用模式定义语言(SDL)，就像 GraphQL 模式一样。

GraphQL 模块还以与任何其他 GraphQL 实现相同的方式实现解析器。根据 [GraphQL 模块文档](https://graphql-modules.com/docs/essentials/resolvers/)，GraphQL 模块创建的模块可以检测重复的、不正确的或旧的解析器(例如，不匹配类型定义或扩展的解析器)。

### 创建图书模块

以下是如何为我们的 GraphQL 应用程序创建图书模块:

```
// book.type.graphql
import { gql } from 'graphql-modules';

export const Book = gql`
  type Query {
      book(id: ID!): Book
  }
  type Book {
    id: String
    title: String
    author: Author
    summary: String
    isbn: String
    genre: [Genre]
    url: String
  }
`;

// book.resolver.graphql

export const BookResolver = {
    Query: {
      book(root, { id }) {
        return {
          _id: id,
          title: "To The Lighthouse",
          author: "Virginia Woolf",
          summary:"Book summary",
          isbn: "12345678EDB"
          genre: ["ficton"],
          url: "http://lighouse.com"
        };
      },
    },
    Book: {
      id(book) {
        return book._id;
      },
      title(book) {
        return book.title;
      },
      author(book) {
        return book.author;
      },
      summary(book) {
        return book.summary;
      },
      isbn(book) {
        return book.isbn;
      },
      genre(book) {
        return book.genre;
      },
      url(book) {
        return book.url;
      },
    },
  },

// book.module.graphql.ts
import{ Book } from './book.type.graphql';
import { BookResolver } '.book.resolver.graphql';
import { createModule } from 'graphql-modules';

export const BookModule = createModule({
  id: 'book-module',
  dirname: __dirname,
  typeDefs: [Book],
  resolvers: [BookResolvers]
});

```

### 其他模块

要为我们的示例 GraphQL 库应用程序创建作者和流派模块，请遵循以下步骤:

```
// author.module.graphql.ts
import{ Author } from './author.type.graphql';
import { AuthorResolver } '.author.resolver.graphql';
import { createModule } from 'graphql-modules';

export const AuthorModule = createModule({
  id: 'book-module',
  dirname: __dirname,
  typeDefs: [Author],
  resolvers: [AuthorResolvers]
});

// genre.module.graphql.ts
import{ Genre } from './genre.type.graphql';
import { GenreResolver } '.genre.resolver.graphql';
import { createModule } from 'graphql-modules';

export const GenreModule = createModule({
  id: 'book-module',
  dirname: __dirname,
  typeDefs: [Genre],
  resolvers: [GenreResolvers]
});

```

## 用 GraphQL 模块合并模式

我们定义的每个模块都是整个模式的一小部分。

要将模式合并在一起，使用 GraphQL 模块的`createApplication`创建一个`application`:

```
import { createApplication } from 'graphql-modules';
import { GenreModule } from './genre/genre.module.graphql';
import { BookModule } from './book/book.module.graphql';
import { AuthorModule } from './author/author.module.graphql';

export const application = createApplication({
  modules: [BookModule, AuthorModule, GenreModule],
});

```

`application`包含您的 GraphQL 模式及其实现。我们需要让 GraphQL 服务器能够使用它。

GraphQL 模块对于流行的 GraphQL 服务器有各种实现，比如[阿波罗](https://www.apollographql.com/)、 [Express GraphQL](https://graphql.org/graphql-js/running-an-express-graphql-server/) 、 [GraphQL](https://github.com/contrawork/graphql-helix) 、 Helix 。如果您使用的是 [Apollo](https://www.apollographql.com/docs/apollo-server/) [服务器](https://www.apollographql.com/docs/apollo-server/)，那么您可以使用`createSchemaForApollo`获得一个适用于该服务器并与之完美集成的模式:

```
import { ApolloServer } from 'apollo-server';
import { application } from './application';

const schema = application.createSchemaForApollo();

const server = new ApolloServer({
  schema,
});

server.listen().then(({ url }) =&gt; {
  console.log(`🚀 Server ready at ${url}`);
});

```

## 图表 QL `context`

在 GraphQL 中，`[context](https://www.apollographql.com/docs/apollo-server/data/resolvers/#the-context-argument)` [参数](https://www.apollographql.com/docs/apollo-server/data/resolvers/#the-context-argument)在为特定操作执行的所有解析器之间共享。您可以使用`context`来共享每个操作的状态，包括认证数据、数据加载器实例等。

在 GraphQL 模块中，`context`可作为每个解析器的第三个参数，并在模块间共享:

```
const resolvers = {
  Query: {
    myQuery(root, args, context, info) {
      // ...
    },
  },
};

```

## 依赖注入

随着您的应用程序的扩展，您可能需要将特定的业务逻辑从解析器分离到一个服务中。GraphQL 模块支持依赖注入(DI ),这有助于使您的服务对解析器可用。

依赖关系是解析器执行其功能所需的服务或对象。因此，解析器从外部资源请求服务，而不是创建服务。

需要注意的是，依赖注入只有在你的代码库非常大并且你需要快速移动的时候才有意义。

要使用 GraphQL 模块的 DI，您需要理解几个术语:

*   `injector`是 DI 系统中的一个对象，它可以在其缓存中找到一个命名的依赖项，或者使用一个已配置的提供程序创建一个依赖项，然后根据其作用域使其对一个模块或整个应用程序可用
*   `InjectionToken`是表示依赖注入空间中的对象或任何值的符号(令牌)或类(服务类)
*   `provider`是一种定义值并将其与`Token`或`Service`类匹配的方法。它提供了特定注入标记的值。提供者被注入到解析器或其他服务中。

有三种提供者:类提供者、值提供者和工厂提供者。让我们仔细看看每一个。

### 类别提供者

类提供者创建一个类的实例，并使其对注入器可用。对于要用作提供者的服务类，它必须用`@Injectable()`装饰器来装饰:

```
// book.service.ts
import { Injectable } from 'graphql-modules';

@Injectable()
export class BookService {}

// book.module.graphql.ts
import{ Book } from './book.type.graphql';
import { BookResolver } '.book.resolver.graphql';
import { createModule } from 'graphql-modules';

export const BookModule = createModule({
  id: 'book-module',
  dirname: __dirname,
  typeDefs: [Book],
  resolvers: [BookResolvers],
  providers: [BookService]
});

```

每个解析器中都有`context`对象。这个`context`对象包含另一个对象`injector`，它可以用来访问解析器中提供的服务类。

```
// book.resolver.graphql
import { BookService } from './book.service.ts'
export const BookResolver = {
    Query: {
      book(root, { id }, context) {
        const bookService = context.injector.get(BookService);
        return {
          _id: id,
         // ...
        };
      },
    },
    Book: {
      id(book) {
        return book._id;
      },
      // ...
    },
  },

```

要在类中使用它，只需在构造函数中请求它。

```
import { Injectable } from 'graphql-modules';
import { BookService } from './book';

@Injectable()
class AuthorService {
  constructor(private bookService: BookService) {}

  allbooks(authorId) {
     return this.bookService.books(authorId)
  }
}

```

### 价值提供者

值提供者提供了一个现成的值。它需要一个令牌来表示一个值，或者是一个类。

```
import { InjectionToken } from 'graphql-modules';
const ApiKey = new InjectionToken&lt;string&gt;('api-key');
import { createModule } from 'graphql-modules';

export const BookModule = createModule({
  id: 'book-module',
  /* ... */
  providers: [
    {
      provide: ApiKey,
      useValue: 'my-api-key',
    },
  ],
});

```

### 工厂供应商

工厂提供者是一个提供值的函数。当您需要基于直到运行时才可用的信息动态地创建一个依赖值时，它就派上了用场。您可以根据应用程序状态的特定条件，对返回哪个值做出明智的决定。

工厂提供程序对于创建类的实例也很有用，例如在使用第三方库时。工厂函数可以有一个带有依赖项(如果有的话)的可选参数。

```
import { InjectionToken } from 'graphql-modules';
const ApiKey = new InjectionToken&lt;string&gt;('api-key');
import { createModule } from 'graphql-modules';

export const BookModule = createModule({
  id: 'book-module',
  /* ... */
  providers: [
    {
      provide: ApiKey,
      useFactory(config: Config) {
        if (context.environment) {
          return 'my-api-key';
        }

        return null;
      },
      deps: [Config],
    },
  ],
});

```

在解析器中访问令牌类似于访问服务。`@Inject`装饰器用于访问构造函数中的令牌。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
import { Injectable } from 'graphql-modules';
import { BookService } from './book';

@Injectable()
class AuthorService {
  constructor(@Inject(ApiKey) private key: string, private bookService: BookService) {}

  allbooks(authorId) {
     return this.bookService.books(authorId)
  }
}

```

### 图形 SQL 性交

每个令牌或提供者都有一个用于定义其生命周期的范围。`Scope`可以是单例，也可以是操作。每个定义为 singleton 的提供者或令牌都创建一次，相同的实例可用于所有传入的 GraphQL 操作。单一提供者贯穿于应用程序的整个生命周期，是 GraphQL 模块的默认和推荐范围。

另一方面，操作提供者是为每个执行上下文或每个需要它的 GraphQL 操作创建的。操作提供者只存在于实例化它的 GraphQL 操作的生命周期中。

```
// genre.service.ts
import { Injectable, Scope } from 'graphql-modules';

@Injectable({
  scope: Scope.Operation,
})
export class GenreService {}

// genre.module.graphql.ts
import{ Genre } from './genre.type.graphql';
import { GenreResolver } '.genre.resolver.graphql';
import { createModule } from 'graphql-modules';
import { GenreService } from './genre.service.ts'

export const GenreModule = createModule({
  id: 'book-module',
  dirname: __dirname,
  typeDefs: [Genre],
  resolvers: [GenreResolvers],
  providers: [GenreService]
});

```

当解析器没有调用`GenreService`时，不会创建服务。

## 结论

可维护的代码很容易扩展，GraphQL 模块旨在帮助您实现这一点。

代码分离或模块化只发生在开发过程中是没有价值的。然而，在运行时，仍然提供统一的模式。

现在，您应该已经具备了开始使用 GraphQL 模块所需的一切。要了解其他高级概念，如订阅、中间件、执行上下文和生命周期挂钩，可以查看 [GraphQL](https://graphql-modules.com/) [模块](https://graphql-modules.com/) [s](https://graphql-modules.com/) [文档](https://graphql-modules.com/)。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.