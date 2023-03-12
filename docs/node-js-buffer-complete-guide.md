# Node.js buffer:完整的指南- LogRocket 博客

> 原文：<https://blog.logrocket.com/node-js-buffer-complete-guide/>

***编者按*** *:这篇文章最后更新于 2022 年 7 月 14 日，以包括额外的缓冲方法。*

如果你回想你被教导计算机只能理解二进制语言的时候，你可能会想起缓冲区是计算机内存中的一个空间，通常是 RAM，用来存储二进制文件。在本教程中，我们将遍历 [Node.js buffer](https://nodejs.org/api/buffer.html) 模块的基础知识，并学习如何在我们自己的应用程序中使用 Node.js buffer 方法。

## 什么是二进制代码？

为了理解什么是缓冲区以及计算机和二进制代码之间的关系，让我们回顾一下二进制代码的一般概念。

为了让计算机理解、处理和存储数据，必须将数据转换成二进制代码。这主要是因为计算机处理器是由晶体管组成的，晶体管是由 on `(1)`和 off `(0)`信号激活的电子机器。

发送到计算机的每一条数据在处理和输出结果之前，首先由微处理器转换成二进制。因此，能够区分不同的数据类型至关重要。通过称为编码的过程，计算机对不同的数据类型进行不同的编码，以区分不同的数据类型。

## Node.js 缓冲区是什么？

二进制流是大量二进制数据的集合。由于其巨大的尺寸，二进制流不会一起发送。相反，它们在发送前被分成更小的片段。

当数据处理单元不能接受更多的数据流时，多余的数据被存储在缓冲器中，直到数据处理单元准备好接收更多的数据。

## Node.js 中的缓冲区类

Node.js 服务器最经常需要读写文件系统，当然，文件存储在二进制文件中。Node.js 还处理 TCP 流，TCP 流在以小块发送二进制数据之前保护与接收者的连接。

发送到接收方的数据流需要存储在某个地方，直到接收方准备好接收更多的数据块进行处理。这就是 Node.js 缓冲区类发挥作用的地方。它在 [V8 引擎](https://en.wikipedia.org/wiki/V8_(JavaScript_engine))之外处理和存储二进制数据。

让我们回顾一下不同的缓冲方法，并了解如何在 Node.js 应用程序中使用它们。

## Node.js 缓冲区方法

Node.js buffer 模块的一个很酷的地方是，在使用它的方法之前，您不需要将它导入到您的应用程序中。让我们回顾一些您应该知道的重要 Node.js 缓冲区方法。

### `Buffer.alloc()`

方法创建一个任意大小的新缓冲区。使用此方法时，以字节为单位指定缓冲区的大小。下面的表达式创建一个字节大小为`6`的缓冲区:

```
const buf = Buffer.alloc(6);

console.log(buf);
// This will print <Buffer 00 00 00 00 00 00>

```

### `Buffer.write()`

`Buffer.write()`方法将一个字符串写入缓冲区，这在您需要以缓冲区的形式传输字符串时非常有用。您可以使用以下方法将字符串写入缓冲区:

```
const buf = Buffer.alloc(100); // Creating a new Buffer
const len = buf.write("Hello world!"); // Writing to the Buffer

// len is now 12

```

`Buffer.write()`函数返回字符串的长度，该长度存储在缓冲区中。

### `Buffer.byteLength()`

您可以使用`Buffer.byteLength()`方法检查缓冲对象的长度。下面的代码演示了如何创建一个缓冲区，给它附加一个大小，并检查刚刚创建的缓冲区的大小:

```
var buf = Buffer.alloc(6);

//check the length of buffer created
var buffLen = Buffer.byteLength(buf);

//print buffer length
console.log(buffLen);
// This will print <6>

```

### `Buffer.compare()`

`Buffer.compare()`方法使您能够比较两个缓冲区对象，以检查它们是否相等。根据比较的结果，该方法返回`-1`、`0`或`1`。

您可以用如下所示的`Buffer.compare()`方法比较缓冲区对象:

```
var buf1 = Buffer.from('xyz');
var buf2 = Buffer.from('xyz');
var a = Buffer.compare(buf1, buf2);

//This will return 0
console.log(a);

var buf1 = Buffer.from('x');
var buf2 = Buffer.from('y');
var a = Buffer.compare(buf1, buf2);

//This will return -1
console.log(a);

var buf1 = Buffer.from('y');
var buf2 = Buffer.from('x');
var a = Buffer.compare(buf1, buf2);

//This will return 1
console.log(a);

```

### `Buffer.concat()`

就像字符串连接一样，您可以将两个或多个缓冲区对象连接成一个对象。您还可以获得新对象的长度:

```
var buffer1 = Buffer.from('x');
var buffer2 = Buffer.from('y');
var buffer3 = Buffer.from('z');
var arr = [buffer1, buffer2, buffer3];

/*This will print buffer, !concat [ <Buffer 78>, <Buffer 79>, <Buffer 7a> ]*/
console.log(arr);

//concatenate buffer with Buffer.concat method
var buf = Buffer.concat(arr);

//This will print <Buffer 78 79 7a> concat successful
console.log(buf);

```

### `buf.entries()`

使用`buf.entries()`，可以从缓冲区对象的内容中返回一个索引和字节的循环，用来知道缓冲区内容的位置和大小:

```
var buf = Buffer.from('xyz');

for (a of buf.entries()) {
/*This will print arrays of indexes and byte of buffer content \\[ 0, 120 \][ 1, 121 \][ 2, 122 ]*/
  console.log(a);
} 

```

### `Buffer.fill()`

`Buffer.fill()`方法使您能够创建一个缓冲区，分配一个大小，并用一个指定的值填充它。下面的表达式显示了如何使用`Buffer.fill()`方法:

```
const b = Buffer.alloc(10).fill('a');

console.log(b.toString());
// This will print aaaaaaaaaa

```

### `Buffer.from()`

`buffer.from()`方法使您能够从任何对象创建新的缓冲区，比如字符串、缓冲区、数组和`ArrayBuffer()`。您所要做的就是指定您想要从中创建缓冲区的对象。使用这种方法的语法是`Buffer.from(object[, offsetOrEncoding[,length]])`。

偏移量或编码参数是可选的；它用于在转换为缓冲区时指定字符串编码。如果在从字符串创建缓冲区时不指定编码参数，将使用默认编码`utf8`创建缓冲区。

您还可以使用 length 参数指定要公开的字节数，尤其是从`ArrayBuffer`创建缓冲区时。从数组创建缓冲区时，数组字节应该在`0`和`255`之间。否则，数组条目将被缩短以适应。

下面的例子展示了如何使用`buffer.from()`方法从字符串、数组和`ArrayBuffer()`创建一个缓冲区:

```
// Create a buffer from a string
var mybuff = Buffer.from("Nigeria");
//Print Buffer Created
console.log(mybuff);

// Create a buffer from a buffer
// Create buffer from string
var mybuff = Buffer.from("Nigeria");
// Create buffer from the first buffer created
var buff = Buffer.from(mybuff);
// Print out final buffer created.
console.log(buff);

// create a buffer from an array
const buf = Buffer.from([0x62, 0x75, 0x66, 0x66, 0x65, 0x72]);

// Create a buffer from an arraybuffer
const ab = new ArrayBuffer(10);
// Specify offset and length
const buf = Buffer.from(ab, 0, 2);
console.log(buff);

```

### `buff.includes()`

如果你想确定一个缓冲对象是否包含任何值，你可以使用`buff.includes()`方法。使用这种方法，您可以搜索缓冲区以确定它们是否包含您想要搜索的表达式。该方法根据是否找到值返回布尔值`true`或`false`:

```
const buf = Buffer.from('this is a buffer');
console.log(buf.includes('this'));
// This will print true

console.log(buf.includes(Buffer.from('a buffer example')));
// This will print false

```

### `Buffer.isEncoding()`

要区分二进制文件，必须对它们进行编码。您可以使用`Buffer.isEncoding()`方法来确认是否支持特定的字符编码类型:

```
console.log(Buffer.isEncoding('hex'));
// This will print true

console.log(Buffer.isEncoding('utf-8'));
// This will print true

console.log(Buffer.isEncoding('utf/8'));
// This will print false

console.log(Buffer.isEncoding('hey'));
// This will print false

```

### `buf.slice()`

就像 JavaScript 中的 slice 方法一样，`buf.slice()`用于从数组中选择的元素创建一个新的数组。对数组进行切片时，会使用切片中选择的元素列表创建一个新数组:

```
var a = Buffer.from('uvwxyz');
var b = a.slice(2,5);

console.log(b.toString());
//This will print wxy

```

### 缓冲交换

缓冲区交换用于交换缓冲区对象的字节顺序。该方法也可用于快速[字符顺序](https://en.wikipedia.org/wiki/Endianness)转换。

您可以使用`buf.swap16()`、`buf.swap32()`和`buf.swap64()`分别交换 16 位、32 位和 64 位缓冲对象的字节顺序:

```
const buf1 = Buffer.from([0x1, 0x2, 0x3, 0x4, 0x5, 0x6, 0x7, 0x8]);
console.log(buf1);
// This will print <Buffer 01 02 03 04 05 06 07 08>

//swap byte order to 16 bit
buf1.swap16();
console.log(buf1);
// This will print <Buffer 02 01 04 03 06 05 08 07>

//swap byte order to 32 bit
buf1.swap32();
console.log(buf1);
// This will print <Buffer 03 04 01 02 07 08 05 06>

//swap byte order to 64 bit
buf1.swap64();
console.log(buf1);
// This will print <Buffer 06 05 08 07 02 01 04 03>

```

### `buf.json()`

`buf.json()`方法返回缓冲区对象的 JSON 版本:

```
const buf = Buffer.from([0x1, 0x2, 0x3, 0x4, 0x5, 0x6, 0x7, 0x8]);

console.log(buf.toJSON());
// This will print {"type":"Buffer", data:[1,2,3,4,5,6,7,8]}

```

### 缓冲器偏移读取

使用 Buffer offset Read 方法，您可以确定在开始从 Buffer 对象读取未赋值的整数之前要跳过的字节数。要跳过的字节数由附加到此方法的偏移量决定。

例如，您可以读取 64 位双精度、32 位浮点、16 位整数和 32 位整数。根据您使用的 [Node.js 方法](https://nodejs.org/api/buffer.html#buffer_buf_readbigint64be_offset)，结果可能是[小端或大端。](https://chortle.ccsu.edu/AssemblyTutorial/Chapter-15/ass15_3.html)也可以用`buf.readInt8()`读取 8 位整数。

### 缓冲器偏移写入

Buffer offset write 方法用于根据附加到该方法的偏移量将指定的字节写入缓冲区对象。

根据所使用的方法，此方法可以以小端或大端方式写入 64 位双精度、32 位浮点、8 位整数、16 位整数和 32 位整数。每个方法中的值应该对应于该方法指定的整数。例如，`buf.writeFloatBE()`应该有一个 32 位浮点值。

## 结论

当你执行一个程序的时候，你必须了解它的内部发生了什么。现在，您应该对二进制文件、流、缓冲区之间的关系以及 Node.js 缓冲区的工作原理有了坚实的基础理解。您还应该理解为什么需要使用 Node.js 缓冲区类和各种 Node.js 缓冲区方法。

我们没有机会介绍 Node.js buffer 类中所有可用的方法。你也可以用 [`allocUnsafe()`方法](https://nodejs.org/api/buffer.html#buffer_static_method_buffer_allocunsafe_size)创建一个缓冲区对象，但是创建的缓冲区将不填充 0。

如果你想比较缓冲对象，`Buffer.compare()`方法很适合你，但是`[buf.equals()](https://nodejs.org/api/buffer.html#buffer_buf_equals_otherbuffer)`比较后返回 true 或 false，而不是`1`、`-1`和`0`。你可以在 [Node.js 文档](https://nodejs.org/api/buffer.html)中阅读更多关于 Node.js 缓冲模块的内容。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.