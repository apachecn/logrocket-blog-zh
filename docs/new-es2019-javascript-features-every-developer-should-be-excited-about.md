# 每个开发人员都应该为 ES2019 新的和潜在的 JavaScript 特性感到兴奋

> 原文：<https://blog.logrocket.com/new-es2019-javascript-features-every-developer-should-be-excited-about/>

自早期以来，JavaScript 已经走过了漫长的道路，增加了许多新的内容和特性，旨在使这种语言更加用户友好，更加简洁。下面是一些我觉得很有趣的 JavaScript 新增内容。

其中一些功能已经在 Node、Chrome、Firefox 和 Safari 中可用，而其他功能仍处于提案阶段。

## 可选链接

使用`?.`操作符完成可选链接。它主要确保问号之前的值既不是未定义的也不是空的。这在评估深度嵌套对象的属性时非常有用。

在评估属性之前，需要确保`?.`操作符存在。

考虑下面的例子:

```
const users = [
  {
   name: "Olagunju Gbolahan",
   occupation: "Software Developer",
   sayName(){
    console.log(`my name is ${this.name}`);
   },
   address: { office: "New York" }
  },
  { name: "Olawaseyi Moses" },
  { name: "Tunde Ednut" }
];
```

让我们考虑用户数组中的第二个用户:

```
const secondUser = users[1];
```

我们可能想要得到这个用户的办公室地址。在可选的链接操作符出现之前，我们必须通过一个相对低效的过程来获取这些信息:

```
const theAddress = secondUser.address && secondUser.address.office;
console.log(theAddress); // undefined
```

如果我们有一个深度嵌套的对象，我们必须在每一层使用`&&`操作符来检查它的值是否存在。

但是对于可选的链接，我们只需执行以下操作:

```
const theAddress = secondUser?.address?.office;
console.log(theAddress); // undefined
```

我们还可以对对象方法使用可选的链接，以在执行之前确认它们的存在:

```
const firstUser = users[0];
console.log(firstUser.sayName?.()); // my name is Olagunju Gbolahan
```

如果对象上不存在具有给定名称的方法，它将简单地返回`undefined`。

```
console.log(firstUser.sayOccupation?.()); // undefined
```

