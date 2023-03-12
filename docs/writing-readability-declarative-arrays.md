# 使用声明性数组提高可读性

> 原文：<https://blog.logrocket.com/writing-readability-declarative-arrays/>

在过去的几年里，JavaScript 经历了一些重大的变化。ES6 的广泛采用和现代框架的兴起已经将前端行业的焦点转移到更具声明性的方法上。

命令式编程侧重于计算机运行的命令。声明性关注于你想从你的计算机中得到什么。虽然命令式方法通常更接近金属，因此性能更高，但除非您正在处理大型数据集，否则这种优势可能可以忽略不计。

通过以声明的方式操作和消化数组，您可以生成可读性更好的代码。

这里有几种方法可以做到这一点。

## 声明性数组方法

### `.reduce`

也许最强大的数组方法是`.reduce`。它通过对数组的每一项调用提供的函数来工作。这个回调函数最多接受四个参数(尽管我发现自己通常只使用前两个):

1.  `previousValue`，也就是常说的‘累加器’。这是上次调用回调时返回的值
2.  `currentValue`，数组中的当前项
3.  `currentIndex`，数组中当前项的索引
4.  `array`，这是被遍历的完整数组

除了这个回调之外，该方法还接受一个可选的初始值作为参数。如果未提供初始值，将使用数组中的第一个值。

一个非常简单的例子是获取一组数字总和的简化器。

```
const numbers = [1,2,3,4,5];
const sum = numbers.reduce(
    (accumulator, currentValue) => accumulator + currentValue
);
console.log(sum); // 15
```

回调将`currentValue`添加到`accumulator`中。因为没有提供初始值，所以它从数组中的第一个值开始。

### `.map`

类似地，`.map`将接受对数组中每个元素调用的回调。

这个回调接受三个参数:`currentValue`、`currentIndex`和`array`。

map 方法返回一个与原始数组长度相等的数组，而不是跟踪累加器。回调函数将原始数组的值“映射”到新数组中。

简单地图回调的一个例子是返回每个数字的平方。

```
const numbers = [1,2,3,4,5];
const squares = numbers.map(currentValue => currentValue * currentValue);
console.log(squares); // [1,4,9,16,25];
```

### `.filter`

`.filter`接受与`.map`具有相同参数的回调。过滤器回调应该返回“真”或“假”值，而不是像`.map`那样“转换”数组中的每个值。如果回调返回真值，那么该元素将出现在新数组中。

一个例子可能是检查一列数字是否能被 3 整除。

```
const numbers = [1,2,3,4,5,6,7,8,9];
const divisibleByThree = numbers.filter(currentValue => currentValue % 3 === 0);
console.log(divisibleByThree); // [3,6,9];
```

## 可读回调的技巧

### 1.说出你的复试

这可能是数组方法可读性最大的一次提升。通过命名数组方法回调，您可以立即提高可读性。

比较这两个:

```
const newEngland = [0,3,6,19,6];
const atlanta = [0,21,7,0,0];
const toScore = (accumulator, value) => accumulator + value;

const atlantaScore = atlanta.reduce((accumulator, value) => accumulator + value); 
const newEnglandScore = newEngland.reduce(toScore);

console.log(Math.max(newEnglandScore, atlantaScore));
```

通过为回调命名，您可以立即更好地理解代码试图完成什么。命名时，有几件事要记住。

**保持一致**。有一个好的命名约定。我喜欢把我所有的`.reduce`和`.map`试镜都叫做`toWhatever`。如果我把一组数字化简为一个和，`toSum`。

如果我将一组用户对象映射到名字。当使用`.filter`时，我喜欢将我的回调命名为`isWhatever`或`isNotWhatever`。如果我只过滤出完美的正方形，`isPerfectSquare`。

**要简洁。**理论上，你的回访应该只做一项工作——试着用一个描述性但简短的名字来描述这项工作。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 2.命名您的回调参数

像`accumulator`和`currentValue`这样的名字在编写代码时很容易找到——它们是如此的通用，以至于永远不会出错。然而，因为它们是如此的通用，它们对代码的读者没有帮助。

进一步扩展一下——如果您正在操作一个对象数组，并且只使用几个值，那么在参数列表中使用对象析构可能更具可读性。

