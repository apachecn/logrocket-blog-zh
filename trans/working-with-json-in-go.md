# 为 Go - LogRocket 博客配置 JSON

> 原文：<https://blog.logrocket.com/working-with-json-in-go/>

大多数现代应用程序需要跨多个服务进行通信，JSON 是最流行的跨 web 存储和传输数据的符号之一。在本文中，我们将探索在流行的开源语言 Go 中使用 JSON。

我们开始吧！

## JSON 简介

JSON 或 [JavaScript 对象符号](https://blog.logrocket.com/the-quiet-revolution-how-json-displaced-xml-1e1f3e8552f7/)，是一种用于存储、发送和接收信息的流行文本格式。JSON 易于编写和理解，因为它的结构简单，使用有序列表将它格式化为键值对。

JSON 是独立于语言的，这意味着它可以用于任何编程语言。许多语言都内置了对 JSON 的支持。

让我们用一个例子来看看 JSON 格式是什么样子的！在下面的代码块中，JSON 对象表示 web 应用程序上的用户:

```
{
  "firstname": "Mohit",
  "lastname": "Khare",
  "id": "mkfeuhrer",
  "age": "24",
  "gender": "male",
  "preferred_topics": ["startups", "books", "chess", "blogging"],
  "social_links": [
    {
      "site": "twitter",
      "url": "https://twitter.com/mkfeuhrer"
    }
  ]
} 

```

根据 JSON [格式模式](https://json-schema.org/understanding-json-schema/reference/object.html)的定义，JSON 键必须是一个字符串。但是，该值可以是字符串、对象或字符串或对象的列表。

## 在 Go 中读取 JSON 文件

将上面代码块中的 JSON 对象保存到名为`user.json`的文件中。我们将使用`ioutil`包中的`ReadFile`来读取 JSON 文件并打印数据:

```
package main
import (
    "fmt"
    "io/ioutil"
)
func ReadJSON(filename string) ([]byte, error) {
    data, err := ioutil.ReadFile(filename)
    if err != nil {
        fmt.Println("Error reading user.json", err)
        return nil, err
    }
    fmt.Println("Success reading user.json")
    return data, nil
}
func main() {
    data, err := ReadJSON("user.json")
    if err != nil {
        return
    }
    fmt.Println("Content of user.json:")
    fmt.Println(string(data))
}

```

上面代码的输出如下:

```
Success reading user.json
Content of user.json:
{
  "firstname": "Mohit",
  "lastname": "Khare",
  "id": "mkfeuhrer",
  "age": "24",
  "gender": "male",
  "preferred_topics": [
    "startups",
    "books",
    "chess"
  ],
  "social_links": [
    {
      "site": "twitter",
      "url": "https://twitter.com/mkfeuhrer"
    }
  ]
}

```

## 在 go struts 中解码 json

既然我们可以读取 JSON 文件，我们将把它解析成 Go 结构。不能直接对 JSON 数据执行 Go 操作。相反，您必须将 JSON 数据映射到 Go 结构，允许它执行其他操作。

Go 中的 [`package json`](https://pkg.go.dev/encoding/json) 提供了`Unmarshal`函数，帮助我们将数据解析成结构体:

```
func Unmarshal(data []byte, v interface{}) error  

```

`Unmarshal`要求数据以字节数组的形式解析到接口。让我们创建一个结构来读取上面定义的用户数据:

```
// parse social link object
type SocialLink struct {
        Site string `json:"site"`
        URL  string `json:"url"`
}

// parse user object
type User struct {
        Firstname       string      `json:"firstname"`
        Lastname        string      `json:"lastname"`
        ID              string      `json:"id"`
        Age             string      `json:"age"`
        Gender          string      `json:"gender"`
        PreferredTopics []string    `json:"preferred_topics"`
        SocialLinks     []SocialLink `json:"social_links"`
}

```

现在，我们将 JSON 解析成这个结构:

```
func DecodeJSON(data []byte, user *User) {
    err := json.Unmarshal(data, user)
    if err != nil {
        fmt.Println("Error parsing JSON", err)
    }
    fmt.Println(*user)
}

// We call this function with the data and user Object
var user User
DecodeJSON(data, &user)

```

## 从 go struts 编码 json

我们还想从我们的 Go 应用程序中编写 JSON 数据，所以让我们将我们创建的结构转换成 JSON 数据。Go 的`package json`提供了`Marshal`函数来帮助将结构编码成 JSON 数据:

```
func Marshal(v interface{}) ([]byte, error)

```

需要一个接口来编码 JSON 数据。让我们将我们的`User`对象编码回 JSON:

```
func EncodeJSON(user *User) {
    data, err := json.Marshal(user)
    if err != nil {
        fmt.Println("Error parsing JSON", err)
    }
    fmt.Println(string(data))
}

// We call this function with the data and user Object
user := User {
    Firstname: "John",
    Lastname: "Doe",
    ID: "john",
    Age: "30",
    Gender: "male",
    SocialLinks: []SocialLink{
        {
            Site: "twitter",
            URL: "https://twitter.com/john",
        },
    },
}
EncodeJSON(data, &user)

```

上面的函数将打印 JSON 数据:

```
{
  "firstname": "John",
  "lastname": "Doe",
  "id": "john",
  "age": "30",
  "gender": "male",
  "preferred_topics": null,
  "social_links": [
    {
      "site": "twitter",
      "url": "https://twitter.com/john"
    }
  ]
}

```

请注意,`preferred_topics`字段是`null`,因为我们的用户对象没有为该属性赋值。现在，让我们学习如何跳过空字段。

当我们定义结构时，我们使用了一些 JSON 标签。标签通过省略空的或 null 字段来帮助我们控制属性的键。

让我们来看一个标签的例子！在下面的代码块中，我们将定义`Firstname`属性来使用 JSON 中的`"first name"`键。如果对象为空，我们将省略`PreferredTopics`:

```
PreferredTopics []string `json:"preferred_topics,omitempty"`

```

当我们编码/解码 JSON 时，我们将简单地忽略`Age`。

```
Age int `json:"-"`

```

当一个值在我们的结构中是可选的时候，我们通常使用`omitempty`。在我们的用户示例中，如果用户的`lastname`不在，我们可以管理，但是我们不能将`omitempty`添加到`id`。

您完成的代码将类似于下面的代码块:

```
package main
import (
    "encoding/json"
    "fmt"
    "io/ioutil"
)
type SocialLink struct {
    Site string `json:"site"`
    URL  string `json:"url"`
}
type User struct {
    Firstname       string       `json:"firstname"`
    Lastname        string       `json:"lastname,omitempty"`
    ID              string       `json:"id"`
    Age             string       `json:"age,omitempty"`
    Gender          string       `json:"gender,omitempty"`
    PreferredTopics []string     `json:"preferred_topics,omitempty"`
    SocialLinks     []SocialLink `json:"social_links,omitempty"`
}
func ReadJSON(filename string) ([]byte, error) {
    data, err := ioutil.ReadFile(filename)
    if err != nil {
        fmt.Println("Error reading user.json", err)
        return nil, err
    }
    fmt.Println("Success reading user.json")
    return data, nil
}
func DecodeJSON(data []byte, user *User) {
    err := json.Unmarshal(data, user)
    if err != nil {
        fmt.Println("Error parsing JSON", err)
    }
    fmt.Println(*user)
}
func EncodeJSON(user *User) {
    data, err := json.Marshal(user)
    if err != nil {
        fmt.Println("Error parsing JSON", err)
    }
    fmt.Println(string(data))
}
func main() {
    data, err := ReadJSON("user.json")
    if err != nil {
        return
    }
    fmt.Println("Content of user.json:")
    fmt.Println(string(data))
    var user User
    fmt.Println("\nDecode JSON data to user struct:")
    DecodeJSON(data, &user)
    // define new user
    user2 := User{
        Firstname: "John",
        Lastname:  "Doe",
        ID:        "john",
        Age:       "30",
        Gender:    "male",
        SocialLinks: []SocialLink{
            {
                Site: "twitter",
                URL:  "https://twitter.com/john",
            },
        },
    }
    fmt.Println("\nEncode struct to JSON:")
    EncodeJSON(&user2)
}  

```

## 结论

在本教程中，我们学习了如何读取 JSON 文件，如何使用`marshal`和`unmarshal`函数将数据编码和解码为结构体，以及如何使用标签定义结构体。

我们的示例包括用于输入用户配置文件信息的简单字段，但是您可以使用本教程中的信息来构建许多复杂的应用程序。

我希望你喜欢这篇文章，并学到一些有用的东西！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)