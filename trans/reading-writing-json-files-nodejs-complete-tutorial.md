# 在 Node.js 中读写 JSON 文件:完整教程- LogRocket 博客

> 原文：<https://blog.logrocket.com/reading-writing-json-files-nodejs-complete-tutorial/>

## JSON 简介

JavaScript Object Notation，简称 JSON，是互联网上最流行的数据存储和数据交换格式之一。JSON 语法的简单性使得人类和机器很容易读写。

尽管名字如此，JSON 数据格式的使用并不局限于 JavaScript。大多数编程语言都实现了可以轻松转换成 JSON 的数据结构，反之亦然。

JavaScript 以及 Node.js 运行时环境也不例外。通常情况下，这些 JSON 数据需要从一个文件中读取或写入到一个文件中，以实现持久性。节点运行时环境有内置的`fs`模块，专门用于处理文件。

本文是关于如何使用内置的`fs`模块读写 JSON 格式数据的综合指南。我们还将了解一些第三方 npm 包，它们简化了 JSON 格式数据的处理。

## 序列化和反序列化 JSON

序列化是将对象或数据结构修改为易于存储或通过互联网传输的格式的过程。您可以通过应用相反的过程来恢复序列化数据。

反序列化是指将序列化的数据结构转换为其原始格式。

您几乎总是需要将 JSON 或 JavaScript 对象序列化为 Node 中的 JSON 字符串。在将其写入存储设备或通过互联网传输之前，您可以使用`JSON.stringify`方法:

```
const config = { ip: '1234.22.11', port: 3000};
console.log(JSON.stringify(config));

```

另一方面，在读取 JSON 文件之后，您需要在访问或操作数据之前使用`JSON.parse`方法将 JSON 字符串反序列化为普通的 JavaScript 对象:

```
const config = JSON.stringify({ ip: '1234.22.11', port: 3000});
console.log(JSON.parse(config));

```

`JSON.stringify`和`JSON.parse`是节点中全局可用的方法。在使用之前，您不需要安装或要求。

## `fs`模块介绍

因为`fs`模块是内置的，所以不需要安装。它提供了一些函数，可以用来读写 JSON 格式的数据，等等。

由`fs`模块公开的每个函数都有同步、回调和基于承诺的形式。方法的同步和回调变量可以从同步和回调 API 中访问。可以从基于承诺的 API 访问函数的基于承诺的变体。

### 同步 API

内置`fs`模块的同步方法阻塞事件循环和剩余代码的进一步执行，直到操作成功或失败。通常情况下，阻塞事件循环并不是您想要做的事情。

所有同步功能的名称都以`Sync`字符结尾。例如，`writeFileSync`和`readFileSync`都是同步函数。

您可以通过要求`fs`来访问同步 API:

```
const fs = require('fs');

// Blocks the event loop
fs.readFileSync(path, options);

```

### 回调 API

与阻塞事件循环的同步方法不同，回调 API 的相应方法是异步的。您将把一个回调函数作为最后一个参数传递给该方法。

如果出现错误，回调函数将调用一个`Error`对象作为第一个参数。回调函数的其余参数取决于`fs`方法。

您也可以像同步 API 一样通过要求`fs`来访问回调 API 的方法:

```
const fs = require('fs');

fs.readFile(path, options, callback);

```

### 基于承诺的 API

基于承诺的 API 是异步的，就像回调 API 一样。它返回一个承诺，您可以通过承诺链或异步等待来管理它。

您可以通过要求`fs/promises`来访问基于承诺的 API:

```
const fs = require('fs/promises');

fs.readFile(path)
  .then((data) => {
    // Do something with the data
  })
  .catch((error) => {
    // Do something if error 
  });

```

我们使用了 commonJS 语法来访问上面代码片段中的模块。我们将在整篇文章中使用 commonJS 语法，因为 Node 默认将 JavaScript 代码视为 commonJS 模块。如果您愿意，也可以使用 ES6 模块。

