# Node.js 中的文件处理:综合指南

> 原文：<https://blog.logrocket.com/file-processing-node-js-comprehensive-guide/>

作为 Node.js 开发人员，您很有可能在某个时候导入了`fs`模块，并编写了一些与文件系统交互的代码。

您可能不知道的是,`fs`模块是一个全功能的、基于标准的、跨平台的模块，它公开了不是一个而是三个 API 来满足同步和异步编程风格。

在本文中，我们将深入探索 Windows 和 Linux 系统中 Node.js 文件处理的世界，重点是`fs`模块的基于 promise 的 API。

### 开始前请注意

本文中的所有例子都打算在 Linux 环境中运行，但是许多例子也可以在 Windows 中运行。请在整篇文章中寻找注释，突出说明在 Windows 中不适用的示例。关于 macOS——在大多数情况下，`fs`模块的工作方式与它在 Linux 上的工作方式相同，但是有一些特定于 MAC OS 的行为没有在本文中讨论。关于 macOS 的细微差别，请参考官方 [Node.js 文档](https://nodejs.org/api/fs.html)。

所有示例的完整源代码都可以在我的 GitHub 上的[briandesousa/node-file-process](https://github.com/briandesousa/node-file-processing)下找到。

## 介绍`fs`模块

`fs`模块是内置在 [Node.js](https://blog.logrocket.com/tag/node) 中的核心模块。它从一开始就存在，一直追溯到最初的 Node.js v0.x 版本。

从最早开始，`fs`模块就与 [POSIX 文件系统标准](https://en.wikipedia.org/wiki/POSIX)保持一致。这意味着您编写的代码在某种程度上可以跨多个操作系统移植，尤其是在不同风格的 Unix 和 Linux 之间。

虽然 Windows 不是一个兼容 POSIX 的操作系统，但是`fs`模块的大部分功能仍然可以工作。然而，有一些功能是不可移植的，仅仅是因为某些文件系统功能不存在或者在 Windows 中以不同的方式实现。

当我们回顾`fs`模块的函数时，请记住以下函数将返回错误或在 Windows 上产生意外结果:

*   修改文件权限和所有权的功能:
*   使用硬链接和软链接的功能:
    *   `link()`
    *   `symlink()`
    *   `readlink()`
    *   `lutimes()`
    *   `lchmod()`
    *   `lchown()`
*   使用`stat()`和`lstat()`时，某些元数据未设置或显示意外值

从 Node v10 开始，`fs`模块包含了三种不同的 API:同步、回调和承诺。这三个 API 公开了相同的文件系统操作集。

本文将重点关注较新的基于承诺的 API。但是，在某些情况下，您可能希望或需要使用同步或回调 API。因此，让我们花点时间来比较一下这三个 API。

## 比较 FS 模块 API

### 同步 API

同步 API 公开了一组阻止执行文件系统操作的函数。当您刚刚开始使用时，这些功能往往是最简单的。

另一方面，它们是线程阻塞的，这与 Node.js 的非阻塞 I/O 设计非常相反。

下面是一个使用同步 API 读取文件内容的示例:

```
import * as fs from 'fs';

const data = fs.readFileSync(path);
console.log(data);

```

### 回调 API

回调 API 允许您以异步方式与文件系统交互。每个回调 API 函数都接受一个在操作完成时调用的回调函数。例如，我们可以用 arrow 函数调用`readFile`函数，如果失败，它会收到一个错误，如果文件读取成功，它会收到数据:

```
import * as fs from 'fs';

fs.readFile(path, (err, data) => {
    if (err) {
        console.error(err);
    } else {
        console.log(`file read complete, data: ${data}`);
    }
});

```

这是一种非阻塞方法，通常更适合 Node.js 应用程序，但它也有自己的挑战。在异步编程中使用回调经常会导致[回调地狱](http://callbackhell.com/)。如果你不注意你的代码结构，你可能会得到一个复杂的嵌套回调函数堆栈，很难阅读和维护。

### 承诺 API

如果应该尽可能避免同步 API，并且回调 API 可能并不理想，那么留给我们的是 promise API:

```
import * as fsPromises from 'fs/promises';

async function usingPromiseAPI(path) {
    const promise = fsPromises.readFile(path);
    console.log('do something else');
    return await promise;
}

```

您可能注意到的第一件事是这个 import 语句与前面的例子的不同之处:promise API 可以从`promises`子路径获得。如果您正在导入 promise API 中的所有函数，惯例是将它们作为`fsPromises`导入。同步和回调 API 函数通常作为`fs`导入。

如果您想保持示例代码的简洁，后续示例中将省略 import 语句。标准的导入命名约定将用于区分 API:`fs`用于访问同步和回调函数，而`fsPromises`用于访问 promise 函数。

promise API 允许您利用 JavaScript 的 async/await 语法优势，以同步方式编写异步代码。上面第 4 行调用的`readFile()`函数返回一个承诺。接下来的代码似乎是同步执行的。最后，从函数返回承诺。`await`操作符是可选的，但是因为我们已经包含了它，所以该函数将在返回之前等待文件操作完成。

是时候对 promise API 进行测试了。变得舒适。有相当多的函数需要涵盖，包括创建、读取和更新文件和文件元数据的函数。

## 使用文件

### 使用文件句柄

promise API 提供了两种不同的方法让[处理文件](https://blog.logrocket.com/uploading-files-using-multer-and-node-js/)。

第一种方法使用一组接受文件路径的顶级函数。这些函数在内部管理文件和目录资源句柄的生命周期。当您处理完文件或目录时，您不需要担心调用`close()`函数。

第二种方法使用一组在`FileHandle`对象上可用的函数。一个`FileHandle`作为文件系统中一个文件或目录的引用。下面是你如何获得一个`FileHandle`对象:

```
async function openFile(path) {
    let fileHandle;
    try {
        fileHandle = await fsPromises.open(path, 'r');
        console.log(`opened ${path}, file descriptor is ${fileHandle.fd}`);
        const data = fileHandle.read()
    } catch (err) {
        console.error(err.message);
    } finally {
        fileHandle?.close();
    }
}

```

在上面的第 4 行，我们使用`fsPromises.open()`为一个文件创建一个`FileHandle`。我们传递`r`标志来指示文件应该以只读模式打开。任何试图修改文件的操作都将失败。(也可以指定[其他标志](https://nodejs.org/api/fs.html#fs_file_system_flags)。)

使用`read()`函数读取文件内容，该函数可从文件句柄对象直接获得。在第 10 行，我们需要显式关闭文件句柄以避免潜在的内存泄漏。

`FileHandle`类中所有可用的函数也可以作为顶级函数使用。我们将继续探索顶级函数，但很高兴知道这种方法也是可用的。

### 读取文件

读取一个文件似乎是如此简单的任务。但是，根据您需要如何处理文件，可以指定几个不同的选项:

```
// example 1: simple read
const data = await fsPromises.readFile(path);

// example 2: read a file that doesn't exist (creates a new file)
const noData = await fsPromises.readFile(path, { flag: 'w'});

// example 3: read a file and return its contents as a base64-encoded string
const base64data = await fsPromises.readFile(path, { encoding: 'base64' });

// example 4: read a file but abort the operation before it completes
const controller = new AbortController();
const { signal } = controller;
const promise = fsPromises.readFile(path, { signal: signal });
console.log(`started reading file at ${path}`);
controller.abort();     
console.log('read operation aborted before it could be completed')
await promise;

```

如果您只想获取文件的内容，那么示例 1 非常简单。

在示例 2 中，我们不知道文件是否存在，所以如果需要的话，我们先通过`w` [文件系统标志](https://nodejs.org/api/fs.html#fs_file_system_flags)来创建它。

示例 3 演示了如何更改返回数据的格式。

示例 4 演示了如何中断文件读取操作并中止它。这在读取大文件或读取速度慢的文件时很有用。

### 复制文件

`copyFile`函数可以复制一个文件，并在目标文件已经存在的情况下给你一些控制权:

```
// example 1: create a copy, overwite the destination file if it exists already
await fsPromises.copyFile('source.txt', 'dest.txt');

// example 2: create a copy but fail because the destination file exists already
await fsPromises.copyFile('source.txt', 'dest.txt', fs.constants.COPYFILE_EXCL);
// Error: EEXIST: file already exists, copyfile 'source.txt' -> 'dest.txt'

```

如果`dest.txt`已经存在，示例 1 将覆盖它。在示例 2 中，我们传入了`COPYFILE_EXCL`标志来覆盖默认行为，如果`dest.txt`已经存在，我们就失败。

### 写文件

有三种方法可以写入文件:

*   添加到文件中
*   写入文件
*   截断文件

这些功能中的每一个都有助于实现不同的用例。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
// example 1: append to an existing file
// content of data.txt before: 12345
await fsPromises.appendFile('data.txt', '67890');
// content of data.txt after: 1234567890 

// example 2: append to a file that doesn't exist yet
await fsPromises.appendFile('data2.txt', '123');
// Error: ENOENT: no such file or directory, open 'data2.txt'

// example 3: write to an existing file
// content of data3.txt before: 12345
await fsPromises.writeFile('data3.txt', '67890');
// content of data3.txt after: 67890

// example 4: write to a file that doesn't exist yet (new file is created)
await fsPromises.writeFile('data4.txt', '12345');

// example 5: truncate data in an existing file
// content of data5.txt before: 1234567890
await fsPromises.truncate('data5.txt', 5);
// content of data5.txt after: 12345

```

示例 1 和 2 演示了如何使用`appendFile`函数将数据添加到现有的或新的文件中。如果一个文件不存在，`appendFile`将首先创建它。

示例 3 和 4 演示了如何使用`writeFile`函数写入现有的或新的文件。如果文件不存在，`writeFile`函数也会在写入之前创建一个文件。但是，如果文件已经存在并且包含数据，文件的内容将被覆盖，而不会发出警告。

示例 5 演示了如何使用`truncate`函数来修剪文件的内容。传递给该函数的参数起初可能会令人困惑。您可能期望一个`truncate`函数接受要从文件末尾去除的字符数，但实际上我们需要指定要保留的字符数。在上面的例子中，您可以看到我们向`truncate`函数输入了一个值`5`，它从字符串`1234567890`中删除了最后五个字符。

### 观看文件

promise API 提供了一个单一的、高性能的`watch`函数，可以监视文件的变化。

```
const abortController = new AbortController();
const { signal } = abortController;
setTimeout(() => abortController.abort(), 3000);

const watchEventAsyncIterator = fsPromises.watch(path, { signal });

setTimeout(() => {
    fs.writeFileSync(path, 'new data');
    console.log(`modified ${path}`);
}, 1000);

for await (const event of watchEventAsyncIterator) {
    console.log(`'${event.eventType}' watch event was raised for ${event.filename}`);
}

// console output:
// modified ./data/watchTest.txt
// 'change' watch event was raised for watchTest.txt
// watch on ./data/watchTest.txt aborted

```

`watch`函数可以无限期地观察文件的变化。每次观察到更改时，都会引发一个观察事件。`watch`函数返回一个异步 iterable，这实质上是函数返回一系列无限承诺的一种方式。在第 12 行，我们利用了`for await … of`的语法优势，等待并迭代收到的每个观察事件。

您很可能不想无休止地查看文件的变化。可以使用一个特殊的信号对象来中止手表，该信号对象可以根据需要触发。在第 1 行到第 2 行，我们创建了一个`AbortController`的实例，它让我们可以访问最终传递给`watch`函数的`AbortSignal`的实例。在这个例子中，我们在一段固定的时间(在第 3 行指定)后调用信号对象的`abort()`函数，但是您可以在需要的时候中止。

`watch`函数也可以用来观察目录的内容。它接受一个可选的`recursive`选项，该选项决定是否监视所有子目录和文件。

## 文件元数据

到目前为止，我们已经关注了读取和修改文件的内容，但是您可能还需要读取和更新文件的元数据。文件元数据包括其大小、类型、权限和其他文件系统属性。

`stat`函数用于检索文件元数据，或“统计数据”,如文件大小、权限和所有权。

```
// get all file metadata
const fileStats = await fsPromises.stat('file1.txt');
console.log(fileStats)
// console output:
// Stats {
//    dev: 2080,
//    mode: 33188,
//    nlink: 1,
//    uid: 1000,
//    gid: 1000,
//    rdev: 0,
//    blksize: 4096,
//    ino: 46735,
//    size: 29,
//    blocks: 8,
//    atimeMs: 1630038059841.8247,
//    mtimeMs: 1630038059841.8247,
//    ctimeMs: 1630038059841.8247,
//    birthtimeMs: 1630038059801.8247,
//    atime: 2021-08-27T04:20:59.842Z,
//    mtime: 2021-08-27T04:20:59.842Z,
//    ctime: 2021-08-27T04:20:59.842Z,
//    birthtime: 2021-08-27T04:20:59.802Z
//  }
console.log(`size of file1.txt is ${fileStats.size}`);

```

这个例子演示了一个完整的元数据列表，可以从一个文件或目录中检索到这些元数据。

请记住，这些元数据中有一些是依赖于操作系统的。例如，`uid`和`gid`属性代表用户和组所有者——这个概念适用于 Linux 和 macOS 文件系统，但不适用于 Windows 文件系统。在 Windows 上运行此函数时，这两个属性返回零。

一些文件元数据可以被操作。例如，`utimes`函数用于更新文件的访问和修改时间戳:

```
const newAccessTime = new Date(2020,0,1);
const newModificationTime = new Date(2020,0,1);
await fsPromises.utimes('test1.txt', newAccessTime, newModificationTime);

```

`realpath`函数对于解析相对路径和完整路径的符号链接很有用:

```
// convert a relative path to a full path
const realPath = await fsPromises.realpath('./test1.txt');
console.log(realPath);
// console output: /home/brian/test1.txt

// resolve the real path of a symbolic link pointing to /home/brian/test1.txt
const symLinkRealPath = await fsPromises.realpath('./symlink1');
console.log(symLinkRealPath);
// console output: /home/brian/test1.txt

```

## 文件权限和所有权

请记住，在本节中，文件权限和所有权功能适用于 Unix、Linux 和 macOS 操作系统。这些函数在 Windows 上产生意想不到的结果。

如果您不确定您的应用程序是否有必要的权限访问或执行文件系统上的文件，您可以使用`access`函数来测试它:

```
// example 1: check if a file can be accessed
try {
  await fsPromises.access('test1.txt');
  console.log('test1.txt can be accessed');
} catch (err) {
  // EACCES: permission denied, access 'test1.txt'
}

// example 2: check if a file can be executed (applies to Unix/Linux-based systems)
try {
  await fsPromises.access('test2.txt', fs.constants.X_OK);
} catch(err) {
  // EACCES: permission denied, access 'test2.txt'
}

```

使用`chmod`功能可以修改文件权限。例如，我们可以通过传递一个特殊的模式字符串来删除文件的执行访问:

```
// remove all execute access from a file
await fsPromises.chmod('test1.txt', '00666');

```

`00666`模式字符串是一个特殊的五位数，由描述文件属性(包括权限)的多个位掩码组成。最后三位数相当于您在 Linux 上可能习惯传递给`chmod`的三位数权限模式。`fs`模块文档为[提供了可用于解释该模式字符串的位屏蔽](https://nodejs.org/api/fs.html#fs_file_mode_constants)列表。

也可以使用`chown`功能修改文件所有权:

```
// set user and group ownership on a file
const root_uid= 0;
const root_gid = 0;
await fsPromises.chown('test1.txt', root_uid, root_gid);

```

在本例中，我们更新了文件，使其归 root 用户和 root 组所有。根用户的`uid`和根组的`gid`在 Linux 上总是`0`。

## 使用链接

提示:链接功能适用于 Unix/Linux 操作系统。这些函数在 Windows 上产生意想不到的结果。

模块`fs`提供了多种功能，你可以使用这些功能来处理硬链接和[软链接，或者符号链接](https://en.wikipedia.org/wiki/Symbolic_link)。我们已经看到的许多文件函数都有处理链接的等效版本。在大多数情况下，它们的操作也是一样的。

在我们开始创建链接之前，让我们快速回顾一下我们将要使用的两种类型的链接。

### 硬链接与软链接

硬链接和软链接是指向文件系统中其他文件的特殊类型的文件。如果软链接所链接的文件被删除，则该链接无效。

另一方面，指向文件的硬链接仍然有效，并且包含文件的内容，即使原始文件被删除。硬链接不指向文件，而是指向文件的底层数据。这个数据在 Unix/Linux 文件系统上被称为 *inode* 。

我们可以很容易地用`fs`模块创建软链接和硬链接。使用`symlink`功能创建软链接，使用`link`功能创建硬链接。

```
// create a soft link
const softLink = await fsPromises.symlink('file.txt', 'softLinkedFile.txt');

// create a hard link
const hardLink = await fsPromises.link('file.txt', 'hardLinkedFile.txt');

```

如果您想确定链接指向的底层文件，该怎么办？这就是`readlink`函数的用武之地。

```
>// read a soft link
console.log(await fsPromises.readlink('softLinkedFile.txt'));
// output: file.txt

// read a hard link... and fail
console.log(await fsPromises.readLink('hardLinkedFile.txt'));
// output: EINVAL: invalid argument, readlink 'hardLinkedFile.txt'

```

`readlink`函数可以读取软链接，但不能读取硬链接。硬链接与其链接的原始文件无法区分。其实所有文件都是技术上的硬链接。`readlink`函数本质上将其视为另一个普通文件，并抛出一个`EINVAL`错误。

`unlink`功能可以删除硬链接和软链接:

```
// delete a soft link
await fsPromises.unlink('softLinkedFile.txt');

// delete a hard link / file
await fsPromises.unlink('hardLinkedFile.txt');

```

`unlink`函数实际上是一个通用函数，也可以用来删除常规文件，因为它们本质上与硬链接相同。除了`link`和`unlink`功能之外，所有其他链接功能都用于软链接。

您可以像修改普通文件一样修改软链接的元数据:

```
// view soft link meta data
const linkStats = await fsPromises.lstat(path);

// update access and modify timestamps on a soft link
const newAccessTime = new Date(2020,0,1);
const newModifyTime = new Date(2020,0,1);
await fsPromises.lutimes('softLinkedFile.txt', newAccessTime, newModifyTime);

// remove all execute access from a soft link
await fsPromises.lchmod('softLinkedFile.txt', '00666');

// set user and group ownership on a soft link
const root_uid= 0;
const root_gid = 0;
await fsPromises.lchown('softLinkedFile.txt', root_uid, root_gid);

```

除了每个函数都带有前缀`l`之外，这些函数的操作与它们的等价文件函数完全相同。

## 使用目录

我们不能仅仅停留在文件处理上。如果您正在处理文件，那么不可避免的是，您也需要处理目录。`fs`模块提供了创建、修改和删除目录的各种功能。

很像我们之前看到的`open`函数，`opendir`函数以`Dir`对象的形式返回一个目录的句柄。`Dir`对象公开了几个可用于操作该目录的函数:

```
let dir;
try {
  dir = await fsPromises.opendir('sampleDir');
  dirents = await dir.read();
} catch (err) {
  console.log(err);
} finally {
  dir.close();
}

```

当你完成时，一定要调用`close`函数来释放目录上的句柄。

`fs`模块还包括为您隐藏目录资源句柄的打开和关闭的函数。例如，您可以创建、重命名和删除目录:

```
// example 1: create a directory
await fsPromises.mkdir('sampleDir');

// example 2: create multiple nested directories
await fsPromises.mkdir('nested1/nested2/nested3', { recursive: true });

// example 3: rename a directory
await fsPromises.rename('sampleDir', 'sampleDirRenamed');

// example 4: remove a directory
await fsPromises.rmdir('sampleDirRenamed');

// example 5: remove a directory tree
await fsPromises.rm('nested1', { recursive: true });

// example 6: remove a directory tree, ignore errors if it doesn't exist
await fsPromises.rm('nested1', { recursive: true, force: true });

```

示例 2、5 和 6 演示了`recursive`选项，如果您在创建或删除路径之前不知道它是否存在，那么这个选项特别有用。

有两种方法可以读取目录的内容。默认情况下，`readdir`函数返回所请求目录下所有文件和文件夹的名称列表。

您可以传递`withFileTypes`选项来获得一个`Dirent`目录条目对象的列表。这些对象包含所请求目录中每个文件系统对象的名称和类型。例如:

```
// example 1: get names of files and directories
const files = await fsPromises.readdir('anotherDir');
for (const file in files) {
  console.log(file);
}

// example 2: get files and directories as 'Dirent' directory entry objects
const dirents = await fsPromises.readdir('anotherDir', {withFileTypes: true});
for (const entry in dirents) {
  if (entry.isFile()) {
    console.log(`file name: ${entry.name}`);
  } else if (entry.isDirectory()) {
    console.log(`directory name: ${entry.name}`);
  } else if (entry.isSymbolicLink()) {
    console.log(`symbolic link name: ${entry.name}`);
  }
}

```

`readdir`函数没有提供递归选项来读取子目录的内容。你必须自己编写递归函数，或者依靠第三方模块，比如 [`recursive-readdir]()`](https://www.npmjs.com/package/recursive-readdir) 。

## 关闭()

是时候`close()`这篇文章的资源句柄了。我们已经彻底了解了如何使用 Node.js `fs`模块处理文件、链接和目录。Node.js 中的文件处理是现成的，功能齐全，随时可用。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.