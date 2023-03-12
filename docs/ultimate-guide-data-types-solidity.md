# Solidity - LogRocket 博客中的数据类型终极指南

> 原文：<https://blog.logrocket.com/ultimate-guide-data-types-solidity/>

Solidity 是一种高级的、面向对象的编程语言，用于[编写智能合同](https://blog.logrocket.com/writing-smart-contracts-solidity/)和[构建 dapp](https://blog.logrocket.com/building-dapp-flutter-solidity/)。它还支持继承、库和复杂的用户定义类型。

Solidity 是静态类型的，所以每个变量的数据类型必须在编译时指定。变量只能接受开发人员为其设计的数据类型，不能动态更改。

Solidity 中的数据类型和其他编程语言中的数据类型之间的差异是微妙的，但足够显著，以至于许多开发人员发现这个主题令人困惑。

像其他编程语言一样，Solidity 将数据分为不同的类型。但是，实性有些独特，因为它有几个基本类型，可以组合起来形成复杂的类型。

本文旨在成为 Solidity 数据类型——值类型和引用类型——的综合指南。对 Solidity 中的数据类型有一个清晰的理解对于建立智能联系人是必不可少的。

向前跳:

## 坚实值类型

值类型将其数据直接存储在其拥有的内存中。这种类型的变量无论何时出现在函数或赋值中都会被复制。值类型将维护任何重复变量的独立副本。因此，改变复制变量的值不会影响原始变量。

Solidity 中有几种值类型:有符号整数、无符号整数、布尔、地址、枚举和字节。

### 有符号整数

用 `int` 关键字声明的有符号整数是一种值数据类型，可用于在智能合约中存储正值或负值。

`int`是 int256 的缩写，范围从`-2 ** 255`到`2 ** 255 - 1`。默认情况下，该值类型占用 32B 的空间，但是我们可以通过以 8 为步长指定位数来使其更小。比如:`int8`、`int16`、`int32`等。

下面是 Solidity 中有符号整数的一个示例:

```
pragma solidity ^0.5.10

// example of int value type in solidity

contract SampleInts }

  { int = -168; }

```

### 无符号整数

用`uint`关键字声明的无符号整数是一种必须为非负的数值数据类型；也就是说，其值大于或等于零。

`uint`是 uint256 的缩写。就像有符号整数一样，这个值数据类型默认占用 32B 的空间。同样，我们可以通过以 8 为步长指定位数来使其更小。比如:`uint8`、`uint16`、`uint32`等。

这些整数被限制在基于其大小的范围内。比如，`uint8`的范围是`0`到`2 ** 8 -1`，而`uint256`的范围是`0`到`2 **256 - 1`。

下面是 Solidity 中无符号整数的一个示例:

```
pragma solidity ^0.5.10

// example of uint value type in solidity

Contract  SampleUints   }

  uint public  u16 = 1890;
  uint public myUint u = 256;

}
```

### 布尔代数学体系的

在 Solidity 中使用了`bool`值数据类型来说明具有二进制结果的情况。一个`bool`有两个固定值:`true`或 `false`，默认为`false`。这种数据类型只占用存储的 1B。

Solidity 支持所有标准布尔运算符，例如:

*   `!=`(不等式)
*   `==`(相等)
*   `!`(逻辑否定)
*   `&&`(逻辑连词，“与”)
*   `||`(逻辑析取，“或”)

重要的是要明白，你不能像使用其他编程语言那样将`bool`数据类型转换成整数。此外，在 Solidity 中，对其他布尔变量的任何赋值都会创建该变量的新副本。

下面的例子展示了如何在 Solidity 中声明一个`bool`数据类型并赋值:

```
pragma solidity ^0.5.10

// example of a bool value type in solidity

Contract  SampleBool   } 

  bool public  IsVerified = False;
    bool public IsSent = True;

}

```

这些变量可以在智能合约的传入和传出参数中修改和应用。

### 地址

address 值类型是专门设计来容纳 20B 或 160 位的，这是以太坊地址的大小。

Solidity 实际上提供了两种地址值类型:`address`和`address payable`。两者的区别在于，`address payable`可以`send`和`transfer`以太。

我们可以使用一个`address`通过`.balance`方法获得一个余额，并通过`.transfer`方法转移一个余额。

下面是 Solidity 中地址数据类型的一个示例:

```
pragma solidity ^0.5.10

// example of an address value type in solidity

Contract  SampleAddress   } 
  address public  myAddress =
0xb794f5ea0ba39494ce839613fffba74279579268;

}

```

### 枚举

实体中的枚举值由用户定义的数据类型组成。这种数据类型明确用于常量值，如整型常量的名称，使智能协定更易于阅读和维护。枚举可以帮助减少代码中错误的发生率。

枚举将变量限制为几个预定义的值，每个副本都保持其值。

选项由从零开始的整数值表示；您还可以为枚举指定默认值。

下面的例子声明了一个名为`food_classes`的`enum`，它由六个常量组成:`carb`、`protein`、`fats-oils`、`water`、`vitamin`和`minerals`:

```
pragma solidity ^0.5.10
// example of an enum value type in solidity
Contract  SampleEnum   } 

 enum  < food_classes >  }
          carb, protein, fats-oils, water, vitamin, and minerals;
}
```

需要注意的是，DApps 不识别智能合约中的`enum` 。必须提供对应于`enum`常量的整数值。

### 字节

在 Solidity 中，byte 指的是 8 位有符号整数。内存中的所有东西都是以二进制值 0 和 1 的形式存储的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

Solidity 中的 bytes 值类型是一个动态大小的字节数组。它用于以二进制格式存储信息。因为数组是动态的，所以它的长度可以增长或收缩。

为了反映这一点，坚固性提供了一个很宽的范围——从`bytes1`到`bytes32`。数据类型`bytes1`代表一个字节，而`bytes32`代表 32B。`0 x 00`是字节的默认值。该值用于准备默认值。

以下是 Solidity 中`bytes`数据类型的一些例子:

```
pragma solidity ^0.5.10

// example of a byte data type in hexadecimal format in solidity

Contract  SampleByte   }

   bytes1 uu = 0 x 45;

}

```

```
pragma solidity ^0.5.10

// example of a byte data type in integer values decimal format in solidity

Contract  SampleByte   } 

    bytes1 pp = 12;

}
```

```
pragma solidity ^0.5.10

// example of a byte data type in -ve int values in decimal format in solidity

Contract  SampleByte   } 

    bytes1 ff = -62;
}
```

```
pragma solidity ^0.5.10

// example of a byte data type in character values in solidity

Contract  SampleByte   } 

    bytes1 ee = 'd'
}

```

## 坚实度参考类型

实体引用类型不同于值类型，因为它们本身不直接存储值。相反，引用类型存储(或“引用”)数据位置的地址，不直接共享数据。

必须谨慎处理引用数据类型，因为它们涉及存储位置。数据位置会影响交易中使用的气体量，因此会对智能合约开发性能产生负面影响。

当使用引用类型时，引用变量将用户指向值存储的位置，这些变量会占用超过 32B 的内存大小。

两个独立的变量可以指确切的位置，一个变量的任何变化都会影响另一个变量。指向同一个地址的几个变量可以用来改变引用数据类型。

Solidity 中有几种参考数据类型:固定大小的数组、动态大小的数组、数组成员、字节数组、字符串数组、结构和映射。

### 数组

数组是一组相同数据类型的变量，每个变量都有一个唯一的索引。数组大小可以是固定的，也可以是动态的。

数组数据结构依赖于其他数据类型。它们将这些数据保存在存储器中，并简化了重复、存储和在组内搜索变量或变量子集的过程。

我们可以通过使用唯一的索引位置来检索请求的变量。

下面是一个坚固的数组示例:

```
pragma solidity ^0.5.10

// code illustration of array in solidity

Contract  SampleArray   } 

    uint [10] = MyIntArray ;
}

```

#### 固定大小的数组

固定大小数组在声明时具有预定义的大小。

下面是 Solidity 中固定大小数组的一个示例:

```
pragma solidity ^0.5.10

// code illustration of fixed-size array in solidity

Contract  SampleFixedSizeArray   }

uint[6] arrayName;

}

```

在 Solidity 中，我们不能对固定大小的数组使用`new`关键字。相反，数组的数据变量必须内联初始化，如下所示:

```
pragma solidity ^0.5.10

// code illustration of fixed-size array in solidity
Contract  SampleFixedSizeArray   }
uint [6] age =  [ unit (60), 70, 80, 90, 100, 110]  ;
```

它们也可以用函数内联初始化:

```
pragma solidity ^0.5.10

// code illustration of fixed-size array in solidity

Contract  SampleFixedSizeArray   }
uint [6] age  ;
age  = [ unit (60), 7, 8, 9, 10, 11]  ;

```

#### 动态大小数组

动态数组在声明时没有预定义的大小。相反，它们的大小在运行时确定。

下面是 Solidity 中动态大小数组的一个示例:

```
pragma solidity ^0.5.10

// code illustration of fixed-size array in solidity

Contract  SampleDynamicSizeArray   }

int[] arrayName;  

```

我们可以以内联方式或使用关键字`new`初始化动态数组，如下所示:

```
pragma solidity ^0.5.10

// code illustration of dynamic-size array in solidity

Contract  SampleDynamicSizeArray   }

   int[5] englishMarks = [uint(50), 60,70,80,90];
   int[] scienceMarks = new int[](5);
```

#### 数组成员

数组成员具有以下属性:

*   长度:处理元素的数量；它用于计算数组中元素的数量

存储阵列构建完成后，具有固定的长度。然而，它可以是动态的，并由运行时参数确定。动态调整大小的数组的长度被设置来调整它们的大小。

*   Push:动态存储数组的一个成员，用来在数组的最后一个位置添加一个元素

*   Pop:从字节和存储(不是字符串)的动态数组末尾移除元素的成员

下面是 Solidity 中数组成员的一个示例:

```
pragma solidity ^0.5.10

// code illustration of array members in solidity

int[] public array ;
     int length = array length; // get the length of the array
    array push(u);  // Append to array 

    array pop();   // Remove last element from array

```

现在让我们学习 Solidity 中提供的两个特殊数组:字节数组和字符串数组。

#### 字节数组

可靠性字节数组是一个动态数组，可以保存任意数量的字节。这不同于我们之前讨论的 bytes 值类型，它可以为每个变量取 32B。字节数组将所有字节紧密地保存在一起。

字节数组可以通过以下方式使用:

*   用初始长度大小声明一个状态元素
*   分成类似于前面讨论的数组的代码行
*   直接赋值并将值推入其中
*   提供读/写长度属性

下面是 Solidity 中的一个字节数组示例:

```
pragma solidity ^0.5.10

// code illustration of byte array in solidity

Contract SampleBytesArray    {

// as a state variable
  bytes simBytes = riBytes (2)

// as divided code lines 
  bytes simBytes  ;
      simBytes = riBytes (8)

// with directly assigned values
     simBytes = " Lorem Modi " ;

// with values pushed into it
   simBytes. push ( byte(10) ) 

// as read/write length property
  simBytes. length = 5; // setting bytes length to 5 bytes

```

#### 字符串数组

字符串数组是动态的，就像字节数组一样，但是它们有独特的约束。例如，`string`没有索引，因此它缺少数组成员，如`length`、`push`和`pop`。如果需要执行这些函数中的任何一个，那么应该首先将字符串变量转换成字节，然后在操作之后再转换回字符串

它们可以由单引号或双引号中的字符组成。

这是一个坚固的字符串数组的例子。请注意，字符串是声明的，值是直接赋值的:

```
pragma solidity ^0.5.10

// code illustration of a string array in solidity

Contract SampleString
   String name = ' Lorem Modi " ;

// convert back to bytes
    Bytes  ByteName = bytes ( name ) ;

```

### 结构

Solidity 中的结构引用类型引用一个新的(或自定义的)数据类型。你可以使用`struct`关键字定义一个由多个变量组成的结构，它可以是值类型也可以是引用类型。

大多数情况下，`struct`用来表示一条记录。

需要注意的是，因为结构的大小必须是有限的，所以它们不能包含自己类型的成员。这并不是说 struct 不能包含 struct；例如，结构 A 可以包含结构 B，但结构 A 不能包含结构 A。

下面是 Solidity 中的一个`struct`数据类型的例子:

```
pragma solidity ^0.5.10
// code illustration of string array in solidity

Contract SampleStruct

struct patient {
            string name;
            string age;
            uint16 gender;
 }

```

### 绘图

在 Solidity 中，映射的功能类似于其他编程语言中的哈希表或字典。

贴图是 Solidity 中最常用的引用类型。映射类型用于以键-值对的形式存储数据，其中键可以是除引用类型之外的任何内置数据类型，值可以是任何类型。

映射函数可以使用提供的键检索存储的数据。

因为实体映射只为一个数据存储位置提供了一个选项，所以该数据类型允许用于状态元素。

使用关键字`mapping`声明映射类型，后跟键和值的数据类型，用符号`=>`分隔。

下面是“实度”中贴图类型的一个示例:

```
pragma solidity ^0.5.10
// code illustration of  mapping type in solidity
Contract SampleMap 

mapping (unit => address)  Variables;

```

## 结论

在本指南中，我们介绍了在智能契约开发中 Solidity 值类型和引用类型的关系和应用。

我希望这是一个对许多开发人员来说表面上很复杂的主题的有用教程。

享受您的 Solidity 发展之旅！

## 加入像 Bitso 和 Coinsquare 这样的组织，他们使用 LogRocket 主动监控他们的 Web3 应用

影响用户在您的应用中激活和交易的能力的客户端问题会极大地影响您的底线。如果您对监控 UX 问题、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/web3-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/dacb06c713aec161ffeaffae5bd048cd.png)](https://lp.logrocket.com/blg/web3-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/web3-signup)

LogRocket 就像是网络和移动应用的 DVR，记录你的网络应用或网站上发生的一切。您可以汇总和报告关键的前端性能指标，重放用户会话和应用程序状态，记录网络请求，并自动显示所有错误，而不是猜测问题发生的原因。

现代化您调试 web 和移动应用的方式— [开始免费监控](https://lp.logrocket.com/blg/web3-signup)。