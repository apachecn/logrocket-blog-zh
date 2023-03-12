# JavaScript 设计模式

> 原文：<https://blog.logrocket.com/javascript-design-pattern-214d888096a3/>

![](img/8e823d4b2186c4fa13089630c5e7ec8a.png)

#### 目标

本文将:

*   让您习惯 Javascript 模式
*   解释为什么您应该考虑在下一个 Javascript 项目中使用设计模式

对于我过去构建的一些项目，我只使用 Javascript 模式，因为我认为它们看起来很花哨，而不是因为它们给体验增加了任何有意义的东西。

我想帮助你避免同样的错误。

这似乎是显而易见的，但是使用特定类型的设计模式确实应该有一个很好的理由。

[![](img/94b3e0f84b30cb0d321f60471481ab64.png)](https://logrocket.com/signup/)

#### 为什么考虑在 Javascript 中使用设计模式？

> 模式是一种可重用的解决方案，可以应用于软件工程中常见的问题。

使用设计模式有助于减少花在代码外观上的时间。

不仅如此，一个好的设计模式强化了 DRY(不要重复自己)的概念，这有助于防止你的代码库变得庞大而笨拙。

设计模式还有助于团队成员协作，尤其是在团队中的每个人都熟悉所讨论的模式的情况下。在构建项目的过程中，当处理统一的风格(模式)时，每个团队成员肯定会更好地交流。

#### 你如何识别一个好的设计模式？

虽然这听起来很有趣，但是一个好的模式需要有一个名字和一个精确、详细的结构。这与单纯的习惯完全不同。

每个开发人员都有解决特定问题的方法(例如，文件上传)。当在我们的任何项目中需要处理一个文件时，我们很乐意立即实施这个特定的解决方案。

我们能把这称为一种模式吗？肯定不是。一个好的或公认的设计模式必须与现有的模式相关联。不仅如此，还必须得到其他开发商的认可。

如何使用模式的例子和详细的文档怎么强调都不为过。

#### 设计模式类别

让我们快速了解一些主要的 Javascript 设计模式。在本文中，我们将只考虑六(6)种模式:

*   构造器模式
*   原型模式
*   模块模式
*   单一模式
*   工厂模式
*   观察者模式

#### 1.构造器模式

这是创建构造器模式的一种方法:

```
function Animal (name) {

    this.name = name;

    this.properties = function() {
        console.log(`This ${this.name} can walk`);
    }
}

const animalOne = new Animal('Dog');
const animalTwo = new Animal('Cat');

console.log(animalOne.name); //Dog
console.log(animalTwo.name);
animalOne.properties(); //This Dog can walk
animalTwo.properties(); //This Cat can walk
```

Constructor pattern

为了在构造函数模式中访问函数的属性，需要对它进行初始化。当考虑面向对象设计时，这种模式很有用。

> const object = new constructor object()；

关键字`new`告诉 Javascript】应该表现得像一个构造函数。这种模式的缺点之一是它不支持继承。不同对象之间共享的属性将总是重复的。

#### 2.原型模式

在构造器模式中，对象中设置的方法或属性在被调用时总是`redefined`。解决这个问题的更好的方法是在原型函数中创建一个函数。

有了这一点，在实例化时调用的函数不会重新定义自己。但是原型模式也有不利的一面。一个属性很容易在所有功能之间共享，即使在不需要的时候。你无法控制你的财产是私有还是公有。它是自动公开的:

```
function Animal(name) {
    this.name = name;
}

Animal.prototype.properties = function() {
    console.log(`This ${this.name} can walk`);
};

const animalOne = new Animal('Dog');
const animalTwo = new Animal('Cat');

console.log(animalOne.name); //Dog
console.log(animalTwo.name);
animalOne.properties(); //This Dog can walk
animalTwo.properties(); //This Cat can walk
```

Prototype pattern

#### 3.模块模式

模块模式是对原型模式的一点改进。在模块模式中，您可以设置不同类型的修饰符(私有的和公共的)。在创建相同的函数或属性时，很有可能不会发生冲突。

你也可以灵活地将`re-naming`函数公开，就像我们将`addAnimal`函数重新命名为`add`。缺点是无法从外部环境覆盖创建的函数。`removeAnimal`不依赖私有属性`container array`功能无法从外部覆盖:

```
function AnimalContainter () {

    const container = []; 

    function addAnimal (name) {
        container.push(name);
    }

    function getAllAnimals() {
        return container;
    }

    function removeAnimal(name) {
        const index = container.indexOf(name);
        if(index < 1) {
            throw new Error('Animal not found in container');
        }
        container.splice(index, 1)
    }

    return {
        add: addAnimal,
        get: getAllAnimals,
        remove: removeAnimal
    }
}

const container = AnimalContainter();
container.add('Hen');
container.add('Goat');
container.add('Sheep');

console.log(container.get()) //Array(3) ["Hen", "Goat", "Sheep"]
container.remove('Sheep')
console.log(container.get()); //Array(2) ["Hen", "Goat"]
```

Module pattern

#### 4.单一模式

尽管上述模式很有趣，但它们不能用于只需要一个实例的场景。让我们来看看数据库连接。当数据库实例已经创建时，不能继续创建它。您可以在实例关闭时创建一个新实例，或者停止正在进行的实例来创建一个新实例。

单一模式确保一个对象的实例只被创建一次。它也被称为严格模式。这种模式的缺点是很难测试。存在隐藏的依赖关系对象，很难挑选出来进行测试:

```
function DatabseConnection () {

    let databaseInstance = null; 

    // track number of times the instance is created 
    let count = 0; 

    function init() {
        console.log(`Opening databse #${count + 1}`);
        /**
         * perform operation 
         */
    }

    function createIntance() {
        if(databaseInstance == null) {
            databaseInstance = init();
        }
        return databaseInstance;
    }

    function closeIntance() {
        console.log('closing database');
        databaseInstance = null;
    }

    return {
        open: createIntance,
        close: closeIntance
    }
}

