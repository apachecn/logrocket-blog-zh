# 树遍历递归入门- LogRocket 博客

> 原文：<https://blog.logrocket.com/getting-started-with-recursion-for-tree-traversal-9a89fe6e772/>

你有没有遇到过你觉得可以用递归解决的问题，只是你不知道从哪里开始？还是看起来你不得不自己想办法解决问题？

处理递归的第一部分是理解问题何时需要递归。当问题可以建模为递归关系时，可以使用递归。递归关系是从以前的值中寻找未来值的规则。斐波那契数列是递归关系的一个例子。当数据被递归定义时，也可以使用递归。文件系统可以递归定义，因为每个目录都由其他目录组成。

第二部分是理解如何实现递归函数。在这篇文章中，我将向您展示使用递归来遍历递归数据结构的技术。

## 在树中查找项目

递归数据结构类似于树。在代码中，这转化为数组的数组或其键是其他对象的对象。我们的案例研究将是一棵树，它模拟了纽约市的社区。树根是纽约。它有两个孩子，曼哈顿和布鲁克林。而曼哈顿有两个孩子，哈莱姆区和上东区。

这是我们的树的列表表示:

```
const locations = [
  'New York', 
  [
    'Manhattan',
    [
      'Harlem', 'Upper East Side'
    ]
  ],
  [
    'Brooklyn'
  ]
];
```

我们将实现一个函数`includes`，来测试我们的列表是否包含指定的条目。如果找到匹配，该函数将返回 true，否则返回 false。

这个功能有三个部分。首先，基本情况。我们的函数将在每一步减少列表，直到我们得到一个没有元素的列表。接下来，我们来看单个节点的情况。一个节点将是字符串`'Manhattan'`。最后，当元素是另一个列表或子树时。列表`['Harlem', 'Upper East Side']`是一个子树。

这是这三个案例的框架:

```
function includes(item, list) {
  if (isEmpty(list)) {
    ...
  } else if(isNode(first(list))) {
    ...
  } else {
    ...
  }
}
```

如果列表中没有元素，`isEmpty`函数将返回`true`。如果遍历了列表中的所有元素，但没有找到匹配，函数返回`false`。`first`函数返回列表中的第一个元素。如果元素是一个列表，`isNode`函数返回`false`。

在`else if`中，您想要测试当前元素是否匹配您正在搜索的项目。如果是，可以返回 true。如果不是，你需要在列表的其余部分重现。

<

div class = " section-inner section layout-inset column " >

这是更新后的代码:

```
function includes(item, list) {
  if (isEmpty(list)) {
    return false;
  } else if(isNode(first(list))) {
    if(first(list) == item) {
      return true;
    } else {
      return includes(item, rest(list));
    }
  } else {
    ...
  }
}
```

`rest`函数返回没有第一个元素的列表。这就是我们如何减少问题，以达到基本情况，一个空列表。条件语句的`else if`块也可以写成:

```
return first(list) == item || includes(item, rest(list));
```

它做同样的工作，但更简洁。比起嵌套的`if`语句，我更喜欢这行代码。

最后，在`else`块中，我们需要在第一个元素上递归，因为它是一个列表，并在列表的其余部分上递归。这是`else`块的代码:

```
return includes(item, first(list)) || includes(item, rest(list));
```

综上所述，您现在拥有:

```
function includes(item, list) {
  if (isEmpty(list)) {
    return false;
  } else if(isNode(first(list))) {
    return first(list) == item || includes(item, rest(list));
  } else {
    return includes(item, first(list)) || includes(item, rest(list));
  }
}
```

## 从树中删除项目

接下来，我们将实现一个函数`remove`,该函数将一个字符串和一个列表作为输入，并返回删除了该字符串的所有匹配项的列表。在真实的树中，您可能会对删除一个节点及其所有子节点感兴趣。为简单起见，我们将只查看删除单个项目的情况。

从列表中删除一个条目类似于查找它的成员，除了我们需要确保当我们在它的子条目上重现时，我们保留了对列表的引用。

这三种情况是相同的:

```
function remove(item, list) {
  if (isEmpty(list)) {
    ...
  } else if (isNode(first(list))) {
    ...
  } else {
    ...
  }
}
```

因为这个函数返回一个列表，我们的基本情况将返回一个空数组。将通过复制列表中除了要移除的项目之外的所有项目来构建新列表。

如果我们使用 for 循环从一维列表中删除一项，函数可能如下所示:

```
function remove(item, list) {
  let result = [];
  for (let i = 0; i < list.length; i++) {
    if (list[i] != item){
      result.push(list[i]);
    }
  }
  return result;
}
```

对于递归实现，测试在`else if`块中进行。如果当前元素等于该项，我们在列表的其余部分循环。这具有移除项目的效果。但是，如果当前元素不是条目，那么我们必须保存这一部分，以便连接到列表的其余部分。当函数到达基本情况时，所有被推迟的连接都将被添加到这个列表中。

```
function remove(item, list) {
  if (isEmpty(list)) {
    return [];
  } else if (isNode(first(list))) {
    if (first(list) == item) {
      return remove(item, rest(list));
    } else {
      return concat(first(list), remove(item, rest(list)));
    }
  } else {
    ...
  }
}
```

这里的`concat`函数将两个输入合并成一个列表。

在`else`块中，我们定义了当前元素是一个列表的情况。我们需要在那部分和列表的其余部分重复出现。此外，这两个部分需要连接成一个列表。这就是我们最终得到的结果:

```
function remove(item, list) {
  if (isEmpty(list)) {
    return [];
    } else if (isNode(first(list))) {
    if (first(list) == item) {
      return remove(item, rest(list));
    } else {
      return concat(first(list), remove(item, rest(list)));
    }
  } else {
    return concat(remove(item, first(list)), remove(item, rest(list)));
  }
}
```

## 锻炼

实现一个函数`occur`，它接受一个字符串和一个列表作为输入，并返回该字符串在列表中出现的次数。首先，建立你的三个案例。在你的基本情况下，你应该返回什么？当你有一个节点时，你应该做什么？当你有一个清单时，你应该怎么做？使用前面的两个例子作为指导。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 结论

用于查找和删除项目的技术可以扩展到解决许多其他需要遍历树的问题。树可以用来模拟游戏中的移动或者表演二分搜索法。实现递归函数时，请记住以下几点:

*   定义基本情况
*   定义元素是节点的情况
*   定义元素是列表的情况
*   在递归调用中，更改参数以使函数达到基本情况

另一个需要考虑的问题是，递归并不总是解决问题的最有效的方法。这就是为什么你应该记住任何可以用递归解决的问题也可以用`for`和`while`循环来解决。当拥有一个更简单的解决方案的好处超过效率的代价时，你会选择递归而不是循环。

最后，这里展示的例子只是解决这类问题的一种方法。使用它们作为起点，并阅读下面列出的资源以获得更深入的理解。

## 进一步阅读

200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

## 部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

.

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.