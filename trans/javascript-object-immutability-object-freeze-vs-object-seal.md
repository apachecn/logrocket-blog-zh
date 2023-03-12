# JavaScript 对象不变性:object . freeze vs . object . seal-log rocket 博客

> 原文：<https://blog.logrocket.com/javascript-object-immutability-object-freeze-vs-object-seal/>

在 JavaScript 中处理值和对象时，有时可能需要限制对它们的操作，以防止更改应用程序范围的配置对象、状态对象或全局常量。

有权访问这些数据的函数可能会在不应该修改它们的时候直接修改它们(这也可能源于开发人员的无意错误)。此外，使用相同代码库(或使用您的代码)的其他开发人员可能会意外地做出这样的更改。

令人欣慰的是，JavaScript 提供了一些结构来处理这种情况。

在本教程中，我们将讨论不变性的概念以及 JavaScript 中的`freeze()`和`seal()`对象方法。我们将使用示例代码来看看它们是如何工作的，并讨论可能的性能限制。现在，让我们开始吧！

## 理解 JavaScript 中的不变性

简而言之，使一个对象成为不可变的意味着对它的进一步修改将不再适用。实际上，它的状态变成了只读。在某种程度上，这就是`const`关键字所要达到的目的:

```
const jarOfWine = "full";

// throws error "Uncaught TypeError: Assignment to constant variable."
jarOfWine = "empty";

```

但是当然，由于`const`声明的工作方式，我们不能将`const`用于对象和数组等实体——它只是创建一个对值的引用。为了解释这一点，让我们回顾一下 JavaScript 数据类型。

### 原语与对象

第一组数据类型是只包含一项的值。这些包括不可变的原语，如字符串或数字:

```
let nextGame = "Word Duel";

// change to "Word Dual"? Doesn't stick.
nextGame[7] = “a”;

nextGame; // still "Word Duel"

// Of course, if we'd declared nextGame with `const`, then we couldn't reassign it.
nextGame = "Word Dual";

nextGame; // now "Word Dual"

```

当我们复制这些基本类型时，我们在复制值:

```
const jarOfWine = "full";

const emptyJar = jarOfWine; // both jars are now 'full'

```

两个变量`jarOfWine`和`emptyJar`现在都包含两个独立的字符串，您可以独立地更改其中的任何一个。但是，对象的行为不同。

当您声明一个对象时，如下面的代码所示，`user`变量不包含对象本身，而是包含对它的引用:

```
const user = {
  name: "Jane",
  surname: "Traveller",
  stayDuration: "3 weeks",
  roomAssigned: 1022,
}

```

这就像写下藏着你那堆金子的洞穴的地址。地址不是山洞。因此，当我们试图使用与复制字符串相同的赋值方法复制一个对象时，我们最终只复制了引用或地址，而没有两个独立的对象:

```
const guest = user;

```

修改`user`也会改变`guest`:

```
guest.name = "John";

// now both user and guest look like this:
{
  name: "John",
  surname: "Traveller",
  stayDuration: "3 weeks",
  roomAssigned: 1022,
}

```

您通常可以用`Object.is()`方法或严格的等式操作符来测试这一点:

```
Object.is(user, guest) // returns true

user === guest // returns true

```

这是一个与关键字`const`类似的玩法。它创建了一个对值的引用，这意味着尽管绑定不能改变(也就是说，不能重新分配变量)，但引用的值可以改变。

这发生在我们之前成功修改了`name`属性的时候，尽管`guest`是用`const` :
<声明的

```
guest.name = "John";

```

换句话说，`const`给我们的是赋值不变性，而不是值不变性。

### 限制对对象属性和整个对象的更改

因为 JavaScript 中的对象是通过引用复制的，所以总是存在复制的引用改变原始对象的风险。根据您的使用情况，这种行为可能并不理想。在这种情况下，本质上“锁定”对象可能是有意义的。

