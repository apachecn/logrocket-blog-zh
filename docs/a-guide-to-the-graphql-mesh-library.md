# GraphQL 网格库指南

> 原文：<https://blog.logrocket.com/a-guide-to-the-graphql-mesh-library/>

GraphQL 是前端开发者和客户的梦想。毕竟，客户不关心数据来自哪里，也不关心您使用的数据库格式。他们关心的是快速、干净、轻松地获得他们需要的数据。如果它不会给服务器带来太重的负载，还会加分。

对于已经习惯了 GraphQL 的简单易用性的开发人员来说，以 graph QL 格式使用所有的 API 是一个目标。多亏了一个新的库 GraphQL Mesh，这个梦想现在变成了现实。GraphQL Mesh 是一块罗塞塔石碑，允许您的所有 API 和本地数据库一起很好地运行。

## GraphQL 是什么？

GraphQL Mesh 是由 [行会](https://medium.com/the-guild) 创建的一个新库，该行会是一个开源团体，致力于授权开发人员利用 GraphQL 的许多好处。

公会还负责流行的 GraphQL 资源，如 GraphQL 代码编辑器、GraphQL Inspector 和 GraphQL-CLI。他们清楚地知道如何让不同的开发人员使用 GraphQL，不管他们之前是否熟悉脸书创建的规范。

对于那些一直想尝试 GraphQL，但由于缺乏经验或拥有 REST 等旧格式的遗留产品而犹豫不决的开发人员来说，GraphQL Mesh 实现了他们的梦想。GraphQL Mesh 旨在充当中间层，从几乎任何地方接收数据，并将其转换为 GraphQL 格式。

GraphQL Mesh 的目标是从大量不同格式的数据中提取数据，并将它们与 GraphQL 集成，以便可以用 GraphQL 查询和变化来修改它们。

到目前为止，GraphQL Mesh 已经本机支持:

*   图表 QL
*   gRPC
*   JSON
*   MongoDB
*   OpenAPI/Swagger
*   PostgreSQL
*   肥皂/WSDL
*   阿帕奇节俭

这使得修改输出模式、跨模式链接类型和合并模式类型变得容易。它还让您能够精确控制如何检索数据，克服后端限制，以及由于模式规范和非类型化 API 导致的复杂性。

GraphQL Mesh 还充当本地数据的代理，并允许您与其他 API 一起使用公共库。您可以在本地使用这个代理，也可以在其他应用程序中使用 execute 函数调用服务。

请记住，GraphQL Mesh 主要用作您企业的背景层。如果您想向公众提供数据，您很可能需要添加一个额外的抽象层。

GraphQL 从收集与之通信的服务的 API 模式开始。然后，它为这些服务创建一个全类型 SDK 的运行时环境。然后它将各种 API 规范翻译成 [GraphQL 模式](https://blog.logrocket.com/speeding-up-changes-to-the-graphql-schema/) ，在这里可以执行定制的模式转换和扩展。最后，所有这些都被打包成一个 SDK，用于从您试图与之通信的服务获取数据。

这是通过使用本地模式实现的，本地模式是在安装 GraphQL Mesh 时从自动生成的目录中创建的。

这个模式允许您使用 GraphQL 的 execute 函数在您的应用程序中本地运行查询和变异函数。这使得 GraphQL 能够充当你的应用程序和你用来驱动它的任何东西之间的中枢神经系统。

## GraphQL 网格的优势

GraphQL 允许客户端和最终用户集成各种不同格式的数据。

用户不需要对复杂的 API 架构有透彻的理解就可以检索到他们需要的数据。这也使得快速原型更快更有效，因为你不必每次想做一个无关紧要的改变时都去 API 的引擎盖下。

GraphQL 也比 [其他规范如 REST](https://blog.logrocket.com/graphql-vs-rest-what-you-didnt-know/) 要高效得多。REST 在被查询时返回存储在数据库中的所有数据，这可能导致过度提取和不足提取。

GraphQL 只返回用户查询的确切数据。这不仅节省了资源，还使 API 更容易使用，因为您花在寻找所需数据上的时间更少了。

## GraphQL 网格如何工作

所有的数据都返回到一个地方。虽然 REST 对端点的大量使用肯定有它的用处，但也有它的缺点。能够将所有数据路由到一个 端点是 GraphQL Mesh 的一大优势和理由，足以让 graph QL Mesh 一试身手。

GraphQL Mesh 将几乎任何给定格式的 API 转换成 GraphQL 格式。它是一个抽象层，几乎可以覆盖任何源，包括本地文件和数据库。

## 安装 GraphQL 网格

GraphQL Mesh 有几个软件包可供选择，您可以根据自己的特定需求进行安装。我们将向您展示如何设置 GraphQL Mesh 的一个基本实例，这样您就可以开始使用该库并亲自试用。

首先，你需要安装`Yarn`包处理程序，它可以让包在全球范围内可用。为了便于管理，在开发文件夹中为这个项目创建一个新目录。我们称我们的为`GraphQL_mesh`。

在项目文件夹的根目录下，使用您选择的文本编辑器创建一个名为`.meshrc.yaml`的文件。

我们使用的是 [记事本++](https://notepad-plus-plus.org/downloads/) ，这是一个开源文本编辑器，可以让你以任何你想要的文件格式保存文件。将以下内容粘贴到文件中，然后保存:

```
sources:
  - name: Wiki
    handler:
      openapi:
        source: https://api.apis.guru/v2/specs/wikimedia.org/1.0.0/swagger.yaml
```

使用终端导航到该目录，并输入以下内容:

```
$ npm install yarn --global
```

要安装基本的 GraphQL 网格包，请键入以下内容:

```
$ yarn add graphql @graphql-mesh/runtime @graphql-mesh/cli
```

现在你需要安装一个网格处理器，这取决于你将要使用的特定 API 的需求。为了这个例子，我们将为 OpenAPI 规范安装网格处理程序:

```
$ yarn add graphql @graphql-mesh/openapi
```

要查看支持的 API 规范的完整列表，请查阅 [GraphQL-mesh 文档](https://graphql-mesh.com/docs/handlers/available-handlers) 。

现在你可以运行 GraphQL 了。键入以下命令:

```
$ yarn graphql-mesh serve
```

这是 GraphQL 的一个实例，遵循您提供给[http://localhost:4000/](http://localhost:4000/)的模式，因此您可以测试您的代码并确保一切正常运行。

## 如何使用 GraphQL 网格

现在让我们来看一个 GraphQL Mesh 的例子，让您了解如何将它集成到您的开发工作流程中。它还将帮助您直观地了解 GraphQL Mesh 如何比其他语言更容易、更直观地整合来自多个 API 源的数据。

为了说明其中的一些概念，我们将构建一个简单的应用程序，整合来自两个不同 API 的数据，并将它们合并在一起。我们正在从天气 API 和地理数据 API 收集数据。

为了便于管理，让我们为我们的项目创建一个新目录。我们把我们的命名为`locationweather`。使用终端导航到此文件夹。

现在我们将开始重新安装我们的库并收集我们需要的权限。一旦你进入你的编程指导，输入:

```
npm install yarn --global
yarn add graphql @graphql-mesh/runtime @graphql-mesh/cli
yarn add apollo-server
yarn add @graphql-mesh/openapi
```

这将安装将在我们的 GraphQL 函数内部调用的库，并使它们全局可用。

现在打开一个你喜欢的文本编辑器的实例进行编程。

我们使用的是 [记事本++](https://notepad-plus-plus.org/downloads/) ，因为它可以让你以你喜欢的任何文件格式保存文件。

让我们从制作 GraphQL 模式开始，它构成了 GraphQL 函数的大部分功能。在项目文件夹的根目录下，使用文本编辑器创建一个名为`.meshrc.yaml`的文件并保存它。然后输入如下:

```
sources:
  - name: Cities
    handler:
      openapi:
        source: https://api.apis.guru/v2/specs/mashape.com/geodb/1.0.0/swagger.json
        operationHeaders:
          'X-RapidAPI-Key': f93d3b393dmsh13fea7cb6981b2ep1dba0ajsn654ffeb48c26
  - name: Weather
    context:
      apiKey: 971a693de7ff47a89127664547988be5
    handler:
      openapi:
        source: https://api.apis.guru/v2/specs/weatherbit.io/2.0.0/swagger.json
transforms:
  - extend: |
      extend type PopulatedPlaceSummary {
        dailyForecast: [Forecast]
        todayForecast: Forecast
      }
  - cache:
      # Geo data doesn't change frequntly, so we can cache it forever
      - field: Query.*
      # Forecast data might change, so we can cache it for 1 hour only
      - field: PopulatedPlaceSummary.dailyForecast
        invalidate:
          ttl: 3600
      - field: PopulatedPlaceSummary.todayForecast
        invalidate:
          ttl: 3600
require:
  - ts-node/register/transpile-only
additionalResolvers:
  - ./src/mesh/additional-resolvers.ts
```

你可以看到这个函数正在调用我们在这个项目中使用的 API。它应该让您了解如何将这些原则应用于您可能想要使用的几乎任何 API 或数据源。

接下来，您将创建`package.json`，它构成了这个简单应用程序的其余部分。创建一个空白文件，并将下面的代码放入主体:

```
{
  "name": "typescript-location-weather-example",
  "version": "0.0.20",
  "license": "MIT",
  "private": true,
  "scripts": {
    "predev": "yarn mesh:ts",
    "dev": "ts-node-dev src/index.ts",
    "prestart": "yarn mesh:ts",
    "start": "ts-node src/index.ts",
    "premesh:serve": "yarn mesh:ts",
    "mesh:serve": "graphql-mesh serve",
    "mesh:ts": "graphql-mesh typescript --output ./src/mesh/__generated__/types.ts"
  },
  "devDependencies": {
    "@types/node": "13.9.0",
    "ts-node": "8.8.2",
    "ts-node-dev": "1.0.0-pre.44",
    "typescript": "3.8.3"
  },
  "dependencies": {
    "@graphql-mesh/cli": "0.0.20",
    "@graphql-mesh/openapi": "0.0.20",
    "@graphql-mesh/runtime": "0.0.20",
    "@graphql-mesh/transform-cache": "0.0.20",
    "@graphql-mesh/transform-extend": "0.0.20",
    "apollo-server": "2.11.0",
    "graphql": "15.0.0"
  }
}
```

你可以看到我们将使用的大多数变量都在`package.json`中定义。这是 GraphQL 的另一个最大优势——它的硬输入能力。使用 GraphQL 的 JSON 字符串，事情更加稳定和固定，因此不太可能出错。

我们根目录下最后一个实体文件是`tsconfig.ts`。创建文件并插入以下几行:

```
{
  "compilerOptions": {
    "target": "es2015",
    "module": "commonjs",    
    "moduleResolution": "node", /* Specify module resolution strategy: 'node' (Node.js) or 'classic' (TypeScript pre-1.6). */
    "lib": [
      "esnext"
    ],
    "sourceMap": true /* Generates corresponding '.map' file. */,
  },
  "include": ["src"],
  "exclude": ["node_modules"]
}
```

现在，如果有人在你之后使用这个应用程序，只需要多做一点家务就可以了。我们将制作一个自述文件，`README.md`:

```
## Location-Weather Example
```

这个例子采用了基于 Openapi 3 和 Swagger 的两个 API 源，以及它们之间的链接。

它允许您查询城市和位置，并包括找到的地方的天气字段。

最后，我们将创建一个负责一些附加路由的文件，名为`.gitingore`，没有文件扩展名。

```
__generated__
src/__generated__
```

我们快完成了！我们只想加入一点点额外的格式。为此，首先创建一个名为`SRC`的子文件夹。然后做一个名为`index.ts`的文件。

插入以下代码:

```
import { ApolloServer } from 'apollo-server';
import { getMesh, findAndParseConfig } from '@graphql-mesh/runtime';
async function main() {
  const meshConfig = await findAndParseConfig();
  const { schema, contextBuilder } = await getMesh(meshConfig);
  const server = new ApolloServer({
    schema,
    context: contextBuilder,
  });
  server.listen().then(({ url }) => {
    console.log(`🚀 Server ready at ${url}`);
  });
}
main().catch(err => console.error(err));
```

你可以看到`index.ts` 导入了我们之前安装的函数，像 apollo-server，当然还有 GraphQL Mesh，并让其余的函数都可以使用。

在`src`目录下再创建一个子文件夹，命名为`mesh`。在这个文件夹中，你将创建最后一个文件，名为`additional-resolvers.ts` :

```
import { Resolvers } from './__generated__/types';
export const resolvers: Resolvers = {
  PopulatedPlaceSummary: {
    dailyForecast: async (placeSummary, _, { Weather }) => {
      const forecast = await Weather.api.getForecastDailyLatLatLonLon({
        lat: placeSummary.latitude!,
        lon: placeSummary.longitude!,
        key: Weather.config.apiKey,
      });
      return forecast.data!;
    },
    todayForecast: async (placeSummary, _, { Weather }) => {
      const forecast = await Weather.api.getForecastDailyLatLatLonLon({
        lat: placeSummary.latitude!,
        lon: placeSummary.longitude!,
        key: Weather.config.apiKey,
      });
      return forecast.data![0]!;
    },
  },
};
```

这是最后一段代码！现在您可以进入命令行并运行:

```
yarn graphql-mesh serve
```

这将把你的应用程序服务到[http://localhost:4000](http://localhost:4000)，运行在 GraphQL 的一个实例上，在这里你可以执行你的查询和突变。

如果你想在不弄乱任何代码的情况下试用 GraphQL Mesh，整个项目 [都可以在 codesandbox](https://codesandbox.io/s/github/Urigo/graphql-mesh/tree/master/examples/location-weather) 上获得，包括代码，所以你可以亲自看到 GraphQL Mesh 的运行，并了解如何将这个聪明的翻译器集成到你现有的工作流中。

## 结论

GraphQL Mesh 是前端开发人员和最终用户的梦想成真。

从客户的角度来看，他们不需要了解太多的 API 结构来做他们想做的事情。相反，他们只需要知道他们在查询什么，而 GraphQL Mesh 提供了这些。

从程序员的角度来看，GraphQL Mesh 使得代码更加健壮和灵活。您不必担心每次更改数据时都要重新配置 API。不再需要路由无止境的端点或不断编码复杂的数据库。

## 监控生产中失败和缓慢的 GraphQL 请求

虽然 GraphQL 有一些调试请求和响应的特性，但确保 GraphQL 可靠地为您的生产应用程序提供资源是一件比较困难的事情。如果您对确保对后端或第三方服务的网络请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/graphql-signup)

.

[![](img/432a3823c85b3fb72a206e6236a29f48.png)![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/graphql-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/graphql-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你网站上发生的每一件事。您可以汇总并报告有问题的 GraphQL 请求，以快速了解根本原因，而不是猜测问题发生的原因。此外，您可以跟踪 Apollo 客户机状态并检查 GraphQL 查询的键值对。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/graphql-signup)

.