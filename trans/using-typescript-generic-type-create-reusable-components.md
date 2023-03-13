# 使用 TypeScript 泛型类型创建可重用组件

> 原文：<https://blog.logrocket.com/using-typescript-generic-type-create-reusable-components/>

## 什么是 TypeScript 泛型？

TypeScript 中的泛型是一种创建可重用组件或函数的方法，这些组件或函数可以处理多种类型。泛型是一个强大的工具，可以帮助我们创建可重用的函数。它们允许我们构建数据结构，而不需要为它们在编译时的执行设置具体的时间。

在 TypeScript 中，它们用于编写可重用的、类型安全的代码，其中变量的类型在编译时是已知的。这意味着我们可以动态定义将要预先声明的参数或函数的类型。当我们需要在应用程序内部使用某些逻辑时，这非常方便；有了这些可重用的逻辑，我们就可以创建接受和给出自己类型的函数。

我们可以使用泛型在编译时实现检查，消除类型转换，并在应用程序中实现额外的泛型函数。如果没有泛型，我们的应用程序代码会在某个时候编译，但是我们可能得不到预期的结果，这可能会将错误推向生产。这个强大的特性可以帮助我们创建可重用的、通用的、类型安全的类、接口和函数。

在这篇文章中，我们将学习如何在不牺牲性能和效率的情况下通过泛型实现类型安全。我们可以在尖括号之间写一个类型参数:`<T>`。我们还可以在 TypeScript 中创建泛型类、泛型方法和泛型函数。

*向前跳转:*

## 运行中的 TypeScript 泛型

### 不使用泛型的函数

让我们考虑下面的例子。下面，我们有一个简单的函数，它将从一个数组中移除一个随机索引。我们将把我们的函数命名为`removeRandomArrayItem`，看起来像:

```
function removeRandomArrayItem(arr: Array<number>): Array<number> {
 const randomIndex = Math.floor(Math.random() * arr.length);
 return arr.splice(randomIndex, 1);
}

removeRandomArrayItem([6, 7, 8, 4, 5, 6, 8, 9]);

```

上面的函数告诉我们，函数名是`removeRandomArrayItem`，它将接受一个参数`item`，这是一种由数字组成的数组。最后，这个函数返回一个值，也是一个数字数组。

如你所见，我们已经在代码中引入了一些约束。假设我们想循环访问一个字符串数组。我们应该构建另一个函数来处理这个用例吗？

不要！这是 TypeScript 泛型发挥作用的最佳时机。

### 使用泛型的函数

在使用泛型编写解决方案之前，让我们先来看看这个问题。如果我们给上面的函数传递一个字符串数组，它会抛出这个错误:

```
'Type ‘string’ is not assignable to type of ‘number’
```

我们可以通过在类型声明中添加`any`来解决这个问题:

```
function removeRandomArrayItem(arr: Array<any>): Array<any> {
 const randomIndex = Math.floor(Math.random() * arr.length);
 return arr.splice(randomIndex, 1);
}

 console.log(removeRandomArrayItem(['foo', 1349, 6969, 'bar']));

```

但是如果我们不处理任何数据类型，就没有理由使用 TypeScript。让我们重构这段函数，使用泛型进行转换。

```
function removeRandomArrayItem<T>(arr: Array<T>): Array<T> {
 const randomIndex = Math.floor(Math.random() * arr.length);
 return arr.splice(randomIndex, 1);
}

console.log(removeRandomArrayItem(['foo', 'bar']));
console.log(removeRandomArrayItem([45345, 3453]));

```

这里，我们表示一个名为`<T>`的类型，这将使它的行为更加通用。这将保存由函数本身接收的数据类型。

### 使用泛型的类型脚本类

```
class Foo {
 items: Array<number> = [];

 add(item: number) {
   return this.items.push(item);
 }

 remove(item: Array<number>){
   const randomIndex = Math.floor(Math.random() * item.length);
   return item.splice(randomIndex, 1);
 }
}

const bar = new Foo();

bar.add(22);
bar.remove([1345, 45312613, 13453]);

```

在这里，我们创建了一个名为`Foo`的简单类，它包含一个数字数组变量。我们还创建了两个方法:一个向数组中添加项目，另一个从数组中移除随机元素。

这段代码工作得很好，但是我们引入了和以前一样的问题:如果我们在数组中添加或删除项目，泛型将只接受数字数组。让我们重构这个类，使用一个泛型来接受一个泛型值，这样我们就可以将任何类型传递给参数。

```
class Foo<TypeOfFoo> {
 items: Array<TypeOfFoo> = [];

 add(item: TypeOfFoo) {
   return this.items.push(item);
 }

 remove(item: Array<TypeOfFoo>){
   const randomIndex = Math.floor(Math.random() * item.length);
   return item.splice(randomIndex, 1);
 }
}

const bar = new Foo();

bar.add(22);
bar.add('adfvafdv');
bar.remove([1345, 45312613, 13453]);
bar.remove([1345, 45312613, '13453']);

```

