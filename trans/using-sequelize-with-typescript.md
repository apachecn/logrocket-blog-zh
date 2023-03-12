# 使用 Sequelize 和 TypeScript - LogRocket Blog

> 原文：<https://blog.logrocket.com/using-sequelize-with-typescript/>

在您的 API 中编写原始 SQL 已经过时了，或者最好是保留给真正复杂的查询使用。对于大多数 API 来说，使用许多对象关系映射器(ORM)中的一个就足够了。

ORM 还方便地封装了与数据库及其查询语言通信的复杂细节。

这意味着您可以在多种数据库类型上使用单个 ORM，如 MySQL、PostgreSQL 或 MongoDb，从而可以轻松地在数据库之间切换，而无需重写代码！您还可以将不同类型的数据库连接到您的项目，同时使用相同的代码来访问它们。

在本文中，您将学习如何使用带有 TypeScript 的 Sequelize ORM。因此，拿起您的笔记本电脑，打开您的 IDE，让我们开始吧！

## 先决条件

要阅读本文，请安装以下软件:

## 设置项目

为了开始我们的项目，让我们设置一个简单的 Express.js API 来创建一个存储食谱和配料的虚拟食谱，并用流行的类别标记我们的食谱。

首先，让我们通过在终端中键入以下命令来创建项目目录:

```
$ mkdir cookbook
$ cd cookbook

```

在新的`cookbook`项目目录中，使用`yarn`安装所需的项目依赖项。首先，运行`npm init`，用一个`package.json`文件初始化 Node.js 项目:

```
$ npm init

```

Node.js 项目初始化后，安装从`express`开始的依赖项:

```
$ yarn add express

```

接下来，通过运行以下命令将[类型脚本](https://www.typescriptlang.org/)添加到项目中:

```
$ yarn add -D typescript ts-node @types/express @types/node

```

💡请注意，我们在安装命令中添加了一个标志`-D`。这个标志告诉 Yarn 将这些库添加为开发依赖项，这意味着只有在项目开发时才需要这些库。我们还为 Express.js 和 Node.js 添加了类型定义。

将 TypeScript 添加到我们的项目后，让我们初始化它:

```
$ npx tsc --init

```

这将创建我们的 TypeScript 配置文件`ts.config`，并设置默认值:

```
// ts.config
{
    "compilerOptions": {
      "target": "es5",                                
      "module": "commonjs",                           
      "sourceMap": true,                           
      "outDir": "dist",                              
      "strict": true,                                 
      "esModuleInterop": true,                        
      "skipLibCheck": true,                           
      "forceConsistentCasingInFileNames": true        
    }
}

```

点击了解更多关于[定制`ts.config`的信息。](https://blog.logrocket.com/boost-your-productivity-with-typescript-project-references/)

最后，让我们通过创建项目目录和文件来为我们的项目定义一个简单的 API 结构，以匹配下面的大纲:

```
- dist # the name of our outDir set in tsconfig.json
- src
  - api
    - controllers
    - contracts
    - routes
    - services
  - db
    - dal
    - dto
    - models
    config.ts
    init.ts
  - errors
  index.ts
  ts.config

```

既然我们已经在`index.ts`文件中定义了我们的项目结构，这是我们的应用程序的起点，添加下面的代码来创建我们的 Express.js 服务器:

```
# src/index.ts

import express, { Application, Request, Response } from 'express'

const app: Application = express()
const port = 3000

// Body parsing Middleware
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.get('/', async(req: Request, res: Response): Promise<Response> => {
    return res.status(200).send({ message: `Welcome to the cookbook API! \n Endpoints available at http://localhost:${port}/api/v1` })
})

try {
    app.listen(port, () => {
        console.log(`Server running on http://localhost:${port}`)
    })
} catch (error) {
    console.log(`Error occurred: ${error.message}`)
}

```

我们还必须包含一些额外的库，以便轻松运行应用程序并传入环境变量。这些附加库是使用`yarn add -D nodemon` 的 [nodemon，使用`yarn add -D eslint`](https://blog.logrocket.com/nodemon-tutorial-automatically-restart-node-js-apps-with-nodemon/) 的 [eslint，以及使用`yarn add dotenv`](https://eslint.org/) 的 [dotenv。](https://blog.logrocket.com/customizing-node-js-env-files/)

## 设置顺序表单

此时，Express.js 应用程序正在运行，所以是时候引入有趣的东西了:Sequelize ORM！

通过运行以下命令，将 Sequelize 添加到项目中:

```
$ yarn add sequelize
$ yarn add mysql2

