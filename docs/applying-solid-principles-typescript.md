# 将坚实的原则应用于打字稿

> 原文：<https://blog.logrocket.com/applying-solid-principles-typescript/>

很久以前定义的坚实原则旨在提高面向对象设计的可读性、适应性、可扩展性和可维护性。面向对象类设计的五个坚实原则促进了可理解的、经过测试的软件的开发，许多开发人员可以在任何时间和地点使用这些软件。

我们把这个想法归功于罗伯特·c·马丁(Robert c . Martin)2000 年的作品 *[设计原则和设计模式](http://staff.cs.utu.fi/~jounsmed/doos_06/material/DesignPrinciplesAndPatterns.pdf)* 。他还因畅销书 *[清洁代码](https://www.oreilly.com/library/view/clean-code-a/9780136083238/)* 和 *[清洁架构](https://www.oreilly.com/library/view/clean-architecture-a/9780134494272/)* 而闻名。缩写 SOLID 后来由 Michael Feathers 创造，用来说明 Bob 叔叔提出的观点。

在本文中，我们将回顾每一个坚实的原则，并提供打字稿示例来说明和理解它们。我们开始吧！

## 单一责任原则

根据单一责任原则，一个类应该只负责一个活动，并且只有一个改变的原因。这个规则也包括模块和函数。

让我们考虑下面的例子:

```
class Student {
  public createStudentAccount(){
    // some logic
  }

  public calculateStudentGrade(){
    // some logic
  }

  public generateStudentData(){
    // some logic
  }
}

```

单一职责的想法在上面的`Student`类中被打破。因此，我们应该将`Student`类划分为不同的责任状态。根据 SOLID 的说法，责任的观念是改变的理由。

为了确定改变的原因，我们需要研究我们的程序的职责是什么。我们可能会因为三个不同的原因改变`Student`类:

*   `createStudentAccount`计算逻辑改变
*   计算学生成绩的逻辑发生了变化
*   生成和报告学生数据的格式发生变化

单一责任原则强调了上述三个方面赋予了`Student`类三种不同的责任:

```
class StudentAccount {
  public createStudentAccount(){
    // some logic
  }
}

class StudentGrade {
  public calculateStudentGrade(){
    // some logic
  }
}

class StudentData {
  public generateStudentData(){
    // some logic
  }
}

```

现在我们已经划分了类，每个类只有一个职责，一个责任，并且只需要做一个改变。现在，我们的代码更容易解释和理解。

## o:开闭原则

根据开闭原则，软件实体应该对扩展开放，但对修改关闭。这种方法背后的基本概念是，我们应该能够添加新的功能，而不需要更改现有的代码:

```
class Triangle {
  public base: number;
  public height: number;
  constructor(base: number, height: number) {
    this.base = base;
    this.height = height;
  }
}

class Rectangle {
  public width: number;
  public height: number;
  constructor(width: number, height: number) {
    this.width = width;
    this.height = height;
  }
}

```

假设我们想开发一个函数来计算形状集合的面积。在我们当前的设计中，它可能如下所示:

```
function computeAreasOfShapes(
  shapes: Array<Rectangle | Triangle>
) {
  return shapes.reduce(
    (computedArea, shape) => {
      if (shape instanceof Rectangle) {
        return computedArea + shape.width * shape.height;
      }
      if (shape instanceof Triangle) {
        return computedArea + shape.base * shape.height * 0.5 ;
      }
    },
    0
  );
}

```

这种方法的问题是，每次我们添加一个新的形状，我们都必须改变我们的`computeAreasOfShapes`函数，从而违反了开闭概念。为了演示这一点，让我们添加另一个名为`Circle`的形状:

```
class Circle {
  public radius: number;
  constructor(radius: number) {
    this.radius = radius;
  }
}

```

与开闭原则相反，`computeAreasOfShapes`函数将不得不变成一个圆形实例:

```
function computeAreasOfShapes(
  shapes: Array<Rectangle | Triangle | Circle>
) {
  return shapes.reduce(
    (calculatedArea, shape) => {
      if (shape instanceof Rectangle) {
        return computedArea + shape.width * shape.height;
      }
      if (shape instanceof Triangle) {
        return computedArea + shape.base * shape.height * 0.5 ;
      }
      if (shape instanceof Circle) {
        return computedArea + shape.radius * Math.PI;
      }
    },
    0
  );
}

```

我们可以通过强制所有形状都有一个返回面积的方法来解决这个问题:

```
interface ShapeAreaInterface {
  getArea(): number;
}

```

现在，`shapes`类必须实现我们为`ShapeArea`定义的`interface`来调用它的`getArea()`方法:

```
class Triangle implements ShapeAreaInterface {
  public base: number;
  public height: number;
  constructor(base: number, height: number) {
    this.base = base;
    this.height = height;
  }

  public getArea() {
    return this.base * this.height * 0.5
  }
}

class Rectangle implements ShapeAreaInterface {
  public width: number;
  public height: number;
  constructor(width: number, height: number) {
    this.width = width;
    this.height = height;
  }
  public getArea() {
    return this.width * this.height;
  }
}

```

既然我们确定所有的形状都有`getArea`方法，我们可以进一步利用它。从我们的`computeAreasOfShapes`函数中，让我们如下更新我们的代码:

```
function computeAreasOfShapes(
  shapes: Shape[]
) {
  return shapes.reduce(
    (computedArea, shape) => {
      return computedArea + shape.getArea();
    },
    0
  );
}

```

现在，我们不必在每次添加新形状时都改变我们的`computeAreasOfShapes`函数。您可以用`Circle` shape 类来测试这一点。我们对扩展开放，但对修改关闭。

## 李:里斯科夫替代原理

Barbara Liskov 提出的 Liskov 替代原则有助于确保修改我们系统的一个方面不会对其他元素产生负面影响。

根据 Liskov 替换原则，子类应该可以与其基类互换。这表明，假设类`B`是类`A`的子类，我们应该能够将类`B`的对象呈现给任何需要类型`A`的对象的方法，而不用担心该方法可能产生奇怪的结果。

为了更清楚，我们将把这个想法分解成不同的部分。让我们以长方形和正方形为例:

```
class Rectangle {
    public setWidth(width) {
        this.width = width;
    }
    public setHeight(height) {
        this.height = height;
    }
    public getArea() {
        return this.width * this.height;
    }
}

```

我们有一个简单的`Rectangle`类，函数`getArea`返回矩形的面积。

现在，我们将选择专门为正方形创建另一个类。众所周知，正方形是一种宽度和高度相等的矩形:

```
class Square extends Rectangle {

    setWidth(width) {
        this.width = width;
        this.height = width;
    }

    setHeight(height) {
        this.width = height;
        this.height = height;
    }
}

```

上面的代码运行时没有错误，如下所示:

```
let rectangle = new Rectangle();
rectangle.setWidth(100);
rectangle.setHeight(50);
console.log(rectangle.getArea()); // 5000

```

但是，当我们用一个父类实例替换它的一个子类时，我们会遇到问题:

```
let square = new Square();
square.setWidth(100);
square.setHeight(50);

```

鉴于`setWidth(100)`被期望将宽度和高度都设置为`100`，你应该有`10,000`。然而，这将返回`2500`作为`setHeight(50)`的结果，打破了利斯科夫替代原则。

要解决这个问题，您应该为所有形状创建一个通用类，其中包含您希望子类的对象能够访问的所有通用方法。然后，您将为每个独特的方法创建一个特定的类，比如矩形或正方形。

## I:界面分离原理

接口分离原则鼓励更小、更有针对性的接口。根据这个概念，多个特定于客户端的接口比单个通用接口更可取。

为了了解理解和使用这个简单的理论有多容易，让我们考虑以下场景:

```
interface ShapeInterface {
    calculateArea();
    calculateVolume();
}

```

当一个类实现这个接口时，必须定义所有的方法，即使你不使用它们，或者它们不适用于那个类:

```
class Square implements ShapeInterface {
    calculateArea(){
        // some logic
    }
    calculateVolume(){
        // some logic
    }  
}

class Cylinder implements ShapeInterface {
    calculateArea(){
        // some logic
    }
    calculateVolume(){
        // some logic
    }    
}

```

从上面的例子中，你会发现你不能确定正方形或长方形的体积。你必须声明每一个方法，甚至是你不会使用或需要的方法，因为类实现了接口。

相反，我们可以设置更紧凑的接口，有时称为角色接口:

```
interface AreaInterface {
    calculateArea();
}

interface VolumeInterface {
    calculateVolume();
}

```

我们可以通过改变我们思考接口的方式来防止接口膨胀并简化程序维护:

```
class Square implements AreaInterface {
    calculateArea(){
        // some logic
    }
}

class Cylinder implements AreaInterface, VolumeInterface {
    calculateArea(){
        // some logic
    }
    calculateVolume(){
        // some logic
    }    
}

```

## 依赖倒置原则

根据依赖倒置的概念，高级模块不应该依赖于低级模块。相反，两者都应该依赖于抽象。

Bob 叔叔在他 2000 年的文章*设计原则和设计模式*中总结了这个规则:

> 如果开闭原则(OCP)陈述了面向对象(OO)体系结构的目标，那么 DIP 陈述了主要的机制。

简单地说，无论是高层模块还是低层模块都将依赖于抽象，而不是高层模块依赖于低层模块。设计中的每一个依赖都应该指向一个抽象类或接口。任何依赖都不应该针对具体的类。

让我们构建一个插图来进一步探讨这一原则。考虑一个订单服务。在这个例子中，我们将使用`OrderService`类，它在数据库中记录订单。低级类`MySQLDatabase`作为`OrderService`类的直接依赖。

在这种情况下，我们违反了依赖倒置原则。将来，如果我们要切换我们正在使用的数据库，我们需要修改`OrderService`类:

```
class OrderService {
  database: MySQLDatabase;

  public create(order: Order): void {
    this.database.create(order)
  }

  public update(order: Order): void {
    this.database.update
  }
}

class MySQLDatabase {
  public create(order: Order) {
    // create and insert to database
  }

  public update(order: Order) {
    // update database
  }
}

```

通过设计一个接口并使`OrderService`类依赖于它，我们可以通过反转依赖关系来改善这种情况。现在，高级类依赖于抽象，而不是依赖于低级类。

我们创建一个接口来帮助抽象我们的服务，如下所示:

```
interface Database {
  create(order: Order): void;
  update(order: Order): void;
}

class OrderService {
  database: Database;

  public create(order: Order): void {
    this.database.create(order);
  }

  public update(order: Order): void {
    this.database.update(order);
  }
}

class MySQLDatabase implements Database {
  public create(order: Order) {
    // create and insert to database
  }

  public update(order: Order) {
    // update database
  }
}

```

现在，在不改变`OrderService`类的情况下，我们可以添加和扩展新的数据库服务。

## 结论

在本文中，我们已经通过使用 TypeScript 的实际例子探索了每一个坚实的原理。坚实的原则允许我们提高代码的可读性和可维护性，它们也使得在不损害应用程序其他方面的情况下增加代码库变得更加简单。

编写代码时，您应该记住这些准则。除了面向对象编程(OOP)之外，一定要理解它们的含义、作用以及为什么需要它们。编码快乐！

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.