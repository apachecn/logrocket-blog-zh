# 使用 Rust collections - LogRocket 博客

> 原文：<https://blog.logrocket.com/working-rust-collections/>

Rust 提供了多种集合类型，以提供高效存储和检索数据的方法。就性能和容量而言，每种类型都有不同的特征。集合允许您按顺序存储多个值，但与数组或元组不同，集合是在堆上分配的，这意味着集合的大小可以随着程序的运行而增长或收缩。

在本文中，我们将概述 Rust 集合，特别是最常见的 Rust 集合类型:向量、字符串和哈希映射。我们还将讨论哪些集合最适合不同的任务，以及如何有效地使用每个集合。

*向前跳转:*

## 什么是铁锈系列？

集合是由 Rust 的标准库提供的数据结构。这些结构以序列或组的形式存储信息。虽然大多数其他 Rust 数据类型只包含一个特定的值，但集合可能一次包含多个值。

铁锈收藏可分为四类:

*   顺序:`Vec`、`VecDeque`、`LinkedList`
*   地图:`HashMap`，`BTreeMap`
*   设置:`HashSet`，`BTreeSet`
*   杂项:`BinaryHeap`

## 您应该使用哪种铁锈系列？

为给定任务选择最佳集合的艺术至关重要，因为每个集合在使用、性能和容量方面都有不同的特征。大多数集合都提供了一个`capacity`方法来查询集合的实际容量或空间分配。当集合有合适的`capacity`来确定添加到其中的元素的大小时，效率最高。

`Vec` (vector)和`HashMap`集合涵盖了一般数据存储和处理的大多数用例。vector 是将项目存储在一起的默认选择。当我们需要一个可调整大小的数组、一个堆分配的数组或者一个特定顺序的元素序列时，我们可以使用向量。

`HashMap`最适合我们希望将随机键与随机值、没有附加功能的地图或缓存相关联的用例。

当我们想要一个优先级队列，或者当我们希望每次只处理最重要或最大的元素时,(二叉树)很有用。

