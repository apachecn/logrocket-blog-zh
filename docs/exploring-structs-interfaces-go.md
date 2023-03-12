# 探索 Go - LogRocket 博客中的结构和接口

> 原文：<https://blog.logrocket.com/exploring-structs-interfaces-go/>

***编者按:*** *这篇文章于 2022 年 1 月 14 日审阅，以更新过时的信息，并在 Golang 中添加了“将* *an* *接口转换为**a**struct**这一节。*

 *Go 是一种类型安全、静态类型、编译的编程语言。类型系统的类型由类型名和类型声明表示，旨在防止出现未经检查的运行时类型错误。

在 Go 中，标识符有几种内置类型，也称为预声明类型。它们包括布尔型、字符串型、数字型(`float32`、`float64`、`int`、`int8`、`int16`、`int32`、`complex`)以及其他许多类型。此外，还有复合类型，由预先声明的类型组成。

复合类型主要是使用类型文本构造的。它们包括数组、切片、接口、结构、函数、映射类型等等。在本文中，我们将关注 Go 中的`struct`和`interface`类型。

在本教程中，我们将涵盖以下内容:

## 教程先决条件

为了轻松地学习本教程，对围棋有一个基本的了解是很重要的。建议在你的机器上安装[Go](https://golang.org/doc/install)[install](https://golang.org/doc/install)[ed](https://golang.org/doc/install)来运行和编译代码。

然而，为了简单起见，也为了本文的目的，我们将使用[Go Playground](https://play.golang.org/)，一个运行 Go 代码的在线 IDE。

## 什么是围棋框架？

Go 是一种现代、快速、编译型语言(即从源代码生成的机器代码)。由于支持开箱即用的并发性，它也适用于与低级计算机网络和系统编程相关的领域。

为了探究它的一些特性，让我们继续学习如何设置我们的开发环境。为此，[根据您的操作系统安装 Go 二进制文件](https://golang.org/doc/install)。

Go 工作区文件夹包含`bin`、`pkg`和`src`目录。在早期的 Go 版本(1.13 之前的版本)中，源代码被写在`src`目录中，该目录包含 Go 源文件，因为它需要一种方法来查找、安装和构建源文件。

这要求我们在开发机器上设置`$GOPATH`环境变量，Go 用它来标识工作空间根文件夹的路径。

因此，要在我们的工作区内创建一个新目录，我们必须像这样指定完整路径:

```
$ mkdir -p $GOPATH/src/github.com/firebase007

```

`$GOPATH`可以是我们机器上的任何路径，通常是`$HOME/go`，除了我们机器上 Go 安装的路径。在上面指定的路径中，我们可以有包目录，然后在那个目录中有`.go`文件。

`bin`目录包含可执行的 Go 二进制文件。`[go](https://golang.org/cmd/go/)`[工具链](https://golang.org/cmd/go/)，用它的命令集，构建并安装二进制文件到这个目录。该工具提供了一种获取、构建和安装 Go 包的标准方式。

`pkg`目录是 Go 为后续编译存储预编译文件缓存的地方。更多关于如何用`[$GOPATH](https://golang.org/doc/gopath_code.html)` [写 Go 代码的详细信息可以在这里](https://golang.org/doc/gopath_code.html)找到。

但是，请注意，在较新的 Go 版本中，特别是从 1.13 开始，Go 引入了带有`go.mode`文件的 Go 模块，我们将在下一节回顾这一点。

## 包在 Go 中是如何工作的？

为了封装、依赖性管理和可重用性，程序被分组为包。包是存储在同一目录中并一起编译的源文件。

它们存储在一个模块中，其中一个[模块是一组执行特定操作的相关 Go 包](https://blog.logrocket.com/getting-started-with-go-for-frontend-developers/#package-management)。

请注意，Go 存储库通常只包含一个模块，它位于存储库的根。但是，一个存储库也可以包含多个模块。

如今，随着 1.13 及以上版本中 Go 模块的引入，我们可以运行并编译一个简单的 Go 模块或程序，如下所示:

```
[email protected] Desktop % mkdir examplePackage // create a directory on our machine outside $GOPATH/src
[email protected] Desktop % cd examplePackage  // navigate into that directory
[email protected] examplePackage % go mod init github.com/firebase007/test  // choose a module path and create a go.mod file that declares that path
go: creating new go.mod: module github.com/firebase007/test
[email protected] examplePackage % ls
go.mod

```

假设`test`是我们上面模块的名称，我们可以继续创建一个包目录，并在同一个目录中创建新文件。下面我们来看一个简单的例子:

```
[email protected] examplePackage % mkdir test
[email protected] examplePackage % ls
go.mod  test
[email protected] examplePackage % cd test 
[email protected] test % ls
[email protected] test % touch test.go
[email protected] test % ls
test.go
[email protected] test % go run test.go 
Hello, Go
[email protected] test %

```

`test.go`文件中的示例代码如下所示:

```
package main  // specifies the package name

import "fmt"

func main() {
  fmt.Println("Hello, Go")
}

```

注意,`go.mod`文件声明了一个模块的路径，它还包括模块中所有包的导入路径前缀。这对应于它在工作区或远程存储库中的位置。

## Go 类型系统

就像其他语言中的类型系统一样，Go 的类型系统指定了一组规则，将类型属性分配给变量、函数声明和标识符。Go 中的类型可以分为以下几类:

### Go 中的字符串类型

字符串类型表示一组字符串值，在 Go 中是一片字节。一旦创建，它们就是不可变的或只读的。字符串是定义的类型，因为它们附有方法

### Go 中的布尔类型

布尔类型由预先声明的常数`true`和`false`表示。

### Go 中的数字类型

数值类型表示整数值或浮点值的集合。包括`uint8`(或`byte`)`uint16``uint32``uint64``int8``int16``int32`(或`rune`)`int64``float32``float64``complex64``complex128`。

这些类型进一步分为有符号整数、无符号整数、实数和复数。它们有不同的大小，大多数是特定于平台的。关于[数字类型的更多细节可以在这里找到](https://golang.org/ref/spec#Numeric_types)。

### Go 中的数组类型

数组类型是同一类型元素的编号集合。基本上，它们是切片的积木。

数组是 Go 中的值，这意味着当它们被赋给变量或作为参数传递给函数时，它们的原始值被复制，而不是它们的内存地址。

### Go 中的切片类型

切片只是基础数组的一部分，或者基本上是对基础数组的引用。`[]T`是包含类型为`T`的元素的切片。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### Go 中的指针类型

指针类型是一种引用类型，表示指向给定类型变量的所有指针的集合。通常，指针类型保存另一个变量的内存地址。指针的零值是`nil`

关于其他类型的更多细节，比如地图、函数、通道等等，可以在语言规范的[类型部分](https://golang.org/ref/spec#Types)中找到。如前所述，我们将在本文中重点关注接口和结构类型。

## golang 接口和 struts

### go 中的 struts 是什么？

Go 的结构类型包含相同或不同类型的字段。结构基本上是具有逻辑意义或构造的命名字段的集合，其中每个字段都有特定的类型。

我们可以把结构比作由不同字段组成的对象或结构。

通常，结构类型是用户定义类型的组合。它们是专用类型，因为它们允许我们在内置类型不足的情况下定义自定义数据类型。

让我们举个例子来更好地理解这一点。假设我们有一篇博文打算发表。使用结构类型来表示数据字段如下所示:

```
type blogPost struct {
  author  string    // field
  title   string    // field  
  postId  int       // field
}
// Note that we can create instances of a struct types

```

在上面的结构定义中，我们添加了不同的字段值。现在，要使用文本实例化或初始化该结构，我们可以执行以下操作:

```
package main

import "fmt"

type blogPost struct {
  author  string
  title   string
  postId  int  
}

func NewBlogPost() *blogPost {
        return &blogPost{
                author: "Alexander",
                title:  "Learning structs and interfaces in Go",
                postId: 4555,
        }

}

func main() {
        var b blogPost // initialize the struct type

        fmt.Println(b) // print the zero value    

        newBlogPost := *NewBlogPost()
        fmt.Println(newBlogPost)

        // alternatively
        b = blogPost{ //
        author: "Alex",
        title: "Understand struct and interface types",
        postId: 12345,
        }

        fmt.Println(b)        

}

//output
{Alexander Learning structs and interfaces in Go 4555}
{  0}  // zero values of the struct type is shown
{Alex Understand struct and interface types 12345}

```

这里有一个[链接](https://go.dev/play/p/PKBsQKPpGV2)到操场运行上面的代码。

我们还可以在初始化之后使用点运算符来访问 struct 类型中的单个字段。让我们通过一个例子来看看我们是如何做到这一点的:

```
package main

import "fmt"

type blogPost struct {
  author  string
  title   string
  postId  int  
}

func main() {
        var b blogPost // b is a type Alias for the BlogPost
        b.author= "Alex"
        b.title="understand structs and interface types"
        b.postId=12345

        fmt.Println(b)  

        b.author = "Chinedu"  // since everything is pass by value by default in Go, we can update this field after initializing - see pointer types later

        fmt.Println("Updated Author's name is: ", b.author)           
}

```

同样，这里有一个[链接来运行上面操场](https://go.dev/play/p/ph5-MseQ3Pv)中的代码片段。此外，我们可以使用短文字符号来实例化结构类型，而不使用字段名，如下所示:

```
package main

import "fmt"

type blogPost struct {
  author  string
  title   string
  postId  int  
}

func main() {
        b := blogPost{"Alex", "understand struct and interface type", 12345}
        fmt.Println(b)        

}

```

请注意，使用上面的方法，我们必须始终按照在 struct 类型中声明字段值的顺序来传递字段值。此外，所有字段都必须初始化。

最后，如果我们有一个只在函数中使用一次的结构类型，我们可以内联定义它们，如下所示:

```
package main

import "fmt"

type blogPost struct {
  author  string
  title   string
  postId  int  
}

func main() {

        // inline struct init
        b := struct {
          author  string
          title   string
          postId  int  
         }{
          author: "Alex",
          title:"understand struct and interface type",
          postId: 12345,
        }

        fmt.Println(b)           
}

```

注意，我们也可以用关键字`new`初始化结构类型。在这种情况下，我们可以执行以下操作:

```
b := new(blogPost)

```

然后，我们可以使用点、`.`、操作符来设置和获取字段的值，就像我们前面看到的那样。让我们看一个例子:

```
package main

import "fmt"

type blogPost struct {
  author  string
  title   string
  postId  int  
}

func main() {
        b := new(blogPost)

        fmt.Println(b) // zero value

        b.author= "Alex"
        b.title= "understand interface and struct type in Go"
        b.postId= 12345

        fmt.Println(*b)   // dereference the pointer     

}

//output
&{  0}
{Alex understand interface and struct type in Go 12345}

```

注意，正如我们从输出中看到的，通过使用`new`关键字，我们为变量`b`分配存储，然后变量`b`初始化我们的结构字段的零值——在本例中是`(author="", title="", postId=0)`。

然后返回一个指针类型`*b`，包含上述变量在内存中的地址。

这里有一个[链接](https://play.golang.org/p/-6YRoVnb-RV)到操场来运行代码。关于`[new](https://golang.org/ref/spec#Allocation)` [关键字行为的更多细节可以在](https://golang.org/ref/spec#Allocation)这里找到。

### 指向结构的 Golang 指针

在我们前面的例子中，我们使用 Go 的默认行为，其中一切都是通过值传递的。有了指针，情况就不一样了。让我们看一个例子:

```
package main

import "fmt"

type blogPost struct {
  author  string
  title   string
  postId  int  
}

func main() {
        b := &blogPost{
                author:"Alex",
                title: "understand structs and interface types",
                postId: 12345,
                }

        fmt.Println(*b)   // dereference the pointer value 

       fmt.Println("Author's name", b.author) // in this case Go would handle the dereferencing on our behalf
}

```

这里有一个[链接到操场运行代码](https://play.golang.org/p/WSPB_KUQeTM)。

随着我们继续学习关于方法和接口的部分，我们将理解这种方法的好处。

### Golang 嵌套或嵌入的结构字段

前面我们提到过结构类型是复合类型。因此，我们也可以拥有嵌套在其他结构中的结构。例如，假设我们有一个`blogPost`和一个`Author`结构，定义如下:

```
type blogPost struct {
  title      string
  postId     int
  published  bool 
}

type Author struct {
  firstName, lastName, Biography string
  photoId    int
}

```

然后，我们可以像这样将`Author`结构嵌套在`blogPost`结构中:

```
package main

import "fmt"

type Author struct {
  firstName, lastName, Biography string
  photoId    int
}

type blogPost struct {
  author  Author // nested struct field
  title   string
  postId  int 
  published  bool  
}

func main() {
        b := new(blogPost)

        fmt.Println(b)

        b.author.firstName= "Alex"
        b.author.lastName= "Nnakwue"
        b.author.Biography = "I am a lazy engineer"
        b.author.photoId = 234333
        b.published=true
        b.title= "understand interface and struct type in Go"
        b.postId= 12345

        fmt.Println(*b)        

}

// output

&{{   0}  0 false}  // again default values
{{Alex Nnakwue I am a lazy engineer 234333} understand interface and struct type in Go 12345 true}

```

下面是在操场运行代码的[链接。](https://play.golang.org/p/aWpZcpcJvGt)

在 Go 中，有一个嵌套结构类型的提升字段的概念。在这种情况下，我们可以直接访问内嵌结构中定义的结构类型，而不需要更深入，也就是做`b.author.firstName`。让我们看看如何实现这一点:

```
package main

import "fmt"

type Author struct {
  firstName, lastName, Biography string
  photoId    int
}

type BlogPost struct {
  Author  // directly passing the Author struct as a field - also called an anonymous field orembedded type 
  title   string
  postId  int 
  published  bool  
}

func main() {
        b := BlogPost{
        Author: Author{"Alex", "Nnakwue", "I am a lazy engineer", 234333},
        title:"understand interface and struct type in Go",
        published:true,
        postId: 12345,
        }

        fmt.Println(b.firstName) // remember the firstName field is present on the Author struct?
        fmt.Println(b)        

}

//output
Alex
{{Alex Nnakwue I am a lazy engineer 234333} understand interface and struct type in Go 12345 true}

```

这里有一个[链接到操场运行代码](https://play.golang.org/p/Rc-dgED1QRd)。

注意，Go 不支持继承，而是支持合成。在前面的章节中，我们已经看到了一个例子，说明了我们如何在组合的帮助下创建一个新的结构。

在接下来的章节中，我们还将学习如何将这些概念应用于接口类型，以及如何用方法将行为添加到结构类型中。

### 其他结构类型考虑事项

值得注意的是，字段名既可以用变量隐式指定，也可以作为没有字段名的嵌入类型指定。在这种情况下，该字段必须指定为类型名`T`，或者指定为指向非接口类型名`*T`的指针。

其他考虑因素包括以下几点:

*   字段名在结构类型中必须是唯一的
*   可以提升嵌入类型的字段或方法
*   提升的字段不能用作结构中的字段名
*   字段声明后面可以跟一个可选的字符串文字标记
*   导出的结构字段必须以大写字母开头
*   除了基本类型，我们还可以将函数类型和接口类型作为结构字段

关于结构类型的更多细节可以在语言规范的[这里](https://golang.org/ref/spec#Struct_types)找到。

### Golang 中的方法集是什么？

Go 中的方法是带有接收器的特殊类型的函数。

一个类型为`T`的方法集，它包含所有用接收器类型`T`声明的方法。注意，接收者是通过方法名前面的一个额外参数指定的。关于接收器类型的更多细节可以在[这里](https://golang.org/ref/spec#Method_declarations)找到。

在 Go 中，我们可以通过在类型上定义一个方法来创建一个带有行为的类型。本质上，方法集是一个类型实现接口必须拥有的方法列表。让我们看一个例子:

```
// BlogPost struct with fields defined
type BlogPost struct {
  author  string
  title   string
  postId  int  
}

// Create a BlogPost type called (under) Technology
type Technology BlogPost

```

注意，我们在这里使用的是结构类型，因为我们在本文中关注的是结构。方法也可以在其他命名类型上定义:

```
// write a method that publishes a blogPost - accepts the Technology type as a pointer receiver
func (t *Technology) Publish() {
    fmt.Printf("The title on %s has been published by %s, with postId %d\n" , t.title, t.author, t.postId)
}

// alternatively similar to the above, if we choose not to define a new type 
func (b *BlogPost) Publish() {
    fmt.Printf("The title on %s has been published by %s, with postId %d\n" , t.title, b.author, b.postId)
}

// Create an instance of the type
t := Technology{"Alex","understand structs and interface types",12345}

// Publish the BlogPost -- This method can only be called on the Technology type
t.Publish()

// output
The title on understand structs and interface types has been published by Alex, with postId 12345

```

这里有一个[链接](https://play.golang.org/p/D3mVOBux2PM)到操场来运行代码。

带有指针接收器的方法既可以处理指针，也可以处理值。然而，反过来却不是这样。

### 什么是 Golang 接口？

在 Go 中，接口服务于封装的主要目的，并允许我们编写更干净和更健壮的代码。通过这样做，我们只暴露了程序中的方法和行为。

正如我们在上一节中提到的，方法集向一个或多个类型添加行为。但是，接口类型定义了一个或多个方法集。

因此，一个类型通过实现它的方法来实现一个接口。从这个角度来看，接口使我们能够构建具有共同行为的自定义类型。

方法集基本上是一种类型必须拥有的方法列表，以便该类型实现该接口。

例如，假设我们有两个或更多的结构类型用相同的返回类型实现相同的方法，我们可以继续用这个方法集创建一个接口类型，因为它是一个或多个结构类型所共有的。

在 Go 中，接口是隐式的。这意味着，如果属于一个接口类型的方法集的每个方法都是由一个类型实现的，那么该类型就实现了该接口。要声明接口:

```
type Publisher interface {
    publish()  error
}

```

在我们上面设置的`publish()`接口方法中，如果一个类型(比如一个 struct)实现了该方法，那么我们可以说该类型实现了该接口。下面让我们定义一个接受 struct 类型`blogpost`的方法:

```
func (b blogPost) publish() error {
   fmt.Println("The title has been published by ", b.author)
   return nil
}
<
```

现在实现接口:

```
package main

import "fmt"

// interface definition
type Publisher interface {
     Publish()  error
}

type blogPost struct {
  author  string
  title   string
  postId  int  
}

// method with a value receiver
func (b blogPost) Publish() error {
   fmt. Printf("The title on %s has been published by %s, with postId %d\n" , b.title, b.author, b.postId)
   return nil
}

 func test(){

  b := blogPost{"Alex","understanding structs and interface types",12345}

  fmt.Println(b.Publish())

   d := &b   // pointer receiver for the struct type

   b.author = "Chinedu"

   fmt.Println(d.Publish())

}

func main() {

        var p Publisher

        fmt.Println(p)

        p = blogPost{"Alex","understanding structs and interface types",12345}

        fmt.Println(p.Publish())

        test()  // call the test function 

}

//output
<nil>
The title on understanding structs and interface types has been published by Alex, with postId 12345
<nil>
The title on understanding structs and interface types has been published by Alex, with postId 12345
<nil>
The title on understanding structs and interface types has been published by Chinedu, with postId 12345
<nil>

```

这里有一个[链接到操场运行代码](https://play.golang.org/p/L4SzJiaJ99w)。

我们也可以给接口类型起别名，如下所示:

```
type publishPost Publisher  // alias to the interface defined above - mostly suited for third-party interfaces

```

但是，请注意，如果有多个类型实现同一个方法，方法集可以构造一个接口类型。

这允许我们将该接口类型作为一个参数传递给一个旨在实现该接口行为的函数。这样，[我们就可以实现多态性](https://blog.logrocket.com/past-present-future-go-generics/)。

与函数不同，方法只能从定义它们的类型的实例中调用。

这样做的好处是，不用指定我们希望作为函数的参数接受的特定数据类型，如果我们能够指定必须作为参数传递给该函数的对象的行为就好了。

让我们看看如何使用接口类型作为函数的参数。首先，让我们向结构类型添加一个方法:

```
package main

import "fmt"

type Publisher interface {
     Publish()  error
}

type blogPost struct {
  author  string
  title   string
  postId  int  
}

func (b blogPost) Publish() error {
   fmt.Printf("The title on %s has been published by %s\n" , b.title, b.author)
   return nil
}

// Receives any type that satisfies the Publisher interface
func PublishPost(publish Publisher) error {
    return publish.Publish()
}

func main() {

        var p Publisher

        fmt.Println(p)

        b := blogPost{"Alex","understand structs and interface types",12345}

        fmt.Println(b)

        PublishPost(b)

}

//output
<nil>
{Alex understand structs and interface types 12345}
The title on understand structs and interface types has been published by Alex

```

下面是在操场上运行代码的[链接](https://play.golang.org/p/tTqgdV--7KX)。

正如我们前面提到的，我们可以通过值或指针类型来传递方法接收器。当我们通过值传递时，我们存储了我们传递的值的一个副本。

这意味着当我们调用该方法时，我们不会更改基础值。然而，当我们通过指针语义传递时，我们直接共享底层内存地址，因此，共享在底层类型中声明的变量的位置。

不过，提醒一下，当一个类型定义了接口类型上可用的方法集时，它就实现了一个接口。

同样，不要求类型指定它们实现一个接口；相反，任何类型都实现一个接口，只要它有签名与接口声明匹配的方法。

### 在 Go 中嵌入接口类型

最后，我们将看看在 Go 中嵌入接口类型的签名。让我们用一个虚拟的例子:

```
//embedding interfaces
type interface1 interface {
    Method1()
}

type interface2 interface {
    Method2()
}

type embeddedinterface interface {
    interface1
    interface2
}

func (s structName)  method1 (){

}

func (s structName)  method2 (){

}

type structName struct {
  field1  type1
  field2  type2

}

// initialize struct type inside main func
var e embeddedinterface = structName // struct initialized
e.method1() // call method defined on struct type

```

根据经验，当我们开始在包中用相同的方法签名实现多个类型时，我们可以开始重构代码并使用接口类型。这样做可以避免早期的抽象。

### 其他接口类型注意事项

空接口包含零个方法。请注意，所有类型都实现空接口。

这意味着，如果您编写一个将空的`interface{}`值作为参数的函数，您可以为该函数提供任何值/方法。

接口通常也属于使用接口类型值的包，而不是实现这些值的包。

最后一个接口的零值是`nil`。关于接口类型的更多细节可以在语言规范中找到[。](http://https:https://golang.org/ref/spec#Interface_types)

### 在 Golang 中将接口转换为结构

有些情况下，我们打算从一个空接口或接口类型派生一个具体类型，比如一个结构。在 Go 中，我们可以通过类型断言检查类型的相等性。

从 Effective Go 开始，要将接口转换为结构，我们可以使用下面的语法符号:

```
v = x.(T)

```

这里，`x`是接口类型，`T`是实际的具体类型。本质上，`T`必须实现`x`的接口类型。

注意`x`通常是动态类型，它的值在运行时是已知的。因此，如果类型断言无效，Go 就会死机。

为了检查正确性并避免类型不匹配，我们可以进一步利用下面的语法符号:

```
v, ok = x.(T)

```

在这种情况下，如果断言成立，`ok`的值就是`true`。让我们看一个使用类型断言来处理下面的结构和接口的简单例子:

```
package main

import "fmt"

type blogPost struct {
        Data interface{}
        postId int
}

func NewBlogPostStruct() interface{} {
        return &blogPost{postId: 1234, Data: "Alexander"}
}

func main() {
        blogPost := NewBlogPostStruct().(*blogPost)
        fmt.Println(blogPost.Data)
}
//returns
Alexander

```

注意，从上面的`blogPost`结构中，我们需要确保将`Data`字段设置为我们期望的类型；在我们的例子中，我们使用一个字符串。

## 结论

正如我们所了解的，接口类型可以存储值的副本，或者通过存储指向值的地址的指针，可以与接口共享值。

关于接口类型需要注意的一件重要事情是，不要过早地关注优化是明智的，因为我们不想在使用接口之前定义它们。

确定接口遵守或使用的规则基于方法接收者和如何进行接口调用。在这里的 [Go 代码评审和评论部分阅读更多相关内容](https://github.com/golang/go/wiki/CodeReviewComments#interfaces)。

对于方法接收者来说，关于指针和值的一个非常令人困惑的规则是，虽然值方法可以在指针和值上调用，但是指针方法只能在指针上调用。

对于接收者类型，如果一个方法需要改变接收者，那么接收者必须是一个指针。

关于接口类型的额外细节可以在无效的 Go 中找到。具体可以看一下[接口和方法](https://golang.org/doc/effective_go.html#interface_methods)、[接口检查](https://golang.org/doc/effective_go.html#blank_implements)、[接口转换和类型断言](https://golang.org/doc/effective_go.html#interface_conversions)。

类型断言更像是应用于接口类型底层值的操作。本质上，它是一个提取接口类型值的过程。它们被表示为`x.(T)`，其中值`x`是一个接口类型。

再次感谢您的阅读，请在下面的评论区随意添加问题或评论，或者联系 [Twitter](https://twitter.com/alex_nnakwue) 。勇往直前，不断学习！🙂

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)*