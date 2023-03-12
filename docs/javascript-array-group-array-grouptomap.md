# 理解 JavaScript 的数组。组和数组。GroupToMap 

> 原文：<https://blog.logrocket.com/javascript-array-group-array-grouptomap/>

***编者按:*** *这本理解 JavaScript 的`Array.Group`和`Array.GroupToMap`的指南最后一次更新是在 2023 年 1 月 9 日，以反映 API 的提议的变化。此更新还包括对这两种方法进行比较的部分、它们的优点以及有关最有效的阵列分组方式的更多信息。*

JavaScript 规范继续发展，以满足使用该语言的开发人员的需求。ECMAScript (ES)是定义不同的[API 的标准，在用 JavaScript](https://blog.logrocket.com/top-5-web-apis-performance-based-analysis/) 编码时可以使用这些 API。

多年来已经有了几个不同的 ES 版本，每个版本都旨在添加更多的 API，改进现有的 API，并确保语言保持相关性。向语言添加新 API 的过程包括从零阶段开始的提议和多个批准阶段，在零阶段，提议计划由 TC39 拥护者提交给 EMCA 委员会，或者提交给委员会并且没有被最终拒绝，到第四阶段，提议将被包括在下一个 es 版本中。

2021 年 6 月， [Justin Ridgewell](https://github.com/jridgewell/) 提出在`Array` API 中增加`group`的提案。分组数据很常见，但目前在标准的 JavaScript `Array` API 中不可用。以前，开发人员必须构建自己的解决方案或使用第三方库。

然而，这个提案，其前身是`Array.GroupBy`，目前正处于[阶段三](https://github.com/tc39/proposal-array-grouping)。这意味着它被认为是完整的，如果没有实现经验、大量使用和外部反馈，就不可能进行进一步的工作。

这意味着它应该很快成为标准。因此，让我们看看`group`和`groupToMap`以及我们如何构建自己的实现。

*向前跳转:*

## 用组对数据进行分组

此外，由于`group`和`groupToMap`数组方法仍处于提议阶段，我们不能在 JavaScript 项目中使用它们。然而，如果我们使用 [polyfill](https://blog.logrocket.com/use-polyfills-react-app/) ，浏览器将理解这些方法做什么，并为我们运行它们，而不会在我们使用它们时抛出错误。

简而言之，聚合填充是在本身不支持某个功能的 web 浏览器上实现该功能的代码。 [core-js](https://github.com/zloirock/core-js#array-grouping) 是一个健壮的聚合填充，我们可以用它来使`group`和`groupToMap`在浏览器中可用。

为了开始使用 [core-js](https://github.com/zloirock/core-js) ，我们可以在项目中运行以下代码来安装这个库:

```
// Terminal
npm install --save [email protected]

```

接下来，我们应该将它导入到我们想要使用`group`和`groupToMap`方法的文件中:

```
// JavaScript
// polyfill all `core-js` features, including early-stage proposals:
import "core-js";

```

作为快速入门，您可以查看下面的示例:

> bonarhyme 使用 core-js、react、react-dom、react-scripts 的数组分组方法示例

创建要分组的数组

### 在开始使用`group`之前，我们需要一些数据来分组。当您想要对数据进行分组时，您通常想要获取该数据的一个或多个属性，并按该属性对其进行组织。

在我们的例子中，让我们以一组狗为例:

我们的狗数组由每只狗的一个对象组成，每只狗有一个`name`、`breed`和`age`。`breed`是最常见的属性，因此我们将使用它来对数据进行分组。你也可以用`age`来得到同龄的狗。

```
// Javascript 
const DOGS = [
 {
  name: 'Groucho Barks',
  breed: 'German Shepherd',
  age: 1
 },
 {
  name: 'Pepper',
  breed: 'Shih Tzu',
  age: 3
 },
 {
  name: 'Bark Twain',
  breed: 'Dachshund',
  age: 5
 },
 {
  name: 'Jimmy Chew',
  breed: 'Shih Tzu',
  age: 10
 },
 {
  name: 'Pup Tar',
  breed: 'Dachshund',
  age: 2
 },
]

```

也可以用`name`。但是，在大多数情况下，这些名称不会匹配或分组任何内容。那么，让我们看看如何使用建议对数据进行分组:

我们可以通过在数组实例上调用新的`group`函数来使用它，就像一个`map`、`filter`或`reduce`函数一样。`group`按照升序对数组中的每个元素调用`callback`函数。

```
const byBreed = DOGS.group(dog => {
 return dog.breed;
});

```

`group`函数返回一个新对象，其中每个键代表不同的品种，值是所有匹配狗的数组。如果我们要记录`byBreed`变量，它看起来像下面的代码:

下面是 React 项目中使用的分组方法:

```
// Javascript
// Value in console

{
    "German Shepherd": [
        {
            "name": "Groucho Barks",
            "breed": "German Shepherd",
            "age": 1
        }
    ],
    "Shih Tzu": [
        {
            "name": "Pepper",
            "breed": "Shih Tzu",
            "age": 3
        },
        {
            "name": "Jimmy Chew",
            "breed": "Shih Tzu",
            "age": 10
        }
    ],
    "Dachshund": [
        {
            "name": "Bark Twain",
            "breed": "Dachshund",
            "age": 5
        },
        {
            "name": "Pup Tar",
            "breed": "Dachshund",
            "age": 2
        }
    ]
} 

```

bonarhyme 使用 core-js、react、react-dom、react-scripts 的数组分组方法示例

> Array Group Method Example by bonarhyme using core-js, react, react-dom, react-scripts

用`groupToMap`分组数据

```
与上一节一样，您可以看看下面的例子来快速入门:
数组组使用 core-js，react，react-dom，react-scripts 通过 bonarhyme 映射方法

Array GroupToMap Map Method by bonarhyme using core-js, react, react-dom, react-scripts

`groupToMap()`方法使用其`callback`函数返回的值对数组中的元素进行分组。它返回一个将来自`callback`函数的唯一值作为键的`Map`，可以用来访问每个组中的元素数组。复杂类型，比如对象，可以通过`groupToMap()`方法用作键。
当对与某个对象关联的元素进行分组时，尤其是当该对象可能会随时间变化时，方法最有用。在我们的例子中，让我们以一组运动员为例:
我们的运动员数组为每个`athlete`包含一个对象，每个对象有一个`name`、`nationality`、`age`和`height`。为了获得参加体育比赛的资格，`age`是最常见的属性，因此我们将使用它来对数据进行分组:

```
const ATHLETES = [
 {
  name: 'Blessing Ogabere',
  nationality: 'Nigerian',
  age: 26,
  height: '78ft'
 },
  {
  name: 'Usain Bolt',
  nationality: 'Jamican',
  age: 29,
  height: '82ft'
 },
{
  name: 'Fred KERLEY',
  nationality: 'USA',
  age: 16,
  height: '82ft'
 },
{
  name: 'Oblique SEVILLE',
  nationality: 'Jamican',
  age: 17,
  height: '82ft'
 },
]

```

下面的代码使用带有 [`arrow`函数](https://blog.logrocket.com/anomalies-in-javascript-arrow-functions/)的`groupToMap()`，该函数根据元素的年龄是否大于 18，返回名为`eligible`或`ineligible`的对象键。返回的结果对象是一个`Map`。

```
const eligible  = {eligible: true };
const ineligible = {ineligible: true };

ATHLETES.groupByToMap((athlete, index, array) => {
  return athlete.age > 18 ? eligible: ineligible;
});

// results: 
Map { 
{eligible: true}: [
{
  name: 'Blessing Ogabere',
  nationality: 'Nigerian',
  age: 26,
  height: '78ft'
 },
  {
  name: 'Usain Bolt',
  nationality: 'Jamican',
  age: 29,
  height: '82ft'
 }
],
 {ineligible: true}: [
{
  name: 'Fred KERLEY',
  nationality: 'USA',
  age: 16,
  height: '82ft'
 },
{
  name: 'Oblique SEVILLE',
  nationality: 'Jamican',
  age: 17,
  height: '82ft'
 }
] 
}

```

为了从`Map`中获得一个数组，我们需要用密钥调用`get()`来获得数组:
下面是 React 项目中使用的`groupToMap`方法:

```
const results = ATHLETES.groupByToMap((athlete) => {
  return athlete.age > 18 ? eligible: ineligible;
});

console.log(results.get(eligible));

//result:
[
{
  name: 'Blessing Ogabere',
  nationality: 'Nigerian',
  age: 26,
  height: '78ft'
 },
  {
  name: 'Usain Bolt',
  nationality: 'Jamican',
  age: 29,
  height: '82ft'
 }
]

```

数组组使用 core-js，react，react-dom，react-scripts 通过 bonarhyme 映射方法

Array GroupToMap Map Method by bonarhyme using core-js, react, react-dom, react-scripts

`group`和`groupToMap`方法之间的区别

```
`Group`

 `GroupToMap` |
 这将返回一个新对象，其中每个键都是您排序时使用的不同键 |

 这将返回一个将来自`callback`函数的唯一值作为键的`Map`，可用于访问每个组中的元素数组 |
 可以使用不同的值访问返回的结果，这些值现在已经被设置为键，就像在常规对象中一样 |

 可以使用`Maps`中的`get`方法访问返回的结果 |
 为排序指定的键值用于对数组进行分组 |

 您创建的自定义值用于对阵列进行分组 |
 这不需要条件来排序数组 |

 这需要一个条件来对数组进行分组 |
 `group`的替代方案 |

使用 Lodash
Lodash 是最著名的 JavaScript 实用程序库，它的创建是为了解决许多在旧版本 JavaScript 中不存在的 API 缺失问题。
虽然现在存在许多 API，但人们在构建应用程序时仍然使用 Lodash。Lodash 有一个可以作为数组传递的 [`groupBy`函数](https://lodash.com/docs/4.17.15#groupBy)和一个`callback`函数。我们可以在下面看到如何使用 Lodash 实现我们的狗的分组示例:
这会产生与`groupBy`提案相同的结果。然而，使用 Lodash 版本的主要区别是需要导入 Lodash 包并调用数组中传递的函数，而不是调用数组上的函数。

```
import groupBy from 'lodash.groupby';

const byBreed = groupBy(dogs, dog => {
  return dog.breed;
});

```

使用 Ramda
 [Ramda](https://ramdajs.com/) 是另一个 JavaScript 实用程序库，它与 Lodash 的主要区别在于它的功能重点。Ramda 是一个一级公民库的函数式编程，你可以[很容易地找到实用函数](https://blog.logrocket.com/a-closer-look-at-javascript-closures-higher-order-functions-and-currying/)。
在下面的例子中，很难看出它和使用 Lodash 有什么区别:
在这个例子中，我们创建了一个`byBreed`函数，稍后我们可以用狗的列表来调用它。当我们调用拉姆达的`groupBy`时，我们也可以传入`DOGS`:

```
import R from 'ramda';

const byBreed = R.groupBy(function(dog) {
 return dog.breed
});

byBreed(DOGS);  

```

这存档了相同的结果，但是如果我们使用 Ramda 的函数性质，我们可以组合多个函数:

```
R.groupBy(dog => dog.breed, DOGS);

```

在这个例子中，我们可以反转每只狗的名字，将它们分组，并使其成为一个可重用的函数。

```
const byBreed = R.groupBy(function(dog) {
 return dog.breed
});

const reverseName = R.map(function(dog) {
  return {
    ...dog,
    name: dog.name.split('').reverse().join('')
  }
})

const processDogs = R.compose(byBreed, reverseName)
processDogs(DOGS);

```

创建您自己的`group`
添加一个像 Lodash 或 Ramda 这样的外部库既有好处也有缺点。好处包括库提供的附加实用程序。然而，缺点包括最终用户的额外捆绑包大小和管理第三方依赖项的开销。
因为`group`函数目前不在核心语言中；你可以创建你自己的`group`函数来使用，直到正式版本发布。让我们看看如何创建我们的版本:
在上面的例子中，我们创建了一个新的函数，它接受一个列表和一个键。这个键是我们要分组的。然后我们使用 [`Array.reduce`函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)进行分组。

```
const group = (list, key) => {
    return list.reduce((prev, curr) => {
        return {
            ...prev,
            [curr[key]]: [
                ...(prev[key] || []),
                curr, 
            ]
        }    
    }, {})
}
group(DOGS, 'breed')

```

我们的`reduce`将狗的数组转换成一个对象，然后遍历每只狗，并将它们添加到对象中。如果该品种(`key`)在对象上不存在，则创建该对象。对象`key`的值是一个添加了品种的数组。
对阵列进行分组的最有效方法
在编写本文时，实际上对数组进行分组的最有效的方法是使用 [`Array.reduce`](https://blog.logrocket.com/write-more-readable-react-code/#:~:text=The%20Array.reduce%20function%20for%20data%20conversion) 方法。本文“创建您自己的组”一节的示例中使用了这种方法。然而，使用`group`和`groupToMap`数组方法意味着我们实际上是在使用一种已经被证实工作良好的方法。
结论
增加`group`和`groupToMap`是对语言的一个小改动。不过，通过深入研究，我们可以看到 JavaScript 提案流程是如何工作的，现有的库是如何应对挑战的，以及我们如何创建自己的函数版本。
通过理解上下文，更容易地调试 JavaScript 错误

## 调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

.

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.

```

```