根据节点文档，内置`fs`模块的回调 API 比基于 promise 的 API 性能更好。因此，本文中的大多数示例将使用回调 API。

## 如何读取 Node.js 中的 JSON 文件

节点运行时环境有内置的`require`函数和`fs`模块，您可以使用它们来加载或读取 JSON 文件。因为`require`是全球通用的，你不需要要求它。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

但是，在使用之前，您需要使用`fs`模块。我将在下面的小节中讨论如何使用内置的`fs`模块和`require`函数读取 JSON 文件。

### 如何使用全局`require`函数加载 JSON 文件

可以使用全局`require`函数同步加载 Node 中的 JSON 文件。使用`require`加载文件后，文件被缓存。因此，使用`require`再次加载文件将会加载缓存的版本。在服务器环境中，该文件将在下一次服务器重启时再次加载。

因此，建议使用`require`加载静态 JSON 文件，比如不经常改变的配置文件。如果你加载的 JSON 文件不断变化，就不要使用`require`，因为它会缓存加载的文件，如果你再次需要同一个文件，就使用缓存的版本。您最近的更改将不会被反映出来。

假设您有一个包含以下内容的`config.json`文件:

```
{
    "port": "3000",
    "ip": "127.00.12.3"
}

```

您可以使用下面的代码在 JavaScript 文件中加载`config.json`文件。`require`总是将 JSON 数据作为 JavaScript 对象加载:

```
const config = require('./config.json');
console.log(config);

```

### 如何使用`fs.readFile`方法读取 JSON 文件

可以使用`readFile`方法读取 JSON 文件。它异步读取内存中整个文件的内容，因此它不是读取大型 JSON 文件的最佳方法。

`readFile`方法有三个参数。下面的代码片段显示了它的函数签名:

```
fs.readFile(path, options, callback);

```

第一个参数`path`是文件名或文件描述符。第二个是可选的对象参数，第三个是一个`callback`函数。也可以将字符串而不是对象作为第二个参数传递。如果你传递一个字符串，那么它必须被编码。

`callback`函数有两个参数。如果出现错误，第一个参数是`error`对象，第二个参数是序列化的 JSON 数据。

下面的代码片段将读取`config.json`文件中的 JSON 数据，并将其记录在终端上:

```
const fs = require('fs');

fs.readFile('./config.json', 'utf8', (error, data) => {
     if(error){
        console.log(error);
        return;
     }
     console.log(JSON.parse(data));

})

```

在开始处理生成的 JavaScript 对象之前，确保反序列化传递给`callback`函数的 JSON 字符串。

### 如何使用`fs.readFileSync`方法读取 JSON 文件

`readFileSync`是另一个类似于`readFile`的读取 Node 中文件的内置方法。两者的区别在于，`readFile`异步读取文件，而`readFileSync`同步读取文件。因此，`readFileSync`阻塞事件循环和剩余代码的执行，直到所有数据都被读取。

