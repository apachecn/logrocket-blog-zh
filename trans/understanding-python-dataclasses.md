# 了解 Python 数据类- LogRocket 博客

> 原文：<https://blog.logrocket.com/understanding-python-dataclasses/>

Python 3.7 版本引入了一个新特性:`dataclasses`。

作为参考，类基本上是创建对象的蓝图。一个类的例子可以是一个国家，我们将使用`Country`类来创建不同的实例，比如摩纳哥和冈比亚。

初始化值时，提供给构造函数的属性(如人口、语言等)被复制到每个对象实例中:

```
class Country:
    def __init__(self, name: str, population: int, continent: str, official_lang: str):
        self.name = name
        self.population = population
        self.continent = continent
        self.official_lang = official_lang

smallestEurope = Country("Monaco", 37623, "Europe")
smallestAsia= Country("Maldives", 552595, "Asia")
smallestAfrica= Country("Gambia", 2521126, "Africa") 

```

如果您曾经在 Java 和 Python 等编程语言中使用过面向对象编程(OOP ),那么您应该已经熟悉了类。

然而，`dataclass`提供了已经实现的基本类功能，减少了编写代码的时间。

在本文中，我们将深入探讨 Python 中的`dataclasses`是什么，如何操作对象字段，如何排序和比较`dataclasses`，等等。

请注意，因为这是在 Python 3.7 中发布的，所以您必须在本地计算机上安装 Python 的最新版本才能使用它。

## 什么是 Python `dataclass`？

如前所述，Python `dataclasses`非常类似于普通的类，但是实现的类功能大大减少了编写样板代码的数量。

这种样板文件的一个例子是`__init__`方法。

在`Country`类的例子中，您可以观察到我们必须手动定义`__init__`方法，当您初始化该类时会调用该方法。现在，对于你定义的每一个普通类，都要求你提供这个函数，这意味着你必须写很多重复的代码。

Python `dataclass`已经定义了这个方法。因此，您可以编写相同的`Country`类，而无需手动定义构造函数。

在幕后，`@dataclass`在用新属性初始化对象时调用这个方法。

注意`__init__`不是默认提供的唯一方法。其他实用程序方法，如`__repr__`(表示)、`__lt__`(小于)、`__gt__`(大于)、`__eq__`(等于)，以及许多其他方法也是默认实现的。

### 使用普通的 Python 类

当使用 Python 中的普通类时，我们有更长的代码来实现基本方法。

再考虑一下`Country`类。在下面的代码块中，您可以看到一些方法，从`__innit__`方法开始。这个方法在一个`Country`实例上初始化诸如国家名称、人口数量、大陆和官方语言等属性。

`__repr__`返回类实例的字符串表示。这会以字符串形式打印每个类实例的属性。

`_lt_`比较两个`Country`实例的人口数，如果当前实例的人口数较少，则返回`True`，如果两个实例的人口数相同，则返回`True`:

```
class Country:
    def __init__(self, name: str, population: int, continent: str, official_lang: str="English" ):
        self.name = name
        self.population = population
        self.continent = continent
        self.official_lang= official_lang

   def __repr__(self):
        return(f"Country(name={self.name},
            population={self.population}, continent={self.continent},
            official_lang={self.official_lang})")

   def __lt__(self, other):
        return self.population < other.population

   def __eq__(self, other):
        return self.population == other.population

smallestAfrica= Country("Gambia", 2521126, "Africa", "English")
smallestEurope = Country("Monaco", 37623, "Europe", "French")
smallestAsia1= Country("Maldives", 552595, "Asia", "Dhivehi")
smallestAsia2= Country("Maldives", 552595, "Asia", "Dhivehi")

print(smallestAfrica) 
# Country(name='Gambia', population=2521126, continent='Africa', #official_lang='English')

print(smallestAsia < smallestAfrica) # True
print(smallestAsia > smallestAfrica) # False

```

### 使用蟒蛇`dataclass`

要在代码中使用 Python 的`dataclass`，只需导入模块并在类的顶部注册`@dataclass`装饰器。这将基类功能自动注入到我们的类中。