通过在类中使用泛型，我们的代码变得更加可重用和简洁。这才是泛型真正发光的地方！

## 在 TypeScript 接口中使用泛型

泛型并不特别依赖于函数和类。我们也可以在接口内部的 TypeScript 中使用泛型。让我们来看一个如何在实际中使用它的例子:

```
const currentlyLoggedIn = (obj: object): object => {
 let isOnline = true;
 return {...obj, online: isOnline};
}

const user = currentlyLoggedIn({name: 'Ben', email: '[email protected]'});

const currentStatus = user.online

```

写完上面几行，我们得到一个错误，一条弯弯曲曲的线告诉我们不能从用户那里访问`isOnline`的属性:

```
Property 'isOnline' does not exist on type 'object'.

```

这主要是因为函数`currentlyLoggedIn`不知道它通过我们添加到参数中的对象类型接收的对象类型。我们可以通过使用一个泛型来解决这个问题:

```
const currentlyLoggedIn = <T extends object>(obj: T) => {
 let isOnline = true;
 return {...obj, online: isOnline};
}

const user = currentlyLoggedIn({name: 'Ben', email: '[email protected]'});

user.online = false;

```

我们当前在函数中处理的对象的形状可以在一个接口中定义:

```
interface User<T> {
 name: string;
 email: string;
 online: boolean;
 skills: T;
}

const newUser: User<string[]> = {
 name: "Ben",
 email: "[email protected]",
 online: false,
 skills: ["foo", "bar"],
};

const brandNewUser: User<number[]> = {
 name: "Ben",
 email: "[email protected]",
 online: false,
 skills: [2456234, 243534],
};

```

这是另一个我们如何使用泛型接口的例子。我们用`Greet`定义了一个接口，它将接受一个泛型，这个特定的泛型将是传递给我们的`skills`属性的类型。

这样，我们可以将所需的值传递给我们的`Greet`函数中的`skills`属性。

```
interface Greet<T> {
 fullName: "Ben Douglass";
 skills: T
 messageGreet: string
}

const messageGreetings = (obj: Greet<string>): Greet<string> => {
 return {
   ...obj,
   messageGreet: `${obj.fullName} welcome to the app`,
 skills: 'sd'
 };
};

```

## 将默认泛型值传递给泛型

我们还可以将默认的泛型类型传递给泛型。这在我们不想强行传入函数中正在处理的数据类型的情况下很有用。默认情况下，我们将其设置为`number`。

```
function removeRandomArrayItem<T = number>(arr: Array<T>): Array<T> {
 const randomIndex = Math.floor(Math.random() * arr.length);
 return arr.splice(randomIndex, 1);
}
console.log(removeRandomArrayItem([45345, 3453, 356753, 3562345, 3567235]));

```

这个片段反映了我们如何在`removeRandomArray`函数中使用默认的泛型类型。这样，我们就能够传递一个默认的泛型类型`number`。

## 传递多个泛型值

如果我们希望我们的可重用函数块接受多个泛型，我们可以做以下事情:

```
function removeRandomAndMultiply<T = string, Y = number>(arr: Array<T>, multiply: Y): [T[], Y] {
 const randomIndex = Math.floor(Math.random() * arr.length);
 const multipliedVal = arr.splice(randomIndex, 1);
 return [multipliedVal, multiply];
}
console.log(removeRandomAndMultiply([45345, 3453, 356753, 3562345, 3567235], 608));

```

这里，我们创建了前面函数的修改版本，以便引入另一个泛型参数。我们用字母`Y`表示它，它被设置为默认类型`number`，因为它将乘以我们从给定数组中选取的随机数。

因为我们在乘数字，所以我们肯定是在处理一个`number`类型，所以我们可以传递默认的泛型类型`number`。

## 向泛型添加约束

泛型允许我们处理任何作为参数传递的数据类型。但是，我们可以向泛型添加约束，将其限制为特定的类型。

可以声明一个受另一个类型参数限制的类型参数。这将帮助我们在对象上添加约束，确保我们不会获得一个可能不存在的属性。

```
function getObjProperty<Type, Key extends keyof Type>(obj: Type, key: Key) {
 return obj[key];
}

let x = { name: "Ben", address: "New York", phone: 7245624534534, admin: false };

getObjProperty(x, "name");
getObjProperty(x, "admin");
getObjProperty(x, "loggedIn"); //property doesn't exist

```

在上面的例子中，我们为函数接收的第二个参数创建了一个约束。我们可以用各自的参数调用这个函数，一切都会正常，除非我们用值`x`传递一个在对象类型中不存在的属性名。这就是我们如何使用泛型来限制对象定义属性。

## 结论

在这篇文章中，我们探索了如何使用泛型，并在代码库中创建可重用的函数。我们实现了泛型来创建函数、类、接口、方法、多个接口和默认泛型。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.