const database = DatabseConnection();
database.open(); //Opening databse #1
database.open(); //Opening databse #1
database.open(); //Opening databse #1
database.close(); //closing database
```

Singleton pattern

来自`DatabaseConnection`的`database.open()`对象将不断返回`1`，因为该实例只被创建过一次。

#### 5.工厂模式

这种模式确保对象是用某种通用接口创建的。我们可以指定想要从`interface`对象创建的对象类型。让我们假设我们想要使用多个供应商(供应商 A、供应商 B …供应商 *n* )来处理用户付款。每个供应商的目标是确保付款成功进行。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

在这种情况下，`Factory pattern`是我们最好的选择。我们不必过多考虑付款将如何进行，而不管在特定时间使用哪个供应商。

工厂模式提供了一个接口，我们可以在其中指定在每个时间点处理付款时要使用的供应商类型:

```
/**
 * Vendor A
 */
VendorA = {};

VendorA.title = function title() {
  return "Vendor A";
};

VendorA.pay = function pay(amount) {
  console.log(
    `setting up configuration using username: ${this.username} and password: ${
      this.password
    }`
  );
  return `Payment for service $${amount} is successful using ${this.title()}`;
};

/**
 *Vendor B
 */
VendorB = {};
VendorB.title = function title() {
  return "Vendor B";
};

VendorB.pay = function pay(amount) {
  console.log(
    `setting up configuration using username: ${this.username} 
        and password: ${this.password}`
  );
  return `Payment for service $${amount} is successful using ${this.title()}`;
};

/**
 *
 * @param {*} vendorOption
 * @param {*} config
 */

function VendorFactory(vendorOption, config = {}) {
  const vendor = Object.create(vendorOption);
  Object.assign(vendor, config);
  return vendor;
}

const vendorFactory = VendorFactory(VendorA, {
  username: "test",
  password: "1234"
});
console.log(vendorFactory.title());
console.log(vendorFactory.pay(12));

const vendorFactory2 = VendorFactory(VendorB, {
  username: "testTwo",
  password: "4321"
});
console.log(vendorFactory2.title());
console.log(vendorFactory2.pay(50));
```

Factory pattern

```
Vendor A
setting up configuration using username: test and password: 1234
Payment for service $12 is successful using Vendor A
```

```
............................................................
Vendor B
setting up configuration using username: testTwo and password: 4321
Payment for service $50 is successful using Vendor B
```

在上面的`factory pattern snippet`中，我们有两个供应商(`A and B`)。与`VendorFactory`接口的客户端不用担心在`vendors`之间切换时调用哪个方法。

如果我们不想创建同一个对象的多个实例，那么使用工厂模式是没有意义的。这反而会使整个解决方案更加复杂。

#### 6.观察者模式

观察者模式在一个对象需要同时与其他对象进行通信的情况下非常有用。假设由于一些变化，您需要在许多组件之间同步更新。

观察者模式可以防止状态间不必要的事件推拉。它通过修改数据的当前状态来通知相关模块:

```
function Observer() {
    this.observerContainer = [];
}

