# ES6 键控集合:地图和集合- LogRocket 博客

> 原文：<https://blog.logrocket.com/es6-keyed-collections-maps-and-sets/>

JavaScript 键控集合是数据的结构化集合，存储值并提供轻松访问它们的方法。听起来很像数组和对象，对吗？

键控集合实际上是在 ES6 中引入的，作为数组和对象的替代，这是 JS 中当时唯一可用的数据结构。尽管对象和数组对于存储数据来说已经足够好了，但是它们也有一些限制，使用起来有点痛苦。

例如，要迭代一个对象或对其进行排序，必须先将其转换为数组，然后执行这些操作。对于数组来说，循环不是问题，但是如果您需要取出一个没有索引的特定值，那就一点也不简单了。

引入键控集合(即，`Map`、`Set`、`WeakMap`和`WeakSet`)是为了解决这些问题，并使处理值或键值对更容易。

在本指南中，我们将仔细研究映射和集合，检查它们的语法，它们与对象和数组的区别，以及它们提供的方法。我们也会比较他们的表现。

## JavaScript 集

JavaScript 集合类似于数组，因为它们也是值的集合。但是，与数组不同，这些数据集合只能包含唯一的值。换句话说，一个集合中不能有重复项。

数组和集合的第二个区别是，在集合内部，值的存储没有特定的顺序，所以你可以直接用它们的名字来调用它们。

