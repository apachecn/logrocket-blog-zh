# HTML5 节点备忘单:2020 年你应该知道的 21 个 API

> 原文：<https://blog.logrocket.com/html5-node-cheat-sheet-21-apis-you-should-know-in-2020/>

这本快速的 HTML 节点指南将涵盖 HTML DOM 节点中最流行和最广泛使用的 21 种 API:

1.  [T2`getElementById(id)`](#getelementbyidid)

2.  [T2`getElementsByClassName(className)`](#getelementsbyclassnameclassname)

3.  [T2`getElementsByTagName(tagName)`](#getelementsbytagnametagname)

4.  [T2`getSelection()`](#getselection)

5.  [`getElementsByName`和`querySelector(selector)`](#getelementsbyname)

6.  [T2`querySelectorAll(selector)`](#queryselectorallselector)

7.  [T2`parentNode.insertBefore(newNode, refNode)`](#parentnodeinsertbeforenewnoderefnode)

8.  [T2`appendChild(node)`](#appendchildnode)

9.  [T2`createElement(elementName)`](#createelementelementname)

10.  [T2`createTextNode(textString)`](#createtextnodetextstring)

11.  [T2`removeChild(childNode)`](#removechildchildnode)

12.  [T2`replaceChild(newNode, childNode)`](#replacechildnewnodechildnode)

13.  [T2`setAttribute(attrKey, attrValue)`](#setattributeattrkeyattrvalue)

14.  [T2`getAttribute(attrKey)`](#getattributeattrkey)

15.  [T2`getAttributeNames()`](#getattributenames)

16.  [T2`cloneNode()`](#clonenode)

17.  [T2`classList`](#classlist)

18.  [T2`parentNode`](#parentnode)

19.  [T2`parentElement`](#parentelement)

20.  [T2`innerHTML`](#innerhtml)

21.  [T2`innerText`](#innertext)

每个开发人员在使用 web 框架之前，都应该对这些 API 了如指掌。掌握它们是建立何时以及如何使用框架的基本理解的最好方法。

让我们开始吧。

## 1.`getElementById(id)`

`getElementById(id)`通过其`id`属性返回元素的节点实例。

```
<div id="div1">Div 1</div>
<p id="par1">I'm a Paragraph</p>

```

要使用`getElementById`方法获取`1`的 DOM 实例，请将其 ID`div1`传递给该方法。

```
const div1Node = document.getElementById("div1")

```

`div1Node`是`HTMLDivElement`的对象或实例。

要获取`I'm a Paragraph`的 DOM 实例，请传递它的 ID`par1`。

```
const par1Node = document.getElementById("par1")

```

`par1Node`是`HTMLParagraphElement`的对象或实例。

通过这些实例，我们可以对元素进行操作或执行动作。

## 2.`getElementsByClassName(className)`

这个方法返回 DOM 节点的集合(`HTMLCollection`)，这些节点的属性`class`值等于`className`字符串。

```
<div class="tab-body">
    <div class="tab-content tab-active">
        Div 1
    </div>        
    <div class="tab-content">
        Div 2
    </div>
    <div class="tab-content">
        Div 3
    </div>
</div>

```

我们有属性为`class`的 div，名称为`tab-body`、`tab-content`和`tab-active`。所有这些都是类名。类名是在元素上设置的`class`属性的值。

要获取名为`tab-content`的 div 的 DOM 节点，通过将`tab-content`作为参数来调用`getElementsByClassName()`方法。

```
>> document.getElementsByClassName("tab-content")

// HTMLCollection
0: <div class="tab-content tab-active">​
1: <div class="tab-content">​
2: <div class="tab-content">​
length: 3

```

如您所见，它返回了 div 的集合。它实际上是一个数组，因为我们可以使用`number-index`来引用它们。你可能会猜到，第一个元素是`Div 1`，第二个元素是`Div 2`，第三个元素是`Div 3`。

这将返回一个 DOM 节点集合，这些节点的标记名(元素名)等于指定的`tagName`字符串。

标签名将由元素名组成，如`div`、`p`、`img`、`video`、`b`、`i`、`br`、`button`、`input`等。

```
<div>
    Div 1
</div>        
<div>
    Div 2
</div>        

<p>
    I'm Paragraph 1
</p>
<p>
    I'm Paragraph 2
</p>

<button>Button 1</button>
<button>Button 2</button>

```

如果用`arg`调用`getElementsByTagName()`，它将返回 HTML 中的 div。

```
document.getElementsByTagName("div")

// HTMLCollection
0: <div>​ Div 1
1: <div>​ Div 2
length: 2

```

传递`p`返回所有段落元素。

```
document.getElementsByTagName("p")

// HTMLCollection
0: <p>​ I'm Paragraph 1
1: <p>​ I'm Paragraph 2
length: 2

```

传递`button`返回所有按钮元素。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
document.getElementsByTagName("button")

// HTMLCollection
// 0: <button>​ Button 1
// 1: <button>​ Button 2
// length: 2

```

## 4.`getSelection()`

此方法返回文档中选定文本的文本节点。该选定的文本区域是在触摸屏上使用鼠标或手指在文档中高亮显示的区域。

这个方法返回`Selection`。这个对象有一个`anchorNode`属性，它的值是高亮文本的文本节点。

```
<div>I'm Div1</div>

```

如果高亮显示`I'm Div1`，`document.getSlection()`将返回对象。

```
document.getSelection()

// Selection
//    anchorNode: #text
//    ...

```

`anchorNode`是一个文本节点，其`nodeValue`是高亮显示的文本`Div1`。

```
// TODO: Complete this.
```

## 5.`getElementsByName`和`querySelector(selector)`

这个方法将返回指定了`selector`字符串的第一个 DOM 节点。

`selector`字符串可以是:

*   元素名(或标记名)
*   类别名
*   ID 名称

让我们放大每个场景。

### 元素或标记名

`selector`将是元素的名称。

```
<div>
    Div 1
</div>        
<div>
    Div 2
</div>
<div>
    Div 3
</div>

```

```
document.querySelector("div")

>> div Div 1

```

它将返回第一次出现的 div 元素—在本例中是`Div 1`。

### 类别名

为了得到元素的属性名`class`，类名 arg 前面加了一个点`.`。

```
<div class="tab div1">
    Div 1
</div>        
<div class="tab div2">
    Div 2
</div>
<div class="div3">
    Div 3
</div>

```

要使用类`div1`获取 div:

```
document.querySelector(".div1")

>> div Div 1

```

我们以一个点(`.`)开始，然后是类名。

要使用类`div2`获取 div:

```
document.querySelector(".div2")

>> div Div 2

```

如果通过`.tab`，有两个 div 的类名是`tab`。只返回第一个 div。

```
document.querySelector(".tab")

>> div Div 1

```

### ID 名称

要获取带有 IS 名称的元素，请在 ID 名称 arg 前面加上一个散列值`#`。

```
<div id="div1">
    Div 1
</div>        
<div id="div2">
    Div 2
</div>
<div id="div3">
    Div 3
</div>

```

要获取 id 为“div1”的 div:

```
document.querySelector("#div1")

```

注意哈希`#`的使用。我们从`#`开始，后面跟着 ID 名称。

这将返回 div `Div 1`。

## 6.`querySelectorAll(selector)`

这将返回文档中所有出现的`selector`的节点列表。

同样，`selector`可以是一个:

*   元素名称
*   类别名
*   ID 名称

### 元素名称

```
<div>
    Div 1
</div>        
<div>
    Div 2
</div>
<div>
    Div 3
</div>
<p>
    I'm Paragraph 1
</p>
<p>
    I'm Paragraph 2
</p>

```

要选择所有 div，请将`div`传递给该方法。

```
document.querySelectorAll("div")

// NodeList(3)
//  0: div Div 1
//  1: div Div 2
//  2: div Div 3

```

它返回所有 div 的一个`NodeList`。`NodeList`就像一个数组，可以通过索引来引用元素。

再次，第一个元素是`Div 1`，第二个是`Div 2`，第三个是`Div 3`。

### 类别名

按类名查询元素。

```
<div class="tab div1">
    Div 1
</div>        
<div class="div2">
    Div 2
</div>
<p class="tab par1">
    I'm Paragraph
</p>

```

要查询类名为`tab`的元素，请传递`.tab`(类名后面跟一个点)。

```
document.querySelectorAll(".tab")

// NodeList(2)
//  0: div Div 1
//  1: p I'm Paragraph

```

它返回`Div 1`和`I'm Paragraph`元素，因为它们有类名`tab`。

### ID 名称

这将查询具有指定的`id`属性值的元素。arg 以一个 hash ( `#`)开始，紧接着是 ID 名称。

在这种情况下，`id` s 是唯一的。如果你给一个以上的元素分配相同的`id`值，浏览器将获取第一个元素并忽略其余的。使用`getElementById`在`id`上返回 null，值被分配给多个元素。但是使用这个`querySelectorAll`返回所有具有指定`id`值的元素。

```
<div class="div1" id="tab">
    Div 1
</div>        
<div class="div2" id="tab">
    Div 2
</div>
<p class="tab par1" id="par1">
    I'm Paragraph
</p>

```

得到带有`tab`和`id`的元素:

```
document.querySelectorAll("#tab")

// NodeList(2)
//  0: div Div 1
//  1: div Div 2

```

`Div 1`和`Div 2`被返回，因为它们有 ID `tab`。

## 7.`parentNode.insertBefore(newNode, refNode)`

这个方法将一个节点放在一个`parentNode`中的子节点`refNode`之前。紧随`newNode`之后的是`refNode`，它们都是`parentNode`的后代。

```
<div id="parent">
    Parent Node
    <div id="child">Child Node</div>
</div>

```

要在`#child`之前放置一个元素，使用`insertBefore`方法。

```
const newNode = document.createElement("b")
newNode.appendChild(document.createTextNode("Bold Child Node"))

parent.insertBefore(newNode, child)

```

这里我们创建了一个带有文本节点`Bold Child Node`的`b`粗体元素，并使用`insertBefore`方法将其插入到`#parent`中的`#child`节点之前。

HTML 看起来像这样:

```
<div id="parent">
    Parent Node
    <b>Bold Child Node</b>
    <div id="child">Child Node</div>
</div>

```

如果您引用 DOM 中的一个现有节点，该节点将从其位置移除并放置在新位置。

```
<div>
    Other Node
    <div id="othernode">
        Other Node Child
    </div>
</div>

<div id="parent">
    Parent Node
    <div id="child">Child Node</div>
</div>

```

我们想使用`insertBefore`将`#othernode`插入到`#parent`的子`#child`之前。

```
parent.insertBefore(othernode, child)

```

这将把`#othernode`从它的父节点`Other Node`中移除，并将它放在`#parent`中的`#child node`之前。

结果是:

```
<div>
    Other Node
</div>

<div id="parent">
    Parent Node
    <div id="othernode">
        Other Node Child
    </div>
    <div id="child">Child Node</div>
</div>

```

## 8.`appendChild(node)`

该方法将一个节点添加到 lemon 中，作为它的最后一个子元素。

```
<div id="divNode">
    <div>Div Node Child</div>
    <div>Div Node Child</div>
    <p>Paragraph Node Child</p>
</div>

```

使用`appendChild`将`p`元素追加到`#divNode`中:

```
const pEl = document.createElement("p")
pEl.append(createTextNode("Paragraph Node Child"))

divNode.appendChild(pEl)

```

结果是:

```
<div id="divNode">
    <div>Div Node Child</div>
    <div>Div Node Child</div>
    <p>Paragraph Node Child</p>
    <p>Paragraph Node Child</p>    
</div>

```

一个新的`p`节点被添加到`#divNode`的末尾。

如果该节点是文档中的现有节点，则将从其位置移除。

```
<p id="pNode">Paragraph Node</p>    

<div id="divNode">
    <div>Div Node Child</div>
    <div>Div Node Child</div>
    <p>Div Node Child</p>
</div>

```

将`p#pNode`附加到`#divNode`会将`p#pNode`从其原始位置移除。

```
divNode.appendChild(pNode)

```

结果是:

```
<div id="divNode">
    <div>Div Node Child</div>
    <div>Div Node Child</div>
    <p>Div Node Child</p>
    <p id="pNode">Paragraph Node</p>    
</div>

```

## 9.`createElement(elementName)`

此方法创建指定元素的节点。`elementName` arg 是要创建的元素节点的字符串值。

要创建 div 节点，传递`div`。一个按钮节点会是`"button"`等。

```
const divNode = document.createElement("div")
const buttonNode = document.createElement("button")

```

`divNode`将是一个`HTMLDivElement`对象。`buttonNode`将成为`HTMLButtonElement`的对象。

由此创建的所有元素节点都有一个来自`HTMLElement`的基本继承。

```
divNode instanceof HTMLDivElement
>> true

divNode instanceof HTMLElement
>> true

buttonNode instanceof HTMLButtonElement
>> true

buttonNode instanceof HTMLElement
>> true

```

返回的元素节点用于在节点及其子节点上操作和执行操作。

## 10.`createTextNode(textString)`

此方法创建一个文本节点。文本节点用于表示 DOM 或元素中放置文本的位置。

要在元素中放置文本，必须首先创建一个文本节点，方法是用文本字符串作为参数调用`createTextNode`，然后将文本节点追加到元素中。

```
<div id="parentNode">
    <div>Child Node</div>
</div>

```

要在`#parentNode`中放置文本:

```
parentNode.appendChild(document.createTextNode("Text"))

```

结果是:

```
<div id="parentNode">
    <div>Child Node</div>
    Text
</div>

```

## 11.`removeChild(childNode)`

这将从节点中移除一个子节点及其子节点。

```
<div id="parentNode">
    <div id="childNode1">Child Node 1</div>
    <div id="childNode2">Child Node 2</div>
    <div id="childNode3">Child Node 3</div>
</div>

```

从`#parentNode`中移除`#childNode1`:

```
parentNode.removeChild(childNode1)

```

结果是:

```
<div id="parentNode">
    <div id="childNode2">Child Node 2</div>
    <div id="childNode3">Child Node 3</div>
</div>

```

要删除的节点必须是引用节点的子节点。试图删除一个不是指定节点的子节点的节点将抛出一个`NotFoundError`。

```
NotFoundError: Node.removeChild: The node to be removed is not a child of this node

```

试图移除不存在的东西会抛出`ReferenceError`。

```
ReferenceError: childNode11 is not defined

```

## 12.`replaceChild(newNode, childNode)`

此方法用指定的节点替换父节点中的子节点。

`newNode`是替换父节点的`chidlNode`的节点。

替换`childNode`的`newNode`可以是父节点的子节点。

```
<div id="node">Node 1</div>

<div id="parentNode">
    <div id="childNode2">Child Node 2</div>
    <div id="childNode3">Child Node 3</div>
</div>

```

将`#parentNode`中的`#childNode2`替换为`#node`:

```
parentNode.replaceChild(node, childNode2)

```

结果是:

```
<div id="parentNode">
    <div id="node">Node 1</div>
    <div id="childNode3">Child Node 3</div>
</div>

```

`#node`从其原始位置移除，并替换为`#childNode2`在`#parent`内的位置。

```
<div id="parentNode">
    <div id="childNode1">Child Node 1</div>
    <div id="childNode2">Child Node 2</div>
    <div id="childNode3">Child Node 3</div>
</div>

```

可以用`#childNode1`代替`#childNode2`。

```
parentNode.replaceChild(childNode1, childNode2)

```

结果是:

```
<div id="parentNode">
    <div id="childNode1">Child Node 1</div>
    <div id="childNode3">Child Node 3</div>
</div>

```

## 13.`setAttribute(attrKey, attrValue)`

此方法为元素设置属性。`attrKey`是属性名，`attrValue`是属性的值。

```
<div id="divNode">Div </div>

```

使用`setAttribute`将`class`属性设置为`#divNode`，其值为`panel`:

```
divNode.setAttribute("class", "panel")

```

结果将是:

```
<div id="divNode" class="panel">Div</div>

```

通过用空格分隔字符串，可以为一个属性设置多个值。

```
divNode.setAttribute("class", "panel panel-group")

```

这将用值`panel panel-group`设置`class`属性。

```
<div id="divNode" class="panel panel-group">Div</div>

```

## 14.`getAttribute(attrKey)`

此方法返回属性的值。

给定一个元素:

```
<div id="divNode" class="panel panel-group">Div</div>

```

要获取`#divNode`中`id`属性的值:

```
divNode.getAttribute("id")

// divNode

```

要获取`class`属性的值:

```
divNode.getAttribute("class")

// "panel panel-group"

```

## 15.`getAttributeNames()`

这个方法以数组的形式返回一个元素的所有属性。

```
<div id="divNode" class="panel panel-group">Div</div>

```

要获取 div 节点中定义的所有属性:

```
divNode.getAttributeNames()

// Array[ "id", "class" ]

```

## 16.`cloneNode()`

这个方法克隆或复制一个 DOM 节点。它创建了一个 DOM 节点的另一个引用，所以原始的没有被触及。

这在 DOM APIs 中常用来移除 DOM 节点，比如`appendChild`、`replaceChild`等。这些 API 会删除它们所作用的 DOM 节点，因此如果您不想删除 DOM 节点，可以克隆 DOM 节点，这样 API 就可以在不影响原始副本的情况下对它的克隆进行操作。

注意:`cloneNode`不克隆元素的子节点。它只是单独复制元素。

例如，假设您有一个想要附加到另一个 DOM 节点的 DOM 节点。

```
<div id="div1Node">
    Div 1
</div>

<div id="div2Node">
    Div 2
</div>

```

如果您想在不影响`#div1Node`位置的情况下将`#div1Node`附加到`#div2Node`上(即，不将其从位置上移除)，您可以使用`cloneNode`克隆它，并将克隆的节点传递给`appendChild`方法。

```
div2Node.appendChild(div1Node.cloneNode())

```

结果是:

```
<div id="div1Node">
    Div 1
</div>

<div id="div2Node">
    Div 2
    <div id="div1Node"></div>
</div>

```

`#div1Node`被附加到`#div2Node`上，但没有其子文本节点。

要克隆一个节点及其所有子树，将布尔值`true`传递给`cloneNode`。

```
div2Node.appendChild(div1Node.cloneNode(true))

```

这将复制#div1Node 及其文本子节点，并将它们追加到#div2Node

```
<div id="div1Node">
    Div 1
</div>

<div id="div2Node">
    Div 2
    <div id="div1Node">
        Div 1
    </div>
</div>

```

## 17.`classList`

这是 DOM 节点中带有数组值的属性。该数组包含元素中`class`名称的值。

```
<div id="divNode" class="panel panel-group item"></div>

```

`#divNode`上的`classList`将是:

```
divNode.classList

// DOMTokenList [ "panel", "pane-group", "item" ]

```

这个`DOMTokenList`是`DOMTokenListPrototype`的子类，它包含用于操作元素中类名值的方法。

### `add`

这会给元素的类名添加一个新值。

```
<div id="divNode" class="panel panel-group item"></div>

```

将“项目组”添加到#divNode 类属性:

```
divNode.classList.add("item-group")

```

```
<div id="divNode" class="panel panel-group item item-group"></div>

```

### `contains`

检查类名是否有指定的值。

```
<div id="divNode" class="panel panel-group item"></div>

```

检查#divNode 的 class 属性是否有“panel”。我们这样做:

```
divNode.classList.contains("panel")

// true

```

### `​​item(index)`

使用索引样式从列表中检索类值。

例如:

```
<div id="divNode" class="panel panel-group item"></div>

```

divNode.classList.item(0)将是 panel
div node . class list . item(1)将是 panel-group
div node . class list . item(2)将是 item

### `remove​​​`

这将从属性中移除一个类值。

例如:

```
<div id="divNode" class="panel panel-group item"></div>

```

要从`#divNode`的类名中删除面板:

```
divNode.classList.remove("panel")

```

结果:

```
<div id="divNode" class="panel-group item"></div>

```

### `​replace`

这将使用新值替换列表中的类值。

例如:

```
<div id="divNode" class="panel panel-group item"></div>

```

要用“tab”替换 divNode 类名中的“item ”,我们这样做:

```
divNode.classList.replace("item", "tab")

```

结果:

```
<div id="divNode" class="panel panel-group tab"></div>

```

### `toggle​`

此方法在类名中移除或添加指定的值。

如果类名不在元素中，它会添加它。如果类名在元素中，它将删除它。这是切换。

```
<div id="divNode" class="panel panel-group item"></div>

```

我们可以删除/添加“面板”类名，就像这样:

#### 实施例 1

```
divNode.classList.toggle("panel")

```

结果:

```
<div id="divNode" class="panel-group item"></div>

```

#### 实施例 2

```
divNode.classList.toggle("panel")

```

结果:

```
<div id="divNode" class="panel panel-group item"></div>

```

#### 实施例 3

```
divNode.classList.toggle("panel")

```

结果:

```
<div id="divNode" class="panel-group item"></div>

```

## 18.`parentNode`

这将返回元素的父节点的 DOM 节点实例。

```
<div id="divNode">
    Parent Node
    <div id="divChildNode"></div>
</div>

```

获取`#divChildNode`的父节点:

```
divChildNode.parentNode

// div ParentNode

```

## 19.`parentElement`

见上；这与`parentNode`的工作原理相同。

## 20.`innerHTML`

这将返回元素的 HTML 标记。

例如:

```
<div id="divNode">
    <p>I'm Paragraph</p>
    <div id="childNode1">Chidl Node 1</div>
    <div id="childNode2">Child Node 2</div>
    <div id="childNode3">Child Node 3</div>
</div>

```

`#divNode`的`innerHTML`应该是:

```
divNode.innerHTML

// "\n    <p>I'm Paragraph</p>\n    <div id=\"childNode1\">Chidl Node 1</div>\n    <div id=\"childNode2\">Child Node 2</div>\n    <div id=\"childNode3\">Child Node 3</div>\n"

```

它是`#divNode`元素的子节点的字符串表示。

`innerHTML`用于通过连接两个或多个 HTML 标记字符串来构建 DOM 节点。

例如:

```
<div id="divNode">
    Div 1
<div>

```

将 HTML 标记`I'm Paragraph`添加到`divNode`中:

```
divNode.innerHTML += "<p>I'm Paragraph</p>"

```

DOM 的结果如下。

```
<div id="divNode">
    Div 1
    <p>I'm Paragraph</p>
<div>

```

要将其添加到`divNode` HTML 之前:

```
divNode.innerHTL = "<p>I'm Paragraph</p>" + divNode.innerHTML

```

## 21.`innerText`

这将返回元素子节点的文本节点。

例如:

```
<div id="divNode">
    <p>I'm Paragraph</p>
    <div id="childNode1">Chidl Node 1</div>
    <div id="childNode2">Child Node 2</div>
    <div id="childNode3">Child Node 3</div>
</div>

```

`innerText` on `divNode`以字符串形式返回`#divNode`中包含的所有文本节点。它将向下运行到它的最后一个子树，并收集那里的所有文本节点。

```
divNode.innerText

// "I'm Paragraph\n\nChidl Node 1\nChild Node 2\nChild Node 3"

```

## 结论

如果你对上面概述的 API 有任何问题，或者对我应该添加、更正或删除的任何内容有任何建议，请随时发表评论、给我发电子邮件或给我发 DM。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)