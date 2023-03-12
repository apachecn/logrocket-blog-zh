# 在 Node.js - LogRocket 博客中使用 writeFileSync 方法

> 原文：<https://blog.logrocket.com/using-writefilesync-node-js/>

Node.js [文件系统模块](https://nodejs.org/dist/latest-v18.x/docs/api/fs.html#fswritefilesyncfile-data-options)提供了许多创建文件、写入文件和更新文件的方法。在这些方法中，有一种方法可以同时执行这三种功能:`writeFileSync`。

在本文中，我们将查看`writeFileSync`函数，并演示如何在 Node.js 中使用它。

## 为什么`writeFileSync`在 Node.js 中有用？

在我们开始实际演示`writeFileSync`函数之前，让我们看看为什么我们可能希望同步创建和写入文件。

像 Node 中的任何其他同步函数一样，`writeFileSync`将[阻塞事件循环](https://blog.logrocket.com/a-complete-guide-to-the-node-js-event-loop/)，直到操作完成或失败。换句话说，它阻塞任何其他语句的执行，直到其执行失败或完成。

例如，当一个`writeFileSync`函数被调用时，它后面的每一个其他语句都必须等待，直到该函数创建一个新文件或因没有创建新文件而抛出一个错误:

```
fs.writeFileSync('index.txt', 'Some content');
console.log('file created');

```

在上面的代码中，如果成功创建了`index.txt`文件，将执行下一行代码。但是如果失败，Node 将抛出一个错误，让您有机会捕捉错误。

阻塞事件循环或主线程对于现实世界的应用程序来说确实不是一个好的做法。在节点中，这被认为是一种不好的做法，因为它会降低性能并导致安全风险。

使用`writeFileSync`创建和写入文件只是出于调试目的，就像 Node 中的其他同步函数一样。因此，您应该使用[异步函数 writeFile](https://nodejs.org/dist/latest-v18.x/docs/api/fs.html#fswritefilefile-data-options-callback) 在现实世界的项目中创建和写入文件。

知道如何使用`writeFileSync`函数可以帮助你轻松上手`writeFile`函数，因为它们有相似的参数。它们工作方式的唯一区别是捕捉和处理错误。

`writeFile`有一个回调函数，当错误发生时，它返回一个`err`参数。然而，在`writeFileSync`中，您必须用`try``…``catch`语句手动捕捉错误，我们将在本文后面看到。

## 如何在 Node.js 中使用`writeFileSync`

`writeFileSync`函数是一个非常简单的`fs`方法。它接受三个参数，根据这些参数创建和写入文件:

*   文件名或描述符
*   要写入文件的数据
*   Options:可用于指定三个附加可选参数的字符串或对象

在这三个参数中，只有两个是必需的。“options”参数是可选的。在下面的例子中，文件名是`index.txt`，要写入文件的数据是`Hello World!`:

```
const fs = require('fs');

fs.writeFileSync('index.txt', 'Hello World!');
console.log('File created');

```

`writeFileSync`不仅创建文件，还可以覆盖或附加到任何现有文件上的数据。让我们仔细看看这个函数中使用的参数。

### 文件名参数

您可以在 Node 中使用 writeFileSync 创建任何文件类型，包括文本文件、HTML 文件、JavaScript 文件、Markdown 文件、Python 文件等。—只要您使用正确的扩展名书写文件名。举个例子，

```
fs.writeFileSync('index.txt', 'Hello World!');

```

上面例子中的`index.txt`文件将在你当前所在的目录中创建。您可以指定某个其他目录的路径，如下所示:

```
fs.writeFileSync('notes/index.txt', 'Hello World!');

```

***注意，如果* `notes` *目录不存在，*** *节点会抛出错误；这是因为* `writeFileSync` *方法无法在* [*节点*](https://nodejs.org/dist/latest-v18.x/docs/api/fs.html#fsmkdirsyncpath-options) 中创建目录

### 数据参数

到目前为止，我们在所有示例中只使用了字符串作为数据，但是在现实世界的项目中，您可能会处理字符串以外的数据。在节点中使用 [`Buffer`类在开发人员中很常见，所以让我们来看看。](https://blog.logrocket.com/node-js-buffer-a-complete-guide/)

在 Node 中，`Buffer`类是一个用于直接处理二进制数据的全局类型。为任何数据构造新的`Buffer`的最简单方法是分配特定大小的字节，如下所示:

```
const fs = require('fs');
const rawData = 'Hello World';
const data = Buffer.alloc(rawData.length, rawData, 'utf8');
fs.writeFileSync('index.txt', data);

```

`Buffer.alloc`方法的第一个参数代表字节的大小。在上面的例子中，我们使用了字符串的长度。

使用字符串的长度并不总是安全的，因为这个数字不考虑用于将字符串转换为字节的编码。相反，我们可以使用另一种`Buffer`方法，就像这样:

```
const rawData = 'Hello World';
const data = Buffer.alloc(Buffer.byteLength(rawData, 'utf8'), rawData, 'utf8');

```

注意，字符串的默认编码是`utf8`，所以我们可以安全地删除这两种方法的编码:

```
const rawData = 'Hello World';
const data = Buffer.alloc(Buffer.byteLength(rawData), rawData);
console.log(data); // <Buffer 48 65 6c 6c 6f 20 57 6f 72 6c 64>
console.log(data.toString()) // Hello World

```

包含文本的`Buffer`类可以用`toString`方法转换回字符串。如果你只是处理字符串，一个更简单的方法是使用`Buffer.from(string, encoding)`方法:

```
const rawData = 'Hello World';
const data = Buffer.from(rawData, 'utf8');
console.log(data); // <Buffer 48 65 6c 6c 6f 20 57 6f 72 6c 64>
console.log(data.toString()) // Hello World

```

使用`Buffer.from`方法，您可以轻松地对字符串、数组或缓冲区进行编码，而不必担心指定字节的大小，就像在`Buffer.alloc`的情况下一样。`Buffer.from`方法的默认编码也是`utf8`。

使用`Buffer.from`方法对数组进行编码就像对字符串进行编码一样简单。它可以接受一个整数数组(特别是范围从 0-255 的 [Uint8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) )或一个八位字节数组。超过 255 或小于 0 的条目将被截断以适应:

```
const buf = Buffer.from([1, 2, 3, 4, 5]);
console.log(buf); // <Buffer 01 02 03 04 05>
console.log(buf.at(-1)) // 5

// creates a new buffer from utf8 string 'john'
console.log(Buffer.from([0x6a, 0x6f, 0x68, 0x6e]));

```

在某些情况下，您可能有一个需要转换为二进制数据的对象数组。将数组传递给`Buffer.from`方法是行不通的。相反，您可以将数组转换成 JSON 字符串，如下所示:

```
const users = [
  { id: 1, name: 'John', age: 12 },
  { id: 2, name: 'Mary', age: 9 },
];

console.log(Buffer.from(JSON.stringify(users)).toString()); // [{"id":1,"name":"John","age":12},{"id":2,"name":"Mary","age":9}]

```

### 选项参数

`writeFileSync`方法的最后一个参数是一个具有三个可选属性的对象:

`encoding`属性的默认值为`utf8`。在`writeFileSync`方法中用作数据的`Buffer`类的编码将覆盖这里的编码。

如果您为数据指定了一个`utf8`编码，并且在这里错误地指定了`base64`编码，`utf8`将覆盖`base64`编码。所以如果你经常使用`Buffer`类来编码你的数据，你不需要在这里为`encoding`属性指定任何值。

这里有一个例子:

```
const rawData = 'Hello World';
const data = Buffer.from(rawData, 'utf8');
fs.writeFileSync('index.txt', data, { encoding: 'base64' }) // utf8 will override this encoding
const txtFile = fs.readFileSync('index.txt')
console.log(txtFile.toString()) // Hello World

```

`mode`属性设置文件模式(权限和粘滞位)，它只对新创建的文件有效。默认模式是`0o666`。

属性控制如何创建和写入文件。默认标志是`w`，它创建文件(如果文件不存在)或者用新数据覆盖文件中的任何数据(如果文件已经存在)。

其他标志如下:

*   `a`:创建文件(如果不存在)或附加到现有数据(如果存在)
*   `ax`和`wx`:创建文件(如果不存在)或抛出错误(如果已经存在)

这里是节点中标志的完整列表。

## 在 Node.js 中使用 writeFileSync 时捕获错误

为了捕捉错误，我们使用了`try...catch`语句。为了展示这个例子，我们必须创建一个自定义错误。假设我们已经有了一个文件，比如一个`index.txt`文件，我们可以说:

```
const fs = require('fs');

try {
  const rawData = 'Hello World';
  const data = Buffer.from(rawData);
  fs.writeFileSync('index.txt', data, { flag: 'ax' });
} catch (e) {
  console.log(e); // will log an error because file already exists
}

```

这将抛出一个错误，因为`ax`标志只创建新文件；它不能追加到现有文件中。所以，如果你试图用`ax`或`wx`标志写一个已有的文件，你会得到一个错误。

## 读取 Node.js 中的简单文件

读取 Node 中的文件可以同步进行，也可以异步进行，分别是`readFileSync`和`readFile`。如前所述，在 Node.js 中使用同步函数仅推荐用于开发。

就像写文件一样，`readFileSync`和`readFile`的区别在于如何处理错误。`readFileSync`方法需要手动处理，而`readFile`使用回调来处理并返回任何错误。

这里有一个使用`readFileSync`的例子:

```
const fs = require('fs');

try {
  const file = fs.readFileSync('notes/index.txt'); // pass in the path to the file
  console.log(file.toString()) // it returns a buffer, convert it back to string
} catch (e) {
  console.log(e) // logs any error encountered with reading the file
}

```

这里有一个使用`readFile`的例子:

```
const fs = require('fs');

fs.readFile('notes/index.txt', (err, data) => {
  if (err) console.log(err);

  console.log(data.toString());
})

```

## 基于用户输入创建新文件

假设我们只想创建新文件，写入它们，然后继续。例如，我们可能有一个应用程序，我们希望为来自终端的每个新用户输入创建一个文件，每个文件都有一个唯一的标识符——用户名。

根据有关应用功能的信息，我们需要:

1.  为每个用户创建一个新文件，将他们的用户名作为唯一标识符
2.  如果给定的用户名已经存在，提醒用户尝试不同的用户名

这样，我们的应用程序代码将如下所示:

```
const fs = require('fs');
const readline = require('readline');
const { stdin: input, stdout: output } = require('process');
const rl = readline.createInterface({ input, output });

// input username
function requestUsername() {
  rl.question('Enter username: ', (username) => {
    try {
      if (!username) return requestUsername();
      const data = Buffer.from(`Your username is ${username}`);
      fs.writeFileSync(`${username}.txt`, data, { flag: 'ax' });
    } catch (e) {
      if (e.code === 'EEXIST') {
        console.log(`${username} already exists, enter a different username`);
        return requestUsername();
      }
    }
    rl.close();
  });
}
requestUsername();

```

不要担心所有额外的细节；这里真正的要点是`ax`标志和`try...catch`语句。`ax`标志帮助我们判断用户名是否已经存在，在这种情况下`try...catch`语句帮助提醒用户。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 用`writeFileSync`更新 Node.js 中的文件

显然，我们不能使用`w`、`ax`或`wx`标志来更新文件，但是我们可以使用`a`标志。如前所述,`a`标志不仅附加到一个文件中，如果该文件不存在，它还会创建该文件。

扩展我们前面的例子，假设我们想要添加一个用户的数据，而不为该用户创建一个新文件。我们将编写以下代码:

```
const fs = require('fs');
const readline = require('readline/promises');
const { stdin: input, stdout: output } = require('process');
const rl = readline.createInterface({ input, output });

async function getUsername() {
  const username = await rl.question('Enter username: ');
  if (!username) return getUsername();

  try {
    fs.readFileSync(`${username}.txt`);
    return username;
  } catch (e) {
    if (e.code === 'ENOENT') {
      console.log(
        `Username "${username}" does not exist, try a different username`
      );
      return getUsername();
    }
  }
}
async function updateUserInfo() {
  const username = await getUsername();
  const rawData = await rl.question('Enter user info (name|age|course): ');
  const data = Buffer.from(`\n${rawData}\n`);
  fs.writeFileSync(`${username}.txt`, data, { flag: 'a' });
  rl.close();
}
updateUserInfo();

```

在上面的代码中，我们提示用户输入用户名，并检查是否存在该用户的文件。如果文件确实存在，我们会向该用户请求更多信息，然后更新该用户的文件。很简单。

有一个类似的标志`r+`，如果文件不存在，它会抛出一个错误，但是如果文件存在，它会读写该文件。然而，这个标志并不适合我们这里所需要的，因为它覆盖了文件中的所有数据，而不是像`a`标志那样附加到文件中。

## 结论

在本文中，我们学习了如何使用`writeFileSync`用 Node 创建和写入文件，这对调试很有用。

我们看到了如何使用两种常用方法通过`Buffer`类传递数据，如何使用标志控制文件的创建，以及如何捕捉错误。我们还学习了如何使用`readFile`和`readFileSync`来读取一个简单的文件，这在编写文件时会很方便。

根据目前所学，您也可以轻松开始使用`writeFile`(异步创建文件)。`writeFile`和`writeFileSync`的唯一区别在于捕捉和处理错误；否则，提到的所有参数在两个函数中都可用。

祝你黑客生涯愉快，感谢你的阅读。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.