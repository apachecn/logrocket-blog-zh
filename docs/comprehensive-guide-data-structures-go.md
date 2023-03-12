# Go - LogRocket 博客中的数据结构综合指南

> 原文：<https://blog.logrocket.com/comprehensive-guide-data-structures-go/>

你可能听说过数据结构，并在其他编程语言中使用过，但你知道如何在 Go 中使用它们吗？

作为业内发展最快的编程语言之一，开发人员了解如何利用这一重要特性来创建可伸缩的、可靠的应用程序是非常重要的。

在本文中，我们将介绍 Go 中的数据结构，并深入探讨数组、切片、映射和结构等概念。此外，我还将提供多个代码示例。

## 先决条件

要跟随并理解本教程，您需要以下内容:

## 数组

数组是特定类型数据的集合。它在单个变量中存储多个值，其中每个元素都有一个索引来引用自己。

当您需要在一个位置保存多项内容时，数组就派上了用场，比如参加活动的人员列表或班级中学生的年龄。

### 创建数组

要创建一个数组，我们需要定义它的名称、长度和将要存储的值的类型:

```
var studentsAge [10]int

```

在这篇代码博客中，我们创建了一个名为`studentsAge`的数组，它最多可以存储十个`int`值。

### 从文本创建数组

您可以从文字创建一个数组，这意味着您在创建时就给它们赋值。

让我们看看如何使用它:

```
// creating an array and assigning values later
var studentsAge [10]int
studentsAge = [10]int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

// creating and assigning values to an array
var studentsAge = [10]int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

// creating and assigning values to an array without var keyword
studentsAge := [10]int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

```

### 创建数组的数组

您可以创建一个数组，其中每个元素都是一个单独的数组(嵌套数组)，如下所示:

```
// creating a nested array
nestedArray := \[3\][5]int{
  {1, 2, 3, 4, 5},
  {6, 7, 8, 9, 10},
  {11, 12, 13, 14, 15},
}
fmt.Println(nestedArray) // \[[1 2 3 4 5\] [6 7 8 9 10] [11 12 13 14 15]]

```

### 访问数组中的值

数组中的每个元素都有一个索引，可以用来访问和修改它的值。数组的索引始终是一个整数，从零开始计数:

```
// creating an array of integers
studentsAge := [10]int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

// accessing array values with their indexes
fmt.Println(studentsAge[0]) // 1
fmt.Println(studentsAge[1]) // 2
fmt.Println(studentsAge[9]) // 10

// using a for loop to access an array
for i := 0; i < 10; i++ {
  fmt.Println(studentsAge[i])
}

// using range to access an array
for index, value := range studentsAge {
  fmt.Println(index, value)
}

```

### 修改数组中的值

数组是可变的数据结构，因此可以在创建后修改它们的值:

```
// creating an array of integers
studentsAge := [10]int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

// modifying array values with their indexes
studentsAge[0] = 5
studentsAge[4] = 15
studentsAge[7] = 10

fmt.Println(studentsAge) // [5 2 3 4 15 6 7 10 9 10]

```

### 获取数组的长度

Go 提供了一个`len`函数，可以用来获得数组的长度。

让我们看看如何使用它:

```
// creating and getting the length of an array with a length of 10
var arrayOfIntegers [10]int
fmt.Println(len(arrayOfIntegers)) // 10

// creating and getting the length of an array with a length of 7
var arrayOfStrings [7]string
fmt.Println(len(arrayOfStrings)) // 7

// creating and getting the length of an array with a length of 20
var arrayOfBooleans [20]bool
fmt.Println(len(arrayOfBooleans)) // 20

```

请注意，改变数组的长度是不可能的，因为它在创建过程中就成为了类型的一部分。

## 部分

像数组一样，切片允许您在单个变量中存储多个相同类型的值，并通过索引来访问它们。切片和数组的主要区别在于切片的长度是动态的，而数组的长度是固定的。

### 创建切片

要创建一个切片，我们需要定义它的名称和我们将存储的值的类型:

```
var sliceOfIntegers []int

```

我们创建了一个名为`sliceOfIntegers`的切片，用于存储`int`值。

### 从数组创建切片

在其原始形式中，切片是数组的提取部分。为了从数组中创建切片，我们需要向 Go 提供要提取的部分。

