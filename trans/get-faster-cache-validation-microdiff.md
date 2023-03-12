# 通过 MicroDiff - LogRocket 博客获得更快的缓存验证

> 原文：<https://blog.logrocket.com/get-faster-cache-validation-microdiff/>

速度和性能是任何应用程序的一个重要方面，提高应用程序速度的最流行的技术之一是实现缓存。

通过缓存，我们可以存储应用程序的数据以便快速访问，并避免重复运行昂贵的查询或应用程序逻辑。

缓存的一个重要方面是确保我们从缓存中发送的数据是相关的和最新的，这导致我们进行缓存验证。

缓存验证可以通过多种方式处理，包括设置缓存过期时间或在应用程序数据发生变化时主动使数据失效。

在本文中，我们将在一个简单的 API 应用程序中实现缓存，并在应用程序中处理缓存验证。为了验证我们的缓存数据，我们将利用一个名为 MicroDiff 的简单差异库来帮助指示我们的缓存的相关性。

MicroDiff 是一个轻量级的 JavaScript 库，它可以检测对象和数组的差异，由于其不到 1kb 的极小的大小、速度以及缺乏依赖性而广受欢迎。

对于我们的缓存验证来说，MicroDiff 是一个很好的选择，因为它不会不必要地增加我们的应用程序，同时提供清晰的差异检测。使用它，我们可以监听表明需要更新缓存数据的数据更改，而不会降低应用程序的速度。

## 教程先决条件

要阅读本文，请安装以下软件:

## 设置项目