让我们创建一个新的场景来看看它是如何工作的。你可以在[jsdild](https://jsfiddle.net/L1whv89z/4/)上找到第一个练习中使用的例子。

```
let events = new Set();
let event1 = { type: "concert", day: "Saturday" };
let event2 = { type: "book launch", day: "Wednesday"};
let event3 = { type: "conference", day: "Thursday"};
let event4 = { type: "meet up", day: "Monday" };
// Let's add each event to the set
events.add(event1);
events.add(event2);
events.add(event3);
events.add(event4);

```

如您所见，语法非常简单。您使用新的`Set()`方法创建一个新的集合，并使用`add()`方法来推送集合中的值。

要查看集合包含哪些值，可以在一个`for … of`循环中使用`values()`方法。

```
for (let item of events.values()) {
  console.log(item);
}

```

如果你想检查一个特定的值是否存在于集合中，你可以使用`has(value)`方法。要删除一个项目，可以使用`delete(value)`方法。

```
console.log(events.has(event2));

events.delete(event3);
for (let value of events) {
        console.log(value)
}

```

可用于集合的其他有用方法包括`clear()`和`size()`。前者从集合中移除所有项目，而后者返回集合中元素的数量。

我们已经确定这种类型的集合只能包含唯一的值。如果我们想添加一个事件两次会发生什么？

```
let cities = new Set();

let city1 = { name: "London" };
let city2 = { name: "Barcelona"};
let city3 = { name: "Milan"};

cities.add(city1);
cities.add(city2);
cities.add(city1);
cities.add(city3);

cities.forEach((city, cities) => {
  console.log(city);
});

```

这将列出三个城市的名称，每个城市只列出一次。

如您所见，set 的语法和方法非常简单且易于使用。但是什么时候以及为什么要使用这种类型的键控集合呢？

## 集合与阵列:用途和性能

如果您想要执行诸如过滤和返回数据集合中的唯一值之类的操作，那么将集合转换为数组很容易，反之亦然。

下面是如何将集合转换为数组:

```
let set = new Set([9, 15, "a string", {"objectKey": "objectValue"}]);
set.add(true);

let arr = [...set]; // destructuring

console.log(arr); fj

// Outputs [9, 15, "a string", {objectKey: "objectValue"}, true]

```

如您所见，该集合这次包含了数据类型的组合:数字、字符串、对象和布尔值。为了将这个集合转换成一个数组，我们使用了重构。

下面是如何将数组转换为集合:

```
let arr2 = [9, 15, "a string", {"objectKey": "objectValue"}];

let arr2converted = [...new Set(arr2)];

console.log(arr2converted);

// Outputs [9, 15, "a string", {objectKey: "objectValue"}, true]

```

同样，你可以在[JD didd](https://jsfiddle.net/38gy4sbz/1/)上找到这个练习的代码。

现在让我们看一个例子，我们在一个数组中有重复的项，我们想把它们过滤掉。我们可以通过两种方式做到这一点:

```
// Method 1

let users = ["John", "Murray", "Jane", "Jane", "Anne"];

function unique(users) {
        return Array.from(new Set(users));
}

console.log(unique(users));

// Method 2

let set = new Set(users);
let arrFromSet = [...set];

console.log(arrFromSet);

```

这个练习的代码可以在[jsdild](https://jsfiddle.net/bp7tofe6/)上找到。

最后，假设我们想要将上面的所有用户添加到一个新的集合和一个新的数组中。让我们看看哪个集合执行操作更快。

```
let arr = [], set = new Set();
let users = ["John", "Murray", "Jane", "Jane", "Anne", "John", "Murray", "Jane", "Jane", "Anne"];

for (let i = 0; i < users.length; i++) {
  arr.push(users[i]);
  set.add(users[i]);
}

let result;

console.time('Array'); 
result = arr.indexOf("Anne") !== -1; 
console.timeEnd('Array');

console.time('Set'); 
result = set.has("Anne"); 
console.timeEnd('Set');

```

直接在您的控制台中运行此代码。结果如下:

```
Array: 0.013916015625ms
Set: 0.0078125ms

```

这里差别很小，但是设置更快。如果在大数据集上执行这样的操作，后一种集合类型是更好的选择。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## JavaScript 地图

在需要使用键-值对但又希望有更多灵活性的情况下，可以使用映射来代替对象。顾名思义，它们只是用来将一个键映射到一个值。

在 JavaScript 对象中，键对中的每个键都需要是字符串或符号。然而，在地图中，键是不受限制的，这意味着您可以使用另一个对象、一个函数甚至一个基本类型作为键。

地图语法如下所示:

```
let users = [{
    id: 1,
    name: 'John'
  },
  {
    id: 2,
    name: 'Murray'
  },
  {
    id: 3,
    name: 'Jane'
  },
  {
    id: 4,
    name: 'Jane'
  },
  {
    id: 5,
    name: 'Anne'
  }
]

let userNames = users.map(function(user) {
  console.log(user.name)
});

```

如果没有这种类型的键控集合，您必须首先创建一个空数组，然后将所有用户名放入该数组。

```
let userNms = [];

users.forEach(function (user) {
  userNms.push(user.name);
});

console.log(userNms);

```

这是这个练习的代码。

地图的使用方法与集合类似:`clear`、`delete`、`has`、`values`、`entries`、`forEach`。我们不会涵盖所有的方法，但是我们将看三个特定于地图的方法:`set()`、`get()`和`entries()`。

`Set()`向`Map`对象添加一个键-值对，而`get()`检索指定键的值。

这里有一个例子:

```
const user1 = new Map();
user1.set('id', 1);
user1.set('name', 'John');

console.log(user1.get('id'));

```

如果我们想从 map 集合中获取键值对，该怎么办？我们可以在迭代器中使用`entries()`方法。

```
const user1 = new Map();
user1.set('id', 1);
user1.set('name', 'John');

console.log(user1.get('id'));

let iterator = user1.entries();

console.log(iterator.next().value);
console.log(iterator.next().value);

```

下面是[代码](https://jsfiddle.net/uvjk7wps/)。

## 地图与对象:用途和性能

映射和对象非常相似，但主要区别是在映射中，任何数据类型都可以是键，所以不仅限于字符串。当您想要存储与对象相关的数据，但由于对象的局限性，不想将数据添加到对象本身或使用对象数组时，这是非常有用的。

您还可以直接迭代映射的键或值。对于对象，您首先必须将它们转换成数组，这并不总是可行的。下面是一个地图集合的迭代示例。

```
let userIDs = new Map();

let user1 = {name: 'John'}, user2 = {name: 'Murray'}, user3 = {name: 'Jane'};

userIDs.set(user1, 1) .set(user2, 2) .set(user3, 3);

// Method 1

for (let [name, id] of userIDs) {
  console.log(name);
  console.log(id);
}

// Method 2

userIDs.forEach((name, id) => {
  console.log(name);
  console.log(id);
});

```

下面是这个例子的[代码。](https://jsfiddle.net/2sgcqewo/)

要将对象转换成地图，我们可以使用`Object.entries()`方法。

```
const obj = {
  'name': John,
  'id': 1,
}

const map = new Map(Object.entries(obj));

console.log(map.get('name')) 
// Outputs John

```

现在，让我们将一个对象与一个地图进行比较，看看它们在性能方面表现如何。当我们比较集合和数组时，我们将使用和以前一样的例子。

```
let obj = {}, map = new Map();

let users = ["John", "Murray", "Jane", "Jane", "Anne", "John", "Murray", "Jane", "Jane", "Anne"];

for (let i = 0; i < users.length; i++) {
  obj[i] = i;
  map.set(i, i);
}

let result;

console.time('Object'); 
result = obj.hasOwnProperty("Anne"); 
console.timeEnd('Object');

console.time('Map'); 
result = map.has("Anne"); 
console.timeEnd('Map');

```

你可以在这里找到这个练习[的代码。](https://jsfiddle.net/0j2hLckq/)

这两个集合的性能如下。

```
Object: 0.031982421875ms
Map: 0.0146484375ms

```

作为比较，阵列和集的性能如下:

```
Array: 0.013916015625ms
Set: 0.0078125ms

```

如您所见，尽管映射和集合类似于数组和对象，但是这些新的键控集合更加灵活，更容易迭代，并且性能更高。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.