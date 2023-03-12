# 使用 JavaScript Reflect API 

> 原文：<https://blog.logrocket.com/working-with-the-javascript-reflect-api/>

作为开发人员，您需要能够创建能够处理动态代码的系统和应用程序。这些程序应该有能力在运行时操作变量、属性和对象方法。为此，ES6 中引入了一个新的全局对象`[Reflect](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect)`，它能够处理简单的代码操作。

本文的目标是帮助您更好地理解 JavaScript 中的`Reflect`概念，以及如何使用提供的各种方法。`Reflect`使您能够轻松地修改现有对象的功能，同时仍然提供其默认行为。

**目录:**

## JavaScript `Reflect`是什么？

JavaScript `Reflect`是一个内置的 ES6 全局对象，它提供了在运行时操纵属性、变量和对象方法的能力。它不是一个构造函数，因此不能使用`[new](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new)`操作符。

## `Proxy`构造函数和`Reflect`有什么区别？

`Proxy`和`Reflect`都是在 ES6 中引入的，都是用来执行任务的，但是有点不一样。

与`Reflect`不同， [JavaScript 的`Proxy`没有任何属性](https://blog.logrocket.com/use-es6-proxies-to-enhance-your-objects/)。相反，它包装另一个对象并拦截它的操作。同时，`Reflect`是一个内置对象，它简化了`Proxy`的创建，并使得调用内部方法成为可能。

`Proxy`只需要两个参数:

*   `target`:`Proxy`将要包装的对象
*   `handler`:拦截目标操作的`Proxy`配置

这里有一个例子:

```
const profile = { name: 'Pascal', age:23 }
const handler = {
 get(target, prop, receiver) {
   if (target[prop]) {
     return target[prop]
    }
    return `"${prop}" prop don't exist on this object !`
  }
}

const profileProxy = new Proxy (profile, handler)

console.log(profileProxy.name) // Pascal
console.log(profileProxy.profession) // "profession" prop don't exist on this object !

```

上面的例子相当于本指南后面描述的`Reflect.get()`，然而，`Reflect.get()`技术更简单、更直接。

## 使用 JavaScript `Reflect` API 方法

让我们仔细看看`Reflect`对象的方法。所有这些方法都是静态的，也就是说，它们只能在`Reflect`对象上使用，而不能在任何实例上使用。

### `Reflect.construct()`

`new`操作符和`Reflect.construct()`方法与`new target(...args)`相似，但可以选择不同的原型。`Reflect.construct()`接受三个参数:

*   `target`:要调用的功能
*   `args`:一个参数数组
*   `newTarget`:可选的构造器，其原型应该被利用；如果未指定，其默认值为`target`

考虑下面的例子:

```
function summation(x,y,z){
  this.add = x + y +z
}
const sum = Reflect.construct(summation, [1,2,3,4,5])
console.log(sum)

// Result: summation {add: 6}

```

`Reflect.construct()`产生一个`target`或`newTarget`(如果指定的话)的新实例，它是用提供的参数数组`args`构造的。在引入`Reflect.construct()`之前，我们会结合构造器和原型来创建一个对象:`Object.create()`。

### `Reflect.apply()`

`Reflect.apply()`是使用提供的参数调用目标函数的简单直接的方法。它接受三个参数:

*   `target`:要调用的功能
*   `thisArgument`:调用`target`功能需要`this`值
*   `args`:包含调用`target`所用参数的数组

这里有一个例子:

```
/* Return the highest value in the array */

const arr = [3,5,20,3,31]
const a = Reflect.apply(Math.max, undefined, arr)
console.log(a)

// Result: 31

```

在引入`Reflect.apply()`之前，我们可以使用`function.prototype.apply()`方法来执行类似的任务，就像这样:

```
const arr = [3,5,20,3,31]
const a = Function.prototype.apply.call(Math.max, undefined, arr);
console.log(a)
// Result: 31

```

### `Reflect.defineProperty()`

要创建或编辑对象的属性，使用`Reflect.defineProperty()`方法。它返回一个布尔值，该值指示是否成功定义了一个属性。这个方法有三个参数:

*   `target`:将定义属性的对象
*   `propertyKey`:要创建或编辑的属性的名称
*   `attributes`:正在定义的属性的属性

请参见以下示例:

```
const obj = {}
Reflect.defineProperty(obj, 'prop', {value: 70})
console.log(obj.prop)
// Result: 70

```

### `Reflect.get()`

顾名思义，`Reflect.get()`用于从对象中检索属性。它接受三个参数:

*   `target`:目标对象
*   `propertyKey`:要获取的属性的名称
*   `receiver`(可选):如果遇到 getter，`this`值作为调用目标对象的接收者被传递

这里有一个例子:

```
// with array
const b = [10,11,12,13,14]
console.log(Reflect.get(b, 2))
// Result: 12

