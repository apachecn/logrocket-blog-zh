# Swift 教程中的 for-in 循环

> 原文：<https://blog.logrocket.com/for-in-loops-swift-tutorial/>

通俗地说，当一个东西循环运行时，就是一遍又一遍地重复同样的事情。例如，一个循环将遍历博客文章的数量，并将它们显示在主页上。

Swift 中有不同类型的控制流循环。这些是`for-in`、`forEach`、`while`和`repeat-while`循环。在本文中，我们将对 Swift 中的`for-in`循环进行基本概述。然后，我们将使用不同数据类型的例子和用例来演示如何使用它们。

我们将重点关注以下内容:

*   `for-in`循环的语法
*   数组
*   范围和步幅
*   字典
*   枚举

为了跟上进度，您应该对 Swift 语言有基本的了解。

## `for-in`循环的语法

语法以单词`for`开始，后面是循环中作为常量创建的特定元素。我们在它后面跟随单词`in`，最后是你想要循环的序列:

```
for element in elements {
    // do something with the element
}

```

例如，我们有一个股票列表，每个股票都包括其在特定日期的价格:

```
struct Stock {
    var name: String
    var price: Double
    var date = Date()
}

```

我们想遍历数组并打印每只股票的数据。循环的语法如下所示:

```
// MARK: - EXAMPLE
func printDetails(for stocks: [Stock]) {
    for stock in stocks {
        print(stock.name)
        print(stock.price)
        print(stock.date)
    }
}

// MARK: - USAGE
let stocks = [Stock(name: "Banana", price: 125),
              Stock(name: "TapeBook", price: 320),
              Stock(name: "Ramalon", price: 3200)]

printDetails(for: stocks)

// MARK: - OUTPUT
Banana
125.0
2021-05-21 22:40:42 +0000
TapeBook
320.0
2021-05-21 22:40:42 +0000
Ramalon
3200.0
2021-05-21 22:40:42 +0000

```

了解了基本语法之后，让我们继续循环基本数据结构:`Array`！

## 数组

从 Swift 官方文档来看，“一个数组在一个有序列表中存储相同类型的值。同一值可以在一个数组中的不同位置出现多次。

我们使用`for-in`循环迭代存储的值，然后访问数组中的每个值。

### 基本示例

假设有一个应用程序，我们正在跟踪一个慢跑的用户。在每个地点，我们都想追踪他们的速度。因此，在应用程序中，我们会收到一系列位置:

```
let locations: [CLLocation] = []

```

我们遍历数组，对于每个位置，我们打印该特定位置的速度:

```
for location in locations {
    print("The speed at location (\(location.coordinate.latitude), \(location.coordinate.longitude) is \(location.speed)")
}

```

再举一个例子，我们创建一个 10×10 的二维数组，并打印每个点的值:

```
var board: [[Int]] = Array(repeating: Array(repeating: 0, count: 10), count: 10)

for row in board {
    for number in row {
        // prints 0, hundred times
        print(number)
    }
}

```

### 使用`where`子句

有些情况下，我们希望将序列限制为仅匹配特定条件的元素。在这个场景中，我们使用`where`关键字。

在待办事项应用中，我们需要所有目标中已完成目标的子集。假设一个这样的模型:

```
struct Goal: Identifiable, Hashable {
    var id = UUID()
    var name: String = "Goal Name"
    var date = Date()
    var goalCompleted: Bool = false
}

```

我们的应用程序有一个用于`Goal`的数组。我们希望遍历数组，只访问那些已经完成的目标:

```
// MARK: - EXAMPLE
func getCompletedGoals(for goals: [Goal]) {
    for goal in goals where goal.goalCompleted == true {
        /// Access to completed goals only.
        print(goal)
    }
}

// MARK: - USAGE
let goals = [Goal(name: "Learn basic syntax of for-in loops", goalCompleted: true),
             Goal(name: "Read about for-in loops and dictionaries"),
             Goal(name: "Read about for-in loops and enums")]

getCompletedGoals(for: goals)

// MARK: - OUTPUT
Goal(id: B7B148D6-853B-486A-8407-CD03A904B348, name: "Learn basic syntax of for-in loops", date: 2021-05-21 22:50:38 +0000, goalCompleted: true)

```

### 使用`enumerated()`

为了同时访问元素的每个索引，我们可以使用实例方法`enumerated()`。它返回一系列包含索引和元素值的对。以前面的例子为例，如果我们想列出数组中位置的索引，我们可以这样写:

```
for (index, location) in locations.enumerated() {
    print("The speed at location (\(location.coordinate.latitude), \(location.coordinate.longitude) is \(location.speed)")

    print("The index for this location is \(index)") 
}

```

### 使用`indices`

如果我们只想要数组中元素的索引，我们可以使用`indices`。它以升序表示数组中的有效索引。它从 0 循环到数组中的最后一个元素，即`array.count`:

```
for index in array.indices {
    // Access the index
}

```

使用我们之前创建的二维数组，我们遍历每个点，并给它分配一个随机整数值:

