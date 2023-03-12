# 更安全的容器类型代码(非此即彼)

> 原文：<https://blog.logrocket.com/safer-code-with-container-types-either-and-maybe/>

### 可能数据类型

计算机科学只有两个硬东西:`null`和`undefined`。

好吧，[菲尔·卡尔顿](https://martinfowler.com/bliki/TwoHardThings.html)没有确切地说出那些话，但我们都同意，处理`null`、`undefined`和空性的概念通常是困难的，对吗？

### 价值的缺失

每次我们用类型注释一个变量时，这个变量可以保存被注释类型的值，`null`或`undefined`(有时甚至是`NaN`！).

这意味着，为了避免类似`Cannot read property 'XYZ' of undefined`的错误，我们必须记住每次使用它时都要始终如一地应用防御性编程技术。

上述事实的另一个棘手的方面是，从语义上讲，`null`和`undefined`应该用来做什么非常令人困惑。它们对不同的人有不同的含义。API 也不一致地使用它们。

### 什么会出错？

即使您应用了防御性编程技术，事情也可能出错。有各种各样的方法可能导致假阴性。

在这个例子中，数字`0`将永远找不到，因为`0`是 *falsy* ，就像`undefined`一样。当查找操作不匹配任何内容时的`Array.find()`结果。

```
const numToFind = 0;
const theNum = [0, 1, 2, 3].find(n => n === numToFind);
if (theNum) {
  console.log(`${theNum} was found`);
} else {
  console.log(`${theNum} was not found`);
}
```

可悲的是，防御性编程(即`if`语句背后的`undefined` / `null`检查)也是常见的错误来源。

### `Maybe`去营救

如果我们能够在编译器的帮助下，并且没有假阴性的情况下，始终如一地处理空，这不是很好吗？

已经有一些东西可以做到这一切:它是`Maybe`数据类型(也称为`Option`)。

`Maybe`概括了可能不存在的价值的概念。

一个`Maybe`值可以是`Just`某个值或者是`Nothing`。

```
type Maybe<T> = Just<T> | Nothing;
```

我们经常把这种类型称为*容器类型*，因为它们的唯一目的是赋予它们所保存的值语义上的含义，并允许你以一种安全的方式对其执行特定的操作。

我们将使用 [ts.data.maybe](https://github.com/joanllenas/ts.data.maybe) 库。让我们熟悉一下它的 API。

我们的应用程序有一个`User`类型:

```
interface User {
  id: number;
  nickname: string;
  email: string;
  bio: string;
  dob: string;
}
```

我们还向 REST API 发出一个`/users`请求，该请求返回以下有效负载:

```
[
  (...)
  {
    "id": 1234,
    "nickname": "picklerick",
    "email": "[email protected]",
    "bio": null
  }
  (...)
]
```

在这一点上，如果我们用`User[]`注释这个有效载荷，那么在我们的代码库中会发生很多不好的事情，因为`User`在撒谎。我们期待`bio`和`dob`永远是一个`string`，但在这种情况下，一个是`null`，一个是`undefined`。

有可能出现运行时错误。

### 用`Maybe`键入注释

让我们用`Maybe`来解决这个问题。

```
import { Maybe } from "ts.data.maybe";

interface User {
  id: number;
  nickname: string;
  email: string;
  bio: Maybe<string>;
  dob: Maybe<Date>;
}
```

一旦你把`Maybe`加到等式中，没有什么是隐含的了。没有办法绕过类型声明——编译器总是强迫你将`bio`和`dob`视为`Maybe`。

### 创建`Maybe`实例

好的，但是我们如何使用这个？

让我们为 API 结果创建一个`User`解析器。

为此，我们将创建一个仅由解析器使用的`UserJson`类型，表示我们从服务器获取的内容，以及另一个表示我们的域模型的`User`类型。我们将在整个应用程序中使用这种类型。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
import { Maybe, just, nothing } from 'ts.data.maybe';

interface User {
  id: number;
  nickname: string;
  email: string;
  bio: Maybe<string>;
  dob: Maybe<Date>;
}

interface UserJson {
  id: number;
  nickname: string;
  email: string;
  bio?: string | null;
  dob?: string | null;
}

const userParser = (json: UserJson): User => ({
  id: json.id,
  nickname: json.nickname,
  email: json.email,
  bio:
    json.bio === null || json.bio === undefined
      ? nothing()
      : just(json.bio),
  dob:
    json.dob === null || json.dob === undefined || json.dob === ''
      ? nothing()
      : just(new Date(json.dob))
});
```

如您所见，要创建一个`Maybe`实例，您必须使用一个可用的构造函数:
–`just<T>(value: T): Maybe<T>`–
–`nothing<T>(): Maybe<T>`

注意我们是如何决定为`bio`和`dob`(出生日期)定义不同的空。

我们可以用一个空字符串来表示`bio`——这没什么错。

然而，我们不能用空字符串来表示`Date`。这就是解析器区别对待它们的原因，尽管来自服务器的数据对两者来说都是一个`string`。

### 从`Maybe`中提取数值

现在我们已经成功地声明并创建了`Maybe`实例，让我们看看如何在我们的逻辑中使用它们。

我们计划创建一个从服务器获取的用户列表的 html 表示，我们将用卡片来表示他们。

这是我们将用来为卡片生成 Html 标记的函数:

```
const userCard = (user: User) => `<div class="card">
  <h2>${user.nickname}</h2>
  <p>${userBio(user.bio)}</p>
  <ul>
    <li>${user.email}</li>
    <li>${userDob(user.dob)}</li>
  </ul>
</div>`;
```

到目前为止没什么特别的，但是让我们看看从`Maybe`中提取值的两个函数是什么样子的:

### `userBio()`

```
const userBio = (maybeBio: Maybe<string>) =>
  withDefault(maybeBio, '404 bio not found');
```

这里，我们引入了一个新的`Maybe`API:`withDefault()`函数(在其他`Maybe`实现中也称为`getOrElse()`)。

```
withDefault<A>(value: Maybe<A>, defaultValue: A): A
```

该函数用于从`Maybe`实例中提取值。

如果`Maybe`实例是`Nothing`，那么将返回默认值——在本例中是`404 bio not found`。如果实例是一个`Just`，它将展开并返回它包含的字符串值。

即
`withDefault(just(5), 0)`会返回`5`。
`withDefault(nothing(), 'This is empty')`将返回`This is empty`。

### `userDob()`

```
const userDob = (maybeDate: Maybe<Date>) =>
  caseOf(
    {
      Nothing: () => 'Date not provided',
      Just: date => date.toLocaleDateString()
    },
    maybeDate
  );
```

这里，我们介绍另一个新的`Maybe`API:`caseOf()`函数。

`caseOf<A, B>(caseof: {Just: (v: A) => B; Nothing: () => B;}, value: Maybe<A>): B`

在`userDob`函数中，我们不想使用`withDefault`,因为我们需要在返回提取的值之前对其执行一些逻辑运算，而这正是`caseOf()`函数的用处。

这个函数给你一个在返回值之前进行计算的机会。

### `Maybe`-验证现有的 API

完成我们的应用程序还需要做最后一件事:我们需要呈现我们的用户卡。

渲染逻辑涉及到处理 DOM APIs，我们需要得到一个对我们想要插入 Html 标记的`div`元素的引用，我们将使用`getElementById(elementId: string): null | HTMLElement`函数。

在我们新获得的避免`null`和`undefined`的困扰中，我们决定创建这个函数的*可能化*版本以避免处理`null`。

```
const maybeGetElementById = (id: string): Maybe<HTMLElement> => {
  const elem = document.getElementById(id);
  return elem === null ? nothing() : just(elem);
};
```

现在，编译器不允许我们将结果视为一个`HTMLElement`，而实际上，如果我们要寻找的`div`不在我们的页面中，结果可能完全是`null`。`Maybe`我们已经准备好了。

让我们使用这个函数并呈现这些用户卡:

```
const maybeAppDiv: Maybe<HTMLElement> = maybeGetElementById('app');

caseOf(
  {
    Just: appDiv => {
      appDiv.innerHTML = '<h1>Users</h1>' + usersJson
        .map(userJson => userCard(userParser(userJson)))
        .join('<br>');
      return;
    },
    Nothing: () => {
      document.body.innerHTML = 'App div not found';
      return;
    }
  },
  maybeAppDiv
);
```

你可以在这里玩这个例子[的代码。](https://stackblitz.com/edit/typescript-nt8mej)

我们已经看到了一些可用的 API。您可以使用这种数据类型做更多的事情。

请访问 [ts.data.maybe](https://github.com/joanllenas/ts.data.maybe) docs 页面了解更多信息。

### `Either`数据类型

错误是软件开发的重要组成部分，忽略它们，你的程序将无法满足用户的期望。

### 定义失败

和往常一样，语义是基础，一致地定义失败对于让我们的程序更容易推理是至关重要的。

那么，到底是什么定义了失败？让我们看一些你能找到的错误的例子:

*   引发运行时异常的操作。
*   返回一个`Error`实例的操作。
*   返回`null`的操作。
*   返回包含`{error: true}`的对象的操作。
*   当我们到达`Promise`.`` 中的`catch()`子句时

大多数时候，您将通过用`if`和`try catch`语句分支您的逻辑来处理错误。

由于需要定义嵌套深度和中间变量来将最终结果从代码的一个点传送到另一个点，结果代码很快就会变得混乱。

### `Either`去营救

如果我们能够抽象出所有那些`if`和`try catch`语句，并减少需要定义的中间变量的数量，这不是很好吗？

已经有一些东西可以做到这一切:它是`Either`数据类型(也称为`Result`)。
`Either`概括了一个可能失败的计算的想法。

一个`Either`值可以是某个值的`Right`或者某个误差的`Left`。

```
type Either<T> = Right<T> | Left;
```

看起来很眼熟，对吧？它非常类似于`Maybe`类型的签名，尽管你马上就会看到它们的不同之处。

我们将使用 [ts.data .要么](https://github.com/joanllenas/ts.data.either)库。让我们熟悉一下它的 API。

### `Either`候选者

这次我们将创建一个`getUserById`服务，从 json 文件中按 id 搜索用户。
该服务执行以下操作:
1 .验证 Json 文件名是否有效。
2。读取 Json 文件。
3。将 Json 解析成对象图。
4。在数组中查找用户。
5。退货。

如你所见，每一步都有失败的可能。这很好，因为我们将使用`Either`来控制错误。

### 我们示例的一些实用程序

让我们创建一些我们的示例赖以工作的东西。

首先，我们将重用上一个示例中的`UserJson`类型:

```
export interface UserJson {
  id: number;
  nickname: string;
  email: string;
  bio?: string | null;
  dob?: string | null;
}
```

我们还需要一个(虚拟)文件系统。

```
const fileSystem: { [key: string]: string } = {
  "something.json": `
        [
          {
            "id": 1,
            "nickname": "rick",
            "email": "[email protected]",
            "bio": "Rick Sanchez of Earth Dimension C-137",
            "dob": "3139-03-04T23:00:00.000Z"
          },
          {
            "id": 2,
            "nickname": "morty",
            "email": "[email protected]",
            "bio": null,
            "dob": "2005-04-08T22:00:00.000Z"
          }
        ]`
};
```

我们的虚拟文件系统需要一个`readFile(filename: string): string;`函数，如果找到文件，它将文件内容作为字符串返回，否则抛出异常。

```
const readFile = (filename: string): string => {
  const fileContents = fileSystem[filename];
  if (fileContents === undefined) {
    throw new Error(`${filename} does not exists.`);
  }
  return fileContents;
};
```

最后，一个(快速且肮脏的)`pipeline`函数实现，它将允许我们使我们的函数调用流类似于流畅的 API 所做的:

> 有一些库以一种类型安全的方式做着同样的事情，但是我不想包含另一个依赖项。
> 并且已经有一个[原生 JavaScript 管道 API 实现](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Pipeline_operator)在工作中！

```
export const pipeline = (initialValue: any, ...fns: Function[]) =>
  fns.reduce((acc, fn) => fn(acc), initialValue);
```

因此，不要像这样调用多个函数:

```
add1( add1( add1( 5 ) ) ); // 8
```

我们可以这样做:

```
pipeline(
    5,
    n => add1(n), // we could go point-free and just use `add1`
    n => add1(n),
    n => add1(n)
); // 8
```

### `Either`构成

事实上，我们的功能是一系列动作，其中下一个动作取决于前一个动作的结果。

每一步都做了一些可能失败的事情，并将结果传递给下一步，正因为如此，表示每一步的最好方式是使用返回`Either`的函数。

### 1.验证 Json 文件名

```
const validateJsonFilename = (filename: string): Either<string> =>
    filename.endsWith(".json")
      ? right(filename)
      : left(new Error(`${filename} is not a valid json file.`));
```

这里我们介绍一下`Left`和`Right`构造函数:

```
left(error: Error): Either;
```

```
right(value: T): Either;
```

逻辑很简单(也很天真):如果文件没有`.json`扩展名，我们返回一个`Left`，这意味着有一个错误，否则返回一个带有文件名的`Right`。

### 2.读取 Json 文件

```
const readFileContent = (filename: string): Either<string> =>
    tryCatch(() => readFile(filename), err => err);
```

正如我们之前看到的，如果没有找到文件，`readFile`函数会抛出一个异常。
控制运行时错误`Either`具有`tryCatch`功能:

```
tryCatch<A>(f: () => A, onError: (e: Error) => Error): Either<A>
```

这个函数包装了可能抛出并返回一个`Either`实例的逻辑。

`tryCatch`接受两个函数参数，一个在成功时执行，另一个在失败时执行。

如果成功，结果被打包在一个`Right`中返回，如果失败，从源函数生成的错误被传递给错误处理程序，结果被打包在一个`Left`中返回。

### 3.解析 Json

```
const parseJson = (json: string): Either<UserJson[]> =>
    tryCatch(
      () => JSON.parse(json),
      err => new Error(`There was an error parsing this Json.`)
    );
```

这里没有什么新东西，我们使用`tryCatch`是因为`JSON.parse`抛出失败。

### 4.在数组中查找用户

在所有这些错误处理之后，是时候搜索用户了，但是让我们想想，如果提供的`id`与任何用户都不匹配，我们应该返回`null`、`undefined`还是`Left`呢？哦！还记得`Maybe`吗？让我们也在这里使用它！

```
const findUserById = (users: UserJson[]): Either<Maybe<UserJson>> => {
  return pipeline(
    users.find(user => user.id === id),
    (user: UserJson) =>
      user === undefined ? nothing<UserJson>() : just(user),
    (user: Maybe<UserJson>) => right(user)
  );
};
```

哇，看那个回报。

```
Type signature Either<Maybe<UserJson>>
```

如此少的字符包含了如此多的内容……让我们来回顾一下:
-`Either`->包含一个值或一个错误。
-`Maybe`->包含有或无。
-`UserJson`->包含一个`UserJson`。
因此，只需阅读签名`findUserById(users: UserJson[]): Either<Maybe<UserJson>>;`，你就可以确定`findUserById`是一个可能已经失败的操作的一部分(`Either`)，并返回一个可以为空的`UserJson`(`Nothing`)。不是一个小壮举！

### 5.返回值

至此，我们拥有了声明我们的`getUserById`服务所需的所有要素。让我们把它们放在一起。

```
const getUserById = (filename: string, id: number): Either<Maybe<UserJson>> => {
  const validateJsonFilename = (filename: string): Either<string> =>
    filename.endsWith(".json")
      ? right(filename)
      : left(new Error(`${filename} is not a valid json file.`));

  const readFileContent = (filename: string): Either<string> =>
    tryCatch(() => readFile(filename), err => err);

  const parseJson = (json: string): Either<UserJson[]> =>
    tryCatch(
      () => JSON.parse(json),
      err => new Error(`There was an error parsing this Json.`)
    );

  const findUserById = (users: UserJson[]): Either<Maybe<UserJson>> => {
    return pipeline(
      users.find(user => user.id === id),
      (user: UserJson) =>
        user === undefined ? nothing<UserJson>() : just(user),
      (user: Maybe<UserJson>) => right(user)
    );
  };

  return pipeline(
    filename,
    (fname: string) => validateJsonFilename(fname),
    (fname: Either<string>) => andThen(readFileContent, fname),
    (json: Either<string>) => andThen(parseJson, json),
    (users: Either<UserJson[]>) => andThen(findUserById, users)
  );
};
```

这个函数对我们在前面四个步骤中所做的唯一补充是将它们全部组合在一个管道中，由于我们刚刚引入的这个新的`Either` Api，`andThen`函数，每个操作都将其结果`Either`提供给下一个操作:

```
andThen<A, B>(f: (a: A) => Either<B>, value: Either<A>): Either<B>
```

这个函数基本上是这样说的:
—给我一个`Either`，我会用这个返回`Either`的函数返回另一个`Either`，你也必须提供这个函数。

这个函数`pipeline`的流程如下:
1。提供初始值。
2。执行这个函数，如果失败，在一个`Left`中返回错误，否则在一个`Right`中返回结果值。
3。如果我们从前面的函数中得到一个`Left`返回那个`Left`，否则执行这个函数，如果失败，返回一个`Left`中的错误，否则，返回一个`Right`中的结果值。
4。如果我们从前面的函数中得到一个`Left`返回那个`Left`，否则执行这个函数，如果失败，返回一个`Left`中的错误，否则，返回一个`Right`中的结果值。
5。如果我们从前面的函数中得到一个`Left`返回那个`Left`，否则执行这个函数，如果失败，返回一个`Left`中的错误，否则，返回一个`Right`中的结果值。

你注意到步骤 3、4 和 5 是一样的吗？这对于该管道可能具有的所有中间操作都是正确的。一旦你有了想法，一切都会水到渠成。

### 使用我们的`getUserById`服务

我们的服务返回一个埋了两层的`UserJson`，一层是`Either`，另一层是`Maybe`。让我们从容器类型中提取这些有价值的信息。
`printUser`函数从`Maybe`中提取`UserJson`。

```
const printUser = (maybeUser: Maybe<UserJson>) =>
  maybeCaseOf(
    {
      Nothing: () => "User not found",
      Just: user => `${user.nickname}<${user.email}>`
    },
    maybeUser
  );
```

> 这里，`maybeCaseOf`是一个别名，因为`Either`和`Maybe`都有一个我们在同一个源文件中使用的函数`caseOf`。
> 您可以创建一个别名来导入函数，如下所示:`import { caseOf as maybeCaseOf } from "ts.data.maybe";`

最后！让我们把一切联系起来:

```
console.log(
    caseOf(
      {
        Right: user => printUser(user),
        Left: err => `Error: ${err.message}`
      },
      getUserById("something.json", 1)
    )
); // rick<[email protected]>

console.log(
    caseOf(
      {
        Right: user => printUser(user),
        Left: err => `Error: ${err.message}`
      },
      getUserById("something.json", 444)
    )
); // User not found

console.log(
    caseOf(
      {
        Right: user => printUser(user),
        Left: err => `Error: ${err.message}`
      },
      getUserById("nothing.json", 2)
    )
); // Error: nothing.json does not exists.

console.log(
    caseOf(
      {
        Right: user => printUser(user),
        Left: err => `Error: ${err.message}`
      },
      getUserById("noExtension", 2)
    )
); // Error: noExtension is not a valid json file.
```

你可以在这里玩这个例子[的代码。](https://stackblitz.com/edit/typescript-ahjaw5)

我们已经看到了一些可用的`Either`API，您可以使用这种数据类型做更多的事情。
访问 [ts.data .要么](https://github.com/joanllenas/ts.data.either) docs 页面了解更多信息。

### 结论

我们已经知道容器类型是提供 API 的值的包装器，所以我们可以安全地操作它们。

`Maybe`容器类型明确了空的概念，而不是依赖低劣的语义和容易出错的选择`null`和`undefined`,我们有这个包装器，它有一个明确定义的 API 和语义含义。

`Either`容器类型封装了失败的概念，并为在`if`和`try catch`语句中分支我们的代码提供了一个替代方案。

这种类型公开的定义明确的可组合 API 感染了我们的程序，使它们更具功能性、更干净、更易于阅读和推理。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.