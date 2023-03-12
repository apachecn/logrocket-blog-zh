# 使用 Jest - LogRocket 博客测试 TypeScript 应用程序

> 原文：<https://blog.logrocket.com/testing-typescript-apps-using-jest/>

测试，测试，一，二，三…🎤

测试是软件工程的一个重要方面。在测试期间，我们确定应用程序的期望和测试用例，并检查它们是否得到满足。

JavaScript 有很多可用的测试框架，可以跨用 React、Angular、Node.js 和 TypeScript 开发的项目使用；但是今天，我们将特别关注一个叫做 [Jest](https://jestjs.io/) 的测试框架。

Jest 是一个简单的轻量级测试框架，为 JavaScript 和 TypeScript 项目提供了多种测试功能。它提供了断言、嘲讽、间谍、并行运行测试、失败测试优先级排序和覆盖分析等功能。在本文中，我们将实现 Jest 必须提供的一些功能。

## 先决条件

为了展示 Jest 的测试能力，我们将使用以前的 LogRocket 文章中的项目，标题为使用 Sequelize 和 TypeScript 。

要继续学习，您需要安装:

一旦我们安装了所有这些，我们将通过运行以下命令来克隆我们的[类型脚本/序列项目](https://github.com/ibywaks/cookbook):

```
$ git clone [email protected]:ibywaks/cookbook.git 

```

既然我们已经克隆了我们的 [TypeScript 项目](https://blog.logrocket.com/tag/typescript)，我们将添加和配置我们的测试框架。

## 设置我们的测试依赖项

首先，我们将安装测试库作为开发依赖项。

```
$ yarn add -D jest @types/jest ts-jest

```

接下来，我们将通过创建一个名为`jest.config.ts`的文件并向其中添加以下内容来为我们的应用程序配置 Jest:

```
# jest.config.ts

import type {Config} from '@jest/types';
// Sync object
const config: Config.InitialOptions = {
  verbose: true,
  transform: {
  ‘^.+\\.tsx?$’: ‘ts-jest’,
  },
};
export default config;

```

根据您的偏好，还有其他几个配置 Jest 的选项，比如运行`jest --init`创建一个带有默认配置的`jest.config.js`文件，或者在`package.json`中添加配置。 [Jest](https://jestjs.io/docs/configuration) [支持很多配置选项](https://jestjs.io/docs/configuration)，但是对你选择的任何配置选项都没有额外的好处。

我们还将在`package.json`文件的脚本部分添加一个 Yarn 命令来运行我们的测试。

```
# package.json

{
  "name": "cookbook",
  "version": "1.0.0",
  "description": "A simple recipe app using typescript and sequelize",
  "main": "dist/index.js",
  "scripts": {
    "test": "jest",
    ...

```

既然我们已经配置了 Jest 库，我们将为我们的测试定义一些结构。

## 设置我们的项目测试结构

对于我们的项目，我们将关注两种类型的功能测试:

*   集成测试——这些测试侧重于我们的应用组件如何相互交互以实现预期结果，即我们测试服务、路由和数据访问层(dal)的结果
*   单元测试——这些测试关注特定组件的动作，而不关注它所引用的其他组件，例如，模仿外部组件，以便我们可以独立测试组件

功能测试关注应用程序的功能和结果。还有一些非功能性测试，关注我们应用程序的各个方面，如性能、负载测试、正常运行时间等。—但是这些不包括在 Jest 的范围内，所以我们在这里不涉及它们。

让我们用集成测试和单元测试的文件夹创建我们的`tests`目录。

```
/tests
  /integration
  /unit

```

## 实现 Jest 的概念和测试功能

现在我们已经为我们的测试创建了一个结构，我们将仔细检查可用的 Jest 概念，以及如何在我们的项目中使用它们。

### 安装和拆卸

这是指我们需要执行的任务，以便让我们的应用程序处于必要的测试状态，例如填充或清除表格。Jest 提供了通过`beforeAll`、`beforeEach`、`afterAll`和`afterEach`等函数执行这些任务的能力。

顾名思义，`beforeAll`和`afterAll`在特定范围内的其他测试运行之前和之后运行一些任务，而`beforeEach`和`afterEach`在其范围内的每个测试之前和之后运行任务。

这里的“范围”指的是这些函数影响的测试部分——例如，当在定义了一组相关测试的`describe`块中调用`beforeAll`时，它适用于该块中的所有测试。

让我们通过为我们的`Ingredient` DAL 编写测试来测试这一点，在这里我们截断了我们的`Ingredient`表，并在运行所有测试之前为我们的测试创建了一些需要它们的`Ingredient`表条目。

首先，我们将在`tests/integrations`中创建一个`dal`目录，然后添加我们的测试文件`ingredient.test.ts`。

```
# tests/integration/dal/ingredient.test.ts

import {Ingredient} from '../../../src/db/models'
import * as ingredientDal from '../../../src/db/dal/ingredient'

const dbTeardown = async () => {
    await Ingredient.sequelize?.query("SET FOREIGN_KEY_CHECKS = 0")
    await Ingredient.truncate({force: true})
    await Ingredient.sequelize?.query("SET FOREIGN_KEY_CHECKS = 1")
}

describe('Ingredient DAL', () => {
    let ingredientId: number
    beforeAll(async () => {
        await dbTeardown()
        ;({id: ingredientId} = await Ingredient.create({
            name: 'Beans',
            slug: 'beans',
        }))
    })
    afterAll(async () => {
        await dbTeardown()
    })

    describe('Create method', () => {
        it('should create and return an object of ingredient details', async () => {
            const payload = {
                name: 'Pasta',
                slug: 'pasta',
                description: 'abcd make some pasta'
            }

            const ingredient = await ingredientDal.create(payload)
            expect(ingredient).not.toBeNull()
        })
    })

    describe('findOrCreate method', () => {
        beforeAll(async () => {
            await Ingredient.create({
                name: 'Brown Rice',
                slug: 'brown-rice'
            })
        })
        it('should create a new entry when none with matching name exists', async () => {
            const payload = {
                name: 'Rice',
                slug: 'rice',
            }
            await ingredientDal.findOrCreate(payload)
            const ingredientsFound = await Ingredient.findAll({where: {name: 'Rice'}})
            expect(ingredientsFound.length).toEqual(1)
        })
        it('should return an existing entry where one with same name exists without updating it', async () => {
            const payload = {
                name: 'Brown Rice',
                slug: 'brownrice',
                description: 'test'
            }
            await ingredientDal.findOrCreate(payload)
            const ingredientsFound = await Ingredient.findAll({where: {name: 'Brown Rice'}})

            expect(ingredientsFound.length).toEqual(1)
            expect(ingredientsFound[0].slug).toEqual('brown-rice')
            expect(ingredientsFound[0].description).toBeNull()
        })
    })

    describe('Update method', () => {
        it('should update a specific existing Ingredient entry', async () => {
            await ingredientDal.update(ingredientId, {
                description: 'A legume'
            })
            const ingredient = await Ingredient.findByPk(ingredientId)
            expect(ingredient?.description).toEqual('A legume')
        })
    })
})

```

在我们上面的成分 DAL 测试中，我们定义了函数`dbTeardown`，我们在根`describe`的`beforeAll`和`afterAll`中调用它。这意味着它将在所有测试执行之前运行，并在所有测试执行之后再次运行。

我们必须考虑到我们的一些测试将覆盖在我们的数据库上运行查询的方法。为了防止扭曲生产或开发数据，我们必须使用单独的数据库专门用于测试。

在这个项目中，我们通过在我们的`.env`中定义一个名为`TEST_DB_NAME`的变量来做到这一点，我们在测试运行期间将这个变量传递给我们的 Sequelize 实例，如下所示:

```
# src/db/config.ts

require('dotenv').config()

import { Dialect, Sequelize } from 'sequelize'

const isTest = process.env.NODE_ENV === 'test'

const dbName = isTest 
                  ? process.env.TEST_DB_NAME as string 
                  : process.env.DB_NAME as string
const dbUser = process.env.DB_USER as string
const dbHost = process.env.DB_HOST
const dbDriver = process.env.DB_DRIVER as Dialect
const dbPassword = process.env.DB_PASSWORD

const sequelizeConnection = new Sequelize(dbName, dbUser, dbPassword, {
  host: dbHost,
  dialect: dbDriver,
  logging: false,
})

export default sequelizeConnection

```

注意，Jest 在测试执行期间将环境变量`NODE_ENV`设置为值`'``test``'`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 在玩笑中理解媒人

[匹配器](https://jestjs.io/docs/using-matchers)是 Jest 的函数，用于测试我们的测试中产生的值。首先，这指的是我们附加到`expect()`的函数，比如`toEqual`和`toBeNull`。

例如，在`ingredient.test.ts`中，我们编写了覆盖`findOrCreate`方法的测试，我们期望它返回一个同名的现有条目，而不更新它。

```
# tests/integration/dal/ingredient.test.ts

describe('findOrCreate method', () => {
        beforeAll(async () => {
            await Ingredient.create({
                name: 'Brown Rice',
                slug: 'brown-rice'
            })
        })

        ...

        it('should return an existing entry where one with same name exists without updating it', async () => {
            const payload = {
                name: 'Brown Rice',
                slug: 'brownrice',
                description: 'test'
            }
            await ingredientDal.findOrCreate(payload)
            const ingredientsFound = await Ingredient.findAll({where: {name: 'Brown Rice'}})

            expect(ingredientsFound.length).toEqual(1)
            expect(ingredientsFound[0].slug).toEqual('brown-rice')
            expect(ingredientsFound[0].description).toBeNull()
        })
    })

```

`expect`函数返回 Jest 调用的 expect 对象。我们可以调用 expect 对象上的匹配器来断言达到了预期的值。

## 用 Jest 和 SuperTest 测试路由和 http 请求

我们可以使用 Jest 和 [SuperTest](https://github.com/visionmedia/supertest#readme) 来测试 API 中定义的路由。我们将用这些来测试我们的`/recipes`路线。首先，通过运行以下命令安装 SuperTest:

```
$ yarn add -D supertest @types/supertest

```

接下来，创建一个 SuperTest 请求代理的实例来调用我们的应用程序的路由。为此，我们将创建一个 helper 文件，该文件创建一个 SuperTest 代理实例，并与所有需要它的测试共享它。

```
# src/index.ts

import express, { Application, Request, Response } from 'express'
import routes from './api/routes'
import dbInit from './db/init'

dbInit()

const port = process.env.PORT || 3000

export const get = () => {
    const app: Application = express()

    // Body parsing Middleware
    app.use(express.json());
    app.use(express.urlencoded({ extended: true }));

    app.get('/', async(req: Request, res: Response): Promise<Response> => {
        return res.status(200).send({ message: `Welcome to the cookbook API! \n Endpoints available at http://localhost:${port}/api/v1` })
    })

    app.use('/api/v1', routes)
    return app
}

# tests/helpers.ts

import { agent as _request } from "supertest"
import {get as getApplication} from '../src/index'

export const request = _request(getApplication())

```

SuperTest 将我们的 Express 应用程序实例作为一个参数，它是从我们的`src/index.ts`中检索的。

现在，我们将在`tests/integration`中创建一个名为`routes`的文件夹，并添加我们的测试文件，我们将其命名为`recipe.test.ts`。

```
# /tests/integration/routes/recipe.test.ts

import Recipe, { RecipeOutput } from "../../../src/db/models/Recipe"
import { request } from "../../helpers"

const dbTeardown = async () => {
    await Recipe.sequelize?.query("SET FOREIGN_KEY_CHECKS = 0")
    await Recipe.destroy({ cascade: true, truncate: true, force: true });
    await Recipe.sequelize?.query("SET FOREIGN_KEY_CHECKS = 1")
}

describe('Recipe routes', () => {
    let recipeId: number
    let recipe: RecipeOutput

    beforeAll(async () => {
         = await Promise.all([
            Recipe.create({title: 'Pesto pasta', slug: 'pesto-pasta'}),
            Recipe.create({title: 'Caesar salad', slug: 'caesar-salad'}),
        ])
        ;({id: recipeId} = recipe)
    })

    afterAll(async () => {
        await dbTeardown()
    })

    describe('Get All', () => {
        it('should return an array of existing recipes', async () => {
            const {body: data} = await request.get('/api/v1/recipes').expect(200)
            expect(data?.length).toEqual(2)
        })
    })

    describe('Get single recipe', () => {
        it('should return a single recipe with specified id', async () => {
            const {body: data} = await request.get(`/api/v1/recipes/${recipeId}`).expect(200)

            expect(data.id).toEqual(recipeId)
            expect(data.title).toEqual(recipe.title)
        })
    })
})

```

## 使用模拟对象进行测试

我们已经讨论过单元测试，单元测试关注于单独测试单个组件，忽略了它调用的外部文件或服务。为了有效地做到这一点，我们创建模拟对象来替换那些外部文件和服务。

让我们通过为我们的审查服务编写测试来演示这一点，同时模仿它调用的 DAL 方法。

首先，我们在我们的`unit tests`目录下创建一个`services`目录，并将我们的测试文件`review.test.ts`添加到其中。

```
# /tests/unit/services/review.test.ts

import {publishReview} from '../../../src/db/services/ReviewService'
import {update as reviewDalUpdate} from '../../../src/db/dal/review'

const reviewId = 10

jest.mock('../../../src/db/dal/review', () => ({
    update: jest.fn(),
}))

const mockDate = new Date('10 Oct 2021').toISOString()
const dateSpy = jest.spyOn(global, 'Date')
    .mockImplementation(() => mockDate)

describe('Review Service', () => {
    afterAll(() => {
        dateSpy.mockRestore()
    })

    describe('Publish', () => {
        it('should accept a payload and call the review dal with it', async () => {
            await publishReview(reviewId)
            expect(reviewDalUpdate).toBeCalledTimes(1)
            expect(reviewDalUpdate).toHaveBeenCalledWith(reviewId, {isPublished: true, publishedOn: new Date() })
        })
    })
})

```

在这个测试中，我们测试了 Review 服务的`publish`方法，并模拟了 Review DAL 的 update 方法。使用模拟，我们可以确认外部方法被调用的次数和方式是我们期望的。

## 监视和覆盖对象属性

使用 Jest，我们可以监视并覆盖对象属性的实现。例如，在我们的审查服务单元测试中，我们监视了[日期类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)，它是节点的[全局对象](https://nodejs.org/api/globals.html#globals_global)的一个属性。

在上面的测试中，我们使用`jest.spyOn`来覆盖 Date 类，并定义其构造函数的模拟实现，以便每次都返回特定的值。记得在你用`jest.spyOn`覆盖的类上应用`mockRestore`。

## 运行特定的测试或测试组

Jest 提供了使用函数`only`和`skip`运行或跳过特定测试和测试组的能力。在使用中，它们看起来如下:

*   `describe.only('xxxx')`
*   `describe.skip('xxxx')`
*   `test.only('xxx')`
*   `it.only('xxxx')`
*   `it.skip('xxxx')`

您也可以通过在测试运行命令中添加测试文件的路径来运行单个测试文件。

```
$ yarn test tests/integration/dal/ingredient.test.ts

```

## 使用 Jest 检查测试覆盖率

我们可以让 Jest 通过在我们的`package.json`中将`--coverage`添加到我们的测试脚本中来检查我们的测试覆盖率。

```
# package.json

...
"main": "dist/index.js",
"scripts": {
  "test": "jest --coverage",
  "dev": "nodemon src/index.ts",
  "build": "npx tsc"
},

```

[Jest 的覆盖率](https://blog.logrocket.com/tag/jest/)表明在我们使用每个文件的指标进行的测试中，我们的应用程序被覆盖了多少。这些覆盖率指标是:

*   语句覆盖率(`% Stmts`):这表示测试运行时执行的语句的百分比
*   分支覆盖(`% Branch`):这表示在测试运行期间，在每个文件中执行的控制结构路径(即 if 和 case 语句路径)的百分比
*   函数覆盖率(`% Funcs`):这表示在测试运行期间每个文件执行的函数的百分比
*   行覆盖率(`% Lines`):这表示我们的测试在运行时执行的代码行(LOC)的百分比
*   未覆盖的行号(`Uncovered Line` `[#s]([https://paper.dropbox.com/?q=%23s](https://paper.dropbox.com/?q=%23s))`):这表示在我们的测试运行期间每个文件中未覆盖的特定行

![View of our example's Jest test coverage](img/d86986f5df97febf0642f169e3e7da6e.png)

Jest 还使用三种颜色对这些指标进行颜色编码:

*   红色代表覆盖率低
*   黄色代表平均覆盖率
*   绿色代表覆盖良好

我们项目当前的测试覆盖需要进一步改进，因为

*   我们没有在测试中覆盖所有的路由、服务或 dal(您会注意到报告的文件数量比我们拥有的文件数量少得多)
*   我们在测试中覆盖的文件仍然有几个语句、分支、函数和行未被覆盖

## 结论

现在我们已经向 API 添加了一些测试，我们可以使用`yarn test`运行它们，我们将得到如下输出:

![Our finished Jest implementation runs the tests and checks coverage](img/df60c47855210f53fae73a1f6d546806.png)

本文的所有代码都可以在我的 GitHub 上找到。请在评论中分享你在自己的项目中使用的测试库中的任何酷的发现和技巧！

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.