在下面的例子中，我们将创建相同的`Country`类，但是代码要少得多:

```
from dataclasses import dataclass

@dataclass(order=True)
class Country:
     name: str
     population: int
     continent: str
     official_lang: str

smallestAfrica= Country("Gambia", 2521126, "Africa", "English")
smallestEurope = Country("Monaco", 37623, "Europe", "French")
smallestAsia1= Country("Maldives", 552595, "Asia", "Dhivehi")
smallestAsia2= Country("Maldives", 552595, "Asia", "Dhivehi")

# Country(name='Gambia', population=2521126, continent='Africa', #official_lang='English')

print(smallestAsia1 == smallestAsia2) # True
print(smallestAsia < smallestAfrica) # False

```

注意到我们没有在`dataclass`上定义构造函数方法；我们刚刚定义了字段。

我们还省略了像`repr`和`__eq__`这样的助手。尽管省略了这些方法，类仍然正常运行。

请注意，对于小于(`<`)，`dataclass`使用默认方法来比较对象。在本文的后面，我们将学习如何定制对象比较以获得更好的结果。

## 使用`field()`功能操作对象字段

`dataclass`模块还提供了一个名为`field()`的函数。这个函数为您提供了对类字段的根深蒂固的控制，允许您随意操作和定制它们。

例如，当调用表示方法时，我们可以通过传递一个`repr`参数并将值设置为`false`来排除`continent`字段:

```
from dataclasses import dataclass, field

@dataclass
class Country:
     name: str
     population: int
     continent: str = field(repr=False) # omits the field
     official_lang: str

smallestEurope = Country("Monaco", 37623, "Europe", "French")

print(smallestEurope)

# Country(name='Monaco', population=37623, official_lang='French') 

```

然后，此代码在 CLI 中输出:

![Output In CLI That Shows All Country Details But Omits The Continent Field](img/f55176e0b296a09bf8772ef18d67c0ea.png)

默认情况下，`repr`总是被设置为`True`

下面是其他一些可以被`field()`接受的参数。

### `init`参数

`init`参数指定在初始化过程中是否应该将一个属性作为参数包含到构造函数中。如果您将一个字段设置为`innit=False`，那么您必须在初始化期间省略该属性。否则，一个`TypeError`就会被抛出:

```
from dataclasses import dataclass, field

@dataclass
class Country:
     name: str
     population: int  
     continent: str
     official_lang: str = field(init=False) #Do not pass in this attribute in the constructor argument  

smallestEurope = Country("Monaco", 37623, "Europe", "English") #But you did, so error!

print(smallestEurope)

```

然后，此代码在 CLI 中输出:

![init Parameter Rendering In The CLI](img/4c510924a85c38b3c8088aed25b1bed2.png)

### `default`参数

如果在初始化期间没有提供值，则传递`default`参数来指定字段的默认值:

```
from dataclasses import dataclass, field

@dataclass
class Country:
     name: str
     population: int  
     continent: str
     official_lang: str = field(default="English") # If you ommit value, English will be used

smallestEurope = Country("Monaco", 37623, "Europe") #Omitted, so English is used

print(smallestEurope)

```

然后，此代码在 CLI 中输出:

![default Parameter Rendered in CLI To Specify A Default Value](img/60da9855f675b4f7c2a9adcfc16fd397.png)

### `repr`参数

`repr`参数用于指定该字段是应该包含在字符串表示中(`repr=True`)还是排除在外(`repr=False`),该字符串表示由`__repr__`方法生成:

```
from dataclasses import dataclass, field

@dataclass
class Country:
     name: str
     population: int  
     continent: str
     official_lang: str = field(repr=False) # This field will be excluded from string representation

smallestEurope = Country("Monaco", 37623, "Europe", "French") 

print(smallestEurope)

```

然后，此代码在 CLI 中输出:

![repr Parameter Rendered In CLI To Included (repr=True) Or Excluded (repr=False) From The String Representation](img/994c42d6aa1f8320f9dc37982437ebb4.png)

