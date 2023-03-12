# 理解 Python - LogRocket 博客中的类型注释

> 原文：<https://blog.logrocket.com/understanding-type-annotation-python/>

Python 是公认的动态类型语言，这意味着变量的数据类型是在运行时确定的。换句话说，作为一名 Python 开发人员，您没有义务声明变量接受的值的数据类型，因为 Python 基于它保存的当前值来实现该变量的数据类型。

然而，这种特性的灵活性带来了一些缺点，这些缺点在使用静态类型语言(如 Java 或 C++)时通常不会遇到:

*   更多的错误将在运行时被检测出来，而这些错误本可以在开发时避免
*   缺少编译可能会导致代码性能不佳
*   冗长的变量使得代码更难阅读
*   对特定功能行为的不正确假设
*   由于类型不匹配导致的错误

Python 3.5 引入了[类型提示](https://docs.python.org/3/library/typing.html)，您可以使用 Python 3.0 中引入的类型注释将其添加到代码中。通过类型提示，您可以用数据类型来注释变量和函数。像 [mypy](https://mypy.readthedocs.io/en/stable/) 、 [pyright](https://github.com/microsoft/pyright) 、 [pytypes](https://pypi.org/project/pytypes/) 或 [pyre](https://pyre-check.org) 这样的工具执行静态类型检查的功能，并在这些类型使用不一致时提供提示或警告。

本教程将探讨类型提示以及如何将它们添加到 Python 代码中。它将关注 mypy 静态类型检查工具及其在代码中的操作。您将学习如何注释变量、函数、列表、字典和元组。您还将学习如何使用`Protocol`类、函数重载和注释常量。

开始之前

## 为了充分利用本教程，您应该:

Python ≥3.10 已安装

*   了解如何编写函数、 [f 字符串](https://docs.python.org/3/tutorial/inputoutput.html#fancier-output-formatting)和运行 Python 代码
*   了解如何使用命令行
*   我们推荐 Python ≥3.10，因为这些版本有新的更好的类型提示特性。如果您使用的是 Python ≤3.9，Python 提供了另一种类型提示语法，我将在本教程中演示。

什么是静态类型检查？

## 当在 C 和 Java 等静态类型语言中声明变量时，您必须声明变量的数据类型。因此，您不能分配与您为变量指定的数据类型不一致的值。例如，如果你声明一个变量是一个整数，你不能在任何时候给它赋一个字符串值。

在静态类型语言中，编译器在编写代码时监控代码，并严格确保开发人员遵守语言规则。如果没有发现问题，程序可以运行。

```
int x = 4;
x = "hello";  // this would trigger a type error

```

使用静态类型检查器有许多优点；其中一些包括:

检测类型错误

*   防止 bug
*   记录您的代码——任何想要使用带注释的函数的人一眼就能知道它接受的参数类型和返回值类型
*   此外，ide 能更好地理解您的代码，并提供良好的自动完成建议
*   Python 中的静态类型是可选的，可以逐渐引入(这就是所谓的渐进类型)。通过逐步键入，您可以选择指定应该动态或静态键入的代码部分。静态类型检查器将忽略代码的动态类型部分，不会对没有类型提示的代码发出警告，也不会阻止不一致的类型在运行时编译。

什么是 mypy？

## 由于 Python 在默认情况下是一种动态类型的语言，所以像 mypy 这样的工具被创建来为您提供静态类型环境的好处。mypy 是一个可选的静态类型检查器，由 Jukka Lehtosalo 创建。它检查 Python 中带注释的代码，如果带注释的类型使用不一致，就会发出警告。

mypy 还检查代码语法，并在遇到无效语法时发出语法错误。此外，支持渐进式键入，允许您以自己的速度在代码中慢慢添加类型提示。

向变量添加类型提示

## 在 Python 中，可以使用以下语法定义带有类型提示的变量:

让我们看看下面的变量:

```
variable_name: type = value

```

你给变量`name`分配一个字符串值`"rocket"`。

```
name = "rocket”

```

要对变量进行注释，您需要在变量名后添加一个冒号(`:`)，并声明一个类型`str`:

在 Python 中，可以使用`__annotations__`字典读取变量上定义的类型提示:

```
name: str = "rocket"

```

字典会显示所有全局变量的类型提示。

```
>>> name: str = "rocket"
>>> __annotations__
{'name': <class 'str'>}

```

如前所述，Python 解释器不强制类型，所以用错误的类型定义变量不会触发错误:

另一方面，像 mypy 这样的静态类型检查器会将此标记为错误:

```
>>> name: int = "rocket"
>>>

```

为其他数据类型声明类型提示遵循相同的语法。以下是一些可用于注释变量的简单类型:

```
error: Incompatible types in assignment (expression has type "str", variable has type "int")

```

`float`:浮点值，如`3.10`

*   `int`:整数，如`3`、`7`
*   `str`:字符串，如`'hello'`
*   `bool`:布尔值，可以是`True`或`False`
*   `bytes`:表示字节值，如`b'hello'`
*   用简单类型如`int`或`str`来注释变量可能没有必要，因为 mypy 可以推断出类型。但是，当处理复杂的数据类型(如列表、字典或元组)时，声明对应变量的类型提示很重要，因为 mypy 可能很难推断出这些变量的类型。

向函数添加类型提示

## 若要注释函数，请在每个参数和返回值后声明注释:

让我们注释以下返回消息的函数:

```
def function_name(param1: param1_type, param2: param2_type) -> return_type:

```

该函数接受一个字符串作为第一个参数，一个浮点数作为第二个参数，并返回一个字符串。为了注释函数参数，我们将在每个参数后附加一个冒号(`:`)，并在其后跟随参数类型:

```
def announcement(language, version):
    return f"{language} {version} has been released"

announcement("Python", 3.10)

```

`language: str`

*   `version: float`
*   要注释返回值类型，请在结束参数括号后立即添加`->`，就在函数定义冒号(`:`)之前:

该函数现在有类型提示，显示它接收`str`和`float`参数，并返回`str`。

```
def announcement(language: str, version: float) -> str:
    ...

```

当您调用该函数时，输出应该类似于如下所示:

尽管我们的代码有类型提示，但是如果您用错误的参数调用函数，Python 解释器不会提供警告:

```
result = announcement("Python", 4.11)
print(result) # Python 4.11 has been released

```

即使您将一个布尔值`True`作为第一个参数，将一个字符串`"Python"`作为第二个参数，该函数也能成功执行。为了收到关于这些错误的警告，我们需要使用一个像 mypy 这样的静态类型检查器。

```
result = announcement(True, "Python")
print(result) # True Python has been released

```

用 mypy 进行静态类型检查

## 我们现在将开始使用 mypy 进行静态类型检查的教程，以获取关于代码中类型错误的警告。

创建一个名为`type_hints`的目录，并将其移动到该目录中:

创建并激活虚拟环境:

```
mkdir type_hints && cd type_hints

```

用`pip`安装最新版本的 mypy:

```
python3.10 -m venv venv
source venv/bin/activate

```

安装 mypy 后，创建一个名为`announcement.py`的文件，并输入以下代码:

```
pip install mypy

```

保存文件并退出。我们将重用上一节中的同一个函数。

```
def announcement(language, version):
    return f"{language} {version} has been released"

announcement("Python", 3.10)

```

接下来，用 mypy 运行该文件:

如您所见，mypy 没有发出任何警告。Python 中的静态类型是可选的，对于渐进类型，除非您通过向函数添加类型提示来选择加入，否则您不会收到任何警告。这允许你慢慢地注释你的代码。

```
mypy announcement.py
Success: no issues found in 1 source file

```

现在让我们理解为什么 mypy 没有向我们显示任何警告。

更多来自 LogRocket 的精彩文章:

* * *

### `Any`型

* * *

### 正如我们注意到的，mypy 忽略没有类型提示的代码。这是因为它假设代码上的`Any`类型没有提示。

以下是 mypy 对该函数的理解:

`Any`类型是一种动态类型，可以兼容任何类型。所以 mypy 不会抱怨函数参数类型是`bool`、`int`、`bytes`等。

```
def announcement(language: Any, version: Any) -> Any:
    return f"{language} {version} has been released"

announcement("Python", 3.10)

```

现在我们知道了为什么 mypy 不总是发出警告，让我们配置它来这样做。

为类型检查配置 mypy

### mypy 可以配置为适合您的工作流和代码实践。您可以在严格模式下运行 mypy，使用`--strict`选项来标记任何没有类型提示的代码:

`--strict`选项是最受限制的选项，不支持逐步输入。大多数时候，你不需要这么严格。取而代之的是，采用渐进的键入方式来分阶段添加类型提示。

```
mypy --strict announcement.py

announcement.py:1: error: Function is missing a type annotation
announcement.py:4: error: Call to untyped function "print_release" in typed context
Found 2 errors in 1 file (checked 1 source file)

```

mypy 还提供了一个`--disallow-incomplete-defs`选项。该选项标记那些没有对所有参数和返回值进行注释的函数。当您忘记注释返回值或新添加的参数，导致 mypy 警告您时，这个选项非常方便。您可以将此视为您的编译器，它提醒您在代码开发中遵守静态类型规则。

为了理解这一点，只向参数添加类型提示，并省略返回值类型(假装您忘记了):

使用 mypy 运行该文件，不使用任何命令行选项:

```
def announcement(language: str, version: float):
    return f"{language} {version} has been released"

announcement("Python", 3.10)

```

如您所见，mypy 没有警告我们忘记注释返回类型。它在返回值上采用了`Any`类型。如果函数很大，就很难确定它返回的值的类型。为了知道类型，我们必须检查返回值，这很耗时。

```
mypy announcement.py
Success: no issues found in 1 source file

```

为了避免这些问题，请将`--disallow-incomplete-defs`选项传递给 mypy:

现在启用`--disallow-incomplete-defs`选项，再次运行文件:

```
mypy --disallow-incomplete-defs announcement.py

announcement.py:1: error: Function is missing a return type annotation
Found 1 error in 1 file (checked 1 source file

```

`--disallow-incomplete-defs`选项不仅警告您缺少类型提示，还标记任何数据类型-值不匹配。考虑下面的例子，其中`bool`和`str`值作为参数传递给分别接受`str`和`float`的函数:

```
def announcement(language: str, version: float) -> str:
    ...

```

```
mypy --disallow-incomplete-defs announcement.py
Success: no issues found in 1 source file

```

让我们看看 mypy 现在是否会警告我们:

```
def announcement(language: str, version: float) -> str:
    return f"{language} {version} has been released"

announcement(True, "Python")  # bad arguments

```

太好了！mypy 警告我们向函数传递了错误的参数。

```
mypy --disallow-incomplete-defs announcement.py
announcement.py:4: error: Argument 1 to "print_release" has incompatible type "bool"; expected "str"
announcement.py:4: error: Argument 2 to "print_release" has incompatible type "str"; expected "float"
Found 2 errors in 1 file (checked 1 source file)

```

现在，让我们消除使用`--disallow-incomplete-defs`选项键入`mypy`的需要。

mypy 允许您将选项保存在一个`mypy.ini`文件中。当运行`mypy`时，它会检查文件并使用文件中保存的选项运行。

每次使用 mypy 运行文件时，不一定需要添加`--disallow-incomplete-defs`选项。Mypy 为您提供了在一个`mypy.ini`文件中添加这个配置的选择，您可以在这个文件中添加一些 mypy 配置。

在项目根目录中创建`mypy.ini`文件，并输入以下代码:

在`mypy.ini`文件中，我们告诉 mypy 我们正在使用 Python 3.10，并且我们希望禁止不完整的函数定义。

```
[mypy]
python_version = 3.10
disallow_incomplete_defs = True

```

将文件保存在项目中，下次您可以在没有任何命令行选项的情况下运行 mypy:

mypy 有许多选项可以添加到`mypy`文件中。我推荐[参考 mypy 命令行文档](https://mypy.readthedocs.io/en/stable/command_line.html)了解更多。

```
mypy  announcement.py
Success: no issues found in 1 source file

```

向没有 return 语句的函数添加类型提示

## 并非所有函数都有 return 语句。当您创建一个没有返回语句的函数时，它仍然返回一个`None`值:

`None`值并不完全有用，因为您可能无法用它来执行操作。它只显示该功能执行成功。您可以通过用`None`注释返回值来暗示函数没有返回类型:

```
def announcement(language: str, version: float):
    print(f"{language} {version} has been released")

result = announcement("Python", 4.11)
print(result)  # None

```

在函数参数中添加联合类型提示

```
def announcement(language: str, version: float) -> None:
    ...

```

## 当函数接受多种类型的参数时，可以使用联合字符(`|`)来分隔类型。

例如，以下函数接受的参数可以是`str`或`int`:

可以用字符串或整数调用函数`show_type`，输出取决于它接收的参数的数据类型。

```
def show_type(num):
    if(isinstance(num, str)):
        print("You entered a string")
    elif (isinstance(num, int)):
        print("You entered an integer")

show_type('hello') # You entered a string
show_type(3)       # You entered an integer

```

为了对参数进行注释，我们将使用 Python 3.10 中引入的联合字符`|`来分隔类型，如下所示:

union `|`现在显示参数`num`不是`str`就是`int`。

```
def show_type(num: str | int) -> None:
...

show_type('hello')
show_type(3)

```

如果你使用的是 Python ≤3.9，你需要从`typing`模块导入`Union`。该参数可以注释如下:

向可选函数参数添加类型提示

```
from typing import Union

def show_type(num: Union[str, int]) -> None:
    ...

```

## 并非函数中的所有参数都是必需的；有些是可选的。下面是一个采用可选参数的函数示例:

第二个参数`title`是一个可选参数，如果它在调用函数时没有收到任何参数，那么它的默认值为`None`。`typing`模块提供了`Optional[<datatype>]`注释，用类型提示来注释这个可选参数:

```
def format_name(name: str, title = None) -> str:
    if title:
        return f"Name: {title}. {name.title()}"
    else:
        return f"Name: {name.title()}"

format_name("john doe", "Mr")

```

以下是如何执行此注释的示例:

```
parameter_name: Optional[<datatype>] = <default_datatype>
```

向列表添加类型提示

```
from typing import Optional

def format_name(name: str, title: Optional[str] = None) -> str:
    ...

format_name("john doe", "Mr")

```

## Python 列表是基于它们拥有的或预期拥有的元素类型来注释的。从 Python ≥3.9 开始，要注释一个列表，可以使用`list`类型，后跟`[]`。`[]`包含元素的类型数据类型。

例如，字符串列表可以注释如下:

如果你使用的是 Python ≤3.8，你需要从`typing`模块导入`List`:

```
names: list[str] = ["john", "stanley", "zoe"]

```

在函数定义中， [Python 文档](https://docs.python.org/3/library/typing.html#typing.List)推荐使用`list`类型来注释返回类型:

```
from typing import List

names: List[str] = ["john", "stanley", "zoe"]

```

但是，对于函数参数，文档建议使用这些抽象集合类型:

```
def print_names(names: str) -> list[int]:
...

```

何时使用`Iterable`类型来注释函数参数

### 当函数接受一个 iterable 并对其进行迭代时，应该使用`Iterable`类型。

iterable 是一个一次可以返回一项的对象。例子的范围从列表、元组和字符串到任何实现`__iter__`方法的东西。

在 Python ≥3.9 中，可以如下注释一个`Iterable`:

在函数中，我们定义了`items`参数，并给它分配了一个`Iterable[int]`类型提示，该提示指定了`Iterable`包含了`int`元素。

```
from collections.abc import Iterable

def double_elements(items: Iterable[int]) -> list[int]:
    return [item * 2 for item in items]

print(double_elements([2, 4, 6])) # list
print(double_elements((2, 4)))     # tuple

```

类型提示接受任何实现了方法的东西。列表和元组实现了该方法，所以您可以用列表或元组调用`double_elements`函数，该函数将对它们进行迭代。

要在 Python ≤3.8 中使用`Iterable`，必须从`typing`模块中导入:

在参数中使用`Iterable`比使用`list`类型提示或任何其他实现`__iter__`方法的对象更加灵活。这是因为在将一个 tuple 或任何其他 iterable 传递给函数之前，不需要将它转换成一个`list`。

```
from typing import Iterable
...

```

何时使用`Sequence`类型

### 序列是允许您访问一个项目或计算其长度的元素的集合。

类型提示可以接受列表、字符串或元组。这是因为他们有特殊的方法:`__getitem__`和`__len__`。当您使用`items[index]`从序列中访问一个项目时，将使用`__getitem__`方法。当得到序列长度`len(items)`时，使用`__len__`方法。

在下面的例子中，我们使用`Sequence[int]`类型接受一个包含整数项的序列:

该函数接受一个序列，并用`data[-1]`访问其中的最后一个元素。这使用序列上的`__getitem__`方法来访问最后一个元素。

```
from collections.abc import Sequence

def get_last_element(data: Sequence[int]) -> int:
    return data[-1]

first_item = get_last_element((3, 4, 5))    # 5
second_item = get_last_element([3, 8]    # 8

```

正如你所看到的，我们可以用一个元组或者列表来调用这个函数，这个函数工作正常。如果函数所做的只是获取一个项目，我们就不必将参数限制在`list`中。

对于 Python ≤3.8，需要从`typing`模块导入`Sequence`:

向词典添加类型提示

```
from typing import Sequence
...

```

## 要将类型提示添加到字典中，可以使用`dict`类型后跟`[key_type, value_type]`:

例如，以下字典将键和值都作为字符串:

您可以对其进行如下注释:

```
person = { "first_name": "John", "last_name": "Doe"}

```

`dict`类型指定`person`字典键属于`str`类型，值属于`str`类型。

```
person: dict[str, str] = { "first_name": "John", "last_name": "Doe"}

```

如果你使用的是 Python ≤3.8，你需要从`typing`模块导入`Dict`。

在函数定义中，文档推荐使用`dict`作为返回类型:

```
from typing import Dict

person: Dict[str, str] = { "first_name": "John", "last_name": "Doe"}

```

对于函数参数，建议使用这些抽象基类:

```
def make_student(name: str) -> dict[str, int]:
    ...

```

何时使用`Mapping`类

### 在函数参数中，当您使用`dict`类型提示时，您[将函数可以接受的参数](https://docs.python.org/3/library/collections.html#collections.defaultdict)限制为仅`dict`、`defaultDict`或`OrderedDict`。但是，有许多字典子类型，如 [`UserDict`](https://docs.python.org/3/library/collections.html#collections.UserDict) 和 [`ChainMap`](https://docs.python.org/3/library/collections.html#collections.ChainMap) ，可以类似地使用。

您可以像使用字典一样访问元素并迭代或计算它们的长度。这是因为它们实现了:

`__getitem__`:用于访问元素

*   `__iter__`:用于迭代
*   `__len__`:计算长度
*   因此，与其限制参数接受的结构，不如使用更通用的类型`Mapping`,因为它接受:

`dict`

*   `UserDict`
*   `defaultdict`
*   `OrderedDict`
*   `ChainMap`
*   `Mapping`类型的另一个好处是，它指定您只阅读字典，而不改变它。

以下示例是一个从字典中访问项目值的函数:

上述函数中的`Mapping`类型提示具有`[str, str]`描述，该描述指定`student`数据结构具有类型为`str`的键和值。

```
from collections.abc import Mapping

def get_full_name(student: Mapping[str, str]) -> str:
    return f'{student.get("first_name")} {student.get("last_name")}'

john = {
  "first_name": "John",
  "last_name": "Doe",
}

get_full_name(john)

```

如果您使用的是 Python ≤3.8，从`typing`模块导入`Mapping`:

使用`MutableMapping`类作为类型提示

```
from typing import Mapping

```

### 当函数需要改变字典或其子类型时，使用`MutableMapping`作为参数中的类型提示。变异的例子是删除项目或改变项目值。

`MutableMapping`类接受实现以下特殊方法的任何实例:

`__getitem__`

*   `__setitem__`
*   `__delitem__`
*   `__iter__`
*   `__len__`
*   `__delitem__`和`__setitem__`方法用于突变，这些方法将`Mapping`类型与`MutableMapping`类型分开。

在以下示例中，该函数接受一个字典并对其进行变异:

在函数体中，`first_name`变量中的值被赋给字典，并替换与`first_name`键配对的值。更改字典键值会调用`__setitem__`方法。

```
from collections.abc import MutableMapping

def update_first_name(student: MutableMapping[str, str], first_name: str) -> None:
    student["first_name"] = first_name

john = {
    "first_name": "John",
    "last_name": "Doe",
}

update_first_name(john, "james")

```

如果你用的是 Python ≤3.8，从`typing`模块导入`MutableMapping`。

使用`TypedDict`类作为类型提示

```
from typing import MutableMapping
...

```

### 到目前为止，我们已经研究了如何用`dict`、`Mapping`和`MutableMapping`来注释字典，但是大多数字典只有一种类型:`str`。但是，字典可以包含其他数据类型的组合。

下面是一个字典示例，它的键属于不同的类型:

字典值的范围从`str`、`int`和`list`。为了注释字典，我们将使用 Python 3.8 中引入的`TypedDict`。它允许我们用类似类的语法来注释每个属性的值类型:

```
student = {
  "first_name": "John",
  "last_name": "Doe",
  "age": 18,
  "hobbies": ["singing", "dancing"],
}

```

我们定义了一个继承自`TypedDict`的类`StudentDict`。在类内部，我们定义了每个字段及其预期类型。

```
from typing import TypedDict

class StudentDict(TypedDict):
    first_name: str
    last_name: str
    age: int
    hobbies: list[str]

```

定义了`TypedDict`之后，您可以使用它来注释一个字典变量，如下所示:

您还可以使用它来注释需要字典的函数参数，如下所示:

```
from typing import TypedDict

class StudentDict(TypedDict):
    ...

student1: StudentDict = {
    "first_name": "John",
    "last_name": "Doe",
    "age": 18,
    "hobbies": ["singing", "dancing"],
}

```

如果字典参数与`StudentDict`不匹配，mypy 将显示一个警告。

```
def get_full_name(student: StudentDict) -> str:
    return f'{student.get("first_name")} {student.get("last_name")}'

```

向元组添加类型提示

## 元组存储固定数量的元素。要向它添加类型提示，可以使用`tuple`类型，后跟`[]`，它接受每个元素的类型。

以下是如何用两个元素注释元组的示例:

不管元组包含多少个元素，都需要为每个元素声明类型。

```
student: tuple[str, int] = ("John Doe", 18)

```

`tuple`类型可以用作参数或返回类型值的类型提示:

如果你的元组应该有未知数量的相似类型的元素，你可以使用`tuple[type, ...]`来注释它们:

```
def student_info(student: tuple[str, int]) -> None:
    ...

```

要注释一个命名元组，需要定义一个从`NamedTuple`继承的类。类字段定义元素及其类型:

```
letters: tuple[str, ...] = ('a', 'h', 'j', 'n', 'm', 'n', 'z')

```

如果您有一个将命名元组作为参数的函数，您可以用命名元组来注释该参数:

```
from typing import NamedTuple

class StudentTuple(NamedTuple):
    name: str
    age: int

john = StudentTuple("John Doe", 33)

```

创建和使用协议

```
def student_info(student: StudentTuple) -> None:
    name, age = student
    print(f"Name: {name}\nAge: {age}")

student_info(john)

```

## 有时候你并不关心函数的参数。你只关心它是否有你想要的方法。

要实现这种行为，您需要使用一个协议。协议是从`typing`模块中的`Protocol`类继承的类。在 protocol 类中，定义一个或多个方法，静态类型检查器应该在使用协议类型的任何地方查找这些方法。

任何实现协议类方法的对象都将被接受。您可以将协议视为 Java 或 TypeScript 等编程语言中的接口。Python 提供了预定义的协议，一个很好的例子就是`Sequence`类型。不管它是什么类型的对象，只要它实现了`__getitem__`和`__len__`方法，它就接受它们。

让我们考虑下面的代码片段。下面是一个通过从当前年份中减去出生年份来计算年龄的函数示例:

该函数有两个参数:`current_year`(一个整数)和`data`(一个对象)。在函数体内，我们发现了`current_year`和从`get_birthyear()`方法返回的值之间的差异。

```
def calc_age(current_year: int, data) -> int:
    return current_year - data.get_birthyear()

```

下面是一个实现`get_birthyear`方法的类的例子:

这是这种类的一个例子，但是也可能有其他的类，比如实现了`get_birthyear`方法的`Dog`或`Cat`。注释所有可能的类型会很麻烦。

```
class Person:
    def __init__(self, name, birthyear):
        self.name = name
        self.birthyear = birthyear

    def get_birthyear(self) -> int:
        return self.birthyear

# create an instance
john = Person("john doe", 1996)

```

因为我们只关心`get_birthyear()`方法。为了实现这种行为，让我们创建我们的协议:

类`HasBirthYear`继承自`Protocol`，它是`typing`模块的一部分。为了让`Protocol`知道`get_birthyear`方法，我们将重新定义该方法，就像我们之前看到的`Person`类例子一样。唯一的例外是函数体，我们必须用省略号(`...`)替换函数体。

```
from typing import Protocol

class HasBirthYear(Protocol):
    def get_birthyear(self) -> int: ...

```

定义好协议后，我们可以在`calc_age`函数中使用它来为`data`参数添加类型提示:

现在，`data`参数已经用`HasBirthYear`协议进行了注释。这个函数现在可以接受任何对象，只要它有`get_birthyear`方法。

```
from typing import Protocol

class HasBirthYear(Protocol):
    def get_birthyear(self) -> int: ...

def calc_age(current_year: int, data: HasBirthYear) -> int:
    return current_year - data.get_birthyear()

```

下面是使用`Protocol`的代码的完整实现:

用 mypy 运行代码不会给你带来任何问题。

```
from typing import Protocol

class HasBirthYear(Protocol):
    def get_birthyear(self) -> int: ...

class Person:
    def __init__(self, name, birthyear):
        self.name = name
        self.birthyear = birthyear

    def get_birthyear(self) -> int:
        return self.birthyear

def calc_age(current_year: int, data: HasBirthYear) -> int:
    return current_year - data.get_birthyear()

john = Person("john doe", 1996)
print(calc_age(2021, john))

```

注释重载函数

## 一些函数根据你给它们的输入产生不同的输出。例如，让我们看看下面的函数:

当您以整数作为第一个参数调用函数时，它返回一个整数。如果调用函数时将列表作为第一个参数，它将返回一个列表，其中每个元素都添加了第二个参数值。

```
def add_number(value, num):
    if isinstance(value, int):
        return value + num
    elif isinstance(value, list):
        return [i + num for i in value]

print(add_number(3, 4))              # 7
print(add_number([1, 2, 5], 4))    # [5, 6, 9]

```

现在，我们如何注释这个函数呢？根据我们目前所知，我们的第一反应是使用 union 语法:

然而，由于其模糊性，这可能会引起误解。上面的代码描述了一个接受整数作为第一个参数的函数，该函数返回一个`list`或一个`int`。类似地，当您传递一个`list`作为第一个参数时，该函数将返回一个`list`或一个`int`。

```
def add_number(value: int | list, num: int) -> int | list:
 ...

```

您可以实现函数重载来正确地注释这个函数。使用函数重载，您可以定义同一个函数的多个定义，而不用定义体，向它们添加类型提示，并将它们放在主函数实现之前。

为此，用来自`typing`模块的`overload`装饰器来注释函数。让我们在`add_number`函数实现之前定义两个重载:

我们在主函数`add_number`之前定义了两个重载。重载参数用适当的类型及其返回值类型进行了注释。它们的函数体包含一个省略号(`...`)。

```
from typing import overload

@overload
def add_number(value: int, num: int) -> int: ...

@overload
def add_number(value: list, num: int) -> list: ...

def add_number(value, num):
    if isinstance(value, int):
        return value + num
    elif isinstance(value, list):
        return [i + num for i in value]

print(add_number(3, 4))
print(add_number([1, 2, 5], 4)

```

第一个重载表明，如果将`int`作为第一个参数传递，函数将返回`int`。

第二个重载表明，如果您将一个`list`作为第一个参数传递，该函数将返回一个`list`。

```
@overload
def add_number(value: int, num: int) -> int: ...

```

最后，主`add_number`实现没有任何类型提示。

```
@overload
def add_number(value: list, num: int) -> list: ...

```

正如您现在看到的，重载比使用联合更好地注释了函数行为。

用 Final 注释常数

## 在撰写本文时， [Python 还没有定义常量的内置方式](https://docs.python.org/3/library/typing.html#typing.Final)。从 Python 3.10 开始，您可以使用来自`typing`模块的`Final`类型。这意味着如果试图改变变量值，mypy 将发出警告。

运行带有`mypy`的代码并发出警告:

```
from typing import Final
MIN: Final = 10
MIN = MIN + 3

```

这是因为我们试图将`MIN`变量值修改为`MIN = MIN + 3`。

```
final.py:5: error: Cannot assign to final name "MIN"
Found 1 error in 1 file (checked 1 source file)

```

请注意，如果没有 mypy 或任何静态文件检查器，Python 将不会强制执行此操作，代码将会顺利运行:

如您所见，在运行时，您可以随时更改变量值`MIN`。要在代码库中强制使用常量变量，您必须依赖于 mypy。

```
>>> from typing import Final
>>> MIN: Final = 10
>>> MIN = MIN + 3
>>> MIN
>>> 13

```

处理第三方包中的类型检查

## 虽然您可以向代码中添加批注，但是您使用的第三方模块可能没有任何类型提示。因此，mypy 会警告您。

如果您收到这些警告，您可以使用类型注释来忽略第三方模块代码:

您还可以选择添加带有存根的类型提示。要了解如何使用存根，请参见 mypy 文档中的[存根文件](https://mypy.readthedocs.io/en/stable/stubs.html)。

```
import third_party # type ignore

```

结论

## 本教程探讨了静态类型代码和动态类型代码之间的区别。您了解了向函数和类添加类型提示的不同方法。您还了解了使用 mypy 进行静态类型检查，如何向变量、函数、列表、字典和元组添加类型提示，以及如何使用协议、函数重载和注释常量。

要继续积累您的知识，请访问 [typing —对类型提示的支持](https://docs.python.org/3/library/typing.html)。要了解更多关于 mypy 的信息，请访问 [mypy 文档](https://mypy.readthedocs.io/en/stable/)。

使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

## [LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

Focus on the bugs that matter — [try LogRocket today](https://lp.logrocket.com/blg/signup-issue-free).