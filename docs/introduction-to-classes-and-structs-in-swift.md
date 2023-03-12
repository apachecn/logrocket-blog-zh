# Swift - LogRocket 博客中的类和结构介绍

> 原文：<https://blog.logrocket.com/introduction-to-classes-and-structs-in-swift/>

结构和类是灵活构造的构建块，帮助开发人员决定如何在程序中存储数据和建模行为。Swift 中的类通常被视为创建对象的蓝图。

由于能够通过定义属性和创建方法添加功能来存储值，类和结构的共享特性通常可以在 Swift 中互换使用。然而，它们都有差异和独特性，这给开发人员带来了在他们认为最好的地方使用它们的灵活性。

我们将回顾类和结构之间的相似和不同之处，以及它们在代码中是如何工作的。

## Swift 类和结构的异同概述

Swift 中类和结构之间的相似性提供了可互换性和灵活性。例如，如前所述，类和结构都可以定义属性来存储值，为存储数据和在代码中建模行为提供了不同的选项。

其他相似之处包括:

*   使用关键字`init()`设置初始状态值的初始化器
*   定义[下标](https://docs.swift.org/swift-book/LanguageGuide/Subscripts.html)的能力，提供对序列值、集合或列表的快速访问
*   使用`[extension](https://blog.logrocket.com/understanding-protocols-in-swift/)`关键字扩展类或结构的能力
*   [协议](https://blog.logrocket.com/understanding-protocols-in-swift/)一致性

然而，类具有额外的功能，使它们不同于结构。类可以从另一个类继承所有属性、行为和方法，也可以向继承的内容添加额外的功能

另一个区别是类型转换，它使开发人员能够在运行时检查和解释类实例类型。

## Swift 中的类和结构语法相似性

在 Swift 中定义类和结构的语法也是相似的。要在 Swift 中定义一个类或结构，使用关键字`class`或`struct`，后跟带有花括号的类或结构的名称。

请注意，确保 Swift 中的类和结构名称遵循 PascalCase 命名惯例。

在我们的例子中，让我们创建一个名为`User`的类和结构:

```
class User {
  ...
}

```

```
struct User {
 ...
}

```

我们现在可以向代码中添加类和结构定义。

### 类别定义

当我们创建类并添加类定义时，我们可以提供默认值，使定义可选，或者创建我们自己的初始化器。

在 Swift 中，每个类都必须有一个初始化器。如果一个类有子类，初始化器向编译器保证子类继承或实现相同的初始化器。这使我们能够定义类定义。

例如，我们可以在下面的代码中创建一个自定义的初始化器，将`firstName`定义为`String`来初始化并给`firstName`赋值:

```
class User {
  var  firstName: String
  var  lastName: String
  var  gender: String

    init(firstName: String, lastName: String, gender: String) {
        self.firstName = firstName
        self.lastName = lastName
        self.gender = gender
    }
}

```

注意在`self.firstName = firstName`中，`self.firstName`指的是我们在`var firstName: String`类中定义的`firstName`。`self`是指`User`的当前实例。

当`User`类的属性有默认值时，`User`类自动实现一个默认的初始化器，创建一个新的实例，其属性设置为默认值。

对于具有默认值的类定义，我们可以添加以下内容:

```
class User {
  var  firstName = "Ejiro"
  var  lastName = "Asiuwhu"
  var  gender = "male"
}

```

如果我们不确定是希望一个变量保存一个值还是稍后赋值，我们可以让变量可选。对于具有可选值的类定义，我们可以添加以下内容:

```
class NewUser {
    var  firstName: String?
    var  lastName: String?
    var  age: Int?
}

```

### 结构定义

在 Swift 中定义结构只有一种方法:

```
struct User {
  var  firstName = "Ejiro"
  var  lastName = "Asiuwhu"
  var  gender = "male"
}

```

## 创建 Swift 类实例

Swift 中的类实例被称为对象。要使用我们之前创建的`User`类，我们必须创建一个实例:

```
class User {
// class definition
  var  firstName: String
  var  lastName: String
  var  gender: String
  var  age: Int

// creating our initilizer
    init(firstName: String, lastName: String, gender: String, age: Int) {
        self.firstName = firstName
        self.lastName = lastName
        self.gender = gender
        self.age = age
    }
}

// creating a class instance
let person:User = User(firstName:  "Ejiro", lastName: "Asiuwhu", gender: "male", age: 45)

```

值得注意的是，Swift 类实例是可变对象，而 struct 的实例是不可变值。因为类是一种引用类型，所以对分配给类实例的变量的任何更改都会影响原始类，使其可变。

另一方面，因为结构是值类型，所以对分配给结构实例的变量的任何更改都会影响原始结构，使其值不可变。

### 访问类实例的属性

当我们需要访问一个类的数据时，我们可以使用点符号。例如，要访问我们在前面的示例中创建的`User`类的`age`属性，我们可以添加以下内容:

```
// creating a class instance
let person:User = User(firstName:  "Ejiro", lastName: "Asiuwhu", gender: "male", age: 45)
person.age // expected output: 45

```

除了访问数据，我们还可以使用点符号语法来设置变量属性的值，从而允许我们添加额外的数据:

```
// creating a class instance
let person:User = User(firstName:  "Ejiro", lastName: "Asiuwhu", gender: "male", age: 45)
person.age = 78 // expected output: 78

```

## 在类和结构中创建 Swift 方法

Swift 类和结构都可以定义方法来提供功能。通过使用`func`关键字在我们的`User`类中创建一个方法，我们可以添加`getDetails()`来访问类似`firstName`、`lastName`、`age`和`gender`的信息:

```
class User {
// class definition
  var  firstName: String
  var  lastName: String
  var  gender: String
  var  age: Int
// creating our initilizer
    init(firstName: String, lastName: String, gender: String, age: Int) {
        self.firstName = firstName
        self.lastName = lastName
        self.gender = gender
        self.age = age
    }

// methods in Swift classes
func getDetails() {
  print("\(firstName) \(lastName) is a \(age) year old \(gender)")
  }
// creating a class instance
let person:User = User(firstName:  "Ejiro", lastName: "Asiuwhu", gender: "male", age: 45)

// the me
person.getDetails() // expected output: Ejiro Asiuwhu is a 45 year old male

```

注意新创建的`getDetails()`方法现在在我们的类实例中是如何可用的。我们可以在`let person:User = User`实例上使用点符号来访问该方法，后跟调用`func`的圆括号。

类似地，我们也可以用点符号在结构中定义方法来提供功能:

```
struct User {
     var  firstName: String
     var  lastName: String
     var  gender: String
     var  age: Int
    func  getDetails() {
        print("\(firstName) \(lastName) is a \(age) year old \(gender)")
  }
}
let person:User = User(firstName:  "Ejiro", lastName: "Asiuwhu", gender: "male", age: 45)
person.getDetails() // expected output: Ejiro Asiuwhu is a 45 year old male

```

## 迅速的阶级差异

### 类继承

继承是类中区别于结构的一个基本特征。在编写 Swift 时，当决定是否使用类或结构时，理解继承如何工作是很重要的。

子类化允许我们从一个类继承到另一个类，这意味着一个类(指定为子类)从另一个类(指定为超类)访问所有数据，例如属性和方法。

要开始子类化，我们必须定义我们的超类，然后在现有超类的基础上建立一个新的子类。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

子类化也不会限制我们，因为不管我们继承什么，我们都可以给子类增加更多的功能和属性。

为了理解 Swift 类中的继承是如何工作的，让我们重用我们的`User`类作为超类，并创建一个名为`Admin`的子类来继承`User`属性:

```
class User {
// class definition
  var  firstName: String
  var  lastName: String
  var  gender: String
  var  age: Int
// creating our initilizer
    init(firstName: String, lastName: String, gender: String, age: Int) {
        self.firstName = firstName
        self.lastName = lastName
        self.gender = gender
        self.age = age
    }
}
class Admin: User {
 var authorize: Bool?
}
var admin = Admin(firstName:  "Ejiro", lastName: "Asiuwhu", gender: "male", age: 45)
admin.authorize = true;

print(admin.authorize) // expected output: true

```

注意我们是如何通过添加除了从`User`超类继承的属性之外的更多属性来细化`Admin`子类的。

### 值和引用

区分结构和类的一个基本特征是，结构是值类型，而类是引用类型。

当创建结构并将其赋给变量时，会复制值，因为它是值类型。通过将`point2`结构的值设置为`point1`结构的值，我们创建了每个变量的单独副本。

因此，当`point1`的值改变时，它不会影响`point2`的值:

```
struct Coordinates {
    var lat: Double
    var lng: Double
}

var point1:Coordinates = Coordinates(lat: 5.519, lng: 5.7599)
// here, we are setting the values of point2 to be the value of point1
var point2:Coordinates = point1

point2.lat = 6.45
point2.lng = 8.211

print(point2) // expected output: Coordinates(lat: 6.45, lng: 8.211)
print(point1) // expected output: Coordinates(lat: 5.519, lng: 5.7599)

```

但是当类被赋给一个变量时，它引用现有的实例而不是复制它:

```
class User {
  var  firstName: String
  var  lastName: String
  var  gender: String
  var  age: Int

    init(firstName: String, lastName: String, gender: String, age: Int) {
        self.firstName = firstName
        self.lastName = lastName
        self.gender = gender
        self.age = age
    }
}

var user1:User  = User(firstName:  "Ejiro", lastName: "Asiuwhu", gender: "male", age: 29)
// user2 now holds the same value as user1
var user2:User  = user1

user1.age = 30

print(user1.age) // expected output: 30
print(user2.age) // expected output: 30

```

请注意这里值类型和引用类型的区别:当引用类型中的值发生变化时，所有被引用的变量也会发生变化。

正如我们在上面的类中看到的，`user1.age`和`user2.age`现在是相同的值。这是因为`user1`不仅仅是`user2`的复制品，更确切地说`user1`是`user2`。

当我们存储一个类时，我们将它的值存储在内存中，而指向一个类的变量只保存对该类的引用。

当我们为我们的类添加`var user2:User = user1`时，我们告诉`user2`引用`user1`，使两个变量中的所有数据同步。如果我们改变其中一个，另一个也会改变。

## Swift 中何时使用类与结构

[苹果官方文档](https://developer.apple.com/documentation/swift/choosing_between_structures_and_classes)很大程度上推荐用户默认使用 structs。这主要是因为结构更安全，没有错误，尤其是在多线程环境中。如果结构相对较小且可复制，那么它们也是更好的选择，因为复制结构比多次引用同一个实例更安全。

在结构和类之间进行选择时，重要的是要记住关键的区别:

*   类是引用类型，结构是值类型
*   如果不需要类继承，结构会更快，内存效率更高
*   对具有独立状态的对象的唯一副本使用结构
*   当处理一些相对简单的数据值时，请使用结构
*   使用类访问 Objective-C 运行时
*   使用类来控制对象的标识
*   当不需要控制对象的标识时，使用结构

在 Swift 中工作时，类和结构提供了灵活性。虽然它们通常可以互换，但它们略有不同的功能为开发人员提供了他们需要的选择。

请随意发表评论，让我知道你对这篇文章的看法。你也可以在 [Twitter](https://twitter.com/ejirocodes) 和 [GitHub](https://github.com/ejirocodes) 找到我。感谢您的阅读！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)