```

虽然我们为 MySQL 添加了数据库驱动程序，这完全是基于个人偏好，但您可以安装您首选数据库的任何驱动程序。查看此处的[了解其他可用的数据库驱动程序](https://sequelize.org/master/manual/getting-started.html#installing)。

### 启动塞奎尔泽的连接

安装 Sequelize 后，我们必须启动它到我们数据库的连接。一旦启动，此连接将注册我们的模型:

```
# db/config.ts

import { Dialect, Sequelize } from 'sequelize'

const dbName = process.env.DB_NAME as string
const dbUser = process.env.DB_USER as string
const dbHost = process.env.DB_HOST
const dbDriver = process.env.DB_DRIVER as Dialect
const dbPassword = process.env.DB_PASSWORD

const sequelizeConnection = new Sequelize(dbName, dbUser, dbPassword, {
  host: dbHost,
  dialect: dbDriver
})

export default sequelizeConnection

```

### 创建和注册顺序模型

Sequelize 提供了两种注册模型的方法:[使用`sequelize.define`](https://sequelize.org/master/manual/model-basics.html#using--a-href-----class-lib-sequelize-js-sequelize-html-instance-method-define---code-sequelize-define--code---a--) 或者扩展 Sequelize 模型类。在本教程中，我们将使用模型扩展方法来注册我们的`Ingredient`模型。

我们首先创建以下接口:

*   定义我们模型的所有可能属性
*   `IngredientInput`定义传递给 Sequelize 的`model.create`的对象的类型
*   `IngredientOuput`定义从`model.create`、`model.update`和`model.findOne`返回的对象

```
# db/models/Ingredient.ts

import { DataTypes, Model, Optional } from 'sequelize'
import sequelizeConnection from '../config'

interface IngredientAttributes {
  id: number;
  name: string;
  slug: string;
  description?: string;
  foodGroup?: string;
  createdAt?: Date;
  updatedAt?: Date;
  deletedAt?: Date;
}
export interface IngredientInput extends Optional<IngredientAttributes, 'id' | 'slug'> {}
export interface IngredientOuput extends Required<IngredientAttributes> {}

```

接下来，创建一个扩展、初始化并导出`import {Model} from 'sequelize'` Sequelize 模型类的`Ingredient`类:

```
# db/models/Ingredient.ts

...

class Ingredient extends Model<IngredientAttributes, IngredientInput> implements IngredientAttributes {
  public id!: number
  public name!: string
  public slug!: string
  public description!: string
  public foodGroup!: string

  // timestamps!
  public readonly createdAt!: Date;
  public readonly updatedAt!: Date;
  public readonly deletedAt!: Date;
}

Ingredient.init({
  id: {
    type: DataTypes.INTEGER.UNSIGNED,
    autoIncrement: true,
    primaryKey: true,
  },
  name: {
    type: DataTypes.STRING,
    allowNull: false
  },
  slug: {
    type: DataTypes.STRING,
    allowNull: false,
    unique: true
  },
  description: {
    type: DataTypes.TEXT
  },
  foodGroup: {
    type: DataTypes.STRING
  }
}, {
  timestamps: true,
  sequelize: sequelizeConnection,
  paranoid: true
})

export default Ingredient

```

💡注意，我们在模型中添加了选项`paranoid: true`;这通过添加一个在调用`destroy`方法时将记录标记为`deleted`的`deletedAt`属性，对模型进行了软删除。

要完成我们的模型并在连接的数据库中创建它的目标表，运行 model `sync`方法:

```
# db/init.ts

import { Recipe, RecipeTags, Tag, Review, Ingredient, RecipeIngredients } from './models'
const isDev = process.env.NODE_ENV === 'development'

const dbInit = () => {
  Ingredient.sync({ alter: isDev })
}
export default dbInit 

```

💡`sync`方法接受`force`和`alter`选项。`force`选项强制重新创建一个表。`alter`选项创建一个不存在的表格，或者更新表格以匹配模型中定义的属性。

💡专业提示:保留在开发环境中使用`force`或`alter`,这样您就不会意外地重新创建您的生产数据库，丢失您的所有数据或应用对数据库的更改，这可能会破坏您的应用程序。

## 在 DAL 和服务中使用我们的模型

数据访问层(DAL)是我们实现 SQL 查询的地方，或者在这种情况下，是 Sequelize 模型查询运行的地方:

```
# db/dal/ingredient.ts