// with object
const obj = {name: "Pascal", age: 23}
console.log(Reflect.get(obj, 'age'))
// Result: 23

```

### `Reflect.getPrototypeOf()`

`Reflect.getPrototypeOf()`函数返回所提供目标的原型，很像`Object.getPrototypeOf()`。此方法只接受一个参数:

*   `target`:我们想要得到原型的物体

请参见以下示例:

```
const profile = {  
  name: 'Pascal'  
};  
const pro = Reflect.getPrototypeOf(profile);  
console.log(pro); 

```

### `Reflect.set()`

`Reflect.set()`方法用于给对象属性赋值。它返回`true`表示属性设置成功。该函数有四个参数:

*   `target`:要设置属性的对象
*   `key`:物业名称
*   `value`:将要分配的值
*   `receiver(optional)`:如果找到 setter，必须使用`this`值来调用目标

这里有一个例子:

```
const arr1 = [];
Reflect.set(arr1, 0, 'first');
Reflect.set(arr1, 1, 'second');
Reflect.set(arr1, 2, 'third');
console.log(arr1);

```

### `Reflect.deleteProperty()`

`Reflect.delete Property()`是从对象中删除属性的方法。如果属性被正确删除，它返回`true`。该函数有两个参数:

*   `target`:物体
*   `ke` y:要删除的属性的名称

请参见以下示例:

```
Reflect.deleteProperty(obj3, 'age');
console.log(obj3)

```

### `Reflect.isExtensible()`

`Reflect.isExtensible()`和`Object.isExtensible()`一样，是一种检测对象是否可扩展的方法(即是否可以向其添加额外的属性)。`Reflect.isExtensible()`返回一个布尔值，表明目标是否可扩展。它只考虑一个论点:

*   `target`:要检查扩展性的对象

`Reflect.preventExtensions()`方法可用于防止对象变得可扩展，即防止新属性被添加到对象中。

请参见下面的示例:

```
const user = {
  name: "John Deeman"
};
console.log(Reflect.isExtensible(user))  
// true

// block extension
Reflect.preventExtensions(user);
console.log(Reflect.isExtensible(user))  
// false

```

### `Reflect.ownKeys()`

`Reflect.ownKeys()`方法基本上返回一个包含目标对象属性键的数组。它只考虑一个论点:

*   `target`:从中获取密钥的对象

这里有一个例子:

```
const obj = {
  car: "Rolls Royce",
  color: "black"
};

const array1 = [];

console.log(Reflect.ownKeys(obj));
// ["car", "color"]

console.log(Reflect.ownKeys(array1));
// ["length"]

```

### `Reflect.getOwnPropertyDescriptor()`

`Reflect.getOwnPropertyDescriptor()`方法返回一个描述符，该描述符定义如何配置给定对象的特定属性。它需要两个参数:

*   `target`:要搜索属性的对象
*   `key`:需要描述的属性的名称

请参见下面的示例:

```
const obj = {
  car: "Rolls Royce",
  color: "black",
  get (){
    return `I have a ${color} ${car} car`
  }
};

console.log(Reflect.getOwnPropertyDescriptor(obj, 'car').value);
// "Rolls Royce"

console.log(Reflect.getOwnPropertyDescriptor(obj, 'color'));
// {value: "black", writable: true, enumerable: true, configurable: true}

console.log(Reflect.getOwnPropertyDescriptor(obj, 'color').writable);
// true

```

属性描述符可以包含以下属性:

*   `value`:与属性关联的值
*   `writable`:一个布尔值，仅当属性的关联值可修改时才返回`true`
*   `configurable`:一个布尔值，仅当属性描述符的类型可以被修改并且属性可以从相关对象中删除时才返回`true`
*   `enumerable`:一个布尔值，仅当属性在相关对象的属性枚举期间出现时才返回`true`

### `Reflect.has()`

`Reflect.has()`方法验证目标对象中是否定义了一个属性。它返回一个布尔值。`Reflect.has()`执行与`in`操作符相似的操作，并接受两个参数:

*   `target`:要检查属性的对象
*   `key`:要验证的属性的名称

这里有一个例子:

```
const obj = {
  name: "Douglas"
};

console.log(Reflect.has(obj, 'name'));
// true

console.log(Reflect.has(obj, 'age'));
// false

console.log(Reflect.has(obj, 'toString'));
// true

```

## 结论

在本文中，我们研究了 JavaScript `Reflect`对象，还讨论了`Proxy`和`Reflect`之间的区别。我们还看了如何使用各种`Reflect`方法的例子，包括用于返回对象属性值的`Reflect.get()`，用于删除对象属性的`Reflect.deleteProperty()`，以及用于返回对象属性键的`Reflect.ownKeys()`。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.