# Node.js 子进程:如何启动外部程序

> 原文：<https://blog.logrocket.com/node-js-child-process-launch-external-programs/>

你的电脑可以让你访问许多复杂的应用程序，这些应用程序可以完成各种任务，如文本处理、编辑图像、压缩文件等。借助 Node.js 的 [`child_process`模块](https://nodejs.org/api/child_process.html)，您可以在 node . js 中启动这些程序，该模块可以在单独的子进程中启动 GUI 或命令行程序。一旦程序结束运行，它就将输出返回给启动它的 Node.js 进程。

在本教程中，我们将使用`child_process`模块在 Node.js 中启动外部程序。

*向前跳转:*

## 先决条件

要学习和理解本教程，您需要:

本教程是在一个类似 Unix 的系统上测试的。如果你使用的是 Windows，有些命令就不起作用了，你必须寻找替代的方法。

## 为什么要使用子进程？

当你编写一个程序，并在你的终端中用`node`命令执行它时，这个程序就变成了一个进程。进程是操作系统管理的运行程序的抽象。

Node.js 进程有自己的内存和一个用于执行 JavaScript 代码的主线程。由于代码是在单线程中执行的，如果一个任务是 CPU 受限的和时间密集型的，它会阻塞[事件循环](https://blog.logrocket.com/a-complete-guide-to-the-node-js-event-loop/)。发生这种情况是因为任务在 JavaScript 主线程中持续运行，并阻止其他代码执行。

为了解决这个问题，您可以使用一个子进程。子进程是由另一个进程(父进程)创建的进程。子进程有其优势:

*   在您的系统上运行外部程序
*   将 CPU 限制的阻塞任务卸载到一个单独的进程，以避免阻塞主线程

我们将看看如何运行外部程序，以及将阻塞任务卸载到子进程，但首先，让我们设置本教程的目录。

## 设置目录

在这一节中，我们将创建我们在本教程中编写的程序所在的目录。

要创建目录，请打开您的终端并输入以下命令:

```
mkdir cp_programs

```

移到目录中:

```
cd cp_programs

```

进入目录后，创建`package.json`文件:

```
npm init -y

```

现在我们已经有了目录，接下来我们将在 Node.js 中启动一个外部程序。

## 启动外部程序并捕获输出

在本节中，我们将在 Node.js 中运行一个外部程序并捕获输出，以便可以在 Node.js 中使用它。为此，我们将使用`child_process`模块的`execFile()`方法，该方法运行任何程序并返回输出。

在运行外部程序之前，让我们看看我们想要使用 Node.js 运行的程序。在您的终端中，运行以下命令:

```
ls -l
// output
total 4
-rw-rw-r-- 1 stanley stanley 225 Dec 20 06:41 package.json

```

`ls`程序列出当前目录下的所有文件和子目录；这是大多数类 Unix 系统的默认程序。不用在 Node.js 中重写程序的功能，我们只需在 Node.js 中从外部调用程序并捕获其输出。

现在我们已经确定了我们想要运行的程序，在文本编辑器中创建并打开`listDir.js`文件，并输入以下内容:

```
const util = require("node:util");
const execFile = util.promisify(require("node:child_process").execFile);

async function lsDir() {
  const { error, stdout, stderr } = await execFile("ls", ["-l"]);
  console.log(`External Program's output:\n ${stdout}`);
}
lsDir();

```

在第一行中，我们导入了 [`util`包](https://nodejs.org/api/util.html)，它提供了有用的实用函数。在第二行中，我们使用`util.promisify()`方法让`execFile()`方法使用 promise API。

接下来，我们定义`lsDir()`函数，它运行一个外部程序并记录其输出。在函数中，我们调用`execFile()`方法在一个单独的子进程中运行`ls`命令行程序。该方法有两个参数:程序名和程序命令行参数的数组。`ls`是程序名，`-l`是修改`ls`命令以列出详细文件的选项。

调用`execFile()`方法后，我们将该方法返回的对象析构为以下变量:

*   `error`:当 Node.js 执行你的程序有问题的时候会设置
*   `[stdout]([https://blog.logrocket.com/using-stdout-stdin-stderr-node-js/](https://blog.logrocket.com/using-stdout-stdin-stderr-node-js/))`:包含外部程序返回的输出
*   `stderr`:如果外部程序出现与 Node.js 无关的错误，则设置该值

从那里，我们在控制台中记录输出并调用`lsDir()`函数。

在运行程序之前，让我们的程序记录它在控制台中遇到的任何错误:

```
const util = require("node:util");
const execFile = util.promisify(require("node:child_process").execFile);

async function lsDir() {
  const { error, stdout, stderr } = await execFile("ls", ["-l"]);
  // add the following code
  if (error) {
    console.error(error);
    return;
  }
  if (stderr) {
    console.error(stderr);
    return;
  }
  console.log(`External Program's output:\n ${stdout}`);
}
lsDir();

```

在前面的代码中，我们检查了`error`和`stderr`变量是否有错误，并将结果记录在控制台中。

添加完代码后，保存文件。在终端中，使用`node`命令运行程序:

```
node listDir.js

```

运行程序时，输出将如下所示:

```
External Program's output:
 total 8
-rw-rw-r-- 1 stanley stanley 384 Dec 20 06:51 listDir.js
-rw-rw-r-- 1 stanley stanley 225 Dec 20 06:41 package.json

```

程序显示了`ls`程序运行时返回的目录中文件的详细列表。

现在我们可以运行一个外部程序并捕获它的输出，在下一节中，我们将把阻塞任务卸载到一个子进程中，使它们成为非阻塞任务。

## 将受 CPU 限制的任务卸载到子进程

在本节中，我们将创建一个程序，它有一个阻塞的 CPU 绑定任务，并将它卸载到一个子进程，以防止 CPU 密集型任务阻塞主线程。CPU 受限或 CPU 密集型任务涉及一段占用 CPU 直到完成的代码，例如数学计算、图像和视频处理、加密等。

为了卸载受 CPU 限制的任务，我们将把 CPU 密集型代码移到一个单独的程序中，然后使用`fork()`方法在一个子进程中调用该程序。`fork()`方法允许父流程和子流程通过消息进行通信。因此，一旦子进程完成执行，它将向父进程发回一条包含数据的消息。

为了了解 CPU 绑定的任务如何阻塞主线程，我们将首先创建一个程序，它有一个阻塞的 CPU 绑定的任务，然后使它不阻塞。

在您的文本编辑器中，创建`blockingTask.js`并添加以下代码:

```
function cpuIntensive() {
  console.log("blocking task starts");
  let total = 0;
  for (let i = 0; i < 30_000_000_000; i++) {
    total += i;
  }
  console.log("blocking task finishes");
  return total;
}

console.log(`Calculated value: ${cpuIntensive()}`);

```

这里，我们创建了运行 CPU 绑定任务的`cpuIntensive()`函数。该函数包含一个循环，该循环迭代 300 亿次，并在每次迭代期间递增`total`变量。之后，它返回`total`变量。这项任务需要一段时间才能完成。

为了了解这个任务是如何阻塞的，让我们向`blockingTask.js`文件添加一些非阻塞代码:

```
function cpuIntensive() {
  console.log("blocking task starts");
  let total = 0;
  for (let i = 0; i < 30_000_000_000; i++) {
    total += i;
  }
  console.log("blocking task finishes");
  return total;
}

console.log(`Calculated value: ${cpuIntensive()}`);

// add the following non-blocking code
js_keywords = ["let", "const", "for"];
console.log("The following are JavaScript Reserved keywords: ");
for (keyword of js_keywords) {
  console.log(keyword);
}

```

在最后五行中，我们添加了一个迭代三次的小循环。与迭代 300 亿次的循环相比，这项任务不需要很长时间就可以完成。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

保存文件，然后运行程序:

```
node blockingTask.js

```

当您运行该程序时，我们将首先获得以下输出:

```
// output
blocking task starts

```

在此之后，我们必须等待很长时间才能获得剩余的输出:

```
// output
blocking task starts
blocking task finishes
Calculated value: 449999999970159100000
The following are JavaScript Reserved keywords: 
let
const
for

```

正如您所看到的，CPU 绑定的任务阻塞了主线程，并阻止了非阻塞任务的执行。为了获得良好的用户体验，最好让非阻塞任务与阻塞任务同时运行。

为此，我们将把 CPU 密集型循环卸载到另一个文件中，并使用`fork()`方法创建一个子进程，释放主线程。

创建并打开`cpuBound.js`文件，然后添加以下代码:

```
function cpuIntensive() {
  console.log("blocking task starts");
  let total = 0;
  for (let i = 0; i < 30_000_000_000; i++) {
    total += i;
  }
  console.log("blocking task finishes");
  return total;
}

// send a message to the parent process.
process.send(cpuIntensive());

```

`cpuIntensive()`函数与我们在`blockingTask.js`文件中定义的函数相同。这里的新内容是`process.send()`方法。该方法发送一条包含`cpuIntensive()`函数返回值的消息。

在`blockingTask.js`文件中，删除`cpuIntensive()`函数并添加以下代码:

```
// add the following code
const { fork } = require("node:child_process");

const childProcess = fork(__dirname + "/cpuBound.js");

childProcess.on("message", (message) => {
  console.log(`Calculated value: ${message}`);
});

// code that is non-blocking
js_keywords = ["let", "const", "for"];
console.log("The following are JavaScript Reserved keywords: ");
for (keyword of js_keywords) {
  console.log(keyword);
}

```

在第一行中，我们从`child_process`模块中导入了`fork()`方法。然后，我们用应该在子进程中运行的 Node.js 程序的路径调用`fork()`方法。之后，我们附加了`on()`方法来监听子进程发送的消息。一旦收到消息，我们就将其记录到控制台中。

让我们再次运行`blockingTask.js`文件:

```
node blockingTask.js

```

现在，输出将与以下内容匹配:

```
// output
The following are JavaScript Reserved keywords: 
let
const
for
blocking task starts

```

现在您将看到非阻塞循环将`js_keywords`数组中的保留字记录到控制台中。在本节的前面，这个循环直到 CPU 相关的任务完成后才运行。

过了一会儿，我们看到了 CPU 绑定任务的输出:

```
// output
...
blocking task finishes
Calculated value: 449999999970159100000

```

即使 CPU 密集型函数正在运行，它也不会影响主线程。父进程中的所有非阻塞代码都能够执行。

既然我们可以将 CPU 绑定的任务卸载到另一个线程以避免阻塞，接下来我们将读取大文件。

## 从外部程序流式传输大量输出

到目前为止，我们已经运行了一个外部程序，并使用`execFile()`方法在 Node.js 中捕获了它的输出。但是如果外部程序读取一个大文件，就会导致内存错误。这是因为`execFile()`方法将输出存储在缓冲区中，然后将其传递给程序的父进程。

为了避免使用太多内存，我们将需要使用`spawn()`方法，该方法将外部程序的输出分成更小的块，并将它们发送给 Node.js 程序。这减少了我们使用的内存量，因为程序将在数据进来时读取较小的数据块，而不是将所有数据保存在缓冲区中。

在这一节中，我们将编写一个使用`spawn()`方法读取大文件的程序。我们将使用`/usr/share/dict`目录中的`words`文件，这在大多数类似 Unix 的系统中都可以找到。如果你没有这个文件，你可以使用你选择的任何大文本文件，或者你可以[在这里](https://gist.github.com/WChargin/8927565)下载样本文件。

在终端中，将字典文件复制到项目的目录中:

```
cp /usr/share/dict/words .

```

让我们给文件添加一个扩展名:

```
mv words words.txt

```

现在使用`cat`命令读取文件:

```
cat words.txt

```

该命令将记录如下所示的输出(为简洁起见，我省略了一些输出):

```
// output
...
zucchini
zucchini's
zucchinis
zwieback
zwieback's
zygote
zygote's
zygotes

```

现在让我们使用 Node.js 中的`child_process`模块运行该命令。创建并打开`readLargeFileStreams.js`并输入下面的代码:

```
const { spawn } = require("node:child_process");
const cat = spawn("cat", ["words.txt"]);

cat.on("error", (error) => {
  console.error(`error: ${error.message}`);
});
cat.stdout.pipe(process.stdout);
cat.stderr.pipe(process.stderr);
cat.on("close", (code) => {
  console.log(`child process exited with code ${code}`);
});

```

在第一行中，我们导入了`spawn()`方法。在第二行中，我们调用`spawn()`方法在子进程中运行`cat`程序来读取`words.txt`文件。由于`spawn()`使用流 API，我们附加了一个事件来监听 Node.js 错误并在控制台中记录错误。之后，我们使用`stdout.pipe()`方法将来自`cat`程序的输出通过管道传输到`process.stdout`，在那里接收到的大块数据将被记录。

接下来，我们使用`stderr.pipe()`方法将错误消息从`cat`程序发送到`process.stderr`，它们将被记录在控制台中。最后，我们监听`close`事件，在控制台中记录一条消息。

保存并运行文件:

```
node readLargeFileStreams.js

```

输出将显示在控制台中:

```
...
zoos
zorch
zucchini
zucchini's
zucchinis
zwieback
zwieback's
zygote
zygote's
zygotes
child process exited with code 0

```

整个输出没有被缓冲；相反，该程序接收成块的输出，并将它们记录在控制台中。

您现在可以使用`spawn()`方法读取大文件，而不会占用太多内存。在下一节中，我们将链接外部应用程序。

## 链接外部应用程序

大多数程序被设计成能很好地做一件事。例如，`cat`程序读取文件，`grep`程序搜索文本。你可以把这些程序连接在一起完成一个特定的任务。

使用`words.txt`文件，您可以使用`cat`读取文件，然后链接`grep`来搜索包含“zip”的单词:

```
cat words.txt | grep zip

```

当`cat`命令读取`words.txt`文件时，其输出作为输入传递给`grep`命令。`grep`然后过滤输入，只显示包含单词“zip”的单词。

您可以使用`pipe()`和`spawn()`方法在 Node.js 中重新创建这个行为。

首先，创建并打开`chainingPrograms.js`文件，然后添加以下代码:

```
const { spawn } = require("node:child_process");
const cat = spawn("cat", ["words.txt"]);
const grep = spawn("grep", ["zip"]);

cat.stdout.pipe(grep.stdin);
grep.stdout.pipe(process.stdout);

cat.on("error", (error) => {
  console.error(`error: ${error.message}`);
});
grep.on("error", (error) => {
  console.error(`error: ${error.message}`);
});

```

在前三行中，我们导入`spawn()`，然后用它运行`cat`和`grep`命令。`cat`命令读取`words.txt`文件，`grep`命令搜索包含单词“zip”的单词。要将`cat`命令输出传递给`grep`，您可以使用`stdout.pipe()`方法，该方法接受应该接收`cat`输出作为输入的程序实例，这里是`grep`。

接下来，您调用`stdout.pipe()`并传递它`process.stdout`以在控制台中记录输出。最后六行检查`cat`或`grep`实例是否有错误，并在控制台中记录错误消息。

完成后，保存文件，然后使用节点运行`chainingPrograms.js`文件:

```
node chainingPrograms.js

```

您的输出将类似于以下内容:

```
// output
marzipan
marzipan's
unzip
unzipped
...
zippiest
zipping
zippy
zip's
zips

```

您会注意到输出只显示包含单词“zip”的单词。这证实了程序的链接是有效的。

## 使用`exec()`运行 shell 命令

到目前为止，我们还没有看到的一个方法是`exec()`方法。该方法创建一个 shell 并运行您传递给它的任何命令；您甚至可以将命令链接起来并作为参数传递，这是用`execFile()`做不到的，因为它不创建 shell。

举以下例子:

```
cat words.txt|nl|grep zip

```

`cat`命令读取`words.txt`文件，然后将其传递给`nl`命令，后者向整个文件添加行号。之后，我们使用`grep`搜索并返回包含“zip”的单词。

到目前为止，我们已经介绍过了，您可以使用 Node.js 中的`spawn()`和`pipe()`方法来链接这个命令，如前一节所示。

使用`exec()`方法，您可以传递链接的命令，它将在 shell 中执行。

为此，创建并打开`filterDictionary.js`文件，并输入以下代码:

```
const util = require("node:util");
const exec = util.promisify(require("node:child_process").exec);

async function filterDictionary() {
  const { error, stdout, stderr } = await exec("cat words.txt|nl|grep zip");
  if (error) {
    console.error(error);
    return;
  }
  if (stderr) {
    console.error(stderr);
    return;
  }
  console.log(`External Program's output:\n ${stdout}`);
}

filterDictionary();

```

首先，我们将`exec()`方法导入到程序中。然后我们定义`filterDictionary()`函数在子进程中运行外部程序。在该函数中，我们调用了`exec()`方法，并将链接的命令作为参数。之后，我们检查并记录遇到的任何错误。

按如下方式运行程序:

```
node filterDictionary.js

```

输出将如下所示:

```
External Program's output:
  64930 marzipan
 64931  marzipan's
 99883  unzip
 99884  unzipped
...
104280  zipping
104281  zippy
104282  zip's
104283  zips

```

正如您所看到的，输出显示了行号，以及包含“zip”的单词，这证明了`exec()`方法成功地运行了链接的命令，没有出现任何问题。

## 外壳注入攻击及其防范

您现在已经学习了如何使用`exec()`方法，该方法允许您访问 shell 并运行任何命令。虽然访问 shell 很有帮助，但有时也很危险。这是由于外壳注入攻击，攻击者可以将有害的命令附加到`exec()`方法输入中，这可以破坏主机的计算机。

为了理解这种攻击是如何发生的，我们将创建一个程序来说明这一点。

首先，使用 npm 下载`prompt-sync`包:

```
npm install prompt-sync

```

我们将使用该包从用户那里获得输入。

接下来，创建并打开`listDirExec.js`文件，添加以下内容:

```
const util = require("node:util");
const exec = util.promisify(require("node:child_process").exec);
const prompt = require("prompt-sync")({ sigint: true });
const dirname = prompt("Enter the directory you want to list");

```

在前两行中，我们导入并承诺了`exec()`方法。然后，我们导入`prompt-sync`包并使用`prompt()`方法从用户那里获得输入，这是用户希望程序列出内容的目录。

在`listDirExec.js`文件中，添加以下代码以列出目录内容:

```
const util = require("node:util");
const exec = util.promisify(require("node:child_process").exec);
const prompt = require("prompt-sync")({ sigint: true });
const dirname = prompt("Enter the directory you want to list");

// add the following
async function listDir() {
  const { error, stdout, stderr } = await exec(`ls -l ${dirname}`);
  if (error) {
    console.error(error);
    return;
  }
  if (stderr) {
    console.error(stderr);
    return;
  }
  console.log(`External Program's output:\n ${stdout}`);
}
listDir();

```

在前面的代码中，我们定义了`listDir()`函数来列出目录内容。在该函数中，我们调用了`exec()`方法，该方法运行`ls -l`命令和用户传递的输入。如果用户输入`/home/stanley/cp_programs`，命令运行将是`ls -l /home/stanley/cp_programs`。

因为我们从用户那里获得输入，所以怀有恶意的人可以附加另一个命令来造成破坏。这可以通过添加分号来实现，如下所示:

```
ls -l; free -h

```

当您在终端中运行该命令时，它将列出目录内容，然后检查内存使用情况，如下所示:

```
total 1008
-rw-rw-r-- 1 stanley stanley    388 Dec 20 07:04 blockingTask.js
-rw-rw-r-- 1 stanley stanley    347 Dec 20 07:31 chainingPrograms.js
...
-rw-rw-r-- 1 stanley stanley    408 Dec 20 07:14 readLargeFile.js
-rw-rw-r-- 1 stanley stanley    323 Dec 20 07:16 readLargeFileStreams.js
-rw-r--r-- 1 stanley stanley 985084 Dec 20 07:12 words.txt
               total        used        free      shared  buff/cache   available
Mem:           7.6Gi       4.1Gi       124Mi       554Mi       3.4Gi       2.7Gi
Swap:          1.9Gi       137Mi       1.7Gi

```

现在我们可以追加命令了，按如下方式运行程序:

```
node listDirExec.js

```

系统将提示我们输入目录名。我们的应用程序希望用户输入他们选择的目录:

```
/home/stanley/cp_programs

```

当程序运行时，输出将显示目录内容:

```
Enter the directory you want to list/home/stanley/cp_programs
External Program's output:
 total 1008
-rw-rw-r-- 1 stanley stanley    388 Dec 20 07:04 blockingTask.js
-rw-rw-r-- 1 stanley stanley    347 Dec 20 07:31 chainingPrograms.js
-rw-rw-r-- 1 stanley stanley    278 Dec 20 07:03 cpuBound.js
-rw-rw-r-- 1 stanley stanley    410 Dec 20 07:36 filterDictionary.js
...
-rw-rw-r-- 1 stanley stanley    323 Dec 20 07:16 readLargeFileStreams.js
-rw-r--r-- 1 stanley stanley 985084 Dec 20 07:12 words.txt

```

攻击者可能有不同的计划并附加另一个命令。让我们通过再次运行该程序来尝试一下:

```
node listDirExec.js

```

出现提示时，输入以下内容:

```
/home/stanley/cp_programs;free -h;df -h

```

运行该命令后，输出如下所示:

```
Enter the directory you want to list/home/stanley/cp_programs;free -h;df -h
External Program's output:
 total 1008
-rw-rw-r-- 1 stanley stanley    388 Dec 20 07:04 blockingTask.js
...
-rw-rw-r-- 1 stanley stanley    408 Dec 20 07:14 readLargeFile.js
-rw-rw-r-- 1 stanley stanley    323 Dec 20 07:16 readLargeFileStreams.js
-rw-r--r-- 1 stanley stanley 985084 Dec 20 07:12 words.txt
               total        used        free      shared  buff/cache   available
Mem:           7.6Gi       4.1Gi       201Mi       533Mi       3.3Gi       2.7Gi
Swap:          1.9Gi       139Mi       1.7Gi
Filesystem      Size  Used Avail Use% Mounted on
tmpfs           784M  1.9M  782M   1% /run
...
tmpfs           3.9G  147M  3.7G   4% /dev/shm
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
/dev/sda4       196M   30M  167M  15% /boot/efi

```

输出显示了目录内容、系统内存使用情况和文件系统磁盘使用情况。

虽然我们添加的`free -h`或`df -h`命令是无害的，但这不是我们的程序期望的输入。该程序只期望目录路径，但我们已经能够操纵该程序来完成不同于预期的任务。攻击者可以利用这个漏洞窥探系统信息，甚至破坏计算机系统。

为了保护自己免受这些攻击，您需要净化用户输入。还建议使用`execFile()`代替`exec()`方法。

## 结论

在本教程中，我们使用子流程模块从 Node.js 启动外部程序。我们首先使用`execFile()`方法运行外部程序并捕获其输出。然后我们使用`fork()`方法创建一个子进程来卸载阻塞的 CPU 绑定任务。之后，我们使用`spawn()`方法读取 Node.js 中的大文件，而不会占用太多内存。接下来，我们在 Node.js 中链接多个外部程序，然后使用`exec()`方法在 shell 中执行命令。最后，我们将了解外壳注入攻击。

现在，您应该可以轻松地在项目中使用 Node.js `child_process`模块了。要了解关于该模块的更多信息，请访问[文档](https://nodejs.org/api/child_process.html)。为了进一步学习，您可以了解一下 [`execa`库](https://blog.logrocket.com/running-commands-with-execa-in-node-js/)，它是`child_process`模块的包装器。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.