import {Op} from 'sequelize'
import {Ingredient} from '../models'
import {GetAllIngredientsFilters} from './types'
import {IngredientInput, IngredientOuput} from '../models/Ingredient'

export const create = async (payload: IngredientInput): Promise<IngredientOuput> => {
    const ingredient = await Ingredient.create(payload)
    return ingredient
}

export const update = async (id: number, payload: Partial<IngredientInput>): Promise<IngredientOuput> => {
    const ingredient = await Ingredient.findByPk(id)
    if (!ingredient) {
        // @todo throw custom error
        throw new Error('not found')
    }
    const updatedIngredient = await (ingredient as Ingredient).update(payload)
    return updatedIngredient
}

export const getById = async (id: number): Promise<IngredientOuput> => {
    const ingredient = await Ingredient.findByPk(id)
    if (!ingredient) {
        // @todo throw custom error
        throw new Error('not found')
    }
    return ingredient
}

export const deleteById = async (id: number): Promise<boolean> => {
    const deletedIngredientCount = await Ingredient.destroy({
        where: {id}
    })
    return !!deletedIngredientCount
}

export const getAll = async (filters?: GetAllIngredientsFilters): Promise<IngredientOuput[]> => {
    return Ingredient.findAll({
        where: {
            ...(filters?.isDeleted && {deletedAt: {[Op.not]: null}})
        },
        ...((filters?.isDeleted || filters?.includeDeleted) && {paranoid: true})
    })
}

```

将`paranoid: true`选项添加到`findAll`模型方法包括结果中设置了`deletedAt`的软删除记录。否则，默认情况下，结果不包括软删除的记录。

在上面的 DAL 中，我们使用我们的`ModelInput`类型定义定义了一些常用的 CRUD 查询，并在`db/dal/types.ts`中放置了任何额外的类型:

```
# db/dal/types.ts

export interface GetAllIngredientsFilters {
    isDeleted?: boolean
    includeDeleted?: boolean
}

```

💡Sequelize ORM 有一些非常酷的建模方法，包括`findAndCountAll`，它返回一个记录列表和所有符合过滤标准的记录的计数。这对于在 API 中返回分页列表响应非常有用。

现在我们可以创建我们的服务，它充当我们的控制器和 DAL 之间的中介:

```
# api/services/ingredientService.ts

import * as ingredientDal from '../dal/ingredient'
import {GetAllIngredientsFilters} from '../dal/types'
import {IngredientInput, IngredientOuput} from '../models/Ingredient'

export const create = (payload: IngredientInput): Promise<IngredientOuput> => {
    return ingredientDal.create(payload)
}
export const update = (id: number, payload: Partial<IngredientInput>): Promise<IngredientOuput> => {
    return ingredientDal.update(id, payload)
}
export const getById = (id: number): Promise<IngredientOuput> => {
    return ingredientDal.getById(id)
}
export const deleteById = (id: number): Promise<boolean> => {
    return ingredientDal.deleteById(id)
}
export const getAll = (filters: GetAllIngredientsFilters): Promise<IngredientOuput[]> => {
    return ingredientDal.getAll(filters)
}

```

## 使用路线和控制器启动模型

我们已经走了很长的路了！现在，我们已经有了从数据库中获取数据的服务，是时候使用路线和控制器将所有的魔力带给公众了。

让我们从在`src/api/routes/ingredients.ts`中创建我们的`Ingredients`路线开始:

```
# src/api/routes/ingredients.ts

import { Router } from 'express'

const ingredientsRouter = Router()
ingredientsRouter.get(':/slug', () => {
  // get ingredient
})
ingredientsRouter.put('/:id', () => {
  // update ingredient
})
ingredientsRouter.delete('/:id', () => {
  // delete ingredient
})
ingredientsRouter.post('/', () => {
  // create ingredient
})

export default ingredientsRouter

```

我们的 cookbook API 最终会有几个路由，比如`Recipes`和`Tags`。因此，我们必须创建一个`index.ts`文件，将不同的路由注册到它们的基本路径，并有一个中央导出来连接到我们之前的 Express.js 服务器:

```
# src/api/routes/index.ts

