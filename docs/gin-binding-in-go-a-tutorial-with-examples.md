# Go 中的 Gin 绑定:示例教程

> 原文：<https://blog.logrocket.com/gin-binding-in-go-a-tutorial-with-examples/>

在过去的几年里，Go 已经非常受微服务的欢迎。Gin 是一个面向 Go 的 web 框架，它专注于性能和生产率，具有类似 Martini 的 API。

在本教程中，我们将向您展示如何使用 Gin 的绑定。我们将带您完成请求有效负载验证，使用反射和[验证器模块](https://pkg.go.dev/github.com/go-playground/validator/v10)编写定制验证，并为各种格式(如 TOML 等)构建定制绑定。

## 什么是杜松子酒？

Gin binding 是一个非常棒的反序列化库。它支持 JSON、XML、query parameter 等现成的东西，并带有一个内置的验证框架。

Gin 绑定用于序列化 JSON、XML、路径参数、表单数据等。结构和映射。它还有一个内置的验证框架，可以进行复杂的验证。

Gin 通过提供 struct 标签支持各种格式。例如，`uri`标签用于序列化路径参数:

```
package main

import (
   "fmt"
   "github.com/gin-gonic/gin"
   "net/http"
)

type Body struct {
  // json tag to de-serialize json body
   Name string `json:"name"`
}

func main() {
   engine:=gin.New()
   engine.POST("/test", func(context *gin.Context) {
      body:=Body{}
      // using BindJson method to serialize body with struct
      if err:=context.BindJSON(&body);err!=nil{
         context.AbortWithError(http.StatusBadRequest,err)
         return
      }
      fmt.Println(body)
      context.JSON(http.StatusAccepted,&body)
   })
   engine.Run(":3000")
}

```

上面的代码片段将来自`POST /test`端点的 JSON 有效负载绑定到 body struct 实例。我们可以用 Postman 工具测试一下，如下图。

![Gin Binding with Go Postman Test](img/e5b558df365cd43d7bc8a3fb060adf8c.png)

`BindJSON`读取主体缓冲区，将其反序列化为结构。`BindJSON`不能在同一个上下文中调用两次，因为它会刷新主体缓冲区。

如果您想将主体反序列化为两个不同的结构，使用`ShouldBindBodyWith`复制主体缓冲区并将其添加到上下文中。

```
if err:=context.ShouldBindBodyWith(&body,binding.JSON);err!=nil{
   context.AbortWithError(http.StatusBadRequest,err)
   return
}
```

同样，XML 参数`body`和`path`被映射到结构。

```
package main

import (
   "fmt"
   "github.com/gin-gonic/gin"
   "net/http"
)

// path paramter with name details will mapped to Details
type URI struct {
   Details string `json:"name" uri:"details"`
}

func main() {
   engine:=gin.New()
// adding path params to router
   engine.GET("/test/:details", func(context *gin.Context) {
      uri:=URI{}
      // binding to URI
      if err:=context.BindUri(&uri);err!=nil{
         context.AbortWithError(http.StatusBadRequest,err)
         return
      }
      fmt.Println(uri)
      context.JSON(http.StatusAccepted,&uri)
   })
   engine.Run(":3000")
}

```

上面的例子是反序列化`body`、`query`和`path`参数的绑定的基本用例。上面的端点通过绑定路径参数产生一个序列化的 JSON 对象，如下所示。

![Serialized JSON Object](img/f4541099d1dfa3fa3fb6a09c2914e93c.png)

## 使用 Gin 进行基本验证

Gin 在内部使用[验证器](https://github.com/go-playground/validator)包进行验证。这个包验证器提供了一组广泛的内置验证，包括`required`、类型验证和字符串验证。

验证通过`binding` struct 标签添加到结构中:

```
type URI struct {
   Details string `json:"name" uri:"details" binding:"required"`
}

```

验证器包还支持更复杂的验证，比如`len`、`max`和`min`。

让我们在几个实际场景中使用 Gin 的内置验证语法。

### 验证电话号码、电子邮件和国家代码

在处理联系信息时，我们经常需要在 web 应用程序后端验证电话号码、电子邮件地址和国家代码。请看下面的示例结构:

```
type Body struct {
   FirstName string `json:"firstName" binding:"required"`
   LastName string `json:"lastName" binding:"required"`
   Email string `json:"email" binding:"required,email"`
   Phone string `json:"phone" binding:"required,e164"`
   CountryCode string `json:"countryCode" binding:"required,iso3166_1_alpha2"`
}

```

上面的 struct 标记使用通用正则表达式来验证电子邮件，使用国际标准 [E.164](https://en.wikipedia.org/wiki/E.164) 来验证电话，使用国际标准[ISO-3166–1](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)双字母标准来验证国家代码。例如，它接受以下用于绑定过程的示例 JSON 有效负载:

```
{
   "firstName": "John",
   "lastName": "Mark",
   "email": "[email protected]",
   "phone": "+11234567890",
   "countryCode": "US"
}

```

validator 包也提供邮政编码验证支持。例如，您可以使用以下语法验证英国邮政编码。

```
type Body struct {
    PostCode string `json:"postCode" binding:"required,postcode_iso3166_alpha2=GB"`
}

```

### 验证自定义字符串格式

之前，我们使用了一些标准格式进行验证。但是，我们经常需要定义自定义格式来验证特定领域的用户输入。例如，您可以使用 struct 标记来验证自定义产品代码格式。验证器包提供了许多有用的字符串验证器助手。

假设您需要验证以`PC`字符串前缀开头的十个字母的产品代码。对于这个场景，您可以将`startswith`标签与`len`标签一起使用:

```
type Body struct {
   ProductCode string `json:"productCode" binding:"required,startswith=PC,len=10"`
}

```

上述结构定义接受以下 JSON 有效负载进行绑定:

```
{
   "productCode": "PC00001120"
}

```

以下是我们经常需要的一些其他字符串验证帮助程序:

| 标签 | 描述 | 用法示例 |
| `uppercase` | 只接受大写字母 | `binding:"uppercase"` |
| `lowercase` | 只接受小写字母 | `binding:"lowercase"` |
| `contains` | 仅接受包含特定字符串段的字符串。 | `binding:"contains=key"` |
| `alphanum` | 仅接受字母数字字符(英文字母和数字)。拒绝包含特殊字符的字符串。 | `binding:"alphanum"` |
| `alpha` | 只接受英文字母 | `binding:"alpha"` |
| `endswith` | 仅接受以特定字符序列结尾的字符串 | `binding:"endswith=."` |

### 与字段和值进行比较

验证器包提供了几个用于比较的标记，您可以使用这些标记将特定字段与另一个字段或硬编码值进行比较，如下所示:

```
type Body struct {
   Width int `json:"width" binding:"required,gte=1,lte=100,gtfield=Height"`
   Height int `json:"height" binding:"required,gte=1,lte=100"`
}

```

上述代码基于以下约束将 JSON 有效负载绑定到上述结构定义中:

*   宽度:1 ≤ x ≤100 且大于`Height`值
*   高度:1 ≤ x ≤100

### 验证日期和时间

Gin 提供了`time_format` struct 标签来验证日期和时间格式。您可以将`time_format`标签与验证助手标签结合起来进行日期和时间验证。

例如，您可以使用以下结构定义来验证日期范围表单输入:

```
type Body struct {
   StartDate time.Time `form:"start_date" binding:"required,ltefield=EndDate" time_format:"2006-01-02"`
   EndDate time.Time `form:"end_date" binding:"required" time_format:"2006-01-02"`
}

```

您可以通过`time_format`标签提供任何有效的日期格式。例如，`2006-01-02 15:04:05`将接受基于`yyyy-mm-dd hh:mm:ss`格式的日期时间输入。

### 嵌套 struts 验证

嵌套结构和数组也是递归验证的。

```
type User struct {
   Name string `json:"name" binding:"required,min=3"`
   Age uint `json:"age" binding:"required,min=18"`
   Comments []*Comment `json:"comments" binding:"required"`
}

type Comment struct {
   Text string `json:"text" binding:"required,max=255"`
   Type string `json:"type" binding:"required,oneof=post nested"`
}

```

Gin 带有许多内置的验证和验证助手标签；你可以在 GitHub 上找到一个[详尽的列表。](https://github.com/go-playground/validator#baked-in-validations)

## 处理验证错误

在前面的例子中，我们使用了`AbortWithError`函数将 HTTP 错误代码发送回客户端，但是我们没有发送有意义的错误消息。因此，我们可以通过发送一个有意义的验证错误消息作为 JSON 输出来改进端点:

```
package main
import (
  "github.com/gin-gonic/gin"
  "net/http"
)
type Body struct {
   Price uint `json:"price" binding:"required,gte=10,lte=1000"`
}
func main() {
  engine:=gin.New()
  engine.POST("/test", func(context *gin.Context) {
     body:=Body{}
     if err:=context.ShouldBindJSON(&body);err!=nil{
        context.AbortWithStatusJSON(http.StatusBadRequest,
        gin.H{
            "error": "VALIDATEERR-1",
            "message": "Invalid inputs. Please check your inputs"})
        return
     }
     context.JSON(http.StatusAccepted,&body)
  })
  engine.Run(":3000")
}

```

现在，上面的代码使用了`AbortWithStatusJSON`函数，并向客户端返回一个唯一的错误代码和消息，因此客户端可以向用户显示一个有意义的错误消息。
![AbortwithStatusJSON](img/4c18eb4e61e22fdf4527509c6a9525af.png)

Also, you can return the technical auto-generated error message, as shown below:

```
gin.H{
    "error": "VALIDATEERR-1",
    "message": err.Error()})

```

上述方法分别给出了一个过于普通的错误和一个技术消息，因此我们可以通过使用以下代码返回一个有意义的错误消息列表来进一步改进错误响应:

```
package main
import (
  "github.com/gin-gonic/gin"
  "github.com/go-playground/validator/v10"
  "net/http"
  "errors"
)

type Body struct {
   Product string `json:"product" binding:"required,alpha"`
   Price uint `json:"price" binding:"required,gte=10,lte=1000"`
}

type ErrorMsg struct {
    Field string `json:"field"`
    Message   string `json:"message"`
}

func getErrorMsg(fe validator.FieldError) string {
    switch fe.Tag() {
        case "required":
            return "This field is required"
        case "lte":
            return "Should be less than " + fe.Param()
        case "gte":
            return "Should be greater than " + fe.Param()
    }
    return "Unknown error"
}

func main() {
  engine:=gin.New()
  engine.POST("/test", func(context *gin.Context) {
     body:=Body{}
     if err:=context.ShouldBindJSON(&body);err!=nil{
        var ve validator.ValidationErrors
        if errors.As(err, &ve) {
            out := make([]ErrorMsg, len(ve))
            for i, fe := range ve {
                out[i] = ErrorMsg{fe.Field(), getErrorMsg(fe)}
            }
            context.AbortWithStatusJSON(http.StatusBadRequest, gin.H{"errors": out})
        }
        return
     }
     context.JSON(http.StatusAccepted,&body)
  })
  engine.Run(":3000")
}

```

现在，我们有了基于验证标签名称的清晰而有意义的错误消息。例如，如果您向 API 发送以下 JSON 有效负载:

```
{
    "price": 5
}

```

您将获得以下输出:

```
{
    "errors": [
        {
            "field": "Product",
            "message": "This field is required"
        },
        {
            "field": "Price",
            "message": "Should be greater than 10"
        }
    ]
}

```

现在，您可以使用上面的错误列表来显示每个字段的详细和具体的错误消息。

## 编写自定义验证

并非所有用例都非常适合内置的 Gin 验证。出于这个原因，Gin 提供了添加定制验证的方法。

在验证过程中使用了`reflect`包，以便在运行时计算出结构字段的类型和值。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

要创建一个新的绑定，您必须向执行验证的函数注册一个验证。

```
 // getting the validation engine and type casting it.
if v, ok := binding.Validator.Engine().(*validator.Validate); ok {
   // registering validation for nontoneof
   v.RegisterValidation("notoneof", func(fl validator.FieldLevel) bool {
     // split values using ` `. eg. notoneof=bob rob job
      match:=strings.Split(fl.Param()," ")
     // convert field value to string
      value:=fl.Field().String()
      for _,s:=range match {
       // match value with struct filed tag
         if s==value {
            return false
         }
      }
      return true
   })
}

```

您可以使用添加定制验证器的`binding`包来访问验证引擎。`Validator`变量被导出。`Validator`提供了`Engine`方法，它返回验证引擎。

引擎上的`RegisterValidation`方法接受一个名称和函数，该函数返回字段是否有效。

您可以使用`Param`方法访问传递给验证器的参数。

`Field`方法返回`struct`中字段的值。该值可以转换为各种数据类型。

可以访问整个结构。您还可以访问父结构的不同键。

### 访问其他结构字段

`FieldLevel`有一个返回结构的`reflect.Value`类型的`Top`方法。可用于访问结构中的字段。

例如，您可以使用`reflect.Value`创建一个验证，其中两个字段不能有相同的值。

```
v.RegisterValidation("unique", func(fl validator.FieldLevel) bool {
  // get the fields which need to be unique
   match:=strings.Split(fl.Param()," ")
  // value of the field
   value:=fl.Field().String()
   for _,s:=range match {
     // access to struct and getting value by field name
      fs:=fl.Top().FieldByName(s)
      // check only for string validation
      if fs.Kind() == reflect.String {
          // check value of both fields
         if value==fs.String() {
            return false
         }
      }
   }
   return true
})

```

上面的示例只检查字符串值，但是您可以很容易地对所有数据类型进行修改:

```
type ExampleStruct struct {
   Name string `json:"name" uri:"name" binding:"notoneof=bob rob job"`
   LastName string `json:"last_name" binding:"unique=Name"`
}

```

## 编写自定义 Gin 绑定

在某些情况下，客户端和服务器使用不同的格式来交换数据。例如，可以用 TOML 代替 JSON 或 XML 作为请求的主体。

对于这种情况，Gin 提供了一种即插即用的方法来改变主体解析器。

每个绑定都需要实现这个接口。`Name`方法返回一个绑定名称，而`Bind`方法解析请求体:

```
type Binding interface {
   Name() string
   Bind(*http.Request, interface{}) error
}

```

下面是一个绑定示例:

```
type Toml struct {
}

// return the name of binding
func (t Toml) Name() string {
   return "toml"
}

// parse request
func (t Toml) Bind(request *http.Request, i interface{}) error {
// using go-toml package 
   tD:= toml.NewDecoder(request.Body)
// decoding the interface
   return tD.Decode(i)
}

```

使用示例:

```
engine.POST("/Toml", func(context *gin.Context) {
   uri:= URI{}

   if err:=context.MustBindWith(&uri, Toml{});err!=nil{
      context.AbortWithError(http.StatusBadRequest,err)
      return
   }
   context.JSON(200,uri)
})

```

实现`BindBody`以使用`ShouldBindBodyWith`:

```
func (t Toml) BindBody(bytes []byte, i interface{}) error {
   return toml.Unmarshal(bytes,i)
}

```

使用示例:

```
engine.POST("/Toml", func(context *gin.Context) {
   uri:= URI{}

   if err:=context.ShouldBindBodyWith(&uri, Toml{});err!=nil{
      context.AbortWithError(http.StatusBadRequest,err)
      return
   }
   context.JSON(200,uri)
})

```

## 结论

在本教程中，我们讨论了 Gin 绑定、各种内置验证器和一些更高级的用例。我们还介绍了如何使用 Gin 库提供的各种接口构建定制绑定。最后，我们使用高级反射和验证器包构建了一些定制的验证器。您可以使用这些构建块来构建各种 HTTP 主体解析器。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)