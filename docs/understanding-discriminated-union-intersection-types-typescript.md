# 区别并集和交集类型

> 原文：<https://blog.logrocket.com/understanding-discriminated-union-intersection-types-typescript/>

理解 TypeScript 中 union 和 intersection 类型背后的思想模式有助于您了解如何在必要时更好地实现它们。

在本文中，我们将探讨并集和交集的概念。首先，我们将看看集合论中使用的这些概念的数学定义。然后，我们将在 [TypeScript](https://blog.logrocket.com/whats-new-typescript-4-5/) 中检查并集和交集概念的定义和用法。

## 集合论中的并与交

集合论提供了我们大多数人在考虑并和交时使用的隐含概念。

下面是两组按升序排列的数字:`D = { 0, 1, 2, 3 }`和`B = { 1, 2, 3, 4, 5 }`。

下面的定义探究了什么是与上面定义的集合相关的并集和交集:

集合 d 和 b 的并集，记为 D ⋃ B，是包含在 d 或 b 或两者中的所有元素的集合。集合 D 和集合 B 的并集是，`{ 0, 1, 2, 3, 4, 5 }`。

集合 d 和集合 b 的交集，记为 D ⋂ B，是集合 d 和 b 中所有元素的集合。集合 d 和集合 b 的交集是，`{ 1, 2, 3 }`。

大多数试图理解上述概念的方法都来自集合论中的这个定义和用法。

## TypeScript 中的联合和交集类型

在 TypeScript 中，联合和交集类型用于从现有类型组合或建模类型。

这些新组合类型的行为不同，这取决于它们是通过现有类型的并集还是交集组合而成的。

在 Typescript 的 Advanced Types 部分中定义，交集类型是将几种类型合二为一的类型；可以是几种类型中任何一种的值都是联合类型。

`&`符号用于创建交集，而`|`符号用于表示并集。一个交集可以理解为一个`And`运算符，一个并集可以理解为一个`Or`。

让我们进一步探索这如何应用于我们的 TypeScript 代码。

使用计算机科学中的基本类型，类型的值就是它的实例。让我们来看一个假设的类型，`Char`，它不包含任何属性，但仍然表示一个值区间，如下所示:

```
char = { -128, ..., 127 } // Char type from -128 to 127
unsignedChar = { 0, ..., 255 } // UnsignedChar type from 0 to 255

```

以上两种类型的交集给了我们一种新的类型:

```
// typescript
type PositiveChar = Char & UnsignedChar // unsignedChar ∩ char <=> { 0, ..., 127 }

```

以上两种类型的结合给了我们另一种新的类型，它能够保存`string`和`number`:

```
// typescript

string // any char array 
number // any number 

type PrimitiveType = string | number

```

最常见的错误是认为类型的值是它的特征(属性)——更确切地说，类型的值是它的实例。

因此，交集的值少于其组件的值，并且可用于为其指定的任何函数中。

类似地，union 产生了一个新的、不太严格的、更加开放的类型——应该注意的是，您不能确定与这个新类型相关联的函数是否仍然有效。

现在，让我们继续上面的定义，看看如何在我们的 TypeScript 代码中实现并集和交集。

## 工会类型

您将希望编写一个函数，该函数期望参数是数字或字符串，并根据传递给它的参数执行函数代码的一部分。
以下面的函数为例:

```
function direction(param: any) {
  if (typeof param === "string") {
    ...
  }
  if (typeof param === "number") {
    ...
  }
  ...
}

```

在上面的代码中，我们期望一个类型为`string`或`number`的参数，这形成了一个新的类型，但是我们对上面的实现有一个问题。

使用类型`any`，我们最终可以传递不是`string`或`number`的类型；但是我们想严格地使函数只接受一个`string`或`number`类型。

在 union 的帮助下，我们可以重新实现上面的代码片段来专门接收一个`string`或`number`实例的值。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
function direction(param: string | number ) {
  ...
}

```

我们可以扩展上面的值类型，使其包含更原始的类型，如布尔值，如果我们传递的值实例超出了我们的联合类型定义，如`boolean`，代码会出现类似这样的错误消息:

```
Argument of type 'boolean' is not assignable to parameter of type 'string | number'.

```

### 歧视工会

为了帮助编译器，我们使用[区分联合](https://www.typescriptlang.org/docs/handbook/unions-and-intersections.html#discriminating-unions)进一步定义联合，也称为标记联合类型。区别并集是一种数据结构，用于保存可以采用不同固定类型的值。这些基本上是带有标签的联合类型。

为了将联合类型转换为有区别的联合类型，我们在我们的类型之间使用一个公共属性。该属性可以是任何名称，并将作为不同类型的 ID。对于该属性，每种类型都有不同的文字类型。

```
// TypeScript
type LoadingState = {
  state: "loading"; // tag
};
type FailedState = {
  state: "failed"; // tag
  status: number;
};
type SuccessState = {
  state: "success"; // tag
  response: {
    isLoaded: boolean;
  };
};

```

当我们使用上面的类型定义来构造一个新的类型，并在我们的代码中使用它来进行模式匹配时，我们可以很容易地知道我们试图访问的是我们类型的哪一部分。

```
type State = LoadingState | FailedState | SuccessState;

function request(state: State): string {
  switch (state.state) {
    case "loading":
      return "Uploading...";
    case "failed":
      return `Error status: ${state.status}, while Uploading`;
    case "success":
      return `Uploaded to cloud: ${state.response.isLoaded} `;
  }
}

```

仔细观察会发现我们是如何帮助编译器根据我们正在执行的代码部分做出决定的。

试图在`switch`之外调用`state.state.status`会引发警告，指出该类型上不存在该属性。然而，当在`case "failed"` switch 语句下执行`status`字段时，尝试访问它是安全的。

使用有区别的联合的一个好处是，可以为联合类型的所有区别提供类型保护。

理解交集的一个简单方法是错误处理。

假设您想要实现一个读写文件的类型。我们处理当我们试图读取或写入文件时可能发生的错误，因为这样，您强制这样的类型包含在读取和写入文件时处理错误或成功的类型。

```
interface ErrorHandling {
  success: boolean;
  error?: { message: string };
}

interface File {
  content: { lines: string }[];
}

type FileReader = File & ErrorHandling;

const writeToAFile = (response: FileReader) => {
  if (response.error) {
    console.error(response.error.message);
    return;
  }

  console.log(response.content);
};

```

交集类型只能包含其组件实例的子集，但是它们可以使用它们的任何功能。交集类型将多种类型合并为一种。

### 通过联合类型的条件交集

通过联合类型可以实现条件交集。在上面的例子中，我们使用了交集类型，它结合了`Files`和`ErrorHandling`类型，并将其分配给了`FilesReader`类型。现在，如果我们传递一个错误的类型，TypeScript 将抛出一个错误。

```
type FilesReader = Files & ErrorHandling;

FilesReader = {
  success: "false", // Type 'string' is not assignable to type 'boolean'
  error: {message: "not found"},
  content: "no file" // Type 'string' is not assignable to type '{ lines: string; }[]'
}

```

如果出于某种原因设置了一个既没有在`Files`类型中也没有在`ErrorHandling`类型中找到的类型的属性，TypeScript 编译器将抛出一个错误。为了防止这种情况，我们通过联合类型使用条件交集，如下所示:

```
type FilesReader = (Files & ErrorHandling) | any;

FilesReader = {
  success: "false",
  error: {message: "not found"},
  content: "no file"
}

```

现在，当我们为`FilesReader`的任何属性传递错误类型时(就像为`success`和`content`属性传递错误类型一样)，不会抛出错误，因为有条件地，我们已经将`FilesReader`类型设置为`any`。你可以在 TypeScript playground 上测试这个例子。

## 处理类型联合错误

作为一名 TypeScript 开发人员，在使用联合类型时，您会经常遇到“类型联合上不存在属性”错误。当您试图访问某个属性时，会发生此错误，该属性并不存在于联合类型的每个对象上。假设我们有两种类型，`Mail`和`Phone`，就像这样:

```
type Mail = {
  message: string
}

type Phone = {
  text: string
}

// trying to access the message property
const info = (obj: Mail | Phone): string => {
  if (obj.message) {
    return obj.message
  }

  return obj.text
}

```

当我们试图访问消息属性时，我们将得到如下错误:

```
// Property 'message' does not exist on type 'Mail | Phone'.

```

发生此错误是因为`message`属性并未出现在联合中的所有类型中，即`message`并未出现在`Phone`类型中。

我们通过使用类型保护来解决这个错误，以确保在访问对象之前该属性存在于对象上。一种方法是使用`[[in]](https://www.typescriptlang.org/docs/handbook/advanced-types.html#using-the-in-operator)`操作符来缩小类型，如下所示:

```
const info = (obj: Mail | Phone): string => {
  if ("message" in obj) {
    return obj.message
  }

  return obj.text
}

```

如果一个指定的属性在一个对象或者它的原型链中，`in`操作符返回`true`。这将允许 TypeScript 推断出`if`块中正确的对象类型。

## 结论

当试图理解 TypeScript 中的并集和交集时，这是一个很好的方法。

我们可以将许多类型组合成一个交集类型。具有交集类型的对象的结构必须包括构成交集类型的所有种类；它由各种类型通过一个`&`符号连接在一起组成。

联合类型产生了一种新的类型，它允许我们创建具有组成联合类型的每种类型的部分或全部属性的对象。管道符号`|`用于连接多个类型以形成联合类型，并允许我们创建一个继承联合类型结构的新类型。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.