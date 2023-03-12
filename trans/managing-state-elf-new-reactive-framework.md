# 用 Elf 管理状态，一个新的反应式框架

> 原文：<https://blog.logrocket.com/managing-state-elf-new-reactive-framework/>

## 介绍

状态管理是控制状态如何更新以及如何从一个组件传递到另一个组件的过程。不管我们使用什么库或框架，状态管理都是开发 JavaScript 应用程序的重要部分。

当使用像 Redux 这样的库[时，我们必须设置动作、reducers 和`<Provider store={store}>`来让我们的应用程序状态工作。Elf 为我们简化了状态管理，减少了在应用程序中创建重复的样板代码的麻烦。](https://blog.logrocket.com/comparing-react-native-state-management-libraries/)

在本文中，我们将了解一个新的状态管理库 [Elf](https://ngneat.github.io/elf/) 。我们将介绍它的特性，以及如何使用它来管理应用程序中的状态。

## 为什么我们需要国家管理？

状态管理是开发应用程序的挑战之一。在构建 web 应用程序时，我们可以将最原始、最基本的状态本地存储在需要它们的组件中。然而，随着我们的应用程序变得越来越大，跨多个组件管理共享状态变得越来越困难。

当这种情况发生时，状态管理变得越来越复杂，我们开始遇到一些问题，比如正确的钻取和不同 UI 组件之间的状态不同步。

这些以及更多的问题是开发人员通过构建 Elf 这样的状态管理库来解决的。

## 什么是精灵？

Elf 是一个建立在 [RxJS](https://blog.logrocket.com/using-rxjs-operators-consume-observables/) 之上的反应式和不可变的状态管理库。Elf 为我们提供了一系列管理状态的工具。正因为如此，有些术语我们应该知道，比如可观测量、观察者和订阅。

可观测量是可以在一段时间内发出数据的物体。它们充当数据源或值流的包装器

观察者是数据观察商店的消费者。如果被观察的数据发生突变或者出现错误，它们就执行一段代码，并对状态变化做出反应。它们还实现了三种方法:`next`、`error`和`complete`。我们不会详细讨论这些，因为它们是 RxJS 特有的，因此超出了本文的范围。

订阅是我们将观察者与可观察事物联系起来的方式。观察者订阅观察值，观察数据中的任何变化，并对这些变化做出反应。

## 装置

在您的终端中运行以下命令来安装 Elf:

```
npm i @ngneat/elf

```

## 精灵商店

每个 Elf 应用程序的中心是“商店”。存储是保存应用程序状态的容器。我们可以把商店想象成一个数据库。

一个 Elf 商店接收一个包含三个属性的对象——一个`state`、一个`name`和一个`config`。

让我们通过创建一个状态来看看商店是如何工作的:

```
import { Store, createState, withProps } from '@ngneat/elf';

const { state, config } = createState({
  data: { name: "John", email: "[email protected]" },
});

const formStore = new Store({ state, name: 'userData', config });

```

`createState`方法返回`state`和`config`对象，我们用它们来创建商店。

### 进入商店

Elf 商店是可观察的，因此我们可以订阅它们并获得它们的值，如下所示:

```
formStore.subscribe((state) => {console.log(state)});

```

## `select`操作员

`select`操作符使我们能够选择商店的一个“部分”(或特定部分)。

例如，假设我们有下面的商店:

```
const dummyStore = {
  todos: [
    { text: "Learn Elf", completed: true },
    { text: "Use Elf", completed: false },
  ],
  users: [ { name: "Jack" }, { name: "Jill" } ],
};

```

`dummyStore`对象中的每个键/值对都是一个切片；`dummyStore.todos`和`dummyStore.users`是切片。

现在让我们看看操作符是如何工作的:

```
import { select } from '@ngneat/elf';

const userEmail = formStore.pipe(select((state) => state.data.email));

```

`select`操作符仅在状态改变时或者当有一个新的对所选状态的引用时运行。

我们使用[管道操作符](https://rxjs.dev/guide/operators#piping)将多个函数链接在一起，以保持代码的可读性。

## Elf 实体

精灵实体是精灵商店的独特类型。实体的行为方式与数据库中的表相同，我们可以在实体中存储大量相似的数据。

例如，如果我们有一个服装电子商务商店，里面有鞋子、衬衫和运动鞋的数据，那么实体就是存储这些数据的合适位置。

我们可以使用`ngneat/elf-entities`包在 Elf 中设置实体。我们还可以使用 Elf 提供的不同查询和变异方法对这些实体进行查询和变异，我将在下面解释。

### 实体查询

Elf 提供了几个查询，我们可以使用它们在实体上运行查询操作。让我们来看看其中的一些查询操作。

`selectFirst`查询返回商店中的第一个实体。你可以这样使用它:

```
import { selectFirst } from '@ngneat/elf-entities';
const users = usersStore.pipe(selectFirst());

```

`selectLast`查询返回商店中的最后一个实体:

```
import { selectLast } from '@ngneat/elf-entities';
const users = usersStore.pipe(selectLast());

```

`selectAll`查询返回商店集合中的所有实体:

```
import { selectAll } from "@ngneat/elf-entities";
const users = usersStore.pipe(selectAll());

```

### 实体突变

有几种方法可以用来变异 Elf 实体。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们可以使用`addEntities`突变将实体添加到存储中。我们可以通过将一组数据传递给`addEntities`来将多个实体添加到一个存储中，如下所示:

```
import { addEntities } from '@ngneat/elf-entities';

//add a single entity
usersStore.update(addEntities(user));

//add multiple entities
usersStore.update(addEntities([user1, user2]));

```

我们可以使用`deleteEntities`突变从一个存储中添加单个或多个实体。要删除多个实体，我们传入一个包含要删除的实体的 id 的数组:

```
import { deleteEntities } from '@ngneat/elf-entities';

//delete a single entity
usersStore.update(deleteEntities(id));

//delete multiple entities
usersStore.update(deleteEntities([id, id]));

```

我们可以使用`updateEntities`突变来更新商店中的实体:

```
import { updateEntities } from '@ngneat/elf-entities';

usersStore.update(updateEntities(id, { name }));

```

## `persistState`功能

`persistState`功能使我们能够通过将应用程序的一些状态保存到浏览器的本地或会话存储中来保存它们。

我们需要安装`elf-persist-state`包才能使用`persistState`功能。在您的终端中运行以下命令来安装软件包:

```
npm i @ngneat/elf-persist-state

```

现在，让我们看看`persistState`是如何工作的:

```
import { Store, createState, select } from "@ngneat/elf";
import {
  persistState,
  localStorageStrategy,
  sessionStorageStrategy,
} from "@ngneat/elf-persist-state";

const { state, config } = createState({
  data: { name: "John", email: "[email protected]" },
});

const formStore = new Store({ state, name: "userData", config });

export const persist = persistState(formStore, {
  key: "auth",
  storage: localStorageStrategy,
});

```

`persistState`函数有两个参数。第一个是我们想要持久化的存储，第二个是一个`options`对象。我们在对象中定义了`key`和`storage`。

`key`是我们保存持久状态的名字。`key`充当状态的标签或标识符。`storage`是我们定义想要用来保持状态的策略的地方。

`elf-persist-state`包提供了两种存储策略，`localStorageStrategy`和
`sessionStorageStrategy`。我们可以选择最适合我们应用需求的策略。在这里，我们使用了`localStorageStrategy`。

## 结论

我们已经简单介绍了 Elf 和它的一些功能。然而，Elf 还能提供更多。Elf 的一大卖点就是学习曲线低。与 Redux 等其他状态管理解决方案相比，Elf 是一个初学者友好的库，并且易于使用。

我对 Elf 的一个问题是它的文档。该文档缺少许多有用的上下文，这些上下文将使该库更容易学习。也缺乏有助于展示 Elf 全部能力的例子。

由于学习曲线低，我会使用 Elf，但只用于个人或宠物项目。请注意，它仍然是一个相对较新的库，所以请查看文档，熟悉这个令人惊叹的状态管理框架。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。