Observer.prototype.subscribe = function (element) {
    this.observerContainer.push(element);
}

/**
 * removes an element from the container
 */
Observer.prototype.unsubscribe = function (element) {

    const elementIndex = this.observerContainer.indexOf(element);
    if (elementIndex > -1) {
        this.observerContainer.splice(elementIndex, 1);
    }
}

/**
 * notifies all the element added to the container by calling 
 * each subscribed components added to the container
 */
Observer.prototype.notifyAll = function (element) {
    this.observerContainer.forEach(function (observerElement) {
        observerElement(element);
    });
}
```

observer pattern

让我们看一个例子来演示观察者模式:

用户在输入字段中键入一个随机数，该数字被修改并显示在两个不同的文档中。

这也可以在 AngularJS 中使用双向绑定来实现，它利用了罩下的`Observer pattern`:

```
    <body style="text-align: center; margin-top: 40px;">

        <input
            type="number"
            class="number-input"
        >
        <br>
        <br>

        <small>Number multiplied by 2</small>
        <p class="document-one">0</p>
        <button id="subscribe-one">Subscribe</button>
        <button id="unsubscribe-one">UnSubscribe</button>

        <br>
        <br>
        <small>Number multiplied by 4</small>
        <p class="document-two">0</p>
        <button id="subscribe-two">Subscribe</button>
        <button id="unsubscribe-two">UnSubscribe</button>
    </body>
```

观察者视图

让我们与我们创建的元素交互来演示观察者模式。

可观察容器(`observerContainer`)处理`events`如何被存储、检索和移除。

用户可以添加一个随机数的视图，该随机数显示在`documentOne`(显示的数字乘以 2)和`documentTwo`(显示的数字乘以 4)上。

此外，还有`subscribe`和`unsubscribe`按钮来修改每个文件的状态，以显示修改后的随机数。

第一组两个按钮(subscribe 和 unsubscribe)更新文档上的显示(`<p></p>`)，通过点击 unsubscribe 按钮从`observerContainer`中删除更新操作来改变显示。

同样的操作也适用于接下来的两个按钮(订阅和取消订阅):

```
[http://observers.js](http://observers.js)

     /**
         * get the reference of the views (input, display One and display Two using class name)
         * */
        const input = document.querySelector('number-input');
        const documentOne = document.querySelector('.document-one'); 
        const documentTwo = document.querySelector('.document-two');

        /**
         * operation to manipulate user input for document one 
         * */
        const updateDocumentOne = function(text) {
            documentOne.textContent = parseInt(text) * 2;
        }

        /**
         * operation to manipulate user input for document two
         * */
        const updateDocumentTwo = function(text) {
            documentTwo.textContent = parseInt(text) * 4;
        }

        const observable = new Observer();
        observable.subscribe(updateDocumentOne);
        observable.subscribe(updateDocumentTwo);

        document.querySelector('.number-input').addEventListener('keyup', function(event){
            observable.notifyAll(event.target.value);
        });

        /**
         * modify subscriptions upon a click of a button
         * */
        document.getElementById('subscribe-one').addEventListener('click', function() {
           observable.subscribe(updateDocumentOne);
        });
        document.getElementById('unsubscribe-one').addEventListener('click', function() {
           observable.unsubscribe(updateDocumentOne);
        });
        document.getElementById('subscribe-two').addEventListener('click', function() {
           observable.subscribe(updateDocumentTwo);
        });
        document.getElementById('unsubscribe-two').addEventListener('click', function() {
           observable.unsubscribe(updateDocumentTwo);
        }); 
```

这是一个演示，展示了我们如何使用观察者模式。源代码也可以在[这里](https://codepen.io/seunboy/pen/ePxVQJ)找到。

![](img/e5a6892aabb5c995f6edde11bf7d734e.png)

Observer pattern demonstration

如果您需要更新一个简单项目的视图，您可以考虑使用观察者模式，而不是使用框架。

观察者模式的一个缺点是难以测试不同的场景。

#### 结论

强烈建议 Javascript 开发人员使用设计模式。它们确保项目易于维护，并防止不必要的工作。

为了进一步阅读，我强烈推荐由*艾迪·奥斯马尼所著的[学习 Javascript 设计模式](https://addyosmani.com/resources/essentialjsdesignpatterns/book/)。*

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.

* * *