(理想情况下，您应该制作对象的副本并修改它们，而不是修改原始对象。虽然大多数复制或克隆机制都是浅层的，但是如果你正在处理深度嵌套的对象，那么你会想要[深度克隆](https://blog.logrocket.com/methods-for-deep-cloning-objects-in-javascript/)。)

JavaScript 提供了三种方法来对对象执行不同级别的访问限制。这些包括`Object.freeze()`、`Object.seal()`和`Object.preventExtensions()`。虽然我们会涉及到后者，但我们将主要关注前两者。

#### `writable`和`configurable`属性标志

然而，在我们继续之前，让我们先了解一下限制访问属性的机制背后的一些基本概念。具体来说，我们对[属性标志](https://javascript.info/property-descriptors)感兴趣，比如`writable`和`configurable`。

使用`Object.getOwnPropertyDescriptor`或`Object.getOwnPropertyDescriptors`方法时，通常可以检查这些标志的值:

```
const hunanProvince = {
  typeOfWine: "Emperor's Smile",
};

Object.getOwnPropertyDescriptors(hunanProvince);

// returns
{
  typeOfWine: {
    value: "Emperor's Smile",
    writable: true,
    enumerable: true,
    configurable: true
  },
}

```

虽然在处理 JavaScript 对象时，我们通常更关心属性的实际值，但是除了保存属性值的`value`属性之外，属性还有其他属性。

这些属性包括已经提到的`value`、`writable`和`configurable`属性，以及`enumerable`，如上所述。

`writable`和`configurable`旗帜对我们来说是最重要的。当属性的`writable`设置为`true`时，其值可以改变。否则，它是只读的。

然后是`configurable`，当在一个属性上设置为`true`时，允许您更改上述标志或删除一个属性。

如果`configurable`被改为设置为`false`，除了一个例外，所有的东西都变成只读的:如果`writable`被设置为`true`，其中`configurable`是`false`，属性的值仍然可以改变:

```
Object.defineProperty(hunanProvince, "capital", {
  value: "Caiyi Town",
  writable: true,
});

hunanProvince.capital = "Possibly Gusu";

Object.getOwnPropertyDescriptors(hunanProvince);
// now returns
{
  typeOfWine: {
    value: "Emperor's Smile",
    writable: true,
    enumerable: true,
    configurable: true
  },
  capital: {
    value: "Possibly Gusu",
    writable: true,
    enumerable :false,
    configurable: false
  },
}

```

注意，`enumerable`和`configurable`都是`capital`属性的`false`，因为它是用`[Object.defineProperty()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)`创建的。如前所述，以这种方式创建的属性将所有标志设置为`false`。然而`writable`是`true`，因为我们显式地设置了它。

我们也被允许将`writable`从`true`改为`false`，但仅此而已。你不能把它从`false`改成`true`。事实上，一旦属性的`configurable`和`writable`都被设置为`false`，就不允许对其进行进一步的更改:

```
Object.defineProperty(hunanProvince, "capital", {
  writable: false,
  // everything else also `false`
});

// no effect
hunanProvince.capital = "Caiyi Town";

```

虽然这些标志是在属性级别使用的，但是像`Object.freeze()`和`Object.seal()`这样的方法是在对象级别工作的。现在让我们继续。

本文假设您对为什么不变性的概念是有用的有一个大致的了解。

然而，如果你想更深入地挖掘并阅读一些支持和反对它的论点，这里有一个非常方便的讨论这个主题的 [StackOverflow 线程](https://stackoverflow.com/questions/34385243/why-is-immutability-so-important-or-needed-in-javascript/43318963#43318963)(带有附加资源的链接)。[的 Immutable.js 文档也证明了不变性](https://immutable-js.com/#the-case-for-immutability)。

## 使用`Object.freeze`与`Object.seal`实现对象不变性

现在，让我们来看看`freeze`和`seal`方法。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 使用`Object.freeze`

当我们使用`Object.freeze`冻结一个对象时，它就不能再被修改了。本质上，不能再向其中添加新属性，也不能删除现有属性。正如您所猜测的，这是通过将所有属性的所有标志设置为`false`来实现的。

让我们看一个例子。以下是我们将使用的两个对象:

```
let obj1 = {
  "one": 1,
  "two": 2,
};

let obj2 = {
  "three": 3,
  "four": 4,
};

```

现在，让我们改变第一个对象的属性，`obj1`:

```
obj1.one = "one"; // returns "one"

```

原来的物体现在看起来像这样:

```
obj1;

{
  one: "one",
  two: 2,
};

```

当然，这是意料之中的行为。默认情况下，对象是可变的。现在，让我们试着冷冻一个物体。我们将使用`obj2`,因为它尚未被篡改:

```
// freeze() returns the same object passed to it
Object.freeze(obj2); // returns {three: 3, four: 2}

// test
obj2 === Object.freeze(obj2); // returns true

```

为了测试对象是否被冻结，JavaScript 提供了`Object.isFrozen()`方法:

```
Object.isFrozen(obj2); // returns true

```

现在，即使我们试图像下面这样修改它，也没有效果。

```
obj2.three = "three"; // no effect

```

然而，我们很快就会看到，当我们开始使用嵌套对象时，我们会遇到麻烦。像对象克隆一样，冻结也可以是浅的或深的。

让我们从`obj1`和`obj2`创建一个新对象，并在其中嵌套一个数组:

```
// nesting
let obj3 = Object.assign({}, obj1, obj2, {"otherNumbers": {
  "even": [6, 8, 10],
  "odd": [5, 7, 9],
}});

obj3;
// {
//    one: "one",
//    two: 2,
//    three: 3,
//    four: 4,
//    "otherNumbers": {
//      "even": [6, 8, 10],
//      "odd": [5, 7, 9],
//    }
//  }

```

您会注意到，即使我们冻结了它，我们仍然可以对嵌套对象中的数组进行更改:

```
Object.freeze(obj3);

obj3.otherNumbers.even[0] = 12;

obj3;
// {
//    one: "one",
//    two: 2,
//    three: 3,
//    four: 4,
//    "otherNumbers": {
//      "even": [12, 8, 10],
//      "odd": [5, 7, 9],
//    }
//  }

```

偶数数组的第一个元素从`6`修改为`12`。因为数组也是对象，所以这种行为也出现在这里:

```
let testArr = [0, 1, 2, 3, [4, 5, [6, 7]]];

Object.freeze(testArr);

testArr[0] = "zero"; // unable to modify top-level elements...

// ...however, nested elements can be changed

testArr[4][0] = "four"; // now looks like this: [0, 1, 2, 3, ["four", 5, [6, 7]]]

```

如果您一直在浏览器控制台中测试您的代码，它可能会无声无息地失败，并且不会抛出任何错误。如果你想让错误更明显，试着把你的代码包装在一个[立即调用的函数表达式(生命)](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)中，并打开`strict`模式:

```
(function() {
  "use strict";

  let obj = {"one": 1, "two": 2};

  Object.freeze(obj);

  obj.one = "one";
})();

```

上面的代码现在应该在控制台中抛出一个`TypeError`:

```
Uncaught TypeError: Cannot assign to read only property 'one' of object '#<Object>'

```

现在，我们如何冻结整个对象，包括顶级(直接属性引用)和嵌套属性？

正如我们注意到的，冻结只适用于对象中的顶级属性，所以我们需要一个递归冻结每个属性的`[deepFreeze()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze#what_is_shallow_freeze)` [函数:](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze#what_is_shallow_freeze)

```
const deepFreeze = (obj) => {
  // fetch property keys
  const propKeys = Object.getOwnPropertyNames(obj);

  // recursively freeze all properties
  propKeys.forEach((key) => {
    const propValue = obj[key];

    if (propValue && typeof(propValue) === "object") deepFreeze(propValue);
  });

  return Object.freeze(obj);
}

```

现在，改变嵌套属性的尝试失败了。

请注意，虽然冻结本质上是防止对对象的更改，但它允许变量重新分配。

### 使用`Object.seal()`

使用`Object.freeze()`，新的更改对冻结的对象没有影响。然而，`seal()`方法允许修改现有的属性。这意味着虽然您不能添加新属性或删除现有属性，但您可以进行更改。

`seal()`方法基本上将我们之前讨论的`configurable`标志设置为`false`，将`writable`设置为每个属性的`true`:

```
const students = {
  "001" : "Kylie Yaeger",
  "002": "Ifeoma Kurosaki"
};

// seal object
Object.seal(students);

// test
Object.isSealed(students); // returns true

// cannot add or delete properties
students["003"] = "Amara King"; // fails
delete students["001"]; // fails

```

这是另一个数组的例子:

```
const students = ["Kylie Yaeger", "Ifeoma Kurosaki"];

// seal
Object.seal(students);

// test
Object.isSealed(students); // returns true

// throws a TypeError saying object is not extensible
students.push("Amara King");

```

密封还可以防止使用`Object.defineProperty()`或`Object.defineProperties()`重新定义属性，无论是添加新属性还是修改现有属性。

但是，请记住，如果`writable`是`true`，您仍然可以将其更改为`false`，但这是不可撤消的。

```
// fails
Object.defineProperty(hunanProvince, "capital", {
  value: "Unknown",
  writable: true,
});

```

密封无法实现的另一个变化是将普通数据属性更改为访问器(即 getters 和 setters):

```
// fails
Object.defineProperty(hunanProvince, "capital", {
  get: () => "Caiyi Town",
  set: (val) => hunanProvince["capital"] = val;
});

```

反之亦然:您不能将访问器更改为数据属性。就像冷冻一样，密封一个对象会阻止其原型发生变化:

```
const languageSymbols = {
  English: "ENG",
  Japanese: "JP",
  French: "FR",
};

const trollLanguageSymbols = {
  trollEnglish: "T-ENG",
  trollJapanese: "T-JP",
  trollFrench: "T-FR",
};

Object.seal(trollLanguageSymbols);

// fails
Object.setPrototypeOf(trollLanguageSymbols, languageSymbols);

```

同样，就像冻结一样，这里的默认行为是浅密封。因此，您可以选择深度密封一个对象，就像深度冷冻一个对象一样:

```
const deepSeal = (obj) => {
  // fetch property keys
  const propKeys = Object.getOwnPropertyNames(obj);

  // recursively seal all properties
  propKeys.forEach((key) => {
    const propValue = obj[key];

    if (propValue && typeof(propValue) === "object") deepSeal(propValue);
  });

  return Object.seal(obj);
}

```

我们在这里修改了 MDN 的`deepFreeze()`函数来执行密封:

```
const students = {
  "001" : "Kylie Yaeger",
  "002": "Ifeoma Kurosaki",
  "003": {
    "004": "Yumi Ren",
    "005": "Plisetsky Ran",
  },
};

deepSeal(students);

// fails
delete students["003"]["004"];

```

现在，我们的嵌套对象也被密封了。

### 使用`Object.preventExtensions()`

另一个专门防止添加新属性的 JavaScript 方法是`preventExtensions()`方法:

```
(() => {
  "use strict";

  const trollToken = {
    name: "Troll",
    symbol: "TRL",
    decimal: 6,
    totalSupply: 100_000_000,
  };

  Object.preventExtensions(trollToken);

  // fails
  trollToken.transfer = (_to, amount) => {}
})();

```

由于我们所做的一切都是为了防止添加新的属性，现有的属性显然可以被修改甚至删除:

```
delete trollToken.decimal;

trollToken;

// {
//    name: "Troll",
//    symbol: "TRL",
//    totalSupply: 100_000_000,
//  }

```

需要注意的是,`[[prototype]]`属性变成了不可变的:

```
const token = {
  transfer: () => {},
  transferFrom: () => {},
  approve: () => {},
};

// fails with a TypeError
Object.setPrototypeOf(trollToken, token);

```

要测试一个对象是否可扩展，只需使用`isExtensible()`方法:

```
// I've omitted `console.log` here since I'm assuming you're typing in the browser console directly
(`Is trollToken extensible? Ans: ${Object.isExtensible(trollToken)}`);

```

就像我们手动将属性的`configurable`和`writable`标志设置为`false`一样，让一个对象不可扩展是一条单行道。

### `Object.freeze`和`Object.seal`用例及性能问题

总而言之，`Object.freeze()`和`Object.seal()`是 JavaScript 语言提供的构造，用于帮助维护对象的不同级别的完整性。然而，理解什么时候需要使用这些方法是非常令人困惑的。

前面提到的一个例子是使用全局对象进行应用程序状态管理。您可能希望保持原始对象不变，并对副本进行更改，特别是如果您希望跟踪状态更改并恢复它们。

冻结可以防止代码试图改变不应该直接修改的对象。

冻结或密封的对象还可以防止添加由于输入错误而引入的新属性，例如错误输入的属性名。

这些方法在调试时也很有帮助，因为对对象的限制有助于缩小错误的可能来源。

也就是说，对于任何使用您的代码的人来说，这可能是一个令人头痛的问题，因为冻结的对象和未冻结的对象之间本质上没有物理差异。

确定一个对象被冻结或密封的唯一方法是使用`isFrozen()`或`isSealed()`方法。这使得推断预期的对象行为变得有些困难，因为[为什么设置这样的限制可能并不完全明显](https://es.discourse.group/t/immutable-objects/658/8)。

[标记模板](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#tagged_templates)是[隐式使用](https://docs.google.com/document/d/1X6zO5F_Zojizn2dmo_ftaOWsY8NltPHUhudBbUzMxnc/preview#) `[Object.freeze()](https://docs.google.com/document/d/1X6zO5F_Zojizn2dmo_ftaOWsY8NltPHUhudBbUzMxnc/preview#)` [的一个特征；样式组件库](https://docs.google.com/document/d/1X6zO5F_Zojizn2dmo_ftaOWsY8NltPHUhudBbUzMxnc/preview#)[和其他一些依赖于它。前者使用带标签的模板文字创建其](https://styled-components.com/)[样式的组件](https://mxstbr.blog/2016/11/styled-components-magic-explained/)。

如果您想知道在使用上述任何方法时存在什么性能成本(如果有的话)，那么在 V8 发动机中有[一些历史性能问题。然而，这与其说是一个错误，不如说是一个 bug，自从](https://bugs.chromium.org/p/v8/issues/detail?id=6831)被修复后，这就是[。](https://v8.dev/blog/v8-release-76#frozen%2Fsealed-array-improvements)

【2013 年至 2014 年，`Object.freeze()`和`Object.seal()`也都在 V8 上进行了一些性能改进。

这里有一个 [StackOverflow 线程，跟踪了 2015 年至 2019 年间冻结对象与非冻结对象](https://stackoverflow.com/questions/8435080/any-performance-benefit-to-locking-down-javascript-objects/23198265#23198265)的性能。它显示了这两种情况下 Chrome 的性能几乎相同。

尽管如此，在某些浏览器如 Safari 中，密封或冻结[可能会影响对象的枚举速度](https://stackoverflow.com/questions/21402108/difference-between-freeze-and-seal)。

## 用于处理不变性的第三方库

JavaScript 中有多种处理不变性的方法。虽然上面讨论的方法可以很方便地使用，但是对于任何实际的应用程序，您最有可能使用一个库。

例子有 [Immer 和 Immutable.js](https://blog.logrocket.com/immer-and-immutable-js-how-do-they-compare/) 。使用 Immer，您可以使用您已经知道的相同 JavaScript 数据类型。然而，尽管 Immutable.js 引入了新的数据结构，但它可能是更快的选择。

## 结论

JavaScript 提供了像`Object.freeze()`和`Object.seal()`这样的方法来改变对象的访问限制级别。

然而，就像克隆一样，因为对象是通过引用复制的，所以冻结通常是浅层的。因此，您可以实现自己的基本 deep freeze 或 deep seal 函数，或者根据您的用例，利用 Immer 或 Immutable.js 等库。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.