因为可选的链接操作符还没有被添加到 JavaScript 规范中，所以它仍然处于[提议阶段](https://github.com/tc39/proposal-optional-chaining)。

你现在可以用[babel-plugin-proposal-optional-chaining](https://babeljs.io/docs/en/babel-plugin-proposal-optional-chaining)插件使用它。

## 可选 catch 绑定

当我们预先知道我们的错误是什么，并且我们不想要多余的未使用的变量时，这个特性就派上了用场。

考虑传统的尝试和捕捉块:

```
try {
  const parsedJsonData = JSON.parse(obj);
} catch (error) {
  //the variable error has to be declared whether used or unused
  console.log(obj);
}
```

但是通过添加可选的 catch 绑定，我们不必提供未使用的变量——特别是当我们的 try 块有默认值时。

```
function getName () {
  let name = "Gbolahan Olagunju";
  try {
    name = obj.details.name
  } catch {}
  console.log(name);
}
```

## 管道运营商

这是 Javascript 的新增内容之一，目前处于第一阶段。

它本质上有助于使对同一个参数的几个函数调用可读。

它通过将表达式的值作为参数传递给函数来实现这一点。考虑在没有管道操作符`|>`的情况下调用以下函数。

```
const capitalize = (input) =>  input[0].toUpperCase() + input.substring(1);
const removeSpaces = (input) => input.trim();
const repeat = (input) => `${input}, ${input}`;
```

```
const withoutpipe = repeat(capitalize(removeSpaces('    i am gbols    ')));
console.log(withoutpipe); // I am gbols, I am gbols
```

但是使用管道操作符，可读性可以大大提高:

```
const withpipe = '    i am gbols    '
                |> removeSpaces
                |> capitalize
                |> repeat;
console.log(withpipe); // // I am gbols, I am gbols
```

## String.trimStart 和 String.trimEnd

这正式命名为 trimRight 和 trimLeft，但在 ES2019 中，这些名称被更改为别名 trimStart 和 trimEnd，以使用户更直观。

考虑下面的例子:

```
let message = "     Welcome to LogRocket      ";
message.trimStart(); // "Welcome to LogRocket      "
message.trimEnd(); // "Welcome to LogRocket";
```

## 对象. fromEntries

在讨论 Object.fromEntries 之前，先讨论 Object.entries 是很重要的。

ES2017 规范中添加了 Object.entries 方法，以提供一种将对象转换为其等效数组的方法，从而授予其访问所有数组方法进行处理的权限。

考虑以下对象:

```
const devs = {
  gbols: 5,
  andrew: 3,
  kelani: 10,
  dafe: 8,
};
const arrOfDevs = Object.entries(devs);
console.log(arrOfDevs);
//[
//  ["gbols", 5]
//  ["andrew", 3]
//  ["kelani", 10]
//  ["dafe", 8]
//]
```

现在，我们可以在阵列上使用`filter`方法来获取具有 5 年以上经验的开发人员:

```
const expDevs = arrOfDevs.filter(([name, yrsOfExp]) => yrsOfExp > 5);
console.log(expDevs);
//[
//  ["kelani", 10]
//  ["dafe", 8]
//]
```

于是问题出现了:没有简单的方法将结果转换回对象。通常，我们会编写自己的代码将它转换回对象:

```
const expDevsObj = {};
for (let [name, yrsOfExp] of expDevs) {
expDevsObj[name] = yrsOfExp;
}
console.log(expDevsObj);
//{
 //dafe: 8
 //kelani: 10
//}
```

但是随着 Object.fromEntries 的引入，我们可以通过一次滑动来实现这一点:

```
console.log(Object.fromEntries(expDevs));
//{
 //dafe: 8
 //kelani: 10
//}
```

## 平的

通常，由于 API 调用，我们需要处理深度嵌套的数组。在这种情况下，展平数组就显得尤为重要。

考虑下面的例子:

```
const developers = [
  {
    name: 'Gbolahan Olagunju',
    yrsOfExp: 6,
    stacks: ['Javascript', 'NodeJs', ['ReactJs', ['ExpressJs', 'PostgresSql']]]
  },
  {
    name: 'Daniel Show',
    yrsOfExp: 2,
    stacks: ['Ruby', 'Jest', ['Rails', ['JQuery', 'MySql']]]
  },
  {
    name: 'Edafe Emunotor',
    yrsOfExp: 9,
    stacks: ['PHP', 'Lumen', ['Angular', 'NgRx']]
  }
];
```

```
const allStacks = developers.map(({stacks}) => stacks);
console.log(allStacks);
// [
// ['Javascript', 'NodeJs', ['ReactJs', ['ExpressJs', 'PostgresSql']]]
// ['Ruby', 'Jest', ['Rails', ['JQuery', 'MySql']]]
// ['PHP', 'Lumen', ['Angular', 'NgRx']]
// ]
```

`allstacks`变量包含深度嵌套的数组。要展平这个数组，我们可以使用 Array.prototype.flat。

方法如下:

```
const flatSingle = allStacks.flat();
console.log(flatSingle);
//[
// "JavaScript",
//  "NodeJs",
// ['ReactJs', ['ExpressJs', 'PostgresSql']]]
// "Ruby",
// "Jest",
// ['Rails', ['JQuery', 'MySql']]]
// "PHP",
// "Lumen"
// ["Angular", "NgRx"]
//]
```

从上面我们可以推断出，数组已经扁平化了一级，这是 array.prototype.flat 的默认参数。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们可以向 flat 方法传递一个参数，以确定我们想要展平的程度。

defaults 参数的值为 1。为了完全展平数组，我们可以传递一个无穷大的参数。`Infinity`参数完全展平数组，而不管数组的深度。

方法如下:

```
const completelyFlat = allStacks.flat(Infinity);
console.log(completelyFlat);
//[
// "JavaScript",
// "NodeJs",
// "ReactJs",
// "ExpressJs",
// "PostgresSql",
// "Ruby",
// "Jest",
// "Rails",
// "JQuery",
// "MySql",
// "PHP",
// "Lumen",
// "Angular",
// "NgRx"
//]
```

## 平面地图

FlatMap 是调用深度为 1 的 Map 方法和 flat 方法的组合。它通常非常有用，因为它以非常高效的方式做同样的事情。

下面是一个同时使用 map 和 flatMap 的简单示例:

```
let arr = ['my name is Gbols', ' ', 'and i am great developer']; 
console.log(arr.map(word => word.split(' ')));
//[
// ["my", "name", "is", "Gbols"],
// ["", ""],
// ["and", "i", "am", "great", "developer"]
//]
```

```
console.log(arr.flatMap(word => word.split(' ')));
//[ "my"
//  "name"
//  "is"
//  "Gbols"
//   ""
//   ""
//   "and"
//   "i"
//   "am"
//   "great"
//   "developer"
//]
```

## 实现新的 JS 特性？了解 JavaScript 错误如何影响用户。

追踪生产 JavaScript 异常或错误的原因是耗时且令人沮丧的。如果您对监控 JavaScript 错误感兴趣，并想看看它们是如何影响用户的，[试试 LogRocket](https://logrocket.com/signup/) 。[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://logrocket.com/signup/)[https://logrocket.com/signup/](https://logrocket.com/signup/)

LogRocket 就像是网络应用的 DVR，记录下你网站上发生的每一件事。LogRocket 使您能够聚合和报告错误，以查看它们发生的频率以及它们影响了多少用户群。您可以轻松地重放发生错误的特定用户会话，以查看导致错误的用户操作。

LogRocket 让你的应用程序记录带有标题+正文的请求/响应，以及关于用户的上下文信息，以全面了解问题。它还记录页面上的 HTML 和 CSS，甚至可以重建最复杂的单页面应用程序的像素级完美视频。

增强您的 JavaScript 错误监控能力–––[开始免费监控](https://logrocket.com/signup/)。

## 结论

在本文中，我们列举了 JavaScript 新增功能的诸多好处。这些增加通过减少冗长和增加可读性来增强开发人员的体验。

下面，看看我们没有提到的几个新特性:
[JSON . stringify](https://github.com/tc39/proposal-well-formed-stringify)
[排序稳定性](https://tc39.es/ecma262/#sec-array.prototype.sort)