import { Router } from 'express'
import ingredientsRouter from './ingredients'

const router = Router()

router.use('/ingredients', ingredientsRouter)

export default router

```

让我们更新我们的`src/index.ts`来导入我们导出的路线，并将它们注册到我们的 Express.js 服务器:

```
# src/index.ts

import express, { Application, Request, Response } from 'express'
import routes from './api/routes'

const app: Application = express()

...

app.use('/api/v1', routes)

```

在创建和连接路由之后，让我们创建一个控制器来链接到我们的路由并调用服务方法。

为了支持在路线和控制器之间输入参数和结果，让我们添加数据传输对象(dto)和映射器来转换结果:

```
# src/api/controllers/ingredient/index.ts

import * as service from '../../../db/services/IngredientService'
import {CreateIngredientDTO, UpdateIngredientDTO, FilterIngredientsDTO} from '../../dto/ingredient.dto'
import {Ingredient} from '../../interfaces'
import * as mapper from './mapper'

export const create = async(payload: CreateIngredientDTO): Promise<Ingredient> => {
    return mapper.toIngredient(await service.create(payload))
}
export const update = async (id: number, payload: UpdateIngredientDTO): Promise<Ingredient> => {
    return mapper.toIngredient(await service.update(id, payload))
}
export const getById = async (id: number): Promise<Ingredient> => {
    return mapper.toIngredient(await service.getById(id))
}
export const deleteById = async(id: number): Promise<Boolean> => {
    const isDeleted = await service.deleteById(id)
    return isDeleted
}
export const getAll = async(filters: FilterIngredientsDTO): Promise<Ingredient[]> => {
    return (await service.getAll(filters)).map(mapper.toIngredient)
}

```

现在，用对控制器的调用更新路由器:

```
# src/api/routes/ingredients.ts

import { Router, Request, Response} from 'express'
import * as ingredientController from '../controllers/ingredient'
import {CreateIngredientDTO, FilterIngredientsDTO, UpdateIngredientDTO} from '../dto/ingredient.dto'

const ingredientsRouter = Router()

ingredientsRouter.get(':/id', async (req: Request, res: Response) => {
    const id = Number(req.params.id)
    const result = await ingredientController.getById(id)
    return res.status(200).send(result)
})
ingredientsRouter.put('/:id', async (req: Request, res: Response) => {
    const id = Number(req.params.id)
    const payload:UpdateIngredientDTO = req.body

    const result = await ingredientController.update(id, payload)
    return res.status(201).send(result)
})
ingredientsRouter.delete('/:id', async (req: Request, res: Response) => {
    const id = Number(req.params.id)

    const result = await ingredientController.deleteById(id)
    return res.status(204).send({
        success: result
    })
})
ingredientsRouter.post('/', async (req: Request, res: Response) => {
    const payload:CreateIngredientDTO = req.body
    const result = await ingredientController.create(payload)
    return res.status(200).send(result)
})
ingredientsRouter.get('/', async (req: Request, res: Response) => {
    const filters:FilterIngredientsDTO = req.query
    const results = await ingredientController.getAll(filters)
    return res.status(200).send(results)
})
export default ingredientsRouter 

```

此时，我们可以添加一个构建脚本来运行我们的 API:

```
# package.json

...
"scripts": {
  "dev": "nodemon src/index.ts",
  "build": "npx tsc"
},
...

```

要查看最终产品，请使用`yarn run dev`运行 API，并访问位于[http://localhost:3000/API/v1/ingredients](http://localhost:3000/api/v1/ingredients)的我们的配料端点。

## 结论

在本文中，我们使用 Express.js 设置了一个简单的 TypeScript 应用程序来使用 Sequelize ORM，并逐步完成了初始化 Sequelize、创建模型和通过 ORM 运行查询的过程。

在我们的项目中使用 Sequelize with TypeScript 有助于我们编写更少的代码并抽象数据库引擎，同时为模型输入和输出定义严格的类型。这使得我们的代码更加一致，即使我们改变数据库类型，也能防止我们的表发生 SQL 注入。

本文的完整代码可以在 Github 上找到。我希望你觉得这篇文章很容易理解，我很想听听你关于在你的应用中使用 Sequelize 的酷方法的任何想法，或者你在评论区有任何问题！

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.