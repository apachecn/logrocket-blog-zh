# 对 ES2022 - LogRocket 博客有什么期待

> 原文：<https://blog.logrocket.com/es2022-what-to-expect/>

根据 [Stack Overflow 的 2020 年开发者调查](https://insights.stackoverflow.com/survey/2020#technology-programming-scripting-and-markup-languages-professional-developers)，JavaScript 是最常用的编程语言，并且它还在不断发展。事实上，自 2015 年以来，每年都有新的 ECMAScript 规范发布。在撰写本文的时候，几个新的特性提案已经通过了第四个提案阶段[，并有望被包含在 JavaScript 的第 13 个版本 ES2022 中。](https://github.com/tc39/proposals/blob/main/finished-proposals.md)

在本文中，我们将了解预计在 2022 年年中发布的八项功能提案:

## 用顶层`await`延迟模块的执行

ES2022 将使开发人员能够在异步(`async`)函数范围之外使用`await`，从而更易于在模块级别使用。

以下是在 ES2022 之前如何使用`await`的示例:

```
import users from './users'

const getUsers = async() => {
  let users = await users()
  return users
}

```

下面是同一个例子，展示了如何在 ES2022 下使用`await`:

```
let users = await import './users'

```

顶层`await`特性延迟当前模块和父模块的执行，直到导入的模块被加载。使用这个特性，模块可以使用运行时值来确定依赖关系。顶级的`await`也可以作为依赖关系的后备。

```
let users;
try {
  users = await import('https://example1.com/users');
} 
catch {
  users = await import('https://example2.com/users');
}

```

## `RegExp`匹配带有`d`标志的索引

正则表达式匹配是用于在字符串中查找特定字符组合的模式。结果，`RegExp.exec`和`String.matchAll`返回匹配的列表。

```
const names = 'Names: John, Frank, Johnson, Kelly'
const regex = /(John)/g;

// .exec
// RegExp(regex).exec(names);
// [
//   'John',
//   index: 7,
//   input: 'Names: John, Frank, Johnson, Kelly',
//   groups: undefined
// ]

// matchAll
const matches = [...names.matchAll(regex)];
matches[0];
// [
//   'John',
//   'John',
//   index: 7,
//   input: 'Names: John, Frank, Johnson, Kelly',
//   groups: undefined
// ]

```

`RegExp.exec`单独传递结果，而`String.matchAll`返回一个迭代器，可用于迭代所有匹配。

ES2022 将允许开发者使用`d`标志来指定`RegExp`结果中`matches`的开始和结束索引。这里有一个例子:

```
const names = 'Names: John, Frank, Johnson, Kelly'
const regex = /(John)/gd;
const matches = [...names.matchAll(regex)];
matches[0];
// [
// "John",
// "John",
// groups: undefined
// index: 7
// indices:[]
//  [7, 11],
//  [7, 11]
// ]
// groups: undefined

```

## `class`字段声明

### 使用前缀`#`定义和实施私有字段

在 ES2022 之前，类字段在`constructor`中简单定义。前缀为下划线(`_`)的字段传统上在类外是不可访问的，因为它们被认为是私有的。看一下下面的示例代码:

```
class User {

  constructor(){

      // public field
      this.name = 'kodex'

      // private field
      this._password = '1234';
  }
}

const user = new User();
console.log(user.name);
// name - public fields are accessible outside the classes

user._password = 'qwert';
console.log(user._password);
// password - no error thrown, we can access it from outside the class

```

在这个例子中，私有属性`_password`在类的外部被访问和修改。

ES2022 将使开发人员只需在字段名前插入一个`#`前缀，即可定义和实施私有字段。ES2022 还消除了在`constructor()`中定义公共或私有字段的需要。

考虑下面的例子:

```
class User {

  // public field
  name = 'kodex'

  // private field
  #password = '1234';
}

const  user = new User()

console.log(user.#password);
user.#password = 'qwert';
// error - Private field '#password' must be declared in an enclosing class

```

在这段代码中，试图访问类外部的私有字段会导致错误。

### 用前缀`#`限制私有字段方法和访问器

ES2022 还允许开发人员使用带有私有方法和访问器的`#`前缀(“getters”和“setters”)来限制类的方法和变量。这使方法和访问器严格保持在内部，并防止它们被类外访问。这里有一个例子:

```
class User {

  // public field
  name = 'kodex'

  // private field
  #password = '1234';

  #getPwd(){
    return this.#password
  }

  set #setPwd(data){
    this.#password = data
  }
}

const user = new User()

// Error - Private field '#getPwd' must be declared in an enclosing class
console.log(user.#getPwd);

// Error - Private field '#setPwd' must be declared in an enclosing class
user.#setPwd = 'qwert';```

```

### 用`static`关键字指定静态字段和私有静态方法

静态`class`字段和方法只能在类原型中访问，而不能在类的每个实例中访问。在 ES2022 之前，`class`的静态字段可以指定如下:

```
class Animal {}
Animal.cow = "mammal"

```

在 ES2022 中，`static`关键字可以用来指定`class`的静态字段和私有静态方法。这里有一个例子:

```
class Animal {
static cow = "mammal"
}

```

## 使用`in`关键字对私有字段进行人体工程学品牌检查

试图访问一个类之外的私有字段会导致抛出一个异常，而不是返回`undefined`。我们可以在类内部使用`try` / `catch`来确定私有字段是否存在。然而，这种方法的问题是很难确定异常的来源。

ES2022 将为开发人员提供关键字`in`,该关键字可用于生成指示私有字段存在的布尔值。这个简单的解决方案不需要`try` / `catch`方法或异常:

```
class Person{
  #name = 'Kelly';
  get #getName(){
    return #name;
  }
  set #setName(){
    #name = 'Anna';
  }
  static hasTitle(obj){
    return #name in obj;
  }
}