```
// MARK: - EXAMPLE
func updateValues(of board: inout [[Int]]) {
    for rowIndex in board.indices {
        for columnIndex in board[0].indices {
            board\[rowIndex\][columnIndex] = Int.random(in: 0..<10)
        }

        print(board[rowIndex])
    }
}

// MARK: - USAGE
var board: [[Int]] = Array(repeating: Array(repeating: 0, count: 10), count: 10)

updateValues(of: &board)

// MARK: - OUTPUT
[9, 4, 1, 7, 5, 2, 6, 4, 7, 4]
[1, 0, 1, 0, 5, 4, 5, 6, 7, 9]
[4, 7, 6, 3, 8, 9, 3, 5, 9, 5]
[8, 0, 9, 9, 6, 1, 2, 0, 2, 7]
[3, 7, 4, 1, 3, 4, 9, 9, 5, 6]
[5, 2, 5, 1, 8, 1, 8, 0, 0, 1]
[0, 4, 3, 4, 0, 6, 1, 8, 7, 5]
[7, 7, 7, 9, 1, 3, 6, 4, 0, 1]
[9, 5, 6, 5, 3, 8, 0, 1, 3, 4]
[1, 7, 7, 3, 1, 0, 7, 4, 5, 6]

```

### 使用可选模式

在序列包含可选值的情况下，我们可以使用`for case let`过滤掉 nil 值，仅对非 nil 元素执行循环。

从之前的待办事项应用的例子来看，让我们假设我们的一些目标没有价值。`getCompletedGoals(for goals:)`现在接受可选`Goal`的数组:

```
// MARK: - EXAMPLE
func getCompletedGoals(for goals: [Goal?]) {
    for case let goal? in goals where goal.goalCompleted == false {
        /// Access to completed goals only.
        print(goal)
    }
}

// MARK: - USAGE
let goals: [Goal?] = [Goal(name: "Learn something new!", goalCompleted: true),
                      Goal(name: "Read about for-in loops and dictionaries"),
                      nil,
                      Goal(name: "Read about for-in loops and enums"),
                      nil]

getCompletedGoals(for: goals)

// MARK: - OUTPUT
Goal(id: F6CB6D77-9047-4155-99F9-24F6D178AC2B, name: "Read about for-in loops and dictionaries", date: 2021-05-21 23:04:58 +0000, goalCompleted: false)
Goal(id: 822CB7C6-301C-47CE-AFEE-4B17A10EE5DC, name: "Read about for-in loops and enums", date: 2021-05-21 23:04:58 +0000, goalCompleted: false)

```

## 范围和步幅

我们也可以使用`for-in`循环来遍历硬编码的数值范围。它们可以分为两部分:

*   使用封闭范围运算符(`…`)
*   使用半开范围运算符(`..<`)

### 使用封闭范围运算符

闭合范围运算符创建一个包含两个结束元素的范围。使用该运算符的一个基本示例是打印 10 个数字。这里，1 和 10 也将被打印:

```
for number in 1...10 {
    print("The number is \(number)")
}

```

FizzBuzz 是一个简单的编程练习，我们可以用它来进行`for-in`循环。提示如下:

> 编写一个程序，打印从 1 到 n 的数字。3 的倍数打印“嘶嘶”，而不是数字，5 的倍数打印“嗡嗡”。对于同时是 3 和 5 的倍数的数字，打印“FizzBuzz”而不是数字。

我们使用封闭范围操作符创建一个`ClosedRange<Int>`常数，通过数字 1 到 *n* 进行循环。然后，我们再次遍历`mapping`中的元组，并检查元组中的每个元素。如果这个数字是 3 的倍数，我们把`Fizz`附加到`string`上。

当我们检查`mapping`中的每个元素时，如果它也是 5 的倍数，我们将`Buzz`附加到字符串，结果是`FizzBuzz`:

```
// MARK: - EXAMPLE
func fizzBuzz(for lastNumber: Int) {
    var result = [String]()
    let mapping = [(number: 3, value: "Fizz"), (number: 5, value: "Buzz")]

    for number in 1...lastNumber {
        var string = ""

        for tuple in mapping {
            if number % tuple.number == 0 {
                string += tuple.value
            }
        }

        if string == "" {
            string += "\(number)"
        }

        print(result)
    }
    return result
}

// MARK: - USAGE
fizzBuzz(for: 10)

// MARK: - OUTPUT
["1", "2", "Fizz", "4", "Buzz", "Fizz", "7", "8", "Fizz", "Buzz"]

```

### 使用半开范围运算符

半开范围运算符创建一个不包括最后一个元素的范围。使用该运算符的一个基本示例是访问数组的索引:

```
for index in 0..<array.count {
    // Access the index
}

```

### 使用`stride`

如果您想跳过循环中特定数量的元素，您可以使用`stride`。我们也可以用它在一个循环中后退，从最后一个元素开始，到第一个元素。

回到我们用随机值创建了一个 10×10 大小的二维矩阵的例子，我们想在第一行打印每个替换元素:

