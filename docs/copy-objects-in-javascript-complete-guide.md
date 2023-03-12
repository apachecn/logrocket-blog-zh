# 如何在 JavaScript 中复制对象:完整指南

> 原文：<https://blog.logrocket.com/copy-objects-in-javascript-complete-guide/>

***编者按:**本帖于 2022 年 3 月 23 日更新，加入了 JavaScript 和 TypeScript 中复制对象的更新信息，包括结构化克隆技术。*

当使用函数式编程时，一个好的经验法则是总是创建新的对象，而不是改变旧的对象。这样做，我们可以确保我们对对象结构的干预不会影响应用程序中一些看似不相关的部分，从而使整个代码更加可预测。

我们如何确切地确定我们对一个对象所做的更改不会影响到其他地方的代码呢？完全删除不需要的引用似乎是个好主意。要去掉一个引用，我们需要将对象的所有属性复制到一个新对象中。在本文中，我们将研究可以用来在 JavaScript 中复制对象的五种技术，以及何时使用每种技术。在适用的地方，我们还将演示如何使用每种技术在 TypeScript 中复制对象。TypeScript 基本上是具有静态类型的 JavaScript 的子集，但它是一些开发人员的首选。与 JavaScript 相比，Typescript 通常更容易阅读、理解和调试。

下面是我们将要讨论的五种 JavaScript 复制方法:

## 浅拷贝

对象的浅表副本将与创建副本的源对象具有相同的引用。因此，当我们修改源或副本时，我们也可能导致另一个对象发生变化。换句话说，我们可能会无意中在源或副本中创建意外的更改。掌握有选择地修改现有元素的共享属性值和为现有元素分配全新值之间的区别非常重要。

JavaScript 为创建浅层副本提供了标准的内置对象复制操作:`Array.from()`、`Array.prototype.concat()`、`Array.prototype.slice()`、`Object.assign()`和`Object.create()`、`spread syntax`。

下面是 JavaScript 中浅层复制的一个例子:

```
let profile = ["Bruce",{"language":["English","Spanish","French"]}];

let profile_copy = Array.from(profile);

profile_copy[1].language = ["Igbo","Yoruba"]
console.log(JSON.stringify(profile)); // ["Bruce",{"language":["Igbo","Yoruba"]}]

profile_copy[0] = "Pascal"
console.log(profile[0]) // Bruce

console.log(JSON.stringify(profile_copy)); // ["Pascal",{"language":["Igbo","Yoruba"]}]

console.log(JSON.stringify(profile)); // ["Bruce",{"language":["Igbo","Yoruba"]}]

```

这里有一个在 TypeScript 中浅层复制的例子。在这个例子中，我们使用 spread 操作符(`…`)复制对象。

```
function shallow<T extends object>(source: T): T {
 return {
   ...source,
 }
}

let profile = {
  "name": "Pascal",
  "age": 12
}
const employee: {} = shallow(profile)
console.log(employee)

```

这是 TypeScript 中浅层复制的另一个例子。在本例中，我们创建了一个新对象，并从`source`对象中复制了每个属性:

```
function shallow<T extends object>(source: T): T {
 const copy = {} as T
 Object.keys(source).forEach((key) => {
   copy[key as keyof T] = source[key as keyof T]
 })

 return copy
}

let profile = {
  "name": "Pascal",
  "age": 23
}
const employee: {} = shallow(profile)
console.log(employee)

```

### 何时使用浅层拷贝

当我们处理只有原始数据类型(例如，字符串或数字)属性的对象时，可以使用浅层复制。如果我们的对象包含非原始数据类型(例如，函数或数组)，它可能会中断我们的程序。

## 深层拷贝

对象的深层副本将具有与创建副本的源对象不共享相同引用的属性。因此，我们可以改变源或副本，而不改变另一个对象。换句话说，对一个对象进行更改不会导致对源或副本的意外更改。

为了在 JavaScript 中制作深层副本，我们使用了`JSON.stringify()`和`JSON.parse()`方法。首先，我们使用`JSON.stringify()`函数将对象转换成 JSON 字符串。然后，我们用`JSON.parse()`方法解析字符串，创建一个新的 JavaScript 对象:

```
let profile = ["Bruce",{"language":["English","Spanish","French"]}];
let profile_deep_copy = JSON.parse(JSON.stringify(profile));

// Change the value of the 'language' property
profile_deep_copy[1].language = ["Ibo","Yoruba"]

// The 'language' property does not change in profile.
console.log(profile[1].language); //[ "English", "Spanish", "French" ]

```

现在，让我们看看如何在 TypeScript 中制作对象的深层副本。