```

## 使用`.at()`方法进行负步进

[`.at()`方法提供了对数组或字符串](https://blog.logrocket.com/using-new-javascript-at-method/)的任何索引的简单访问，无论是正数还是负数。这里有一个例子:

```
array= [1, 2, 3, 4, 5]
console.log(array[array.length-1]); // 5
console.log(array.at(-1)); // 5

```

ES2022 将使开发人员能够将`.at()`方法添加到一个数组中，并使用负整数值从数组的末尾向后计数。

## 使用`Object.hasOwn()`方法更容易访问`Object.prototype.hasOwnProperty`

按照惯例，`hasOwnProperty`不能在原型本身之外使用。一个对象可能有一个名为`hasOwnProperty`的属性，它不同于`Object.prototype.hasOwnProperty`。这里有一个例子:

```
const employee = {
  hasOwnProperty:()=> {
    return false
  }
}
obj.hasOwnProperty('prop'); // false

```

ES2022 将使开发人员能够通过使用`Object.hasOwn()`方法来解决这个问题。该方法将对象作为第一个参数，将我们希望验证的属性作为第二个参数:

```
const employee = {
name: 'Grace',
age: 18
}
Object.hasOwn(employee, 'name'); // true
Object.hasOwn(employee, 'position'); // false
```

## 使用`static{}`程序块进行类定义评估时的初始化

传统上，像`try` / `catch`这样的语句是在初始化期间在类体之外进行计算的。

```
class UserStatus{
    status = false;
    get getStatus(){
        if(!this.#status){
            throw new Error('User is not active');
        } 
        return this.#status
    }
}

// evaluate outside the class body
try {
    const state = UserStatus.getStatus;
    UserStatus.status = state
} catch {
    UserStatus.status = false
}

```

ES2022 将允许开发者使用`static{}`块来评估类声明范围内的语句。这在需要设置多个静态字段的情况下非常有用。`static{}`块允许访问一个类的私有状态(方法和字段),并允许在同一个作用域中声明的类或函数之间共享信息。

```
let initState;

class UserStatus{
    #status = false;

    get getStatus(){
        if(!this.#status){
            throw new Error('User is not active');
        } 
        return this.#status
    }
    static {
        initState = () => {
            this.#status = this.getStatus;
        }
    }
}

initState();

```

## 用`cause`属性链接错误

传统上，在运行时使用上下文信息(如错误消息和错误实例属性)来识别和处理错误。如果错误发生在深度嵌套的函数中，在缺乏适当的异常设计模式的情况下，很难确定其原因。

```
function processData(arrayData) {
  return arrayData.map(data => {
      try {
        const json = JSON.parse(data);
        return json;
      } catch (error) {
        // throw random error
      }
    });

```

在 ES2022 中，`cause`属性作为一个额外的参数被添加到`Error()`构造函数中，允许链接错误，而不需要在条件中包装错误的不必要的手续。

```
function processData(arrayData) {
  return arrayData.map(data => {
      try {
        const json = JSON.parse(data);
        return json;
      } catch (err) {
        throw new Error(
          `Data processing failed`,
          {cause: err}
        );
      }
    });
}
```

## 结论

开发人员必须了解最新的语言规范。在本文中，我们研究了 JavaScript 的 ES2022 规范将提供的八个新特性。JavaScript 在不断发展，每年都会增加新的特性。我们很高兴看到 ES2023 有哪些新功能！

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.