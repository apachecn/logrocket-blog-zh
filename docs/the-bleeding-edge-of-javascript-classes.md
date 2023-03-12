# JavaScript 类的前沿——log rocket 博客

> 原文：<https://blog.logrocket.com/the-bleeding-edge-of-javascript-classes/>

在本文中，我们将看看 JavaScript 类的前沿。

JavaScript 类是一种特殊类型的函数。然而，它们类似于典型的函数，因为 JavaScript 类是用关键字声明的，并用表达式语法初始化。

JavaScript 最初没有类。随着 ECMASCRIPT 6 (es6)的引入，添加了一些类，ECMASCRIPT 6 是 JavaScript 的一个新的改进版本(ECMASCRIPT 5 是旧版本)。

典型的 JavaScript 类是一个带有默认构造函数方法的对象。JavaScript 类是建立在原型之上的，但是有一个表达式语法。

在类出现之前，原型被用来模拟 JavaScript 中的类。原型是附加到每个 JavaScript 函数和对象的默认对象。原型可以附加额外的属性，这有助于我们模拟 JavaScript 类。

为了更好地理解这一点，让我们用两个参数声明一个名为‘car’的函数:年龄和姓名。

```
function Car(){
    this.name = 'dragon';
    this.age = 3;
}

```

可以使用原型添加一个额外的属性，正如您将在下面的代码块中看到的:

```
Car.prototype.color = 'white'

```

接下来，让我们创建一个新的汽车实例:

```
let Car1 = new Car()

```

现在，我们将把新添加的属性记录到控制台:

```
console.log(Car1.color)

```

本例中的 JavaScript 函数 car 充当一个具有三个属性的类:name、age 和 color。换句话说，JavaScript 使用原型附带的继承来模拟类。

## ES6 classes

随着 JavaScript 中类的引入，ES6 使用类似于其他面向对象编程语言的语法，为我们提供了一种更简洁的类声明方式。与 ES5 处理类的方法相反，ES6 在处理类时不需要 function 关键字，尽管是在幕后。JavaScript 仍然认为类是一种特殊类型的函数。

可以说，类和函数的主要区别在于提升:与函数不同，JavaScript 类需要在被访问之前声明。否则，它会抛出一个错误。

### 类别关键字

JavaScript 为我们提供了 class 关键字，这是定义类的主要方式。它充当已经存在的原型继承模式的语法糖。

```
//javascript class declaration
class Car  {
   //methods
}

```

如上所示，class 关键字用于指定正在定义的 JavaScript 类。

当使用类表达式定义一个类时，可以遵循与上面不同的方法，以获得更大的灵活性。这样，一个类既可以命名也可以不命名。

```
//unnamed javascript class expression
let Car = class {
    constructor(name, age){
        this.name = name
        this.age = age
    }
}

```

下面是一个命名 JavaScript 类表达式的示例:

```
//named javascript class expression
let Car = class Car {
    constructor(name, age){
        this.name = name
        this.age = age
    }
}

```

### 构造函数方法

构造函数方法是 JavaScript 中用于初始化类对象的特殊方法。一旦一个类被初始化，它就会在 JavaScript 中被自动调用。任何 JavaScript 类中只能有一个构造函数方法。

如果没有定义，JavaScript 会向相关的类添加一个零参数的空构造函数。

下面是一个带有构造函数方法的类的示例:

```
//javascript class with a constructor
 class Car {
    constructor(name, age){
        this.name = name
        this.age = age
    }
}

```

上面的类包含一个带有两个参数的构造函数:name 和 age。

### 静态法

静态方法是在类本身上调用的方法，而不是在类的实例上调用的方法。静态方法不是类的实例，但是它在功能上与类相关。

下面是静态方法的一个典型示例:

```
class Math {
    static add(number1 , number2){
        return number1 + number2
    }
}

```

然后可以如下所示调用上面的静态方法:

```
let additionResult = Math.add(2,3)

```

注意，静态方法 add 向我们展示了做加法意味着什么。

### ES6 类语法

典型的 JavaScript 类具有如下所示的语法:

```
class Car {
    constructor(){
        //default operation
    }
    method(){
        //operation2
    }

}

```

## ES6 类的问题

对于在 JavaScript 中执行某些操作的简单方式，类可以提供更复杂的解决方案。具有面向对象编程语言背景的人可能会决定用类来执行简单的操作，即使它们并不是必需的。

一些开发人员可能会争辩说，类的添加夺走了 JavaScript 的原创性，利用原型是执行需要类的操作的更灵活的方式。这是因为与其他面向对象编程语言中的类不同，JavaScript 不提供基本的类功能，比如私有变量声明。

ECMASCRIPT 2020 旨在解决其中一些问题。

## ECMASCRIPT 2020 对类的补充

每年都会对 JavaScript 进行添加和修改，以满足 JavaScript 用户的需求。最新的修改在 ECMASCRIPT 2020 中。2020 年类的一些新增内容包括私有类变量和静态字段。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

**私有类变量**:当在有大量变量声明的大型代码库中工作时，可能需要一个只能在类中访问的变量。私有类变量解决了这个问题。通过在变量名前添加散列，可以很容易地创建私有变量。

```
 class Detail {
      #name = "steve"
     welcome(){
        console.log(this.#message)
      }
 }

 const detail_1 = new Detail()
   detail_1.welcome() 

```

上述变量' #name '只能在类' Detail '中访问。

**静态字段**:为了理解静态字段，让我们考虑下面的代码片段；

```
class Detail {
     #name = "steven"

     welcome(){
         console.log(this.#message)
     }
 }

```

对于旧版本的 JavaScript，试图在不创建新的类实例的情况下访问方法“welcome”似乎是不可能的。但是通过最新的添加，我们无需创建实例就可以访问这些方法。

上述方法的访问如下所示:

```
 const DetailMethod = Detail.welcome()

```

## 结论

JavaScript 类解决了使用原型带来的一些问题。它们使得类声明更加直接和简单。最新的 ECMASCRIPT 2020 增加了更多的灵活性，使得利用类变得更加容易。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.