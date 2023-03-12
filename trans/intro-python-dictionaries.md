# Python 字典介绍- LogRocket 博客

> 原文：<https://blog.logrocket.com/intro-python-dictionaries/>

字典在 Python 中是必不可少的，因为它们的引入代表了编程的重大进步。在字典出现之前，编程要困难得多；您必须在列表或数组中维护所有数据，并记住哪些索引用于哪些数据，从而创建了非常容易出错的过程。

为了更快地定位值，开发人员必须对列表进行排序并利用二分搜索法，如果数据是动态的，开发人员必须不断地重新排序。这个过程经常导致非常缓慢的代码，需要进一步关注。

然而，Python 字典解决了这个问题。

Python 中的字典是一种以无序方式存储变量的数据类型，其中值被映射到一个键，并且可以使用每个项目的键轻松访问。键是一个不可变的元素，代表字典中的一个值。

在本文中，您将了解 Python 中的字典、它们的属性、可以对它们执行的操作，以及一些使用字典的内置 Python 函数和方法。

## Python 字典的属性

Python 字典拥有一些独特的行为，使它不同于其他数据结构。这些属性包括:

*   不可变字典键
*   无序的数据集合
*   值的多种数据类型
*   关键数据类型可以是数字、字符串、浮点、布尔、元组以及类和函数等内置对象

此外，字典关键字必须唯一。如果字典中定义了重复的键，Python 会考虑最后一个重复的键。

## Python 字典操作

### 用 Python 声明字典

在 Python 中，可以通过将一系列值对(格式为`key: value`的键和键-值)用逗号分隔在大括号中来声明字典:

```
dict = {"first-key":1,"second-key":2}
```

您还可以使用空花括号定义一个空字典，如下面的代码片段所示:

```
dict = {}
```

### 在 Python 字典中检索项目

要获取字典中项的值，请输入字典名称，并将该项的关键字放在方括号中:

```
# declared the dictionary
dict = {"first-key":1,"second-key":2}

# retrieved a value from the dictionary
dict["first-key"]
dict["second-key"]

# your result should be 1 and 2
```

在这里，您可以访问与字典中的方括号(`dict`)中输入的键名相关联的值。

### 插入或更新 Python 字典项目

使用`append()`功能可以在字典中插入或更新条目。该函数在检查是插入还是使用它来更新值之前，收集要插入字典中的键和值:

```
# declared the dictionary
dict= {"first-key":1,"second-key":2}

# inserting an item in the dictionary using append()
dict["third-key"].append (3)

# inserting an item in the dictionary without using append()
dict["third-key"] = 3

print(dict)

# output item was created
 # {"first-key":1,"second-key":2, "third-key":3}
```

通过使用`append`函数在字典(`dict`)中插入一个值，该函数使用在字典方括号中输入的关键字注册在`append`函数的括号中输入的值。

但是，如果指定的键已经存在于字典中，Python 只在适当的地方更新字典中的值:

```
# declared the dictionary
dict= {"first-key":1,"second-key":2,"third-key":3}

# updating an item in the dictionary using append()

dict["third-key"].append (4)

    # updating an item in the dictionary without append()
dict["third-key"] = 4

    print(dict)
    # output  value for key updated
    # {"first-key":1,"second-key":2, "third-key":4}
```

因为我们在方括号中输入的键已经存在于字典(`dict`)中，所以与该键相关联的值将被更新为在`append`函数的括号中输入的新值。

### 删除 Python 字典中的项目

您还可以从字典中删除一个条目，方法是使用键检索该条目，然后使用`del()`命令删除该条目，如下面的代码片段所示:

```
# declared the dictionary
dict= {"first-key":1,"second-key":2,"third-key":3}

#retrieve and delete an item in the dictionary
del(dict["third-key"])

    print(dict)
    #output  value for key updated
    {"first-key":1,"second-key":2}
```

这里，我们使用`del`函数从字典中删除了与方括号中指定的键相关联的条目。

您也可以删除整个字典，如下面的代码所示:

```
dict= {"first-key":1,"second-key":2,"third-key":3}

#delete entire dictionary
del(dict)
```

### 遍历 Python 字典中的项目

