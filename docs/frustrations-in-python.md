# Python 中的挫折

> 原文：<https://blog.logrocket.com/frustrations-in-python/>

黑暗势力施展他们邪恶的魔法，侵入我们珍贵的 Python 程序领域。

他们不受控制地发送他们扭曲的魔法，污染我们可读的代码。

今天，我将展示几个可能已经存在于你的代码库中的生物，它们已经习惯于开始制定自己的规则。我们需要一个英雄来保护我们和平的世界免受这些邪恶实体的侵害。你将成为与他们战斗的英雄！

![](img/c1005556d44667289238506c1d68cbdd.png)

在史诗般的战斗中，所有的英雄都需要被光魔法附体的武器来为他们服务。

[wemake-python-styleguide](https://github.com/wemake-services/wemake-python-styleguide) 将是你的利器，也是你最好的伴侣。

让我们开始我们的旅程吧！

### 太空入侵者

不久前，太空入侵者在 Python 中被[发现。它们采取奇异的形式。](https://twitter.com/raymondh/status/1131103570856632321)

```
  5:5      E225  missing whitespace around operator
  x -=- x
      ^

  5:5      WPS346 Found wrong operation sign
  x -=- x
      ^

  10:2     E225  missing whitespace around operator
  o+=+o
   ^

  14:10    E225  missing whitespace around operator
  print(3 --0-- 5 == 8)
           ^

  14:10    WPS346 Found wrong operation sign
  print(3 --0-- 5 == 8)
           ^

  14:11    WPS345 Found meaningless number operation
  print(3 --0-- 5 == 8)
            ^

  14:12    E226  missing whitespace around arithmetic operator
  print(3 --0-- 5 == 8)
             ^

  14:13    WPS346 Found wrong operation sign
  print(3 --0-- 5 == 8)
              ^

```

我们的代码库应该是这样的:

```
x = 1
x += x

o = 2
o += o

print(3 + 5 == 8)

```

可读性和干净！

### 神秘的点

一些市民报告说一些奇怪的代码开始出现。看，他们在这里！

```
print(0..__eq__(0))
# => True

print(....__eq__(((...))))
# => True

```

这是怎么回事？在我看来像是部分`float`和`Ellipsis`，但最好确定一下。

```
  21:7     WPS609 Found direct magic attribute usage: __eq__
  print(0..__eq__(0))
        ^

  21:7     WPS304 Found partial float: 0.
  print(0..__eq__(0))
        ^

  24:7     WPS609 Found direct magic attribute usage: __eq__
  print(....__eq__(((...))))
        ^

```

哎哟！现在我们确定了。确实是点属性访问的分部`float`和点访问相同的`Elipsis`。现在让我们揭示所有隐藏的东西:

```
print(0.0 == 0)
print(... == ...)

```

尽管如此，最好不要激怒别人，也不要在其他地方比较常数。

### 误导的道路

我们有一个新的事件。有些值从未从函数中返回。让我们弄清楚这是怎么回事。

```
def some_func():
    try:
       return 'from_try'
    finally:
       return 'from_finally'

some_func()
# => 'from_finally'

```

由于我们的代码中有一个损坏的实体，我们丢失了`'from_try'`，如何解决这个问题？

```
 31:5 WPS419 Found `try`/`else`/`finally` with multiple return paths
 try:
 ^

```

原来`wemake-python-styleguide`一直都知道！它教导我们永远不要从`finally`返回。让我们服从它。

```
def some_func():
  try:
      return 'from_try'
  finally:
      print('now in finally')

```

### 继承的遗产

一些远古生物正在苏醒。几十年没见过了。而现在它已经[返回了](https://twitter.com/dabeaz/status/1199376319961849861)。

```
a = [(0, 'Hello'), (1, 'world')]
for ['>']['>'>'>'], x in a:
    print(x)

```

这是怎么回事？人们可以在循环中隐式解包值。解包的目标几乎可以是任何有效的 Python 表达式。

但是，我们不应该从这个例子中做很多事情:

```
  44:1     WPS414 Found incorrect unpacking target
  for ['>']['>'>'>'], x in a:
  ^

  44:5     WPS405 Found wrong `for` loop variable definition
  for ['>']['>'>'>'], x in a:
      ^

  44:11    WPS308 Found constant compare
  for ['>']['>'>'>'], x in a:
            ^

  44:14    E225  missing whitespace around operator
  for ['>']['>'>'>'], x in a:
               ^

  44:21    WPS111 Found too short name: x
  for ['>']['>'>'>'], x in a:
                      ^

```

看起来`['>'\]['>'>'>']`只是`['>'\][0]`，因为`'>' > '>'`是`False`。

这个案子解决了。

### 黑巫师的签名

[Python 中的表达式可以有多复杂](https://sobolevn.me/2019/10/complexity-waterfall)？黑巫师在他接触的所有职业上都留下了复杂的印记:

```
class _:
    # There are four of them, do you see it?
    _: [(),...,()] = {((),...,()): {(),...,()}}[((),...,())]

print(_._) # this operator also looks familiar 🤔
# => {(), Ellipsis}

```

这个签名怎么看，怎么评价？看起来是由几个部分组成:
-声明和类型注释:`_: [(),...,()] =`
-以 set 为值的字典定义:`= { ((),...,()): {(),...,()} }`
-Key access:`[((),...,())]` 
虽然它对这个世界的人类没有任何意义，但它仍然是一个有效的 Python 代码，可以用来做一些邪恶的事情。让我们删除它:

```
  55:5     WPS122 Found all unused variables definition: _
  _: [(),...,()] = {((),...,()): {(),...,()}}[((),...,())]
  ^

  55:5     WPS221 Found line with high Jones Complexity: 19
  _: [(),...,()] = {((),...,()): {(),...,()}}[((),...,())]
  ^

  55:36    WPS417 Found non-unique item in hash: ()
  _: [(),...,()] = {((),...,()): {(),...,()}}[((),...,())]
                                 ^

  57:7     WPS121 Found usage of a variable marked as unused: _
  print(_._)  # this operator also looks familiar 
        ^

```

现在这个复杂的表达式(琼斯复杂度为 19)被移除或重构。任何黑色来源的签名从这个可怜的职业中移除。让我们平静地离开它。

### 超魔法

我们的普通班开始和一些可疑的人混在一起。我们需要保护他们免受这种不良影响。
目前，他们的产量真的很奇怪:

```
class Example(type((lambda: 0.)())):
 ...

print(Example(1) + Example(3))
# => 4.0

```

为什么`1 + 3`是`4.0`而不是`4`？为了找到答案，让我们打开`type((lambda: 0.)())`的包装:
–`(lambda: 0.)()`就是`0.`，而`0.`就是`0.0`。
–`type(0.0)`是`float`
–当我们写`Example(1)`时，它在类内部被转换为`Example(1.0)`。
–`Example(1.0) + Example(3.0)`是`Example(4.0)`

让我们确保我们的武器一如既往的锋利:

```
  63:15    WPS606 Found incorrect base class
  class Example(type((lambda: 0.)())):
                ^

  63:21    WPS522 Found implicit primitive in a form of lambda
  class Example(type((lambda: 0.)())):
                      ^

  63:29    WPS304 Found partial float: 0.
  class Example(type((lambda: 0.)())):
                              ^

  64:5     WPS428 Found statement that has no effect
  ...
  ^

  64:5     WPS604 Found incorrect node inside `class` body
  ...
  ^

```

我们在这里发现了所有可能的问题。我们的班级是安全的。是时候向前看了。

### 再生器

如此相似又如此不同。再生器[在我们的源代码中找到](https://stackoverflow.com/questions/32139885/yield-in-list-comprehensions-and-generator-expressions)。它看起来像一个普通的生成器表达式，但它是完全不同的东西。

```
a = ['a', 'b']
print(set(x + '!' for x in a))
# => {'b!', 'a!'}

print(set((yield x + '!') for x in a))
# => {'b!', None, 'a!'}

```

这是 Python 中的一个 bug 是的，它们确实存在。并且由于`python3.8`是一个`SyntaxError`，不应该在发生器功能之外使用`yield`和`yield from`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

以下是我们对该事件的常规报道:

```
  73:7     C401  Unnecessary generator - rewrite as a set comprehension.
  print(set(x + '!' for x in a))
        ^

  76:7     C401  Unnecessary generator - rewrite as a set comprehension.
  print(set((yield x + '!') for x in a))
        ^

  76:11    WPS416 Found `yield` inside comprehension
  print(set((yield x + '!') for x in a))
```

此外，让我们按照建议正确地写理解。

```
print({x + '!' for x in a})

```

这是一个很难解决的问题。但最终，再生器消失了，错误的理解也消失了。下一步是什么？

### 电子邮件邪恶克隆

如果需要写电子邮件地址，就用字符串。对吗？不对！

做常规的事情有不寻常的方法。还有常规数据类型的邪恶克隆。我们将会发现它们。

```
class G:
    def __init__(self, s):
        self.s = s
    def __getattr__(self, t):
        return G(self.s + '.' + str(t))
    def __rmatmul__(self, other):
        return other + '@' + self.s

username, example = 'username', G('example')
print([email protected])
# => [email protected]

```

它是如何工作的？
–`@`是 Python 中的一个操作符，它的行为可以通过`__matmul__`和`__rmatmul__`魔法方法修改
–`.com`是一个属性`com`点访问，它可以通过`__getattr__` 
修改，这段代码和其他例子的一个很大的区别是这段代码实际上是有效的。只是不寻常。我们可能不应该使用它。但是，让我们把这个写进我们的知识探索书。

### 海象的谬误

黑暗降临到了 Python 身上。它分裂了友好的开发人员社区，带来了争议。你已经获得了字符串编程的能力:

```
from math import radians
for angle in range(360):
    print(f'{angle=} {(th:=radians(angle))=:.3f}')
    print(th)

# => angle=0 (th:=radians(angle))=0.000
# => 0.0
# => angle=1 (th:=radians(angle))=0.017
# => 0.017453292519943295
# => angle=2 (th:=radians(angle))=0.035
# => 0.03490658503988659

```

这是怎么回事？
–`f'{angle=}`是一种新的(python3.8+)编写方式`f'angle={angle}`
–`(th:=radians(angle))`是一个赋值表达式，是的，你现在可以在字符串中进行赋值了
–`=:.3f`是格式化部分，它返回表达式及其舍入结果值
–`print(th)`起作用，因为`(th:=radians(angle))`具有局部范围效应

应该使用赋值表达式吗？这取决于你。应该在字符串内部赋值吗？绝对不行。

这里有一个友好的提示，提醒你可以(但也可能不应该)用`f`字符串本身做些什么:

```
print(f"{getattr(__import__('os'), 'eman'[None:None:-1])}")
# => posix

```

只是一个字符串中的常规模块导入——继续，这里没什么可看的。

幸运的是，我们不允许在真实代码中写这一行:

```
105:1    WPS221 Found line with high Jones Complexity: 16
  print(f"{getattr(__import__('os'), 'eman'[None:None:-1])}")
  ^

  105:7    WPS305 Found `f` string
  print(f"{getattr(__import__('os'), 'eman'[None:None:-1])}")
        ^

  105:18   WPS421 Found wrong function call: __import__
  print(f"{getattr(__import__('os'), 'eman'[None:None:-1])}")
                   ^

  105:36   WPS349 Found redundant subscript slice
  print(f"{getattr(__import__('os'), 'eman'[None:None:-1])}")
                                     ^

```

还有一点:`f`string 不能用作 docstrings:

```
def main():
    f"""My name is {__file__}/{__name__}!"""

print(main().__doc__)
# => None

```

### 结论

我们与许多滋生在我们代码中的丑陋怪物战斗，让 Python 的土地成为一个更好的居住地。你应该为自己感到骄傲，英雄！

那是一次史诗般的旅程。我希望你学到了新的东西:在接下来的战斗中变得更强大。世界需要你！

今天到此为止。注意安全，旅行者。

### 有用的链接

使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

## [LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

Focus on the bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/signup-issue-free).