要理解同步和异步代码的区别，可以在这里阅读文章“[了解异步 JavaScript](https://blog.logrocket.com/understanding-asynchronous-javascript/) ”。

下面是`fs.readFileSync`的函数签名:

```
fs.readFileSync(path, options);

```

`path`是要读取的 JSON 文件的路径，可以传递一个对象作为第二个参数。第二个参数是可选的。

在下面的代码片段中，我们使用`readFileSync`从`config.json`文件中读取 JSON 数据:

```
const { readFileSync } = require('fs');
const data = readFileSync('./config.json');
console.log(JSON.parse(data));

```

## 如何在 Node.js 中写入 JSON 文件

就像读取 JSON 文件一样，`fs`模块提供了写入 JSON 文件的内置方法。

你可以使用`fs`模块的`writeFile`和`writeFileSync`方法。两者的区别在于`writeFile`是异步的，而`writeFileSync`是同步的。在编写 JSON 文件之前，确保使用`JSON.stringify`方法将 JavaScript 对象序列化为 JSON 字符串。

### 如何使用`fs.writeFile`方法写入 JSON 文件

如果您没有将可选的格式化参数传递给指定如何格式化 JSON 数据的`JSON.stringify`方法，那么`JSON.stringify`将在一行中格式化您的 JSON 数据。

如果您传递给`writeFile`方法的路径是一个现有的 JSON 文件，那么该方法将覆盖指定文件中的数据。如果文件不存在，它将创建一个新文件:

```
const { writeFile } = require('fs');

const path = './config.json';
const config = { ip: '192.0.2.1', port: 3000 };

writeFile(path, JSON.stringify(config, null, 2), (error) => {
  if (error) {
    console.log('An error has occurred ', error);
    return;
  }
  console.log('Data written successfully to disk');
});

```

### 如何使用`fs.writeFileSync`方法写入 JSON 文件

与`writeFile`不同，`writeFileSync`同步写入文件。如果您使用`writeFileSync`，您将阻塞事件循环和剩余代码的执行，直到操作成功或出现错误。如果您传递的路径不存在，它将创建一个新文件，如果存在，它将覆盖它。

在下面的代码片段中，我们正在写入`config.json`文件。我们将代码包装在`try-catch`中，这样我们可以捕捉任何错误:

```
const { writeFileSync } = require('fs');

const path = './config.json';
const config = { ip: '192.0.2.1', port: 3000 };

try {
  writeFileSync(path, JSON.stringify(config, null, 2), 'utf8');
  console.log('Data successfully saved to disk');
} catch (error) {
  console.log('An error has occurred ', error);
}

```

### 如何附加一个 JSON 文件

Node 没有内置的函数来追加或更新现成的现有 JSON 文件的字段。但是，您可以使用`fs`模块的`readFile`方法读取 JSON 文件，更新它，并用更新后的 JSON 覆盖 JSON 文件。

下面是一个代码片段，说明了如何去做:

```
const { writeFile, readFile } = require('fs');
const path = './config.json';

readFile(path, (error, data) => {
  if (error) {
    console.log(error);
    return;
  }
  const parsedData = JSON.parse(data);
  parsedData.createdAt = new Date().toISOString();
  writeFile(path, JSON.stringify(parsedData, null, 2), (err) => {
    if (err) {
      console.log('Failed to write updated data to file');
      return;
    }
    console.log('Updated file successfully');
  });
});

```

## 如何使用第三方 npm 包读写 JSON 文件

在这一节中，我们将研究用于读写 JSON 格式数据的最流行的第三方节点包。

### 如何使用`jsonfile` npm 包读写 JSON 文件

`jsonfile`是一个流行的 npm 包，用于在 Node 中读写 JSON 文件。您可以使用下面的命令安装它:

```
npm i jsonfile

```

它类似于`fs`模块的`readFile`和`writeFile`方法，尽管`jsonfile`比内置方法有一些优势。

该软件包的一些功能如下:

*   它开箱即用地序列化和反序列化 JSON
*   它有一个内置的实用程序，可以将数据附加到 JSON 文件中
*   支持承诺链

您可以在下面的代码片段中看到运行中的`jsonfile`包:

```
const jsonfile = require('jsonfile');
const path = './config.json';

jsonfile.readFile(path, (err, data) => {
  if (err) {
    console.log(err);
    return;
  }
  console.log(data);
});

```

您还可以使用承诺链来代替传递回调函数，如上例所示:

```
const jsonfile = require('jsonfile');
const path = './config.json';

jsonfile
  .readFile(path)
  .then((data) => {
    console.log(data);
  })
  .catch((err) => {
    console.log(err);
  });

```

### 如何使用`fs-extra` npm 包读写 JSON 文件

`fs-extra`是另一个流行的节点包，你可以用它来处理文件。虽然您可以使用这个包来管理 JSON 文件，但是它有一些方法，这些方法的功能不仅仅是读写 JSON 文件。

顾名思义，`fs-extra`拥有`fs`模块提供的所有功能，甚至更多。根据文档，您可以使用`fs-extra`包来代替`fs`模块。

在使用之前，您需要先从 npm 安装`fs-extra`:

```
npm install fs-extra

```

下面的代码展示了如何使用`fs-extra`包的`readJson`方法读取 JSON 文件。您可以使用回调函数、承诺链接或`async/await`:

```
const fsExtra = require('fs-extra');
const path = './config.json';

// Using callback
fsExtra.readJson(path, (error, config) => {
  if (error) {
    console.log('An error has occurred');
    return;
  }
  console.log(config);
});

// Using promise chaining
fsExtra
  .readJson(path)
  .then((config) => {
    console.log(config);
  })
  .catch((error) => {
    console.log(error);
  });

// Using async/await
async function readJsonData() {
  try {
    const config = await fsExtra.readJson(path);
    console.log(config);
  } catch (error) {
    console.log(error);
  }
}
readJsonData();

```

下面的代码说明了如何使用`writeJson`方法编写 JSON 数据:

```
const { writeJson } = require('fs-extra');

const path = './config.json';
const config = { ip: '192.0.2.1', port: 3000 };

// Using callback
writeJson(path, config, (error) => {
  if (error) {
    console.log('An error has occurred');
    return;
  }
  console.log('Data written to file successfully ');
});

// Using promise chaining
writeJson(path, config)
  .then(() => {
    console.log('Data written to file successfully ');
  })
  .catch((error) => {
    console.log(error);
  });

// Using async/await
async function writeJsonData() {
  try {
    await writeJson(path, config);
    console.log('Data written to file successfully ');
  } catch (error) {
    console.log(error);
  }
}
writeJsonData();

```

就像`fs`模块一样，`fs-extra`既有异步方法也有同步方法。在写入 JSON 文件之前，不需要对 JavaScript 对象进行字符串化。

类似地，在读取 JSON 文件后，您不需要解析到 JavaScript 对象。该模块为您开箱即用。

### 如何使用`bfj` npm 包读写 JSON 文件

`bfj`是另一个可以用来处理 JSON 格式数据的 npm 包。根据文档，它是为管理大型 JSON 数据集而创建的。

> `bfj`实现异步函数并使用预先分配的固定长度数组来尝试和缓解与解析和字符串化大型 JSON 或 JavaScript 数据集相关的问题—[bfj 文档](https://gitlab.com/philbooth/bfj/-/blob/master/README.md)

您可以使用`read`方法读取 JSON 数据。`read`方法是异步的，它返回一个承诺。

假设您有一个`config.json`文件，您可以使用以下代码来读取它:

```
const bfj = require('bfj');
const path = './config.json';

bfj
  .read(path)
  .then((config) => {
    console.log(config);
  })
  .catch((error) => {
    console.log(error);
  });

```

类似地，您可以使用`write`方法将数据写入 JSON 文件:

```
const bfj = require('bfj');
const path = './config.json';

const config = { ip: '192.0.2.1', port: 3000 };
bfj
  .write(path, config)
  .then(() => {
    console.log('Data has been successfully written to disk');
  })
  .catch((error) => {
    console.log(error);
  });

```

有许多你可以在文档中读到的功能。它是专门为处理大型 JSON 数据而创建的。它也很慢，所以只有在处理相对较大的 JSON 数据集时才应该使用它。

## 结论

正如上面几节所解释的，JSON 是互联网上最流行的数据交换格式之一。

节点运行时环境有内置的`fs`模块，您可以使用它来处理一般的文件。`fs`模块有一些方法，您可以使用回调 API、基于 promise 的 API 或同步 API 来读写 JSON 文件。

因为回调 API 的方法比基于承诺的 API 的方法性能更好，正如文档中所强调的，您最好使用回调 API。

除了内置的`fs`模块，还有`jsonfile`、`fs-extra`、`bfj`等几个流行的第三方包存在。它们有额外的实用函数，使得处理 JSON 文件变得轻而易举。另一方面，您应该评估向您的应用程序添加第三方包的局限性。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.