我们的第一个例子递归工作。我们编写一个`deep`函数，它检查发送给它的参数的类型，或者为该参数调用一个适当的函数(如果它是一个数组或一个对象)，或者简单地返回该参数的值(如果它既不是数组也不是对象)。

```
function deep<T>(value: T): T {
 if (typeof value !== 'object' || value === null) {
   return value
 }
 if (Array.isArray(value)) {
   return deepArray(value)
 }
 return deepObject(value)
}

```

`deepObject`函数获取一个对象的所有键，并对它们进行迭代，为每个值递归调用`deep`函数。

```
function deepObject<T>(source: T) {
 const result = {} as T
 Object.keys(source).forEach((key) => {
   const value = source[key as keyof T]
   result[key as keyof T] = deep(value)
 }, {})
 return result as T
}

```

因此，`deepArray`遍历提供的数组，为数组中的每个值调用`deep`。

```
function deepArray<T extends any[]>(collection: T): any {
 return collection.map((value) => {
   return deep(value)
 })
}

const deep_copy = deep(["profile", {"list": ["Noodle", "Bross"]}])
console.log(deep_copy)

```

现在，让我们看看另一个采用不同方法的 TypeScript 示例。我们的目标是创建一个新对象，而不引用前一个对象，对吗？那我们为什么不使用`JSON`对象呢？首先，我们`stringify`对象，然后`parse`结果字符串。我们得到的是一个完全不知道其来源的新物体。

需要注意的是，在前面的例子中，对象的方法被保留，但是在这里没有。由于`JSON`格式不支持函数，所以一起去掉。

```
function deep<T extends object>(source: T): T {
 return JSON.parse(JSON.stringify(source))
}

const deep_copy = deep(["profile", {"list": ["Noodle", "Bross"]}])
console.log(deep_copy)

```

### 何时使用深层拷贝

当对象同时包含原始和非原始数据类型时，可以使用深度复制。它也可以在你需要更新嵌套对象或数组的任何时候使用。

## 分配

`Object.assign()`函数可用于将所有可枚举的自身属性从一个或多个源对象复制到一个目标对象。该函数将目标对象返回给`newObject`变量。

这里有一个用 JavaScript 中的`Object.assign()`函数复制的例子:

```
const target = {};
const source = { name: 'Pascal', age: 23 };

const newObject = Object.assign(target, source);

console.log(target); // {name: "Pascal", age: 23}

console.log(newObject); // {name: "Pascal", age: 23}

```

这里有一个在 TypeScript 中通过赋值进行复制的例子。这里，我们只取每个`source`对象，并将其属性复制到`target`，为了防止变异，我们通常将其作为`{}`传递。

```
const assign = (target: { [key: string]: any }, ...sources: object[]) => {
 sources.forEach((source) => {
   return Object.keys(source).forEach((key) => {
     target[key] = source[key as keyof Object]
   })
 })
 return target
}

let target: {} = {};
assign(target, ["profile", {"list": ["Noodle", "Bross"]}])
console.log(target)

```

这是另一个在 TypeScript 中通过赋值进行复制的例子。这个例子是一个安全的版本，在这个版本中，我们没有改变`target`对象，而是创建了一个全新的对象，稍后我们将其赋给一个变量。这意味着我们根本不需要传递`target`参数。不幸的是，这个版本不能使用关键字`this`，因为`this`不能被重新分配。

```
const assign = (...sources: object[]) => {
 return sources.reduce((result, current) => {
   return {
     ...result,
     ...current,
   }
 }, {})
}

const target = assign({ name: 'Pascal', age: 23 })
console.log(target)

```

### 何时使用赋值

`Object.assign()`功能可用于复制未修改的对象，并为现有对象分配一些新属性。在上面的示例代码中，我们创建了一个名为`target`的空对象`{}`，并从`source`对象中分配属性。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 合并

merge 方法类似于 assign 方法，但是它不是改变目标中的属性，而是将它们连接在一起。如果一个值是一个数组或一个对象，这个函数以递归的方式合并属性。JavaScript 中有两种合并对象的方法:使用 spread 操作符或`Object.assign()`方法。

### 传播算子

扩展操作符`...`在 ES6 中实现，可用于将两个或多个对象合并成一个新对象，该对象将具有合并对象的属性。如果两个对象具有相同的属性名，后一个对象属性将覆盖前一个对象属性。

下面是一个在 JavaScript 中与 spread 操作符合并的例子:

```
let employee = {
    position: 'Frontend Developer',
    country: 'USA'
};

let location = {
    city: 'Asaba',
    country: 'Nigeria'
};

let profile = {
    ...employee,
    ...location
};

console.log(profile);

// {
//    position: 'Frontend Developer',
//    city: 'Asaba',
//    country: 'Nigeria'
// }

```

