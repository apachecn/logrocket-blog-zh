# JavaScript ES6:更多新的抽象来改进您的代码日志博客

> 原文：<https://blog.logrocket.com/javascript-es6-even-more-new-abstractions-to-improve-your-code-f046869e4a92/>

ES6，也称为 ECMAScript 2015，有许多非常酷的新功能，可以提高 JavaScript 代码的质量。昨天， [Glad 介绍了 ES6 中的五个新特性](https://blog.logrocket.com/javascript-es6-5-new-abstractions-to-improve-your-code-54a369e82407),这些特性可能会改进你编写的代码。

所以我想，为什么不多拍五部呢？

对于那些略读的人来说，这里是它们的列表:

1.  `let`和`const`
2.  `for … of`循环
3.  传播
4.  `Map`
5.  承诺

## 1.`let`和`const`

`let`只是声明变量的另一种方式。唯一的区别是`let`是块范围的，也就是说，当用于在块内声明变量时，该变量的值在块外是不可访问的。

```
let foo = 10;
let bar = 5;

if (true) {
    let bar = foo * 2;
    console.log( bar ); // 20
}

console.log( bar ); // 5
```

`const`允许您为变量设置一个值，该值在应用程序的整个生命周期中保持不变。

```
const foo = 20;
```

## 2.`for … of`循环

在 ES6 中，引入了一种迭代数组中每个值的新方法，称为`for ... of`循环。

它还使得遍历集合的元素变得更加容易。

```
let colors = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet'];

for(var color of colors){
  console.log(color);
}
```

这将记录数组的直接值，而不必像通常那样通过键`colors[i]`获取它。

## 3.传播

spread 运算符，也称为`...`运算符，是在 ES6 中引入的。它使处理对象变得更加容易，因为它有助于两件事:

1.  将一个数组或对象扩展到另一个数组或对象中
2.  将多个参数加入一个数组

将一个数组或对象扩展到另一个数组或对象中:

```
let arr1 = [1, 2 , 3];
let arr2 = [0, ...arr1, 4, 5, 6];
console.log(arr2); // 0,1,2,3,4,5,6
```

如上所述，spread 运算符在使用数组向函数传递参数时也很方便:

```
function user(name, age)
{
  console.log(`My name is ${name}, I am ${age} years old.`);
}

let person = ['Brian Willer', 38];

user(...person); //My name is Brian Willer, I am 38 years old.
```

## 4.`Map`

地图类似于数组。它们拥有一个键值对，但是允许您指定自己的索引，并且指定的索引是惟一的。

```
var greetings = new Map();

greetings.set("hello", "Bless");
greetings.set(name, "panda");

greetings.get(name); // panda
greetings.get("hello"); // Bless

greetings.size; // 2
```

**注意:**要在多种浏览器上使用地图，您需要一个 polyfill，因为并非所有浏览器都实现了它。

## 5.承诺

承诺为我们提供了一种以同步方式处理异步操作和流程的方法。有了它，我们可以轻松地编写非依赖代码。

有人认为不需要承诺，可以使用异步、回调等。然而，Javascript ES6 现在有了一个易于使用的标准承诺实现。

```
var welcomeMessage =  new Promise(function(resolve, reject) {
  setTimeout(resolve, 1000)
}).then(function() {
  console.log('Welcome User!')
})
```

**注意:**并不是所有的浏览器都支持开箱即用，所以你需要一个 polyfill 来跨浏览器使用。

## 结论

ES6 有这么多很酷很棒的功能，有些你可能会用到，有些你可能不会马上用到，但它们仍然值得一试。

如果你想了解更多的功能，卢克·霍班创建了一个 ES6 功能回购，列出了 ES6 提供的所有酷功能。

如果你觉得这篇文章有帮助，别忘了在评论中表达你的爱。

干杯！！！