```
const cart = [
    {
        name: 'Waterloo Sparkling Water',
        quantity: 4,
        price: 1,
    },
    {
        name: 'High Brew Coffee',
        quantity: 2,
        price: 2,
    },
];

const toTotal = (totalPrice, {quantity, price}) => 
    totalPrice + quantity * price;

const total = cart.reduce(toTotal, 0);
console.log(total); // 8
```

### 3.选择正确的方法

前面我提到过`.reduce`可能是最强大的数组方法。这是因为，由于其累加器的概念，它在返回什么方面是无限灵活的。一个`.map`必须返回一个与原数组长度相等的数组。一个`.filter`必须返回它原来的一个子集。有了`.reduce`，你可以做`.map`和`.filter`所做的一切，甚至更多……那么为什么不总是使用`.reduce`？

你应该用`.map`和`.filter` *因为*有它们的局限性。当你的代码的读者看到一个`.filter`时，他们会知道它将返回一个子集，但是如果他们看到一个`.reduce`，他们可能需要在知道这个之前查看一下回调。使用最具体的工作方法。

### 4.将小功能链接在一起

到目前为止，大部分的例子都相当自然地展示了每一个例子是如何工作的。这里有一个更类似于现实生活场景的例子:获取一组对象，类似于您可能从 API 接收到的内容，并对它们进行格式化，以便在您的应用程序上使用。

在这种情况下，假设我们从一个 API 接收附近餐馆的选择。

```
const restaurants = [
    {
        name: "Pizza Planet",
        cuisine: 'Pizza',
        hours: {
            open: 11,
            close: 22,
        },
    },
    {
        name: "JJ's Diner",
        cuisine: 'Breakfast',
        hours: {
            open: 7,
            close: 14,
        },
    },
    {
        name: "Bob's Burgers",
        cuisine: 'Burgers',
        hours: {
            open: 11,
            close: 21,
        },
    },
    {
        name: "Central Perk",
        cuisine: 'Coffee',
        hours: {
            open: 6,
            close: 20,
        },
    },
    {
        name: "Monks Cafe",
        cuisine: 'American',
        hours: {
            open: 6,
            close: 20,
        }
    },
];
```

我们希望通过在我们的网站上创建一个列表来消化(双关语)这些数据，该列表列出了附近所有目前正在营业并提供食物的餐馆。

实现这一点的一种方法是通过单个大型减速器。

```
const currentTime = 15; // 3:00 PM
const toOpenRestaurants = (openRestaurants, restaurant) => {
    const {
        name,
        cuisine,
        hours: {
            open,
            close,
        }
    } = restaurant;

    const isOpen = currentTime > open && currentTime < close;
    const isFood = cuisine !== 'Coffee';
    return isFood && isOpen ? [...openRestaurants, name] : openRestaurants;

};
const openRestaurants = restaurants.reduce(toOpenRestaurants, []);
console.log(openRestaurants); // ["Pizza Planet", "Bob's Burgers", "Monks Cafe"]
```

然而，这个缩减器做三件事:检查是否打开，检查它是否是一个有效的机构(不是 coffee)，并映射到名称。

下面是用单一目的回调编写的相同功能。

```
const currentTime = 15; // 3:00 PM
const isOpen = ({hours: {open, close} }) => 
    currentTime > open && currentTime < close;
const isFood = ({cuisine}) => cuisine !== 'Coffee';
const toName = ({name}) => name;

const openRestaurants = restaurants
    .filter(isOpen)
    .filter(isFood)
    .map(toName)
;
console.log(openRestaurants); // ["Pizza Planet", "Bob's Burgers", "Monks Cafe"]
```

将您的功能分成多个回调还有其他一些好处。如果您的任何过滤器的逻辑发生了变化，您可以很容易地准确隔离出需要发生这种变化的地方。您还可以在其他地方重用某些回调的功能(例如，您可以过滤到`isOpen`和`isPizza`)。

这种方法也使得测试变得更容易——你可以为你所有的构建模块编写单元测试，当你添加新的功能时，你只需要重用这些模块，而不需要担心任何东西被破坏。

## 结论

命令式和声明式都有它们的位置。如果你正在处理大量的数据，每一毫秒都很重要，坚持使用`while`和`for`循环。[这就是幕后发生的事情](https://www.ecma-international.org/ecma-262/5.1/#sec-15.4.4.21)。

我认为在大多数情况下，代码可读性(以及可维护性)是值得权衡的。通过有意识地使用这些回调，您可以最大限度地提高收益。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.