现在，让我们看一个在 TypeScript 中合并的例子。

函数`mergeValues`接受两个参数:`target`和`source`。如果这两个值都是对象，我们调用并返回`mergeObjects`，将前面提到的`target`和`source`作为参数。类似地，当两个值都是数组时，我们调用并返回`mergeArrays`。如果`source`是`undefined`，我们只保留先前的值，这意味着我们返回`target`参数。如果以上都不适用，我们只返回`source`参数。

```
function mergeValues(target: any, source: any) {
 if (typeof target === 'object' && typeof source === 'object') {
   return mergeObjects(target, source)
 }
 if (Array.isArray(target) && Array.isArray(source)) {
   return mergeArrays(target, source)
 }
 if (source === undefined) {
   return target
 }
 return source
}

```

`mergeArrays`和`mergeObjects`的工作方式相同:我们获取`source`属性，并将它们设置在`target`中的同一个键下。

```
function mergeObjects(target: { [key: string]: any }, source:{ [key: string]: any }) {
 Object.keys(source).forEach((key) => {
   const sourceValue = source[key]
   const targetValue = target[key]
   target[key] = mergeValues(targetValue, sourceValue)
 })

 return target
}
function mergeArrays(target: any[], source: any[]) {
 source.forEach((value, index) => {
   target[index] = mergeValues(target[index], value)
 })

 return target
}

```

现在剩下要做的就是创建一个 TypeScript `merge`函数:

```
const merge = (target: object, ...sources: object[]) => {
 sources.forEach((source) => {
   return mergeValues(target, source)
 })
 return target
}

console.log(merge({position: "Technical Writer"}, { name: 'Pascal', age: 23 }))

```

### `Object.assign()`方法

`Object.assign()`方法可用于合并两个对象，并将结果复制到一个新的目标。就像 spread 运算符一样，如果源对象具有相同的属性名，后一个对象将替换前一个对象。

这里有一个例子:

```
let employee = {
    firstName: 'Pascal',
    lastName: 'Akunne',
    age: 23
};

let job = {
    position: 'Frontend Developer',
    country: 'Nigeria'
};

let profile = Object.assign(employee, job);
console.log(profile);
// {
//    firstName: 'Pascal',
//    lastName: 'Akunne',
//    age: 23,
//    position: 'Frontend Developer',
//    country: 'Nigeria'
// }

```

现在，让我们看另一个在 Typescript 中合并的例子。使用这种方法，我们希望首先获得`source`对象的所有属性，即使它们嵌套了三个对象，并将一个`path`保存到属性中。这将允许我们在`target`对象内部的正确路径上设置值。

一个`path`是一个字符串数组，看起来像这样:`[‘firstObject’,‘secondObject’, ‘propertyName’]`。

这是一个工作原理的例子:

```
const source = {
 firstObject: {
   secondObject: {
     property: 5,
   },
 },
}
console.log(getValue(source))
// [[[{ value: 5, path: ['firstObject', 'secondObject', 'property']}]]]

```

我们调用`getValue`函数来获取包含路径和值的对象数组。如果参数`value`是`null`或者不是类似于对象的，我们不能更深入，所以我们返回一个包含参数`value`及其路径的对象。

否则，如果参数是类对象的而不是`null`，我们可以确定它要么是数组，要么是对象。如果是数组，我们叫`getArrayValues`。如果它是一个物体，我们称之为`getObjectValues`。

```
function getValue(value: any, path: (number | string)[] = []) {
 if (value === null || typeof value !== 'object') {
   return {
     value,
     path: [...path],
   }
 }
 if (Array.isArray(value)) {
   return getArrayValues(value, path)
 }
 return getObjectValues(value, path)
}

```

`getArrayValues`和`getObjectValues`都迭代调用各自的`getValue`属性，当前的`index` / `key`现在附加到`path`。

```
function getArrayValues(collection: any[], path: (number | string)[] = []): any {
 return collection.map((value, index) => {
   return getValue(value, [...path, index])
 })
}
function getObjectValues(source: { [key: string]: any }, path: (number | string)[] = []): any {
 return Object.keys(source).map((key) => {
   const value = source[key]

   return getValue(value, [...path, key])
 })
}

```

在获得整个`source`对象的路径和值之后，我们可以看到它们被深深地嵌套了。尽管如此，我们还是希望将它们都放在一个数组中。这意味着我们需要`flatten`这个数组。

展平数组归结为迭代每一项以检查它是否是一个数组。如果是，我们`flatten`它，然后`concat`这个值到结果数组。

