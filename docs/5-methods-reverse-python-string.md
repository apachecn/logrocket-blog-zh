# 逆转 Python 字符串日志博客的 5 种方法

> 原文：<https://blog.logrocket.com/5-methods-reverse-python-string/>

在 Python 中，一个字符序列形成一个字符串。然而，在 Python 中，字符串是不可变的——改变字符串不会改变字符串，但会创建一个新的字符串。因此，Python 中没有内置的方法来反转字符串。

嗯，不用担心；我们有几个变通办法来完成反转字符串的任务。我们一个一个来看这些方法。

## 限幅

这是反转字符串最简单快捷的方法之一。Python 中的切片接受下面提到的三个参数:

1.  开始索引
2.  结束索引
3.  步骤(可选，但在我们的例子中我们需要它)

有两种方法可以使用扩展切片语法或`[slice](https://docs.python.org/3/library/functions.html#slice)`函数来使用这个操作符。

### 扩展切片

```
>>> 'raed ,tsop eht ot emocleW'[::-1]
'Welcome to the post, dear.'

>>> x = '.gnidaer rof sknahT'
>>> x[::-1]
'Thanks for reading.'
```

在上面的例子中，我们没有提供任何开始和结束索引。默认情况下，起始索引被视为`0`，结束索引被视为 `n-1`。我们将 step 称为`-1`，这意味着字符串从结尾继续遍历，并到达开头，提供期望的结果。如上所示，我们也可以将字符串赋给一个变量并执行这个操作。

### `slice`方法

```
>>> step = -1
>>> stop = None
>>> start = None
>>> slicing=slice(start, stop, step)
>>> '.siht gniyojne era uoy epoh I'[slicing]
'I hope you are enjoying this.'
```

这里，我们将值`-1`分配给步进，将`None`分配给开始和停止索引。然后，我们使用`slice()`函数，将对象存储到变量`slicing`中，稍后在字符串中使用它。

## `reversed`和`join`

在这个方法中，我们使用了两个内置函数:`reversed`和`join`。

### `reversed`

```
>>> for letter in reversed('abc'):
...     print(letter)
...
c
b
a
```

`reversed`方法返回迭代器(允许我们逐个访问元素)，我们可以以相反的顺序访问它。

### `join`

```
>>> ''.join(['a','b','c'])
'abc'
```

`join`方法从迭代器中获取值，并将它们缝合成一个字符串。开头的引号用于指定连接字符串的字符或模式。我们不想要任何模式；因此我们把它留为空白。

### 一起使用`reversed`和`join`

通过同时使用这两个函数，我们可以获得以下结果:

```
>>> custom_string = '.flesym ecudortni em teL'
>>> ''.join(reversed(custom_string))
'Let me introduce myself.'
```

这里，我们将字符串传递给`reversed`函数，并将其传递给`join`函数，后者提供所需的结果。

## 环

### `for`循环

```
>>> def reverse(input_string):
...     new_string = ''
...     for character in input_string:
...             new_string = character + new_string
...     return new_string
...
```

```
>>> reverse('radeK si eman yM')
'My name is Kedar'
```

1.  这里，我们创建一个函数`reverse`，它接受一个字符串作为输入。
2.  然后我们初始化一个空字符串，它将用于存储输出。
3.  遍历`input_string`中的每个字符，我们将`new_string`添加到该字符中。
4.  一旦循环完成，我们得到想要的结果，并返回它。

### `while`循环

使用`while`循环可以执行类似的操作。但是，首先，让我们看看这个例子:

```
>>> def w_reverse(input_string):
...     new_string = ''
...     count = len(input_string) - 1
...     while count >= 0:
...             new_string = new_string + input_string[count]
...             count = count - 1
...     return new_string

>>> w_reverse('?uoy era woH')
'How are you?'
```

1.  这里，我们创建一个函数并初始化一个新变量，与上一个例子相同
2.  现在我们将输入字符串的长度减去`1`，因为 Python 中的索引是从 `0`开始的。减法的动作也可以用另一种方式来完成，即`new_string = new_string + input_string[count - 1]`
3.  在`while`循环中，我们检查计算的长度是否大于或等于零。这一部分应该仔细完成，因为任何错误都可能导致程序无限循环
4.  接下来，我们将新字符串添加到迭代的字符中，并将计数减少`1`。我们减少计数，因为一旦迭代结束，我们需要停止；否则，将导致无限循环
5.  一旦计数小于零，`while`循环完成，我们得到结果

## 递归

```
>>> def reverse(str):
...     if len(str) == 0: # Checking the length of string
...         return str
...     else:
...         return reverse(str[1:]) + str[0]
...
>>> reverse('.syaw tnereffid ni gnirts nohtyP a esrever ot elba eb dluohs uoy ,won yB')
'By now, you should be able to reverse a Python string in different ways.'
```

递归是函数调用自身的概念。在我们的例子中，我们定义了一个接受字符串输入的函数。首先，我们检查接收到的字符串的长度是否等于零；如果是，我们返回字符串。然后，我们调用同一个函数，如果这个条件不满足，就传递除第一个字符以外的输入。结果将与第一个省略的字符连接。

只有当输入字符串的长度为零时，该函数才会结束。

## 列表`reverse`和`join`

这是我们今天要看的最后一个方法。这里我们将输入字符串转换成字符列表。然后我们在它上面使用`reverse`方法。最后，我们使用`join`方法来连接反转列表中的字符:

```
>>> def list_reverse(input_string):
...     input_list = list(input_string)
...     input_list.reverse()
...     return ''.join(input_list)
...
>>>
>>> list_reverse('dohtem tsal ehT')
'The last method'
```

## 结论

在任何编程语言中，处理字符串和反转字符串都是一项常见的任务。尽管这个主题可能没有令人信服的用例，但是这些知识将在您的编码面试中对您有所帮助。

以下是这篇文章中使用的策略的总结:

*   扩展切片(`[::-1]`)是反转字符串的最短和最简单的方法。然而，我们也可以使用`slice()`方法使其可读
*   使用`Join`和内置的`reversed`方法，返回一个 iterable
*   使用循环 [`for`和`while`](https://blog.logrocket.com/for-while-loops-python/) 是反转字符串最直接的方式
*   使用自调用函数—递归来反转字符串
*   利用 Python 中内置的`reverse`方法来反转列表并连接它

在计算机科学中，解决一个问题有多种方法，但并不是每个解决方案都是有效的。既然我们已经看到了反转 Python 字符串的不同方法，我鼓励你为你在上面学到的各种方法计算[运行时](https://stackoverflow.com/questions/1557571/how-do-i-get-time-of-a-python-programs-execution)。通过这种方式，你可以自己尝试每一种方法，这样你就能很好地掌握它，并最终拥有最快的策略。

在评论里让我知道哪个最快。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)