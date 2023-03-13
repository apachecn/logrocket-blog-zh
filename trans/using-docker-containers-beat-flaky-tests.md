# 使用 Docker 容器击败片状测试

> 原文：<https://blog.logrocket.com/using-docker-containers-beat-flaky-tests/>

即使方法参数和源代码(断言是针对它们编写的)没有改变，易变的测试在不同的运行中也会产生不同的结果。剥落可能是由多种因素造成的，包括随机性、竞争条件或外部服务等依赖性。

在本文中，我们将测试一个由 [Express.js](https://blog.logrocket.com/organizing-express-js-project-structure-better-productivity/) 驱动的 [Node.js 应用程序](https://blog.logrocket.com/tag/node/)，它能够存储和读取工作列表。我们将向您展示如何使用 Docker 容器来击败脆弱的测试。为了简洁起见，我们将只讨论主题范围内的相关代码。代码可以在 [GitHub 库](https://github.com/creatrixity/simple-job-board)中找到。

*向前跳转:*

## 为什么要避免古怪的测试？

避免不可靠的测试是至关重要的，因为不可靠的测试会损害软件的完整性。片状测试还会降低开发速度，因为在运行缓慢的 CI 环境中会发生多次失败。

当特定的断言在被提供特定的参数时返回特定的结果时，测试就是确定的。函数式编程中的纯函数是用与确定性测试相同的规则编写的。软件模块与其他模块交互，并在应用程序生命周期内共享状态。写得不好的测试有时会通过交叉污染引入状态不一致，即前一次测试运行的状态会影响正在运行的测试。由于测试变得不确定，这可能导致测试剥落。

易变行为的其他原因包括`async`行为、并发问题和网络交互(易变系统)。为了减轻这些场景的风险，我们必须确保测试被适当地隔离，并且共享的资源使用被最小化。

## 测试时为什么要使用 Docker 容器？

Docker 容器帮助我们保持测试环境的确定性。我们可以使用 Docker 容器将应用程序的运行时环境与其主机操作系统隔离开来。这允许我们隔离文件系统、网络、系统调用和资源使用。

容器是轻量级的进程，成本低廉，易于创建和拆卸。这些品质使它们成为[隔离测试](https://blog.logrocket.com/comparing-best-node-js-unit-testing-frameworks/)的理想选择。使用像 [Docker Compose](https://docs.docker.com/compose/install/) 这样的容器编排工具，我们可以在一个清单中定义依赖服务，这使得它非常适合为外部服务旋转模拟容器。

本文的下一部分将演示用 [Docker](https://blog.logrocket.com/tag/docker/) 、 [Docker Compose](https://blog.logrocket.com/node-js-docker-improve-dx-docker-compose/) 、 [Knex.js](https://knexjs.org/) 和 [PostgreSQL](https://blog.logrocket.com/tag/postgresql/) 进行测试。

## 测试 Node.js 应用程序

保证生产中运行的应用程序的行为非常重要。为了增加我们的信心水平，我们运行手动和[自动测试](https://blog.logrocket.com/how-automate-api-tests-postman/)。为了在我们的测试套件中获得信心，我们需要测试输出是可预测的和确定的。

在本文中，我们将测试一个基于 Express.s 的 Node.js 应用程序。我们将使用`Postgres`来实现持久性，但是我们将使用`knex`来管理迁移、种子和构建查询对象。

### 设置和安装

首先，克隆`simple-job-board` repo 并安装必要的依赖项:

```
git clone https://github.com/creatrixity/simple-job-board
cd simple-job-board
cp .env.example .env
npm install
```

由于应用程序将`PostgreSQL`作为服务依赖项，我们将为`PostgreSQL`运行一个 [Docker 容器](https://blog.logrocket.com/docker-for-front-end-developers/):

```
# We need to create an isolated bridge network to allow us connect to our instances
docker create network simple-job-board --driver bridge
docker pull postgres
docker run --name simple-job-board-postgres -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=5uperIn5ecurePa55word -e POSTGRES_DB=simple-job-board -p 5432:5432 -d postgres

```

在上面的代码片段中，我们为应用程序定义了一个[隔离网桥网络](https://docs.docker.com/network/bridge/)。我们还提取了官方的`postgres`映像，并在容器初始化时传递了一些配置选项。

在 PostgreSQL 容器运行并监听连接的情况下，我们将尝试连接到正在运行的实例。安装了`psql` CLI 后，执行:

```
psql -h 127.0.0.1 -p 5432 -U postgres -d simple-job-board
```

我们应该会看到下面的提示，表示连接成功:

```
# psql (13.3, server 15.0 (Debian 15.0-1.pgdg110+1))
# WARNING: psql major version 13, server major version 15.
#         Some psql features might not work.
# Type "help" for help.

# simple-job-board=# 

```

### 定义`application`和`testing`配置

接下来，我们将使用`dotenv`来获取环境变量。我们还将设置一个小的`getEnvVariable`助手来为环境变量提供后备。然后我们为`server port`和数据库主机定义键，用`localhost`作为`fallback`。`config`文件在`src/config.ts`可用:

```
import dotenv from "dotenv";
dotenv.config();

function getEnvVariable(name: string, fallback: string = ""): string {
  const envVariable = process.env[name];
  const fallbackProvided = fallback.length;
  if (!envVariable && !fallbackProvided) {
    throw new Error(`Environment variable ${name} has not been set.`);
  }
  return envVariable || fallback;
}

const config = {
  server: {
    port: Number(getEnvVariable("PORT")),
  },
  database: {
    host: getEnvVariable("DATABASE_HOST", "localhost"),
  },
};

export default config;

```

然后，在`./knexfile.ts`中，定义`development`和`testing`环境的配置。定义这两种环境是相似的，除了在`testing`中定义`host`参数:

```
import type { Knex } from "knex";
import appConfig from "./src/config";

// Update with your config settings.

const config: { [key: string]: Knex.Config } = {
  development: {
    client: "postgresql",
    connection: {
      user: "postgres",
      password: "5uperIn5ecurePa55word",
      port: 5432,
    },
    pool: {
      min: 2,
      max: 10,
    },
    migrations: {
      tableName: "knex_migrations",
    },
  },

  testing: {
    client: "postgresql",
    connection: {
      user: "postgres",
      password: "5uperIn5ecurePa55word",
      host: appConfig.database.host,
      port: 5432,
    },
    pool: {
      min: 2,
      max: 10,
    },
    migrations: {
      tableName: "knex_migrations",
    },
  },
};

module.exports = config;

```

### 引导 Node.js 应用程序

接下来，我们将在`src/index.ts`中引导我们的应用程序。为了保持 Node.js 应用程序引导代码的可重用性，我们将在名为`createServer`的工厂函数中创建新的应用程序实例。

`createServer`工厂将接受一个数据库连接池文件描述符的引用和一个可选的`port`号，以在其中公开应用程序。

幸运的是，`knex`提供了数据库连接的抽象:

```
// src/index.ts
import knex from "knex";
import config from "./config";
import { createServer } from "./createServer";
const knexConfig = require("../knexfile");
const knexSetup = knex(knexConfig.development);

createServer(knexSetup, config.server.port);

```

在检查了导出`createServer`工厂的`src/createServer.ts`文件后，我们将复制一份数据库连接引用。通过存储在`app.locals`内，其他[快速模块](https://blog.logrocket.com/nestjs-vs-express-js/)也可以使用。

我们也有条件地监听提供的`port`号。我们打算在我们的测试设置中重用`createServer`工厂，如果没有提供工厂，`superagent`将把应用程序绑定到一个随机的`port`:

```
// src/createServer.ts
export function createServer(dbConnection: Knex, port?: number): Express {
  const app: Express = express();
  app.use(bodyParser.json());

  // Cache a copy of the dbConnection socket
  app.locals.dbConnection = dbConnection;

  app.use("/jobs", jobsRouter);
  app.get("/", (_req: Request, res: Response) => {
    res.json({ message: "Hello 👋 ! Welcome to the simple job board app" });
  });

  if (port) {
    app.listen(port, () => {
      console.log(`☁️ Server is running at https://localhost:${port}`);
    });
  }

  return app;
}

```

### 编写集成测试

我们将为`job`模块添加几个[集成测试](https://blog.logrocket.com/unit-and-integration-testing-for-node-js-apps/)。在每个测试套件运行之前，我们将:

*   为短暂测试构造一个随机的唯一名称`database`
*   获得一个`Postgres`连接
*   用生成的随机惟一名称创建一个临时测试`database`
*   获取一个引用`database`的新`Postgres`连接
*   迁移并播种新创建的`database`
*   调用`createServer`来帮助创建 Express 应用程序的新实例，并将新实例分配给顶级`app`变量:

```
  // src/modules/job/job.test.ts
  // -------------------------------------------------------------------------
  // Setup up
  // -------------------------------------------------------------------------
  let app: Express;
  let knexSetup: Knex;
  let databaseName: string;

  beforeAll(async () => {
    databaseName = uniqueNamesGenerator({
      dictionaries: [colors, adjectives, animals],
    });
    knexSetup = knex(knexConfig.testing);
    await knexSetup.raw(`CREATE DATABASE ${databaseName}`);
    knexSetup = knex({
      ...knexConfig.testing,
      database: databaseName,
    });
    await knexSetup.migrate.latest();
    await knexSetup.seed.run();
    app = createServer(knexSetup);
  });

```

在每个测试套件运行之后，我们通过丢弃短暂的`database`并关闭到`database`的连接来进行清理，以避免池饥饿，并将资源利用率保持在最佳水平:

```
  // src/modules/job/job.test.ts
  // -------------------------------------------------------------------------
  // Tear Down
  // -------------------------------------------------------------------------
  afterAll(async () => {
    await knexSetup.raw(`DROP DATABASE ${databaseName}`);
    await knexSetup.destroy();
  });

```

对于`test`的情况，我们对查询在前面步骤中植入到`database`中的`jobs`列表返回的数据运行断言。我们通过`superagent`发出`POST`请求来创建新的工作记录。我们还断言我们的新作业记录，该记录是在查询在`database`中可用的`jobs`列表时返回的:

```
// src/modules/job/job.test.ts
import { jobData } from "../../../seeds/jobs";

describe("/jobs", () => {
  // -------------------------------------------------------------------------
  // Test cases
  // -------------------------------------------------------------------------
  test("GET: / should return a success status", async () => {
    await request(app).get("/").expect("Content-Type", /json/).expect(200);
  });

  test("GET: /jobs should return a list of jobs", async () => {
    const result = await request(app).get("/jobs");
    expect(
      result.get("Content-Type").includes("application/json")
    ).toBeTruthy();
    expect(result.statusCode).toEqual(200);
    expect(result.body.results).toEqual(
      expect.arrayContaining(jobData.map((job) => expect.objectContaining(job)))
    );
  });

  test("POST: /jobs should create a new job", async () => {
    const jobCreationData = {
      role: "Virtual Reality Designer",
      location: "Seattle",
      organization: "Microsoft",
      description: "Reimagine virtual reality experiences",
    };

    const jobCreationResponse = await request(app)
      .post("/jobs")
      .send(jobCreationData)
      .set("Accept", "application/json");

    expect(jobCreationResponse.statusCode).toEqual(201);

    const jobsResponse = await request(app).get("/jobs");
    const jobs = jobsResponse.body.results;
    expect(jobs[jobs.length - 1]).toMatchObject(jobCreationData);
  });
});

```

运行`npm test`应该会给出如下输出:

```
PASS  src/modules/job/job.test.ts
  /jobs
    ✓ GET: / should return a success status (267 ms)
    ✓ GET: /jobs should return a list of jobs (64 ms)
    ✓ POST: /jobs should create a new job (97 ms)

Test Suites: 1 passed, 1 total
Tests:       3 passed, 3 total
Snapshots:   0 total
Time:        3.705 s, estimated 14 s
Ran all test suites.

Watch Usage: Press w to show more.

```

我们已经在本地开发环境中成功测试了我们的应用程序。接下来，我们将在 Docker 容器中运行我们的测试。

## 在 Docker 容器中运行测试

首先，我们将从创建我们的`Dockerfile`开始，其中包含构建我们的[应用程序映像](https://blog.logrocket.com/node-js-docker-improve-dx-docker-compose/#dockerize-app-docker-multi-stage-build)的指令:

```
FROM node:alpine

WORKDIR /usr/app
COPY ./ /usr/app

RUN npm install

# Define the command to run the test
CMD ["npm", "test"]

```

接下来，让我们定义一个包含两个服务的`docker-compose.yml`清单:为我们的 Express 应用程序运行测试的`simple-job-board`服务和运行在容器中的 PostgreSQL 数据库`db`服务:

```
version: "3"
services:
  simple-job-board:
    build: .
    environment:
      DATABASE_HOST: db
    links:
      - db
  db:
    image: postgres:11
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: 5uperIn5ecurePa55word
      POSTGRES_DB: simple-job-board
    ports:
      - "5432:5432"

```

然后，我们将通过运行`docker-compose run test`来启动测试运行，以确保我们的数据库服务在测试开始运行之前启动:

![The Running PostgreSQL Container](img/0c9fd86d554b6815b4da4e48c654e35c.png)

我们的测试结果将通过 Docker 容器中运行的绿色复选标记。您可能会注意到，在我们的测试运行之前，等待时间略有增加。这是因为当我们启动 Docker 容器时会产生开销:

![Passing Tests Running in a Docker Container](img/d81a34d3e7025de90083b5be607588a6.png)

## 结论

有了 Docker 容器和测试隔离，我们大大减少了遇到脆弱测试的机会。在本文中，我们为每个测试运行创建了新的连接和新的数据库，以保证每个运行都是干净的。我们还消除了作为片状剥落来源的州污染。然而，请记住，还有其他的剥落来源，比如`async`行为、并发问题和网络交互。重要的是要认识到测试碎片在本质上是多变量的。

请随意从本教程中克隆这个项目并进行测试。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)