```
function flatten(collection: any[]) {
 return collection.reduce((result, current) => {
   let value = current

   if (Array.isArray(current)) {
     value = flatten(current)
   }
   return result.concat(value)
 }, [])
}

```

既然我们已经介绍了如何获取`path`，让我们考虑如何在`target`对象中设置所有这些属性。

让我们来讨论一下`setAtPath`函数，我们将使用它来设置它们各自路径上的值。我们希望访问路径的最后一个属性来设置值。为此，我们需要检查路径的项目、属性名称，并每次获取属性值。
我们从目标对象开始`reduce`函数，然后目标对象作为`result`参数可用。

每次我们返回`result[key]`下的值，它就成为下一次迭代中的`result`参数。这样，当我们到达路径的最后一项时，`result`参数就是我们设置值的对象或数组。

在我们的例子中，每次迭代的`result`参数是:`target`->-`firstObject`->-`secondObject`。

我们必须记住,`target`可能是一个空对象，而源可以有很多层。这意味着在设置一个值之前，我们可能必须自己重新创建一个对象或数组的结构。

```
function setAtPath(target: object, path: (string | number)[], value: any): any {
 return path.reduce((result:{ [key: string]: any }, key, index) => {
   if (index === path.length - 1) {
     result[key] = value
     return target
   }
   if (!result[key]) {
     const nextKey = path[index + 1]
     result[key] = typeof nextKey === 'number' ? [] : {}
   }
   return result[key]
 }, target)
}

```

我们在`path`的最后一项设置值，并返回我们开始的对象。

```
if (index === path.length - 1) {
  result[key] = value
return target
}

```

如果在`firstObject`中没有`secondObject`，我们将得到`undefined`，然后如果我们试图设置`undefined[‘property’]`将会出错。为了防止这种情况，我们首先检查`result[key]`是否存在。如果它不存在，我们需要创建一个对象或数组。如果下一项的类型是一个`'number'`(实际上是一个索引)，那么我们需要创建一个数组。如果是字符串，我们就创建一个对象。

```
if (!result[key]) {
  const nextKey = path[index + 1]
  result[key] = typeof nextKey === 'number' ? [] : {}
}

```

现在，剩下要做的就是创建将一切联系在一起的`merge`函数。

```
const result = function merge(target: object, ...sources: object[]) {
  return flatten(
    sources.map((source) => {
      return getValue(source)
    }),
  ).reduce((result: object, path: [], value: any ) => {
    if (value === undefined) {
      return result
    }
    return setAtPath(result, path, value)
  }, target)
}

console.log(result(source))

```

### 何时使用合并

合并对象不是 JavaScript 中的典型做法，但是这种方法使我们能够组合对象属性，甚至是嵌套非常深的对象。

## 结构化克隆

结构化克隆是一种在 JavaScript 中复制对象的新技术。它是一种全局方法，使用结构化克隆算法创建指定项目的深层副本。它不是克隆对象，而是将对象从其原始源转移到新的源，在新的源中无法再从原始源中访问这些对象。

这种技术可以与[可转移对象](https://developer.mozilla.org/en-US/docs/Glossary/Transferable_objects)一起使用，可转移对象是一种拥有资源的对象。这些对象只能使用原始参数的传递值进行传递。作为转移的结果，原始对象将变得不可用。

在下面的例子中，代码将从传入的值中传输`Pascal`，而不是`Akunne`:

```
const transferred = structuredClone(
   { profile: { name: { first: Pascal, last: Akunne } } },
   { transfer: [Pascal] });

```

### 何时使用结构化克隆

当您需要在保存数据之前异步验证缓冲区中的数据时，结构化克隆非常有用。为了避免在保存数据之前修改缓冲区，可以克隆缓冲区并验证数据。如果您正在传输数据，这种技术也很有用。使用结构化克隆，任何修改原始缓冲区的尝试都将失败，从而防止其被意外误用。

## 结论

在本文中，我们讨论了在 JavaScript 和 TypeScript 中复制对象的五种有用的技术。当处理一个只有原始数据类型(字符串或数字)属性的对象时，我们使用浅层复制。深层复制确保没有对源对象或其任何属性的引用。分配是复制一个对象或者只是给一个现有对象分配一些新属性的好方法。合并允许我们合并对象的属性，即使对象嵌套很深。最后，结构化克隆允许我们异步验证和`transfer`对象数据，然后使原始对象不可用。

对象是我们在 JavaScript 中组织和传输数据的基本方法。它们通过对象类型(`result: object`)在 TypeScript 中表示。无论您选择用 JavaScript 还是 TypeScript 复制对象，希望本指南为您提供了多种用例的选择。如果你熟悉 JavaScript 中复制对象的其他技术，请在评论部分分享。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.