```
// MARK: - EXAMPLE
func printFirstRow(for board: [[Int]]) {
    for rowIndex in stride(from: board.count - 1, through: 0, by: -2) {
        print(board\[rowIndex\][0])
    }
}

// MARK: - USAGE
printFirstRow(for: board)

// MARK: - OUTPUT
7
4
4
4
8

```

现在，我们希望在第一列中打印每个替代元素，但顺序相反:

```
// MARK: - EXAMPLE
func printFirstColumn(for board: [[Int]]) {
    for rowIndex in stride(from: board.count - 1, through: 0, by: -2) {
        print(board\[rowIndex\][0])
    }
}

// MARK: - USAGE
printFirstColumn(for: board)

// MARK: - OUTPUT
8
6
0
6
5

```

## 字典

我们也可以使用`for-in`循环遍历`Dictionary`，尽管结果将是无序的。语法类似于数组，每个元素都有自己的键和值:

```
// MARK: - EXAMPLE
func printDictionary(for numbers: [Int: Int]) {
    for number in numbers {
        // number is a Dictionary<Int, Int>.Element
        print("The value for key \(number.key) is \(number.value)")
    }
}

// MARK: - USAGE
let numbers: [Int: Int] = [1: 2, 2: 3, 3: 4]

printDictionary(for: numbers)

// MARK: - OUTPUT
The value for key 1 is 2
The value for key 2 is 3
The value for key 3 is 4

```

我们也可以明确地使用我们自己的关键字来代替:

```
// MARK: - EXAMPLE
func printStockPrices(for stocks: [String: Int]) {
    for (name, price) in stocks {
        print("\(name) is currently valued at $\(price).")
    }
}

// MARK: - USAGE
let stocks: [String: Int] = ["Banana": 125, "TapeBook": 320, "Ramalon": 3200]

printStockPrices(for: stocks)

// MARK: - OUTPUT
Banana is currently valued at $125.
Ramalon is currently valued at $3200.
TapeBook is currently valued at $320.

```

我们也可以在字典中使用`where`:

```
// MARK: - EXAMPLE
func printStockPrices(for stocks: [String: Int]) {
    for (name, price) in stocks where name == "Banana" {
        print("\(name) is currently valued at $\(price).")
    }
}

// MARK: - USAGE
let stocks: [String: Int] = ["Banana": 125, "TapeBook": 320, "Ramalon": 3200]

printStockPrices(for: stocks)

// MARK: - OUTPUT
Banana is currently valued at $125.

```

如果你想要这本字典中最高的价格，你可以使用`sorted(by:)`对字典进行排序:

```
// MARK: - EXAMPLE
func printStockPrices(for stocks: [String: Int]) {
    for (name, price) in stocks.sorted(by: { $0.value > $1.value }) {
        print("\(name) is currently valued at $\(price).")
    }
}

// MARK: - USAGE
let stocks: [String: Int] = ["Banana": 125, "TapeBook": 320, "Ramalon": 3200]

printStockPrices(for: stocks)

// MARK: - OUTPUT
Ramalon is currently valued at $3200.
TapeBook is currently valued at $320.
Banana is currently valued at $125.

```

### 使用`KeyValuePairs`

如前所述，`Dictionary`没有定义顺序。如果想要有序的键值对，可以使用`KeyValuePairs`。这在您愿意牺牲快速、恒定的查找时间来获得线性时间的情况下非常有用:

```
// MARK: - EXAMPLE
func printStockPrices(for stocks: KeyValuePairs<String, Int>) {
    for (name, price) in stocks {
        print("\(name) is currently valued at $\(price).")
    }
}

// MARK: - USAGE
let stocks: KeyValuePairs = ["Banana": 125, "TapeBook": 320, "Ramalon": 3200]

printStockPrices(for: stocks)

// MARK: - OUTPUT
Banana is currently valued at $125.
TapeBook is currently valued at $320.
Ramalon is currently valued at $3200.

```

## 枚举

通过遵循名为`CaseIterable`的特定协议，您甚至可以迭代 Swift 中的 [enum。此类型提供了其所有值的集合。在我们的例子中，它给出了`Enum`中的所有情况。为了访问它们，我们使用了`allCases`属性。](https://blog.logrocket.com/swift-enums-an-overview-with-examples/)

再举一个例子，我们正在开发一款超休闲游戏。我们需要在主屏幕上设置不同的游戏模式。我们将创建一个`enum`并遍历它来访问模式名和图像名:

```
enum GameModes: String {
    case arcade
    case challenge
    case casual
    case timed
}

extension GameModes {
    var name: String {
        self.rawValue.capitalized
    }

    var image: String {
        switch self {
            case .arcade: return "🕹"
            case .casual: return "🎮"
            case .challenge: return "🎖"
            case .timed: return "⏳"
        }
    }
}

extension GameModes: CaseIterable {}

// Usage
for mode in GameModes.allCases {
    let gameOptionsView = GameOptionsView()
    gameOptionsStackView.addArrangedSubview(gameOptionsView)

    gameOptionsView.set(name: mode.name, image: mode.image)
}

```

## 结论

循环是帮助您在 Swift 变得更好的基础知识。在本文中，我们用不同的例子和用例概述了`for-in`循环。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)