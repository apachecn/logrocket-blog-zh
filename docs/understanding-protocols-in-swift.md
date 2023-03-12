# 理解 Swift 中的协议

> 原文：<https://blog.logrocket.com/understanding-protocols-in-swift/>

当苹果在 2015 年全球开发者大会(WWDC)上宣布发布第一个面向协议的语言 Swift 时，这表明了他们现有的面向对象编程 Objective-C 的转变

但是因为 Objective-C 使用了隐式共享继承，所以创建新对象变得很慢，而且新对象通常具有不需要的功能。在这种情况下，如果一个类层次结构很复杂，那么维护它会导致一些问题，比如效率低下和竞争条件。

借助 Swift 的协议范式，开发人员现在可以在没有继承的情况下构建对象，对象可以由现有代码使用，单个类可以在不需要继承的情况下与多个协议一起工作。

在本帖中，我们将讨论 Swift 的协议模式及其优势。面向对象编程的知识有助于理解这篇帖子的内容。

## 什么是协议，它们在 Swift 中是如何工作的？

通常，协议:

*   是类或结构遵循的蓝图
*   是不相关对象依赖的通信契约
*   定义方法和值

为了理解 Swift 中的协议如何工作，让我们假设我们正在构建应用软件，并且必须对满足应用的要求进行建模。我们可以从超类开始，通过继承来塑造关系，或者从协议开始，通过实现来塑造关系。

如果我们想为我们的应用程序构建一个工资汇款系统，并且我们有一个`Employee`类，那么使用如下所示的协议:

```
protocol EmployeeProtocol {
    var emplname: String { get }
    var description: String { get }
    var salary: Int { get set }
    func paySalary(salary: Int) -> String
}

```

通常，如果我们使用`get`，我们可以使它成为`const`、`var`、`let`或计算属性。然而，使用属性声明`get set`对`salary`属性进行限制`var salary: Int { get set }`到`var`。

如果我们想写一个遵循这个协议的类，比如`Employee`类，我们有如下:

```
class Employee: EmployeeProtocol {
    var emplname: String = "Victor Jonah"

    var description: String = "Software Engineer"

    var salary: Int = 5000

    func paySalary(salary: Int) -> String {
        return "Salary disbursed to {emplname}"
    }
}

```

总之，协议允许我们对方法、属性和函数进行分组。然而，这些协议只能符合类、[枚举和结构。](https://blog.logrocket.com/swift-enums-an-overview-with-examples/)

一个对象可以符合多个协议，但必须用逗号分隔:

```
struct Player: MainPlayer, EnemyPlayer {
   // code definition 
}

```

同样，如果一个类有一个超类，我们可以在超类名后定义任何协议:

```
class TheClass: ItsSuperclass, FirstProtocol, SecondProtocol {
  // class definition goes here
}

```

我们可以将`enum`与我们的协议一起用于计算属性，但是它们不适用于存储属性:

```
enum Employer: EmployerProtocol {
  var name: String { 
    return "Alex"
  }

  var description: String {
    return "CEO"
  }

  var salary: Int {
    get {
      return 
    }
  }
}

```

如果协议不符合类、结构或枚举，Swift 也会在编译时抛出错误。

## Swift 移动协议示例

让我们通过一个移动示例来看一个更常见的协议用例:

```
protocol Mobile {
    var name: String { get }
    var iEMICode: Int { get }
    var sIMCard: String { get }
    var processor: String { get }
    var internalMemory: Int { get}
    var isSingleSIM: Bool { get }

    mutating func GetIEMICode() -> String

    func SendMessage() -> String

    func Dial() -> String

    func Receive() -> String

    init(name: String)
}

struct Apple: Mobile {
    var name: String = "Apple"

    init(name: String) {
        self.name = name
    }
    var iEMICode: Int  = 3244332

    var sIMCard: String = "Vodaphone"

    var processor: String = "Snapdragon"

    var internalMemory: Int = 213424

    var isSingleSIM: Bool = true

    mutating func GetIEMICode() -> String {
        return "IEMEICode"
    }

    func SendMessage() -> String {
       return "Message sent"
    }

    func Dial() -> String {
        return "Dialed"
    }

    func Receive() -> String {
        return "Receiving call"
    }  
}

struct Samsung: Mobile {
    var name: String = "Samsung"

    init(name: String) {
        self.name = name
    }

    var iEMICode: Int = 3243433

    var sIMCard: String = "TMobile"

    var processor: String = "Snapdragon"

    var internalMemory: Int = 324432

    var isSingleSIM: Bool = false

    func GetIEMICode() -> String {
        return "IEMEICode"
    }

    func SendMessage() -> String {
        return "Message sent"
    }

    func Dial() -> String {
        return "Dialed"
    }

    func Receive() -> String {
       return "Receiving call"
    }
}

```

注意，当我们有一个对象必须改变它的一个属性时，第 9 行的关键字`mutating`起作用。我们必须指定`GetIEMICode()`是我们协议中的一个变异方法。在我们的结构中，我们还必须指定关键字`mutating`，但不是在类中。

## Swift 中协议的优势

从上面的例子中，我们可以看到为什么协议是有用的，为什么 Swift 使用面向协议的范例。使用协议的优势体现在以下几个方面:

### 代码清晰度

命名协议有助于更好地理解它们的实例。在我们的第一个例子中，我们创建了一个符合`Employee`类的`EmployeeProtocol`，展示了协议如何为类、枚举或结构提供意义。

正如戴夫·亚伯拉罕在 2015 年 WWDC 上所说，“不要从课程开始，从协议开始。”

### 复用性

有了协议扩展，我们可以在它们所符合的类、枚举或结构中为我们的方法提供一个默认实现。我们可以在下面的代码中看到这一点:

```
protocol Person {
    var name: String { get }
    var age: Int { get }
    var gender: String { get }

    func speak()
}

extension Person {
    func speak() {
        print("Hello, this works!")
    }
}

class Male: Person {
    var name: String = ""

    var age: Int = 23

    var gender: String = "Male"
}

struct Female: Person {
    var name: String

    var age: Int

    var gender: String
}

```

通过在第 9 行使用`extension`关键字创建默认功能，我们不需要在我们的类或结构中重复它。

### 阶级分离

协议还消除了类、枚举和结构相互依赖的需要，因为它们不使用继承。

## 结论

总之，Swift 中的协议提供了不相关对象之间的通信，其中我们定义了在类、枚举和结构中观察到的方法和变量。由于 Swift 采用了面向协议的范式，我们可以在定义类、结构或枚举之前对我们的系统进行建模，从而提高流程的效率。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)