对于我们的项目，我们将利用以前构建的 [simple Express.js API](https://blog.logrocket.com/using-sequelize-with-typescript/) 并扩展它来实现缓存和缓存验证。我们将从克隆 API 应用程序开始:

```
$ git clone [email protected]:ibywaks/cookbook.git 

```

接下来，让我们安装我们的缓存库， [node-cache](https://www.npmjs.com/package/node-cache) ，以及我们的差异库 [MicroDiff](https://github.com/AsyncBanana/microdiff) :

```
$ yarn add microdiff node-cache

```

## 在我们的项目中设置缓存

接下来，我们将从在项目中使用自定义缓存类封装缓存库开始。这样，我们可以轻松地在缓存库之间切换，而无需在应用程序中进行大量编辑:

```
# src/lib/local-cache.ts

import NodeCache from 'node-cache'

type CacheKey = string | number

class LocalCache {
    private static _instance: LocalCache
    private cache: NodeCache
    private constructor(ttlSeconds: number) {
        this.cache = new NodeCache({
            stdTTL: ttlSeconds,
            checkperiod: ttlSeconds * 0.2,
        })
    }
    public static getInstance(): LocalCache {
        if (!LocalCache._instance) {
            LocalCache._instance = new LocalCache(1000)
        }
        return LocalCache._instance
    }
    public get<T>(key: CacheKey): T | undefined {
        return this.cache.get(key)
    }
    public set<T>(key: CacheKey, data: T): void {
        this.cache.set(key, data)
    }
    public unset(key: CacheKey): void {
        this.cache.del(key)
    }
    public hasKey(key: CacheKey): boolean {
        return this.cache.has(key)
    }
}

export default LocalCache.getInstance()

```

## 缓存应用程序数据

当在应用程序中实现缓存以提高速度和性能时，考虑应该缓存哪些数据是很重要的。应该缓存以提高性能的一些重要数据标准是:

*   不经常更改的数据
*   频繁提取/读取的数据
*   来自复杂查询、逻辑或外部应用程序的数据

在您的应用程序中实现缓存时，一个重要的考虑因素是重点缓存读取频率高于更改频率的数据。

我们的 cookbook API 有一个列出食谱的端点，用户可能会比添加新食谱更频繁地通读这些食谱。因此，recipes list 端点是缓存的绝佳选择。

此外，cookbook API 用户将经常通读可用食谱的列表。因此，让我们给我们的`GET` recipes 端点添加一些缓存。

首先，让我们从检索数据后更新控制器上的缓存开始:

```
# src/api/controllers/recipes/index.ts

...
import localCache from '../../../lib/local-cache'

...
export const getAll = async (filters: GetAllRecipesFilters): Promise<Recipe[]> => {
    const recipes = await service.getAll(filters)
      .then((recipes) => recipes.map(mapper.toRecipe))

    if (recipes.length) {
        localCache.set(primaryCacheKey, recipes)
    }
    return recipes
}

```

接下来，我们将创建一个中间件来检查缓存中是否存在数据。在存在缓存数据的地方，我们返回数据作为响应，否则我们调用控制器来检索数据并保存到缓存中:

```
# src/lib/check-cache.ts

import { Request, Response, NextFunction } from 'express'
import LocalCache from './local-cache'

export const checkCache = (req: Request, res: Response, next: NextFunction) => {
    try {
        const {baseUrl, method} = req
        const [,,,cacheKey] = baseUrl.split('/')
        if (method === 'GET' && LocalCache.hasKey(cacheKey)) {
            const data = LocalCache.get(cacheKey)
            return res.status(200).send(data)
        }
        next()
    } catch (err) {
        // do some logging
        throw err
    }
}

```

在上面的代码片段中，我们从`baseUrl`中提取我们的资源名称，也就是从`api/v1/recipes`中提取`recipes`。这个资源名称是我们检查数据缓存的关键。

接下来，我们将缓存检查器中间件添加到适当的路由:

```
# src/api/routes/recipes.ts

...
import {checkCache} from '../../lib/check-cache'

const recipesRouter = Router()

recipesRouter.get('/', checkCache, async (req: Request, res: Response) => {
    const filters: GetAllRecipesFilters = req.query

    const results = await controller.getAll(filters)

    return res.status(200).send(results)
})

```

## 使用微 Diff 的缓存验证

既然我们已经成功地缓存了我们最常访问的端点`/api/v1/recipes`，那么处理食谱被更改或添加的情况以确保我们的用户总是收到最相关的数据就很重要了。

为了做到这一点，我们将通过监听我们的序列模型的变更挂钩来监听数据的最低级别变更点。我们通过创建一个钩子定义并[将其添加到我们的 Sequelize 初始化](https://blog.logrocket.com/why-you-should-avoid-orms-with-examples-in-node-js-e0baab73fa5/#usingsequelizewithnodejs)中来做到这一点:

```
# src/db/config.ts

...
import { SequelizeHooks } from 'sequelize/types/lib/hooks'
import localCache from '../lib/local-cache'

...

const hooks: Partial<SequelizeHooks<Model<any, any>, any, any>> = {
  afterUpdate: (instance: Model<any, any>) => {
    const cacheKey = `${instance.constructor.name.toLowerCase()}s`
    const currentData = instance.get({ plain: true })

    if (!localCache.hasKey(cacheKey)) {
      return
    }

    const listingData = localCache.get<any>(cacheKey) as any[]
    const itemIndex = listingData.findIndex(
      (it) => it.id === instance.getDataValue('id')
    )
    const oldItemData = ~itemIndex ? listingData[itemIndex] : {}

    const instanceDiff = diff(oldItemData, currentData)

    if (instanceDiff.length > 0) {
      listingData[itemIndex] = currentData
      localCache.set(cacheKey, listingData)
    }
  },
  afterCreate: (instance: Model<any, any>) => {
    const cacheKey = `${instance.constructor.name.toLowerCase()}s`
    const currentData = instance.get({ plain: true })

    if (!localCache.hasKey(cacheKey)) {
      return
    }

    const listingData = localCache.get<any>(cacheKey) as any[]

    listingData.push(currentData)
    localCache.set(cacheKey, listingData)
  },
}

const sequelizeConnection = new Sequelize(dbName, dbUser, dbPassword, {
  host: dbHost,
  dialect: dbDriver,
  logging: false,
  define: {hooks}
})

export default sequelizeConnection

```

上面我们在 Sequelize 实例上定义了监听`create` s 和`update` s 的钩子。对于我们缓存的模型上的`create`,我们只是用新的数据实例更新缓存的对象。

对于使用我们缓存的数据更新模型，我们使用我们的`microdiff`库将旧的缓存数据与更新的模型数据进行比较。该库突出显示数据中的变化，并指示需要更新缓存。

将缓存应用于应用程序时，最重要的考虑因素之一是确保为用户提供最新和有效的数据，这就是为什么我们应用缓存失效技术来确保缓存的数据是正确的，特别是当应用程序允许用户写入和更新数据源时。

当执行缓存失效时，缓存变得对更改过于敏感是很常见的，这意味着每次写入或更新操作，无论是否显著改变数据，都会转换为对缓存的更新。

避免缓存敏感性是在我们的缓存验证中使用`microdiff`的一个重要影响，因此我们可以通过首先检测到有要更新的更改来避免对我们的缓存进行不必要的写入或刷新。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 结论

在本文中，我们探讨了缓存、缓存的好处以及缓存数据的重要注意事项。我们还检查了缓存数据的有效性，特别是使用`Microdiff`检查缓存数据的变化。

这篇文章的所有代码都可以在 GitHub 上找到。我希望你能在评论中分享，你是如何处理缓存验证的，以及你在项目中使用的任何很酷的缓存技巧！

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。