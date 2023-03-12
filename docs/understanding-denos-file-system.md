# 了解 Deno 的文件系统

> 原文：<https://blog.logrocket.com/understanding-denos-file-system/>

[Deno](https://blog.logrocket.com/deno-1-0-what-you-need-to-know/) 自带文件系统作为核心模块。这个文件系统模块可以用于任何类型的文件或目录操作。

在本教程中，我们将介绍可以在 Deno 应用程序中使用的文件系统方法。

目前，一些方法仍然是实验性的，并且相当不稳定；当使用这些方法时，您应该总是添加`--unstable`标志。

我们将带您了解以下内容:

## 如何用 Deno 写文件

用 Deno 写文件有几种方法。它们都需要`--allow-write`标志，如果出现一个错误就会抛出。

您可以使用`Deno.writeTextFile`或`Deno.writeFile`将文本写入文件。`Deno.writeTextFile`有同步和异步两种格式，接受两个参数:文件的位置和要写入下面文件的内容。

```
// using the async method
await Deno.writeTextFile('./file.txt', 'This is the content to be written');

//using sync method
Deno.writeTextFileSync('./file.txt', 'This is the content to be written');

```

您也可以使用带有`TextEncoder`的`Deno.writeFile`方法。`TextEncoder`将字符串转换为 Uint8Array:

```
const encoder = new TextEncoder(); // to convert a string to Uint8Array
await Deno.writeFile("./file.txt", encoder.encode("Content to be written"));

```

最后，您可以使用`Deno.open`和`Deno.writeAll`方法打开一个文件，向其中写入内容，然后关闭该文件，如下所示。

```
const file = await Deno.open("./image.png", { write: true, create: true }); //this opens the file
await Deno.writeAll(file, imageBytes); //writes to file
file.close(); // closes the file

```

如果您尝试写入一个不存在的文件，Deno 会自动创建该文件。

## 如何在 Deno 中读取文件

像写文件一样，Deno 中有无数种读取文件的方法，所有这些方法都需要使用`--allow-read`标志。

您可以使用`Deno.readTextFile`和`Deno.readFile`方法在 Deno 中读取文件。`Deno.readTextFile`有同步和异步两种格式，并接受文件路径作为参数:

```
// using the async method
const text = await Deno.readTextFile("file.txt");
console.log(text);

//using the sync method
const sync = Deno.readTextFileSync("file.txt");
console.log(sync);

```

另一种方法是`Deno.readFile`。您必须首先使用`TextDecoder`方法将文件解码为可读格式。

```
const decoder = new TextDecoder("utf-8");
const text = decoder.decode(await Deno.readFile("file.txt"));

console.log(text);

```

## 删除 Deno ( `remove`和`removeSync`)中的文件

要删除 Deno 中的文件，请使用`remove`或`removeSync`方法。

```
// Deno remove file asynchronous (non-block)
await Deno.remove("file.txt");

// Deno remove file synchronous (blocking way)
Deno.removeSync("image.png");

```

如果你试图删除一个不存在的文件，Deno 会抛出一个错误。

```
Uncaught NotFound: No such file or directory (os error 2)

```

## 检查目录中的文件夹(`ensureDir`)

`ensureDir`方法确保文件夹存在于您的工作目录中。你可以用它来写一个简单的程序。

让我们写一个简单的程序来检查文件夹是否存在。如果文件夹存在，它会创建一个新文件并向其中添加一些文本内容。

要使用此方法，必须将其导入到应用程序中。在您的工作目录中创建一个`notes`文件夹。这是你存放笔记的地方。

```
import { ensureDir, ensureDirSync } from "https://deno.land/std/fs/mod.ts";

```

像我们到目前为止讨论的其他方法一样，这种方法有异步和同步两种格式。下面是如何使用异步方法:

```
ensureDir("./notes")
  .then(() => Deno.writeTextFile("./notes/note1.txt", "This is the content for note 1")); 

```

## 复制文件内容(`copy`)

`copy`方法使您能够将文件内容复制到另一个文件。

要将`copy`方法导入到 Deno 应用程序中:

```
import { copy } from "https://deno.land/std/fs/mod.ts";
copy("file.txt", "test.txt", {
  overwrite: true,
});

```

该方法将文件内容从`file.txt`复制到`test.txt`。这也需要一些选择。例如，`overwrite`选项会覆盖您正在复制内容的文件的内容。

## 检查目录(`exists`)

方法检查一个目录是否存在。`exists`返回一个承诺，而`existsSync`返回一个布尔值。

让我们写一个简单的程序来检查一个目录是否存在:

```
import { exists, existsSync } from "https://deno.land/std/fs/mod.ts";

exists("./notes").then((res) => console.log(res)); //res here returns a boolean
 //or do this
let fileExists = existsSync("./notes"); 
console.log(fileExists);// returns boolean

```

## 检查文件是否存在(`ensureFile`)

确保文件存在。

让我们编写一个简单的程序来试验它:

```
import { ensureFile, ensureFileSync } from "https://deno.land/std/fs/mod.ts";
let ok = ensureFileSync("./read.ts");

```

如果文件不存在，Deno 会自动创建它。同样，记住添加`--unstable`标志，因为其中一些方法仍然是实验性的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 如何清空 Deno ( `emptyDir`)中的目录

方法检查一个目录是否为空。如果指定的目录包含任何文件或目录，Deno 将清空目录。

```
import { emptyDir } from "https://deno.land/std/fs/mod.ts";

emptyDir("./notes").then((res) => console.log("res", res)); // this method return a promise.

```

## 在 Deno 中创建 CLI 应用程序

现在让我们创建一个简单的 CLI 应用程序，它将为我们创建文件。我们将使用终端来创建这个文件。

`Deno.args`返回命令中传递的所有参数。这样，我们可以创建一个简单的应用程序。我们的命令将保存我们想要创建的文件的名称。

运行以下命令:

```
deno run main.ts create-file test.ts.
```

这个命令有两个参数:`create-file`和`test.ts`。如果我们记录`Deno.args`，它将返回一个数组中的参数。

我们可以用这个来检查美国是否

ser 传递了`create-file`参数并提供了一个文件名:

```
let params = Deno.args;
console.log(params);
let createFile = () => {
  if (params[0] !== "create-file") {
    console.log(
      `${params[0]} is not a valid command, Did you mean 'create-file'`,
    );
    return false;
  } else if (!params[1]) {
    console.log(
      `You need to provide a name of a file`,
    );
    return false;
  } else {
    Deno.writeTextFileSync(`./${params[1]}`, "//This is your created file");
  }
};
createFile();

```

现在要运行应用程序，打开您的终端并运行以下命令:

```
deno run --allow-all main.ts create-file <name of file>

```

记得添加`--allow-all`或`--allow-write`文件标志。

## 结论

如你所见， [Deno 的](https://deno.land/)文件系统相当通用，在向 Deno 写入文件时有多种方法和场景需要考虑。知道如何使用同步和异步方法总是好的，因为它们都有自己的用例。当写入较大的文件时，您应该考虑利用写入流的分块写入功能。

本演示中使用的源代码可在 [GitHub](https://github.com/Wisdom132/deno/tree/master/fs) 上获得。