Rust 中有许多不同集合的其他用例，这篇[指南](https://doc.rust-lang.org/std/collections/index.html#when-should-you-use-which-collection)可以作为了解更多信息的参考。

Rust 的标准库包括许多用于存储值的丰富集合。让我们仔细看看三个最常用的 Rust 集合:向量、字符串和哈希映射。

## 向量

向量是相同类型的项目的全局连续堆栈，允许我们存储多个彼此相邻的值，就像数组一样。事实上，向量是可调整大小的数组，这意味着它们可以在运行时动态地增加或减少大小。

注意，vectors 就像堆中存储的任何其他类型一样，当它们超出范围或者不再被程序使用时，就会被丢弃。

### 创建向量

`Vec::new()`函数用于创建一个空矢量。

在下面的代码中，我们通过调用 vector 类型上的`new`函数，将`v`变量声明并初始化为 vector 类型。这将创建一个空向量。因为我们还没有用任何值填充向量，Rust 不能推断出我们希望向量包含什么类型的值。因此，我们必须使用 64 位整数、`i64`通用语法来手动注释该类型。

```
fn main() {
  let v : Vec<i64> = Vec::new();
}

```

为了给向量变量添加元素，我们使用`mut`关键字使向量可变。然后，我们调用`push()`方法将元素推入向量。

```
fn main() {
  let mut v : Vec<i64> = Vec::new();
  v.push(1);
  v.push(2);
}

```

### 访问向量中的元素

有两种方法可以访问 vector 中的元素。我们可以直接引用 vector 中的一个索引，也可以使用`get()`方法。

在下面的代码片段中，我们创建了一个等于`&v`的变量`second`。这指定了对向量的引用，然后我们在方括号中指定所需的索引。我们传递方括号中的`1`来获取向量中的第二个元素，因为就像数组一样，向量是零索引的。

```
fn main() {
  let v: Vec<i64> = vec![1, 2, 3, 4];
  let second = &v[1];
  println!(“the second number is {}”, second);
}

```

在上面的例子中，如果我们传递的索引超过了向量的长度，我们会得到一个错误。

就处理`index-out-of-bound`错误而言，`get()`方法是一种更安全的方法:

```
fn main() { 
  let v: Vec<i64> = vec![1, 2, 3, 4]
  match v.get(index: 12) {
    Some(second: &i64) => println!(“The second number is {}”, second),
    None => println!(“Out of bound index”),
  }
}

```

在上面的`Some`案例中，我们将值存储在一个名为`second`的变量中，并将其打印到标准输出中。在`None`案例中，我们打印了`"Out of bound index"`消息。只有当我们传递一个大于向量长度的索引时，才会触发`None`事件。

### 迭代向量中的元素

我们可以使用`for in`循环来迭代向量中的元素。

```
fn main(){
  let v: Vec<i64> = vec![1, 2, 3, 4];
  for i in &v {
    println!("{}", i);
  }
}

```

### 将枚举差异存储在向量中

如本文前面所述，向量只存储相同类型的值。然而，我们可以通过使用`enum`变量在一个向量中存储不同类型的元素。

```
...
enum MyVector {
  Int(i32),
  Float(f64),
  Text(String),
};

let row = vec![
  MyVector::Int(20),
  MyVector::Text(String::from("This is a string")),
  MyVector::Float(15.12),
];
...

```

## 用线串

在 Rust 中，字符串被存储为 UTF 8 编码字节的集合，基本上只是 vector，`Vec`，type 的一个次要类型。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

为了让计算机打印出正确的字符，程序需要解释这些值。这就是编码的用武之地。UTF-8 是 Unicode 的[变宽字符编码，支持从一个字节到四个字节的字符串。](https://techterms.com/definition/unicode)

Rust 核心语言中只有一种字符串类型；字符串切片。这通常以其借来的形式出现。

### 创建字符串

我们可以使用`new`函数创建一个空字符串。我们还可以使用字符串片段`&str`来创建一个字符串，然后使用`to_string()`方法将字符串片段转换成一个拥有的字符串。

```
fn main() {
  let mut myString: String = string::new();
  let myString2: &str = “This is a string”;
  let myString3: String = myString2.to_string();
}

```

### 串联字符串

方法可以用来将元素添加到字符串的末尾。

这里我们构建了一个包含`"John"`的字符串，并使用`push_str`方法将`"Doe"`附加到该字符串:

```
fn main() {
  let mut myString = String::from(“John”);
  myString.push_str(string: “Doe”);
}

```

现在，当我们运行代码时，`myString`将返回“John Doe”。我们还可以使用`+`操作符连接字符串。然而，使用这种技术，保持变量之间的类型一致性是很重要的。

在下面的代码中，我们将`s1`的所有权移到了`s3`中。然后，我们将`s2`的角色副本连接到`s3`的结尾。这节省了一点内存，并且实现比复制两个字符串然后用副本创建一个新字符串更有效。因为我们已经转移了`s1`的所有权，我们不能再使用`s1`元素。

```
fn main() {
  let s1: string = String::from(“John”);
  let s2: string = String::(“Doe”);
  let s3: string = s1 + &s2;
  println!("{}", s3);
}
// "John Doe"

```

我们也可以使用`format`宏连接字符串。与`+`操作符不同，`format`宏不转移字符串的所有权。

```
fn main() {
  let s1: string = String::from(“John”);
  let s2: string = String::(“Doe”);
  let s3: string = format!("{}{}", s1,s2);
}

```

## 散列表

hash map 是一个关联容器，它允许我们将数据存储在名为 entries 的键值对中。在哈希映射中，任何两个条目都不能有相同的键。

### 创建新的哈希映射

为了创建一个新的哈希映射，我们需要将这个哈希映射纳入标准库的范围。同样，就像向量和字符串一样，我们可以使用`new`函数创建一个空的哈希映射，然后使用`insert`函数将值插入到哈希映射中。

```
use std::collections::HashMap;

fn main() {
  let john = String::from("John");
  let peter = String::from("Peter");

  let mut person: HashMap<String, i32> = HashMap::new();

  person.insert(john, 40);
  person.insert(peter, 20); 
}

```

在上面的代码中，在`insert()`函数中传递`john`和`peter`会将字符串的所有权转移到`HashMap`中。

### 访问哈希表中的值

我们可以使用`get()`方法访问哈希表中的值。`get()`方法引用一个键并返回一个可选值。我们得到了`Option`枚举，因为我们不能保证返回一个值。

```
...
  let person_name = String::from("John");
  let new_person: &i32 = if let Some(age) = person.get(&person_name) {
      age
  } else {
      &0i32
  };

    println!("{}", new_person);
...

```

## 结论

Rust 集合是允许我们按顺序存储和检索数据的数据结构。集合存储在堆上，这意味着我们可以在运行时增加或减少其中的数据。

在本文中，我们研究了 Rust 集合，并分享了一些特定集合的示例和用例。我们讨论了常用的集合，如向量、字符串和哈希映射。向量和散列映射涵盖了一般数据存储和处理的大多数用例。

Rust 集合对于一些基本操作非常有用，例如使用哈希映射进行缓存，使用 BTreeMap 按键对值进行映射和排序，以及使用数组存储和处理值。Rust 集合也可以作为本地存储的一种形式，用于在负载不重的程序和进程中存储和检索数据。

要了解更多关于铁锈系列的信息，请参考[官方文件](https://doc.rust-lang.org/std/collections/index.html)。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。