让我们看看如何做到这一点:

```
// creating an array of integers
studentsAge := [10]int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

// creating slices from arrays
fiveStudents := studentsAge[0:5]
fmt.Println(fiveStudents) // [1 2 3 4 5]
threeStudents := studentsAge[3:6]
fmt.Println(threeStudents) // [4 5 6]

```

切片格式要求您提供索引来启动和停止 Go 切片提取。如果省略任何参数，Go 将使用零作为起点(数组的开头),如果省略结尾，则使用数组的长度:

```
// creating an array of integers
studentsAge := [10]int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

// creating slices from arrays
fmt.Println(studentsAge[:4]) // [1 2 3 4]
fmt.Println(studentsAge[6:]) // [7 8 9 10]
fmt.Println(studentsAge[:])  // [1 2 3 4 5 6 7 8 9 10]

```

也可以使用与数组相同的格式从其他切片创建切片:

```
// creating an array of integers
studentsAge := [10]int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

// creating slices from arrays
firstSlice := studentsAge[:8]
fmt.Println(firstSlice) // [1 2 3 4 5 6 7 8]

// creating slices from slices
secondSlice := firstSlice[1:5]
fmt.Println(secondSlice) // [2 3 4 5]

```

### 使用`make`创建切片

Go 提供了一个`make`函数，您可以通过指定切片的长度来创建切片。创建后，Go 将使用其类型的[零值](https://tour.golang.org/basics/12)填充切片:

```
// creating slices with make specifying length
sliceOfIntegers := make([]int, 5)  // [0 0 0 0 0]
sliceOfBooleans := make([]bool, 3) // [false false false]

```

每个切片都有长度和容量。切片的长度是切片中的元素数，而容量是基础数组中的元素数，从切片中的第一个元素开始计数。

`make`函数允许我们创建一个具有指定容量的切片。下面是用法:

```
// creating a slice with a length of 5 and a capacity of 10
sliceOfStrings := make([]string, 5, 10)

```

### 从文字创建切片

您可以从文字创建切片，这意味着您在创建时就为它们赋值:

```
// creating a slice and assigning values later
var tasksRemaining []string
tasksRemaining = []string{"task 1", "task 2", "task 3"}

// creating and assigning values to a slice
var tasksRemaining = []string{"task 1", "task 2", "task 3"}

// creating and assigning values to a slice without var keyword
tasksRemaining := []string{"task 1", "task 2", "task 3"}

```

### 创建切片的切片

您可以创建一个切片，其中每个元素都是一个单独的切片(嵌套切片)，如下所示:

```
// creating a nested slice
nestedSlice := [][]int{
  {1},
  {2, 3},
  {4, 5, 6},
  {7, 8, 9, 10},
}
fmt.Println(nestedSlice) // \[[1\] [2 3] \[4 5 6\] [7 8 9 10]]

```

### 访问和修改切片中的值

切片中的每个元素都有一个索引，您可以使用它来访问和修改它的值。切片的索引始终是一个整数，并从零开始计数:

```
// creating a slice from literals
sliceOfIntegers := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

// accessing slice values with their indexes
firstInteger := sliceOfIntegers[0]  // 1
secondInteger := sliceOfIntegers[1] // 2
lastInteger := sliceOfIntegers[9]   // 10

// using a for loop to access a slice
for i := 0; i < 10; i++ {
  fmt.Println(sliceOfIntegers[i])
}

// using range to access a slice
for index, value := range sliceOfIntegers {
  fmt.Println(index, value)
}

```

切片是可变的数据结构，因此可以在创建后修改它们的值:

```
// creating a slice from literals
sliceOfIntegers := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

sliceOfIntegers[0] = 3
sliceOfIntegers[5] = 2
sliceOfIntegers[9] = -10

fmt.Println(sliceOfIntegers) // [3 2 3 4 5 2 7 8 9 -10]

```

### 获取切片的长度和容量

Go 提供了一个`len`函数，可以用来获得切片的长度:

```
// creating and getting the length of a slice
sliceOfIntegers := make([]int, 10)
fmt.Println(len(sliceOfIntegers)) // 10

```

还有一个`cap`函数，您可以使用它来获得一个片的容量:

```
// creating and getting the capacity of a slice
sliceOfIntegers := make([]int, 10, 15)
fmt.Println(cap(sliceOfIntegers)) // 15

```

### 向切片添加元素

Go 提供了一个`append`函数，您可以使用它向现有切片添加元素:

```
// creating a slice from literals
sliceOfIntegers := []int{1, 2, 3}

// using append to add a single value to the slice
sliceOfIntegers = append(sliceOfIntegers, 4)
fmt.Println(sliceOfIntegers) // [1 2 3 4]

// using append to add multiple values to the slice
sliceOfIntegers = append(sliceOfIntegers, 5, 6, 7)
fmt.Println(sliceOfIntegers) // [1 2 3 4 5 6 7]

// using append to add a slice to a slice
anotherSlice := []int{8, 9, 10}
sliceOfIntegers = append(sliceOfIntegers, anotherSlice...)
fmt.Println(sliceOfIntegers) // [1 2 3 4 5 6 7 8 9 10]

```

`append`函数是[变量](https://gobyexample.com/variadic-functions)，接受可变数量的参数。这就是为什么我们可以通过用逗号分隔来传递多个值给它。

## 地图

映射是一种将键分配给其值(键-值对)的数据结构。它类似于 JavaScript 中的对象、Java 中的 HashMap 和 Python 中的字典。一张地图的零值是`nil`。

### 创建地图

要创建映射，我们需要定义它的名称以及它的键和值的数据类型:

```
var studentsAge map[string]int

```

在这里，我们创建了一个名为`studentsAges`的映射，将它的键存储为`strings`，值存储为`ints`。

### 用`make`初始化和创建地图

Go 提供了一个`make`函数，您可以使用它来初始化您创建的地图:

```
// creating a string -> int map
var studentsAge map[string]int
studentsAge = make(map[string]int)

```

地图创建后，必须用`make`初始化，然后才能赋值。

也可以用`make`创建地图。这样做不需要您在使用之前再次初始化它:

```
// creating a string -> int map
studentsAge := make(map[string]int)

```

### 从文字创建地图

从文字创建映射意味着在创建时分配它们的键和值。让我们看看如何使用它:

```
// creating a map from literals
studentsAge := map[string]int{
  "solomon": 19,
  "john":    20,
  "janet":   15,
  "daniel":  16,
  "mary":    18,
}

fmt.Println(studentsAge) // map[daniel:16 janet:15 john:20 mary:18 solomon:19]

```

### 创建地图的地图

您可以创建一个映射，其中每个键引用另一个映射(嵌套映射)，如下所示:

```
// creating nested maps
studentResults := map[string]map[string]int{
  "solomon": {"maths": 80, "english": 70},
  "mary":    {"maths": 74, "english": 90},
}

fmt.Println(studentResults) // map[mary:map[english:90 maths:74] solomon:map[english:70 maths:80]]
fmt.Println(studentResults["solomon"]) // map[english:70 maths:80]
fmt.Println(studentResults\["solomon"\]["maths"]) // 80

```

在这个代码块中，我们创建了一个带有`string`键的映射，每个值是另一个带有`string`键和`int`值的映射。

### 在地图中添加和访问值

要将值添加到映射中，您需要将键指定为您希望的值:

```
// creating a string -> int map
studentsAge := make(map[string]int)

// adding values to the map
studentsAge["solomon"] = 19
studentsAge["john"] = 20
studentsAge["janet"] = 15

fmt.Println(studentsAge) // map[janet:15 john:20 solomon:19]

```

要访问映射中的值，您需要引用分配的键:

```
// creating a map from literals
studentsAge := map[string]int{
  "solomon": 19,
  "john":    20,
  "janet":   15,
  "daniel":  16,
  "mary":    18,
}

// accessing values in the map
fmt.Println(studentsAge["solomon"]) // 19
fmt.Println(studentsAge["mary"])    // 18
fmt.Println(studentsAge["daniel"])  // 16

```

### 检查映射中的键是否存在

有时您想检查一个键是否已经存在于映射中。Go 允许您通过将[二值赋值](https://gobyexample.com/multiple-return-values)给贴图值来实现这一点:

```
// creating a map from literals
studentsAge := map[string]int{
  "solomon": 19,
  "john":    20,
  "janet":   15,
  "daniel":  16,
  "mary":    18,
}

// two-value assignment to get an existing key
element, ok := studentsAge["solomon"]
fmt.Println(element, ok) // 19 true

// two-value assignment to get a non-existing key
element, ok = studentsAge["joel"]
fmt.Println(element, ok) // 0 false

```

当使用双值赋值来访问映射中的值时，返回的第一个值是映射中的键值，而第二个变量是一个布尔值，指示该键是否存在。

如果键不存在，第一个值被赋给 map 值类型的`zero value`。

### 更新地图中的值

要更新映射中的值，您需要引用现有的键并为其分配新值:

```
// creating a map from literals
studentsAge := map[string]int{
  "solomon": 19,
  "john":    20,
  "janet":   15,
  "daniel":  16,
  "mary":    18,
}

// updating values in the map
studentsAge["solomon"] = 20
fmt.Println(studentsAge["solomon"]) // 20

// updating values in the map
studentsAge["mary"] = 25
fmt.Println(studentsAge["mary"]) // 25

```

### 从地图中删除键

Go 提供了一个`delete`函数，您可以使用它从现有地图中移除关键点:

```
// creating a map from literals
studentsAge := map[string]int{
  "solomon": 19,
  "john":    20,
  "janet":   15,
  "daniel":  16,
  "mary":    18,
}
fmt.Println(studentsAge) // map[daniel:16 janet:15 john:20 mary:18 solomon:19]

// deleting keys from the studentsAge map
delete(studentsAge, "solomon")
delete(studentsAge, "daniel")

fmt.Println(studentsAge) // map[janet:15 john:20 mary:18]

```

## 结构

一个[结构](https://blog.logrocket.com/exploring-structs-and-interfaces-in-go/)是定义了数据类型的数据字段的集合。结构类似于 OOP 语言中的类，因为它们允许开发人员创建自定义的数据类型，在他们的系统中保存和传递复杂的数据结构。

### 创建结构

为了创建一个结构，我们将在 Go 中使用`type`关键字，然后用它们各自的数据类型定义它的名称和数据字段:

```
type Rectangle struct {
  length  float64
  breadth float64
}

```

我们用类型为`float64`的`length`和`breadth`数据字段创建了一个名为`Rectangle`的结构。

结构本身就是类型，所以当用`type`关键字创建它们时，它们必须直接在包声明下创建，而不是在像`main`这样的函数内创建。

### 创建结构实例

要创建一个实例，我们需要定义它的名称、键的数据类型和值的数据类型:

```
// creating a struct instance with var
var myRectangle Rectangle

// creating an empty struct instance
myRectangle := Rectangle{}

```

### 从文本创建结构实例

您可以从文本创建 struct 实例，这意味着您在创建时将它们的字段值赋给它们:

```
// creating a struct instance specifying values
myRectangle := Rectangle{10, 5}

// creating a struct instance specifying fields and values
myRectangle := Rectangle{length: 10, breadth: 5}

// you can also omit struct fields during their instantiation
myRectangle := Rectangle{breadth: 10}

```

如果在实例化过程中省略了 struct 字段，它将默认为该类型的零值。

### 创建结构的数组和切片

因为结构是数据类型，所以可以创建数组和它们的切片，如下所示:

```
arrayOfRectangles := [5]Rectangle{
  {10, 5},
  {15, 10},
  {20, 15},
  {25, 20},
  {30, 25},
}
fmt.Println(arrayOfRectangles) // [{10 5} {15 10} {20 15} {25 20} {30 25}]

sliceOfRectangles := []Rectangle{
  {10, 5},
  {15, 10},
  {20, 15},
  {25, 20},
  {30, 25},
}
fmt.Println(sliceOfRectangles) // [{10 5} {15 10} {20 15} {25 20} {30 25}]

```

### 创建指针结构实例

Go 还允许创建结构实例，这些实例是指向结构定义的[指针](https://tour.golang.org/moretypes/1):

```
// creating a pointer struct instance
myRectangle := &Rectangle{length: 10, breadth: 5}
fmt.Println(myRectangle, *myRectangle) // &{10 5} {10 5}

```

也可以用`new`创建一个指针结构实例。让我们看看如何:

```
// creating a struct instance with new
myRectangle := new(Rectangle)
fmt.Println(myRectangle, *myRectangle) // &{0 0} {0 0}

```

### 访问和更新结构字段值

若要访问结构中的字段，需要引用字段名:

```
// creating a struct instance specifying fields and values
myRectangle := Rectangle{length: 10, breadth: 5}

// accessing the values in struct fields
fmt.Println(myRectangle.length)  // 10
fmt.Println(myRectangle.breadth) // 5

```

若要更新结构字段中的值，需要引用字段名并为其分配一个新值:

```
// creating a struct instance specifying fields and values
myRectangle := Rectangle{length: 10, breadth: 5}
fmt.Println(myRectangle) // {10 5}

myRectangle.length = 20
myRectangle.breadth = 8
fmt.Println(myRectangle) // {20 8}

```

### 在结构中嵌套结构

Go 允许您将结构用作另一个结构(嵌套结构)中的数据字段:

```
// creating a nested struct
type address struct {
  houseNumber int
  streetName  string
  city        string
  state       string
  country     string
}

type Person struct {
  firstName   string
  lastName    string
  homeAddress address
}

```

在创建`Person`结构的新实例时，必须创建`Person`和`address`结构的实例，如下所示:

```
// creating an instance of a nested struct
person := Person{
  firstName: "Solomon",
  lastName:  "Ghost",
  homeAddress: address{
    houseNumber: 10,
    streetName:  "solomon ghost street",
    city:        "solomon city",
    state:       "solomon state",
    country:     "solomon country",
  },
}

fmt.Println(person.firstName)           // Solomon
fmt.Println(person.homeAddress.country) // solomon country

```

### 匿名 struts

匿名结构允许你在函数中创建结构并随时使用它们。让我们看看如何使用它:

```
// creating a struct anonymously
circle := struct {
  radius float64
  color  string
}{
  radius: 10.6,
  color:  "green",
}

fmt.Println(circle)       // {10.6 green}
fmt.Println(circle.color) // green

```

### 创建结构方法

结构方法是附加到结构的函数。它们只能通过结构实例调用，并自动接收结构实例作为参数。

要创建一个 struct 方法，我们需要定义它将附加到的结构、它的名称、参数(如果有的话)和返回类型(如果有的话)。让我们来看看它的实际应用:

```
type Rectangle struct {
  length  float64
  breadth float64
}

func (r Rectangle) area() float64 {
  return r.length * r.breadth
}

```

这里，我们为我们的`Rectangle`结构创建了一个`area`方法，它使用字段值来计算并返回形状的面积作为`float64`。我们可以继续在代码中使用它，如下所示:

```
// creating a struct instance
myRectangle := Rectangle{10, 5}

// calling the Rectangle area method
fmt.Println(myRectangle.area()) // 50

```

### 用方法更新结构字段值

结构将其实例的副本传递给方法，因此，如果您要更新方法中字段的值，这些更改将不会反映出来。

但是，在某些情况下，您可能希望从方法中更新字段值。Go 允许方法接收指针引用而不是值本身:

```
func (r *Rectangle) setLength(length float64) {
  r.length = length
}

func (r *Rectangle) setBreadth(breadth float64) {
  r.breadth = breadth
}

```

我们为我们的`Rectangle`结构创建了一个`setLength`和`setBreadth`方法，用我们传递给它的参数更新字段变量。我们可以继续在代码中使用它，如下所示:

```
// creating a struct instance
myRectangle := Rectangle{10, 5}
fmt.Println(myRectangle) // {10 5}

// calling the modifier methods on our instance
myRectangle.setLength(20)
myRectangle.setBreadth(10)
fmt.Println(myRectangle) // {20 10}

```

## 结论

在本文中，我们学习了 Go 中的各种数据结构，如数组、切片、映射和结构。我们还展示了多个代码示例、用例以及函数。

我希望这是一个有用的指南，可以经常是一个复杂的话题。有了这篇文章作为参考指南，你就可以自信地为你的用例使用正确的数据结构，并[创建快速、高性能的应用](https://blog.logrocket.com/functional-programming-in-go/)。

如果你愿意，可以前往 Go 的[之旅，获得更多关于 Go 数据结构的参考和例子。](https://tour.golang.org/list)

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)