## 用`__post_init__`初始化后修改字段

在初始化之后立即调用`__post_init__`方法。换句话说，它是在对象接收到它的字段的值之后被调用的，比如`name`、`continent`、`population`和`official_lang`。

例如，我们将使用方法根据某个国家的官方语言来确定我们是否要迁移到该国家:

```
from dataclasses import dataclass, field

@dataclass
class Country:
     name: str
     population: int
     continent: str = field(repr=False) # Excludes the continent field from string representation
     will_migrate: bool = field(init=False) # Initialize without will_migrate attribute
     official_lang: str = field(default="English") # Sets default language. Attributes with default values must appear last

     def __post_init__(self):
           if self.official_lang == "English":
                 self.will_migrate == True
           else:
                 self.will_migrate == False 

```

在对象用值初始化之后，我们执行一个检查，看看`official_lang`字段是否从`post_init`内部被设置为`English`。如果是这样，我们必须将`will_migrate`属性设置为`true`。否则，我们将其设置为`false`。

## 将`dataclasses`与`sort_index`进行排序和比较

`dataclasses`的另一个功能是为比较对象和排序对象列表创建自定义顺序的能力。

例如，我们可以通过人口数量来比较两个国家。换句话说，我们想说，当且仅当一个国家的人口数量大于另一个国家时，这个国家才大于另一个国家:

```
from dataclasses import dataclass, field

@dataclass(order=True)
class Country:
     sort_index: int = field(init=False)
     name: str
     population: int = field(repr=True)
     continent: str 
     official_lang: str = field(default="English") #Sets default value for official language

     def __post_init__(self):
           self.sort_index = self.population

smallestEurope = Country("Monaco", 37623, "Europe")
smallestAsia= Country("Maldives", 552595, "Asia")
smallestAfrica= Country("Gambia", 2521126, "Africa") 

print(smallestAsia < smallestAfrica) # True
print(smallestAsia > smallestAfrica) # False

```

要在 Python `dataclass`中启用比较和排序，必须将`order`属性传递给带有`true`值的`@dataclass`。这将启用默认的比较功能。

因为我们想通过人口计数进行比较，所以我们必须在初始化之后从`__post_innit__`方法内部将`population`字段传递给`sort_index`属性。

您还可以使用特定字段作为`sort_index`对对象列表进行排序。例如，我们必须按人口数量对国家列表进行排序:

```
from dataclasses import dataclass, field

@dataclass(order=True)
class Country:
     sort_index: int = field(init=False)
     name: str
     population: int = field(repr=True)
     continent: str 
     official_lang: str = field(default="English")

     def __post_init__(self):
           self.sort_index = self.population

europe = Country("Monaco", 37623, "Europe", "French")
asia = Country("Maldives", 552595, "Asia", "Dhivehi")
africa = Country("Gambia", 2521126, "Africa", "English")
sAmerica = Country("Suriname", 539000, "South America", "Dutch")
nAmerica = Country("St Kits and Nevis", 55345, "North America", "English")
oceania = Country("Nauru", 11000, "Oceania", "Nauruan")  

mylist = [europe, asia, africa, sAmerica, nAmerica, oceania]
mylist.sort()

print(mylist) # This will return a list of countries sorted by population count, as shown below

```

然后，此代码在 CLI 中输出:

![Using sort_index To Sort List Of Objects](img/1881fd7a957eed8bd56230b5a2111689.png)

不希望`dataclass`被篡改？您可以通过简单地向装饰器传递一个`frozen=True`值来冻结该类:

```
from dataclasses import dataclass, field

@dataclass(order=True, frozen=True)
class Country:
     sort_index: int = field(init=False)
     name: str
     population: int = field(repr=True)
     continent: str 
     official_lang: str = field(default="English")

     def __post_init__(self):
           self.sort_index = self.population

```

## 包扎

Python `dataclass`是一个非常强大的特性，它极大地减少了类定义中的代码量。该模块提供了大多数已经实现的基本类方法。您可以定制`dataclass`中的字段并限制某些动作。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)