[Python](https://blog.logrocket.com/for-while-loops-python/)中提供了循环来执行复杂的字典操作，比如删除所有带有空键的条目、从嵌套字典中检索数据、反转字典中的值等等。

本质上，循环有助于将复杂的字典操作分解成步骤，使它们更容易完成。

下面是一个利用循环逐一检索字典中的条目的示例:

```
# declaring the dictionary
dict =  {
  "brand": "Ford",
  "model": "Mustang",
  "year": 1964
}
 #looping through items keys in the dictionary
for x in thisdict:
  print(x)

#output
    # brand
    # model
      # year

 #looping through item values in the dictionary
for x in thisdict:
  print(thisdict[x])

#output
    # Ford
    # Mustang
            # 1964
```

这里的第一个实例描述了如何通过遍历字典来访问和打印条目键，而第二个实例描述了如何访问和打印它的值。

## Python 中的嵌套字典

我们前面说过，我们可以在字典中的一个条目的键值中插入任何数据类型。但是，嵌套字典中有另一个字典作为键值。

当您必须使用特定的键将一组项目进一步关联为一个整体，同时将每个项目与其键相关联时，可以使用嵌套字典。这方面的一个例子是将橙子与柑橘联系起来，将蓝莓与浆果联系起来，然后进一步将它们归类为水果。

让我们看看如何用这个例子声明一个嵌套字典:

```
# declaring the nested dictionary
products = {"fruits":{"citrus":"orange","berry":"blueberry"}, "gadgets":{"laptop":"macbook","phone":"iphone"}}
```

在上面的代码示例中，我们可以将两个字典作为条目与另一个字典中的特定键相关联。覆盖其他字典的字典称为父字典。

在下一节中，您将学习如何检索嵌套字典的条目。

## 在 Python 嵌套字典中检索项目

要从嵌套字典中检索一个条目，必须使用两个或更多的键来获得所需的键值，这取决于字典包含的嵌套数量。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

例如，如果父字典包含一个字典级别，则需要两个键来检索条目值。下面的示例显示了如何使用键值的键来检索键值:

```
# declaring the single nested dictionary
products = {"fruits":{"citrus":"orange","berry":"blueberry"}, "gadgets":{"laptop":"macbook","phone":"iphone"}}

# get the laptop value
print(products["gadgets"]["laptop"])
print(products["fruits"]["citrus"])

# output 
# macbook
# orange
```

我们只需要两个方括号来检索上面代码示例中的项目值，因为字典只有一个嵌套。

在字典有两个嵌套的情况下，我们必须使用三个方括号来检索条目值。下面是一个双重嵌套字典的示例:

```
# declaring the double nested dictionary
creatures = {"animal":{"mammal":{"human": "baby"}}, "plant":{"seeds":{"flower":"sun flower"}}}

# get the laptop value
print(creatures["animal"]["mammal"]["human"])
print(creatures["plant"]["seeds"]["flower"])

# output 
# baby
# sun flower

```

## 在 Python 嵌套字典中插入或更新项目

要将项目插入嵌套字典，必须向字典分配或追加一个键和值。如果字典中已经存在该项的键，那么只更新键值。否则，项目会插入字典中。

下面是一个代码示例，显示了如何在嵌套字典中插入或更新项目:

```
# declaring the nested dictionary
products = {"fruits":{"citrus":"orange","berry":"blueberry"}, "gadgets":{"laptop":"macbook","phone":"iphone"}}

# inserting or updating using append
new_item={"shirt": "sleeves", "socks":"short socks"}
products["clothes"].append(new_item)

# inserting or updating without append
new_item={"shirt": "sleeves", "socks":"short socks"}
products["clothes"].append(new_item)

print(products)

# output
# {"fruits":{"citrus":"orange","berry":"blueberry"}, "gadgets":{"laptop":"macbook","phone":"iphone"}, 
"clothes": {"shirt": "sleeves", "socks":"short socks"}
}
```

这里，我们声明了一个名为`products`的嵌套字典。为了向`products`字典添加条目，我们向`append`函数传递一个新字典。新字典可以作为一个条目添加到`products`父字典中。

## 从 Python 嵌套字典中删除项目

要从嵌套字典中删除一个条目，必须首先使用键检索该条目，然后使用`del()`方法删除该条目。

这与非嵌套字典上的删除操作的区别在于，通过将嵌套字典中的字典或键传递给要删除的`delete`函数，可以将字典和值作为条目删除。

下面是一个使用 Python 从嵌套字典中删除项目的示例。如果我们声明一个`products`嵌套字典，我们可以将字典传递给`delete`函数，该函数从嵌套字典中删除该字典:

```
# declaring the nested dictionary
products = {"fruits":{"citrus":"orange","berry":"blueberry"}, "gadgets":{"laptop":"macbook","phone":"iphone"}}

# deleting the laptop  item 
del(products["gadgets"]["laptop"])

print(products)

#output
# products = {"fruits":{"citrus":"orange","berry":"blueberry"}, "gadgets":{"phone":"iphone"}}
```

## Python 字典函数

Python 函数有特定的用途，可以帮助减轻开发人员的工作，因为它使您能够构建可重用的代码。下面是一些内置的 Python 函数，您可以使用它们在字典上执行简单的操作。

### `cmp(dict1, dict2)`功能

`cmp()`函数比较两个字典，看它们是否有相等的值。如果它们的值相等，则返回响应`0`。

例如，如果我们创建了四个字典，我们可以使用`cmp`函数来比较它们:

```
# declared 4 dictionaries
dict1 = {"name":"john","age":18}
dict2 = {"name":"Mary","age":12} 
dict3 = {"name":"Lisa","age":12}
dict4 = {"name":"john","age":18}

#comparing the dictionaries
print("value returned : %d" %  cmp (dict1, dict2))
print("value returned : %d" %  cmp (dict2, dict3))
    print("value returned : %d" %  cmp (dict1, dict4))

    # output
    # value returned: -1
    # value returned: 1
    # value returned: 0
```

比较`dict1`和`dict2`会返回`-1`的输出，因为它们之间没有相似之处。

然而，比较`dict2`和`dict3`会返回结果`1`，因为它们具有相同的年龄值，比较`dict1`和`dict4`会返回输出`0`，因为它们都具有相同的值，如前所述。

### `len(dict)`功能

`len()`函数获取传递给它的字典的长度，并返回列表中的项目总数[。这个陈述暗示如果一个字典有三个条目，那么它的长度是`3`。](https://blog.logrocket.com/working-lists-python/)

您可以使用此函数来查找任何词典的长度:

```
# declared dictionary
dict = {"name":"john","age":18, "weight": "65kg"}

# get the length of dict
print("The length of dict is: %d" % len(dict))

#output
# The length of dict is: 3
```

### `str(dict)`功能

`str(dict)`函数可以获得传入的字典的可打印字符串表示。当您想要将字典打印为字符串时，可以使用此选项:

```
# declared dictionary
dict = {"name":"john","age":18, "weight": "65kg"}

# get the str representation of dict
print("The string equivalent of dict is: %s" % str(dict))

#output
# The string equivalent of dict is {'name': 'john', 'age': 18, 'weight': '65kg'}
```

### `type()`功能

`type()`函数可以返回传递给它的变量的数据类型。如果在`type()`函数中传递一个字典，它将返回一个`dict`数据类型。您可以使用此函数来了解任何变量的数据类型:

```
# declare dictionary
dict = {"name":"john","age":18, "weight": "65kg"}

# return the data type
print("Data Type : %s" %  type (dict))

# output
# Data Type: <type 'dict'>
```

## Python 字典方法

Python 方法，类似于我们前面看到的函数，允许您重用和执行为您预先构建的操作。下面是一些内置的 Python 方法，您可以使用它们对字典执行操作。

### `dict.clear()`法

`dict.clear()`方法从字典中删除所有条目，返回一个空字典。当你想快速清空字典重新开始时，你可以用这个。下面是使用`clear()`方法的一个例子:

```
# declare the dictionary
dict = {'Name': 'Andrew', 'Age': 7};

# delete all items in the dictionary
dict.clear()

print("Dictionary : %s" %  str(dict))

# output 
# Dictionary : {}
```

## `dict.copy()`法

`copy()`方法获取传递给它的字典副本。当你不想从头开始创建字典时，你可以使用它。它还减少了将条目从当前词典逐个复制到新词典的压力:

```
# declare the dictionary
dict1 = {'Name': 'Andrew', 'Age': 7}

# make a copy of the dictionary
dict2 = dict1.copy()

print("New Dictionary : %s" %  str(dict2))

# output 
# New Dictionary : {'Name': 'Andrew', 'Age': 7}
```

通过创建一个字典(`dict1`)，然后使用`copy`方法在`dict2`中创建一个副本，您可以从输出中看到两个字典具有相同的值。

## `dict.fromkey()`法

`dict.fromkey()`方法可以从一系列值中创建一个字典。创建字典时，序列中的每个值都成为字典中的一个键。

您可以使用这种方法来创建一个字典，使用您还没有值的键。这是按照`dict.fromkeys(seq[, value])`语法完成的:

```
# create a sequence
seq = ('name', 'age', 'sex')

#create a dictionary from the sequence
dict = dict.fromkeys(seq)

print("New Dictionary : %s" %  str(dict))

dict = dict.fromkeys(seq, 10)
print("New Dictionary : %s" %  str(dict))

#output
# New Dictionary : {'age': None, 'name': None, 'sex': None}
# New Dictionary : {'age': 10, 'name': 10, 'sex': 10}
```

在上面的代码示例中，我们可以使用`fromkeys()`方法从包含一系列键(`seq`)的变量中创建`dict`。从`dict`的输出中，我们可以看到键存在于字典中，但是值被设置为`none`。

## `dict.has_key()`法

`has_keys()`方法检查一个键是否存在于传递给它的字典中。您还可以使用它来轻松验证字典中是否存在某个键。然后它返回一个布尔值(或者是`True`或者是`False`)。

这里，我们可以声明一个变量(`dict`)并使用 has_key 方法检查其中是否存在关键字`Age`和`Sex`:

```
# declare the dictionary
dict = {'Name': 'Andrew', 'Age': 13}

# check for key in the dictionary
print("Value : %s" %  dict.has_key('Age'))
print("Value : %s" %  dict.has_key('Sex'))

#Output

# Value : True
# Value : False
```

检查第一个键时，`Age`返回`true`，表示字典中存在该项。在检查第二个关键字时，`Sex`返回`false`，表示字典中不存在该条目。

## `dict.items()`法

`items()`方法获取一个以元组对排列的字典的键和值的列表。我们可以用它来获得字典中所有条目的键和值的列表。

我们可以通过创建一个字典(`dict`)并使用`items`方法在一个列表中并排打印其中所有条目的键和值来做到这一点:

```
# declare the dictionary
dict = {'Name': 'Molly', 'Age': 7}

# get items in the dictionary
print("Value : %s" %  dict.items())

# output
# Value : [('Age', 7), ('Name', 'Molly')]
```

## `dict.keys()`法

`keys()`方法返回字典中所有现有键的列表。您可以使用它来获取字典中所有键的列表，以执行您想要的任何进一步的操作:

```
dict = {'Name': 'Andrew', 'Age': 23}
print("Value : %s" %  dict.keys())

#output
# Value : ['Age', 'Name']
```

## `dict.update(dict2)`法

如果值不存在，`update()`方法将一个字典的条目插入到另一个字典中。否则，它会在适当的地方更新这些值。

您可以使用`update`方法来替代`append`函数。然而，`update`方法可以使用另一个字典更新一个字典中的条目:

```
# declare the dictionaries
dict = {'Name': 'Molly', 'Age': 7}
dict2 = {'Sex': 'female' }

# update dict with dict2 items
dict.update(dict2)

print("Value : %s" %  dict)

# output
# Value : {'Name': 'Molly', 'Age': 7, 'Sex': 'female'}
```

通过创建两个字典`dict`和`dict2`，我们可以使用`update`方法用`dict2`的值更新`dict`的值。输出显示`dict`现在包含了`dict2`项，这意味着在运行`update`方法之前它不存在于`dict`中。

## `dict.values()`法

`values()`方法返回存在于字典中的值的列表，没有它们的键。您可以使用此方法仅获取字典中的值，而不用担心使用它们的键来访问它们:

```
# declare dictionary
dict = {'Name': 'Zubair', 'Age': 7}

# get all item values
print("Value : %s" %  dict.values())

# output
# Value : [7, 'Zubair']
```

## 结论

通过这篇文章，我们学习了如何用 Python 声明一个字典，管理它的数据，并对它执行一些操作。此外，我们还了解了嵌套字典及其工作原理。

我希望这篇文章能帮助你成为一名更好的 Python 开发者。编码快乐！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)