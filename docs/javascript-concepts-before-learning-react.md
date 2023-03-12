# 学习 React 之前需要掌握的 JavaScript 概念

> 原文：<https://blog.logrocket.com/javascript-concepts-before-learning-react/>

您可能已经知道，React 是一个创建 UI 组件的库，可以用作 web 和移动应用程序的基础。React 与其竞争对手的区别在于，它的代码完全是用 JavaScript 编写的。甚至类似 HTML 的模板也是使用 [JSX](https://facebook.github.io/jsx/) 用 JS 编写的，这是 JS 语言的一个扩展，用于构造 UI 组件。

本文的目标是通过强调在真正投入 React 之前应该掌握的 JavaScript，来帮助有抱负的 React 开发人员入门。对 JavaScript 的完整介绍超出了本文的范围，但是 React 建立在对现代 JavaScript 特性的使用之上，这些特性主要是在 [ES2015](https://en.wikipedia.org/wiki/ECMAScript#6th_Edition_%E2%80%93_ECMAScript_2015) 中引入的。

下面，我概述了几乎每个 React 应用程序中大量使用的常见 JavaScript 模式和语言特性。对于每个概念，我都提供了外部链接。如果您感兴趣，可以了解更多关于在 React 上下文中使用它的信息。

## 条件逻辑有`if`语句、三元运算符和逻辑运算符

这些操作符已经成为 JavaScript 的一部分很长时间了。在 React 中，它们对于组件的条件呈现特别有用。

[三元运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)是这样的:

```
const buttonLabel = playback === "stop" ? "play ▶️" : "stop ⏹️";
```

如果变量`playback`的值为`stop`，那么操作者将字符串值`play ▶️`赋给`buttonLabel`，在所有其他情况下，将字符串值`stop ⏹️`赋给【】。它基本上与下面的代码相同:

```
let buttonLabel;
if (playback === "stop") {
  buttonLabel = "play ▶️";
}
else {
  buttonLabel = "stop ⏹️"
}
```

当然，您可以使用这样的`if...else`语句，但是如果您需要对[使用单行表达式有条件地以内联方式呈现元素](https://reactjs.org/docs/conditional-rendering.html#inline-if-else-with-conditional-operator)，那么三元运算符通常是首选工具。

否则，您必须调用一个[函数，在那里您放置了用于条件渲染的代码](https://codepen.io/gaearon/pen/ZpVxNq?editors=0011)。在 React 中，您还可以使用更复杂的条件逻辑(例如，一个 [`if...else`级联](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else))并将值存储在变量中，这些变量可用于 JSX 代码中的[条件渲染](https://codepen.io/gaearon/pen/QKzAgB?editors=0010)。

[逻辑运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators) `&&`或`||`对于构建 React 组件来说非常方便。

```
const isLoggedIn = true;
const userComponent = isLoggedIn && getUserComponent();
```

在我们的例子中，`&&`操作符的左操作数(`isLoggedIn`)计算为`true`。因此，右操作数的结果(函数调用`getUserComponent()`)被赋给`userComponent`变量。

这个概念对于 React 中的[条件渲染也非常有用，因为`true && jsxComponent`返回`jsxComponent`，而`false && jsxComponent`返回`false`。如果你返回`false`，React 会忽略它，简单地不渲染任何东西。](https://codepen.io/gaearon/pen/ozJddz?editors=0010)

也可以组合多个条件。在下一个例子中，当两个条件都满足时，返回`getOtherUsersComponent()`的结果。

```
const otherUsers = isLoggedIn && users?.length > 0 && getOtherUsersComponent();
```

注意第二个操作数`users?.length > 0`中的`?`。这就是[可选链接](https://blog.logrocket.com/optional-chaining-and-nullish-coalescing-in-javascript/)，这在 React 项目中并不少见。

如果返回`[null](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/null)`，React 不渲染任何东西。与`undefined`相反，`null`代表任何价值的有意缺失。

```
if (shouldRenderComponent()) {
  return getComponent();
}
else {
  return null;
}
```

这对[防止组件渲染](https://codepen.io/gaearon/pen/Xjoqwm?editors=0010)很有用。

## 对象文字和内联函数

创建对象有[多种方式。用文字符号初始化对象看起来像这样:](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer)

```
const foo = { bar: 3, hello: "world" };
```

这种符号经常在 React 项目中使用，在不将对象赋给变量的情况下内联创建对象，例如，初始状态为`useReducer`。

```
// 2nd function argument uses inline object literal
foo("bar", { hello: "world" })
```

借助 ES2015 语法，您还可以使用简写属性和方法名称。

```
// instead of 
function foo(id) {
  return {
    name: "dummy",
    id: id,
    bar: function() {
      console.log("bar");
    }
  }
}
// you can do
function foo(id) {
  return {
    name: "dummy",
    id, // shorthand property name
    bar() { // shorthand method name
      console.log("bar");
    }
  }
}
```

在 React 开发中，速记属性尤其被广泛使用，因为它们消除了冗余代码。

也就是说，您必须意识到内联对象文字和指向对象的变量(由对象文字创建)之间的细微差别。在某些情况下，出于 React 性能优化的目的，您应该[避免将对象文字](https://www.digitalocean.com/community/tutorials/react-keep-react-fast#avoid-object-literals)传递给 React 组件，因为每次都会创建一个新对象，导致不必要的重新渲染。

同样的原则也适用于匿名函数(即内联函数)，在一些 React 性能用例中应该避免使用[。](https://www.digitalocean.com/community/tutorials/react-keep-react-fast#avoid-anonymous-functions)

```
// inline function
foo(() => {console.log("bar")});
// passing variable pointing to function
const barFunc = () => console.log("bar");
foo(barFunc);
```

## 模板文字

[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)或模板字符串是 ES2015 中引入的，允许创建嵌入 JavaScript 表达式的字符串。在反斜线中，您可以在`${}`中将“硬编码”的字符串与 JavaScript 表达式结合起来。

```
const name = "doppelmutzi";
console.log(`Hello, my name is ${name}`); // Hello, my name is doppelmutzi
```

当然，表达式可以更复杂，比如内联计算或函数调用。

```
const name = "doppelmutzi";
const getRandomIndex = max =>  Math.floor(Math.random() * Math.floor(max))
const food = ["🥞", "🧇", "🍔", "🍟", "🍕"];
const getFood = index => food[index]
console.log(`Hello, my name is ${name} 
and I'm hungry for ${getFood(getRandomIndex(food.length))}`);
```

后一个例子也使用了 multiline 特性，因此输出在表达式插值(`${name}`)后有一个换行符。

## 交换语句

在大中型 React 应用程序中，您很可能会遇到用于跨组件管理状态的`[switch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/switch)`语句。像`[useReducer](https://reactjs.org/docs/hooks-reference.html#usereducer)`钩子或者 [Redux](https://redux.js.org/) 这样的工具经常被用于这样的任务。

下面的例子显示了一个所谓的[缩减器函数](https://medium.com/async-la/a-short-and-sour-guide-to-reducers-b5b54d3bb018)，它使用一个`switch`语句进行状态管理。您不一定要使用带有缩减器的`switch`语句，但是这是一个已经建立的模式。

```
export default (state, action) => {
  switch (action.type) {
    case "TOGGLE_DARK_MODE":
      return {
        ...state,
        darkMode: action.darkMode,
      };
     case "UPDATE_PLAYBACK": {
      return {
        ...state,
        currentSound: action.currentSound,
      };
    }  
    default:
      return state;
  }
};
```

上面的例子检查了`action.type`的值，并执行了`case`语句的代码。如果它的计算结果是一个字符串`TOGGLE_DARK_MODE`，那么执行第一个 case 语句的代码。

拥有一个可选的默认条款是一个很好的做法。如果`switch`表达式与任何一个 case 子句都不匹配，它就会被执行。使用扩展操作符(例如`...state`)是一种常见的做法。

在上面的例子中，每个 case(和 default)子句返回一个新的对象，表示新的 React 状态。这就给我们带来了 React 开发的一个重要话题。

## 对象析构

[对象析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)的原理非常简单。使用下面优雅的语法，我们可以将属性提取到变量中。

```
const creatures = {
  human: ["👨🏿‍💼", "👩🏼‍💼", "🧑🏻‍💼", "👩🏾‍💻", "🧑‍💻"],
  supernatural: ["👾", "🤖", "👽", "👹", "👺"]
};
const { human, supernatural } = creatures;
console.log(human); // ["👨🏿‍💼", "👩🏼‍💼", "🧑🏻‍💼", "👩🏾‍💻", "🧑‍💻"]
console.log(supernatural); // ["👾", "🤖", "👽", "👹", "👺"]
```

如果使用不带变量声明的赋值，就需要使用括号。

```
const creatures = {
  human: ["👨🏿‍💼", "👩🏼‍💼", "🧑🏻‍💼", "👩🏾‍💻", "🧑‍💻"],
  supernatural: ["👾", "🤖", "👽", "👹", "👺"]
};
let human, supernatural;
({human, supernatural} = creatures);
console.log(human); // ["👨🏿‍💼", "👩🏼‍💼", "🧑🏻‍💼", "👩🏾‍💻", "🧑‍💻"]
console.log(supernatural); // ["👾", "🤖", "👽", "👹", "👺"]
```

对象析构为你提供了节省额外代码行的语法优势。

```
// you can do this
const { human, supernatural } = creatures;
// instead of
const human = creatures.human;
const supernatural = creatures.supernatural;
```

在 React 上下文中，对象析构经常与函数参数一起使用。

```
const infos = {name: "doppelmutzi", hobby: "React" };
function printInfos({name, hobby}) {
  console.log(name, hobby);
}
printInfos(infos);
const printName = ({name}) => console.log(name);
printName(infos);
```

对于更简洁的代码，React 开发人员使用这种模式和 [props](https://reactjs.org/docs/components-and-props.html) ，它们是 React 组件的输入。

```
function MyReactComponent({name, age}) {
  // ...
}
```

将赋值与重命名变量结合起来可能有助于提高代码的可读性。

```
const creatures = {
  human: ["👨🏿‍💼", "👩🏼‍💼", "🧑🏻‍💼"]
};
const { human: people  } = creatures;
console.log(people); // ["👨🏿‍💼", "👩🏼‍💼", "🧑🏻‍💼"]
```

您还可以在从分配的对象中解包字段时定义默认值。以下示例结合了多种技术。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
const { human: people = ["👨🏿‍💼"], supernatural = ["👾", "👽"] } = {
  human: ["👨🏿‍💼", "👩🏼‍💼", "🧑🏻‍💼"]
};
console.log(people); // ["👨🏿‍💼", "👩🏼‍💼", "🧑🏻‍💼"]
console.log(supernatural); // ["👾", "👽"]
```

嵌套也是可以的，但是我不建议过度嵌套；否则，可理解性降低。

```
const creatures = {
  animals: {
    wildlife: ["🦂", "🐍"],
    pet: ["🐕", "🐈"]
  },
  human: ["👨🏿‍💼", "👩🏼‍💼", "🧑🏻‍💼"]
};
const { animals: { pet }} = creatures;
console.log(pet); //  ["🐕", "🐈"]
```

Ronald Chen 提供了更多关于析构嵌套对象的见解。

## 数组析构

在[析构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)的帮助下，一个数组可以以这样的方式解包，它的值被提取到不同的变量中，就像这样:

```
const array = [1, 2];
const [varForVal1, varForVal2] = array;
console.log(varForVal1); // 1
console.log(varForVal2); // 2
```

如您所见，变量是从数组的左到右赋值的，因此顺序是保持不变的。

```
const [fruit, veggie] = ["🍓", "🥦", "🍕", "🌮", "🥪", "🍔"];
console.log(fruit); // 🍓
console.log(veggie); // 🥦
```

考虑到顺序，您也可以跳过值。

```
const [fruit,,pizza,,,burger] = ["🍓", "🥦", "🍕", "🌮", "🥪", "🍔"];
console.log(fruit); // 🍓
console.log(pizza); // 🍕
console.log(burger); // 🍔
```

下面的“闲聊代码”可能有助于理解到底发生了什么。

```
const [
  fruit,
  /* skip entry 2 (🥦) */,
  pizza,
  /* skip entry 4 (🌮) */,
  /* skip entry 5 (🥪) */,
  burger] = ["🍓", "🥦", "🍕", "🌮", "🥪", "🍔"];
```

您还可以使用 rest 模式一次分配多个值。

```
const [fruit, veggie, ...junkfood] = ["🍓", "🥦", "🍕", "🌮", "🥪", "🍔"];
console.log(fruit); // 🍓
console.log(veggie); // 🥦
console.log(junkfood); // ["🍕", "🌮", "🥪", "🍔"]
```

数组析构允许默认值。当然，您也可以将这种模式与函数调用结合起来。

```
const getFood = () => ["🍓", "🥦"];
const [fruit, veggie, junkfood = "🍕"] = getFood();
console.log(fruit); // 🍓
console.log(veggie); // 🥦
console.log(junkfood); // 🍕
```

优雅的数组析构概念经常与 [React Hooks](https://reactjs.org/docs/hooks-overview.html) 一起使用，因为你可以想出几行语义代码。要为 React 组件创建状态变量和更新函数，可以使用 React 的`[useState](https://reactjs.org/docs/hooks-reference.html#usestate)`钩子。

```
const initialValue = false;
// instead of 
const stateWithUpdater = useState(initialValue);
const darkMode = stateWithUpdater[0];
const darkModeUpdater = stateWithUpdater[1];
// you can do
const [darkMode, setDarkMode] = useState(initialValue);
```

下面的例子演示了您可以为具体的用例实现通用函数。数组析构允许函数调用方使用语义变量名。

```
const getFood = type => {
  let food = [];
  let error = false;
  if (type === "fruits") {
    food = ["🍓", "🥝", "🍌"];
  }
  else if (type === "junk") {
    food = ["🍕", "🌮", "🥪"];
  }
  else {
    error = true;
  }
  const addFood = newFood => food.push(newFood);
  return [food, error, addFood];
};
const [healthyFood, noFruitsAvailable, addFruitFunc] = getFood("fruits");
console.log(healthyFood); // ["🍓", "🥝", "🍌"]
console.log(noFruitsAvailable); // false
console.log(addFruitFunc("🍒")); 
console.log(healthyFood); // ["🍓", "🥝", "🍌", "🍒"]
```

对于这个用例，在我看来，使用`getFood`函数返回数组比使用对象析构会产生更简洁的代码。数组析构允许自定义变量名。相比之下，使用对象析构，您需要重命名变量。

```
const getFood = type => {
  // same function body as above, only different return statement
  return {food, error, addFood};
};
const {food: healthyFood, error: noFruitsAvailable, addFood: addFruitFunc} = getFood("fruits");
console.log(noFruitsAvailable); // false
console.log(addFruitFunc("🍒")); 
console.log(healthyFood); // ["🍓", "🥝", "🍌", "🍒"]
```

这可能就是为什么`useState`返回一个数组而不是一个对象——更一般地说。

## 传播算子

[spread 操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) ( `...`)允许将一个可迭代项(例如，一个数组)提取到它的各个部分，并插入到需要单个元素的地方。使用这种语法，您可以拆分对象属性或数组元素。

在下面的下一个用例中，我们取出数组的元素，并将每个元素作为单独的函数参数传递。

```
const numbers = [11, 5, 3, 1, 26];
// Math.max expects to be called like Math.max(11,5,3,1,26)
console.log(Math.max(...numbers)); // 26
```

另一个用例是复制对象属性，从而创建一个新对象。

```
const food = {
  breakfast: ["🥞", "🧇"],
  lunch: ["🍔", "🍟", "🍕"]
};
const foodAndDrinks = {
  ...food,
  drinks: ["🍷", "🍹", "🍺", "🥃"],
};
console.log(foodAndDrinks); 
/* 
{
  breakfast: ["🥞", "🧇"],
  lunch: ["🍔", "🍟", "🍕"],
  drinks: ["🍷", "🍹", "🍺", "🥃"],
} */
```

使用这种简洁的语法，您可以方便地创建数组的副本。

```
  const food = ["🥞", "🧇", "🍔", "🍟", "🍕"];
  const copy = [...food];
  console.log(copy); // ["🥞", "🧇", "🍔", "🍟", "🍕"]
  console.log(food === copy); // false
```

回想一下 switch 语句小节的最后一段，spread 语法经常在 React state 的上下文中使用。使用 React，您不应该直接操作状态对象。相反，每当您想要更新状态时，都需要创建一个全新的状态对象。以下概念被认为是良好的实践。

```
const restaurantState = {
  drinks: ["🍷", "🍹", "🍺", "🥃"],
  food: ["🥞", "🧇", "🍔", "🍟", "🍕"],
  lastOrder: null
}
// the customer ordered a 🍔
const stateAfterOrder = {
  drinks: [...restaurantState.drinks], // copy drinks
  food: [...restaurantState.food], // copy food
  lastOrder:  "🍔" // override lastOrder
}
```

## Rest 运算符

在 [rest 操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters) ( `...`)的帮助下，你可以将一列函数参数合并成一个数组。虽然 rest 操作符的语法与 spread 操作符相同，但是它们的使用位置却完全不同。

如果在`{}`或`[]`中使用操作符，则分别使用对象或数组展开。另一方面，如果在函数签名中使用带有最后一个参数的操作符，那就是 rest 参数。

它的目的是将一列值合并到一个数组中。在下一个例子中，提供了各种食物作为函数参数。对于 rest 操作符，它们被放入一个名为`food`的数组中。`findIndex`方法对一个数组进行操作，并测试西兰花是否包含在内。

```
const cleanFoodIncluded = (...food) => food.findIndex(f => f === "🥦") !== -1;
console.log(cleanFoodIncluded("🍕", "🌮", "🥪", "🥦", "🍔")); // true
```

通常的做法是将 rest 运算符与 spread 运算符结合使用。这将多个参数组合成一个数组，以将条目再次分布到 React 组件内的另一个位置。

```
function renderProduct(
  // these are considered as component-specific
  {id, name}, 
/* all other arguments are relevant for container component. Therefore, consolidate them into an array with rest operator */
...containerProps) { 
  // output 0815, choco, [{margin: "10px"}, {padding: "5px"}]
    console.log(id, name, containerProps); 
  /* unpack array again with spread operator to provide them as individual args */
  renderContainer(...containerProps); 

  }
function renderContainer(margin, padding) {
  // output {margin: "10px"}, {padding: "5px"}
  console.log(margin, padding); 
}
  const product = {
    id: "0815", name: "choco"
  }
  renderProduct(product, {margin: "10px"}, {padding: "5px"} );
```

## 函数声明、函数表达式和箭头函数

一个[函数声明](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function)看起来像这样:

```
function getFood(index) {
  const food = ["🥞", "🧇", "🍔", "🍟", "🍕"];
  return food[index];
}
```

相反，这是一个[函数表达式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/function):

```
const getFood = function(index) {
  const food = ["🥞", "🧇", "🍔", "🍟", "🍕"];
  return food[index];
}
```

一个[箭头函数表达式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)构成了前两者的替代。它的优势在于它的语法，允许你以更简洁的方式编写函数。

```
const getFood = (index) => {
  const food = ["🥞", "🧇", "🍔", "🍟", "🍕"];
  return food[index];
}
```

如果只有一个参数，可以跳过括号。

```
const getFood = index =>  {
  const food = ["🥞", "🧇", "🍔", "🍟", "🍕"];
  return food[index];
}
```

如果您的函数不接受任何参数，您需要使用括号。

```
const getFood = () =>  {
  return ["🥞", "🧇", "🍔", "🍟", "🍕"];
}
```

如果函数体由单个 return 语句组成，您可以用隐式返回替换这个显式返回，如下所示:

```
const getFood = index =>  \["🥞", "🧇", "🍔", "🍟", "🍕"\][index];
```

只有使用函数声明，才能在定义函数之前调用它们。这是因为函数声明是提升的，也就是说，它们在执行之前被移动到其作用域的顶部。

```
console.log(getFoodDeclaration()); // "🍟"
console.log(getFoodExp()); // ReferenceError: Cannot access 'getFoodExp' before initialization
console.log(getFoodArrow()); // ReferenceError: Cannot access 'getFoodArrow' before initialization
function getFoodDeclaration() {
  return "🍟";
}
const getFoodExp = () =>  {
  return "🍔";
}
const getFoodArrow = () =>  "🍕";
```

函数声明/表达式和箭头函数表达式之间的另一个区别是`this`关键字，我将在类的上下文中讨论它。

## 班级

一个[类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/class)代表了新对象的蓝图。变量和函数可以附加到一个类上，分别称为属性和方法。在类的上下文中，`[this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)`关键字指的是当前实例。如果你来自面向对象的背景，你很可能对它有一些不正确的假设；[这篇文章](https://www.vojtechruzicka.com/javascript-this-keyword/)有助于揭开`this`关键词的神秘面纱。

一个类可以有一个[构造函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/constructor)方法，它代表一种特殊的函数来初始化蓝图的新对象。您可以用关键字`[new](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new)`实例化该类。这样就调用了构造函数(或者默认构造函数，如果没有提供的话)，并创建了一个新的对象。

```
class Fruit {
  // class body
  constructor() {
    // property
    this.popular = "🥝"
  }
  whatsPopular() {
    // method body
    console.log(this.popular) "🥝"
  }
}
// instantiate an object from the class
const fruit = new Fruit();
// call the method on the instance
fruit.whatsPopular();
```

另一个重要的概念是用类语法继承[。使用](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Inheritance)`[super](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/super)`关键字，您可以访问父节点。

```
class Food {
  constructor() {
    this.popular = "🍕"
  }
}
class Fruit extends Food {
  constructor() {
    // required to call constructor of parent class
    // needs to be first statement in child constructor
    super();
    // override
    this.popular = "🥝"
  }
  whatsPopular() {
    console.log(this.popular) // "🥝"
  }
}
const fruit = new Fruit();
fruit.whatsPopular();

```

有了 [ES2017](https://www.dotnetcurry.com/javascript/1405/es8-es2017-javascript-new-features) ，使用类属性和方法的语法是一个更加简洁的[。您也可以使用箭头函数作为方法。](https://medium.com/@charpeni/arrow-functions-in-class-properties-might-not-be-as-great-as-we-think-3b3551c440b1)

```
class Food {
  popular = "🍕";
}
class Fruit extends Food {
  popular = "🥝";
  // method by arrow function expression
  whatsPopular = () => {
    console.log(this.popular)
  };
}
const fruit = new Fruit();
fruit.whatsPopular();
```

通常，当你在 React 开发中遇到[类时，你会发现 ES2017 语法。](https://reactjs.org/docs/components-and-props.html#function-and-class-components)

```
// class-based React components must extend React.Component (or a subclass like React.PureComponent)
class Food extends React.Component {
  // define default React state with ES2017 property syntax
  state = {
    popular = "🍕"
  }
  render() {
    // required method by every class-based React component
  }
}
```

注意，这一节绝不是对 JavaScript 类的完整解释。在我看来，如果你的计划是学习反应，你不需要花太多的时间去学习课程。我为 React 初学者推荐的学习途径是理解这里介绍的类的基础知识，以便能够阅读和理解基于类的 React 组件。

我认为对于新的 React 开发人员来说，没有必要广泛地理解类，因为从去年开始，类的重要性已经大大降低了。这和 React 钩子的引入有关。

在此之前，使用基于类的方法只能拥有复杂的 React 组件。只有使用类，才有可能定义组件状态和使用生命周期方法。钩子也允许功能组件做类似的事情。整个 React 社区都强烈要求只使用功能组件。

然而，如果您使用现有的基于类的组件进行遗留项目，或者如果您遇到一些需要您使用类的[少数用例](https://reactjs.org/docs/hooks-faq.html#do-hooks-cover-all-use-cases-for-classes)，或者即使您只是喜欢使用类(例如，使用特定的生命周期方法)，那么您需要理解与 React 相关的类基础。

幸运的是，对于 React 开发，JavaScript 类的相关方面并不复杂。我喜欢对类组件使用 ES2017 语法，对方法使用 arrow 函数，因为它们[不需要](https://medium.com/@joespinelli_6190/using-arrow-functions-to-avoid-binding-this-in-react-5d7402eec64)使用`bind()`方法。代码变得更容易理解。

考虑第一个例子，它需要一个`[bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)`；否则，调用`this.setState`会导致错误。

```
class Button extends React.Component {
  constructor(props) {
    super(props);
    this.state = { clicked: false };
    // this.onClick = this.onClick.bind(this);
  }
  onClick() {
    this.setState({ clicked: true }); // ERROR this.setState is not a function
  }
  render() {
    return (
      <div>
        <p>{this.state.clicked && "clicked"}</p>
        <button onClick={this.onClick}>click</button>
      </div>
    );
  }
}
```

使用 ES2017 语法，您可以编写更易于理解的基于类的组件。原因是因为 arrow 函数有一个[词法`this`](https://hackernoon.com/javascript-es6-arrow-functions-and-lexical-this-f2a3e2a5e8c4) ，它在 arrow 函数中的值是由周围的作用域决定的(在我们的例子中，是用`this`访问 state 的`render`方法)。

```
class Button extends React.Component {
  state = {
    clicked: false
  }  
  onClick = () => {
    this.setState({ clicked: true });
  }
  render() {
    return (
      <div>
        <p>{this.state.clicked && "clicked"}</p>
        <button onClick={this.onClick}>click</button>
      </div>
    );
  }
}
```

## 数组函数

掌握[数组函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)是 React 开发者的重要技能。`[map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray/map)`基本上用于每个 React 应用程序，例如，循环遍历食物对象列表，并在`li`标签中呈现每个条目。

`map()`函数产生一个具有相同数量元素的新数组。然而，对于原始条目的每个条目，应用一个操作来产生新的条目。下面的示例为每个条目创建一个带有重复水果的新数组。

```
const fruits = ["🍓", "🥝", "🍌", "🍒"];
const moreFruits = fruits.map(f => `${f}${f}`);
console.log(moreFruits); // ["🍓🍓", "🥝🥝", "🍌🍌", "🍒🍒"] 
```

`[filter()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray/filter)`通常与状态管理一起使用，因为它返回一个全新的数组，该数组只包含原始数组中那些通过所提供条件的元素。

```
const people = [
  { name: "Max", sex: "male" }, 
  { name: "Jacky", sex: "female" },
  { name: "Stephanie", sex: "female" }
];
const women = people.filter(person => person.sex === "female");
console.log(women); /*  [{ name: "Jacky", sex: "female" }, { name: "Stephanie", sex: "female"}] */..
```

`[findIndex()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray/findIndex)`返回通过测试的第一个元素的索引；否则返回`-1`。

```
const fruits = ["🍓", "🥝", "🍒", "🍌", "🍒"];  
console.log(fruits.findIndex(fruit => fruit === "🥝")); // 1
console.log(fruits.findIndex(fruit => fruit === "🍌🍌")); // -1
console.log(fruits.findIndex(fruit => fruit === "🍒")); // 2 (first match)
```

`[find()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray/find)`返回第一个通过测试的条目。这在 React 状态管理的上下文中很有用。例如，我们有一个用户列表。我们单击了列表中的一个特定用户，并希望显示一个显示该用户信息的模式对话框。

```
const users = [
  { id: 1, name: "Max", sex: "male" },
  { id: 2, name: "Jacky", sex: "female" },
  { id: 3, name: "Stephanie", sex: "female" }
];
function logUserInfo(id) {
  console.log(users.filter(user => user.id === id));
}
logUserInfo(2); // { id: 2, name: "Jacky", sex: "female" }
```

## 不可变值与可变值

理解这个概念很重要。不可变的值和对象之后不能被改变，所以原始的保持不变。像字符串或数字这样的原始值本质上是不可变的。另一方面，默认情况下，对象是可变的。我们来看看这是什么意思。

```
// immutable use case
// Change strings won't work. Throws error in strict mode
"use strict";
const hello = "world";
hello[0] = "W"; // try to upper case the first char
console.log(hello); // world (in none-strict mode)
```

关于可变性的错误思维会导致错误。

```
// mutability use case
const meal = {
  kind: "🍕",
  origin: {
    country: "Italy"
  }
}
const fruit = {
  kind: "🍇",
  origin: meal.origin
};
console.log(`${fruit.kind} from ${fruit.origin.country}`); // ✅ "🍇 from Italy"
console.log(`${meal.kind} from ${meal.origin.country}`); // ✅  "🍕 from Italy"
// we bought new grapes from Germany
fruit.origin.country = "Germany";
console.log(`${fruit.kind} from ${fruit.origin.country}`); // ✅  "🍇 from Germany"
// we have caused an unwanted side effect
console.log(`${meal.kind} from ${meal.origin.country}`); // ❌ "🍕 from Germany"
```

对象是可变的，但是你可以使用`[Object.freeze()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)`或者像 [Immutable.js](https://immutable-js.github.io/immutable-js/) 这样的第三方库来使它们不可变。

React 团队建议您在应用程序设计的多个领域使用不可变对象，例如，使用基于[组件的](https://reactjs.org/docs/state-and-lifecycle.html#using-state-correctly)和[全局状态](https://reactjs.org/docs/hooks-reference.html#usereducer)。这是因为不变性通常会导致[多重架构利益](https://stackoverflow.com/a/34385684)。这就是为什么大多数开发人员社区建议追求不变性的编码思维模式。

我需要强调的是，突变本身并不坏。然而，无序变异的一个问题是，它会导致意想不到的副作用，而这些副作用通常会导致意想不到的错误。

在 React 开发中，不要试图直接改变状态变量；相反，使用状态管理库的首选方法。作为一个例子，下面的代码片段显示了应该如何以及不应该如何更新基于类的 React 组件的本地状态。

```
class Button extends React.Component {
  state = {
    clicked: false
  }  
  onClick = () => {
    // ❌ don't do this
    this.state.clicked = true;
    // ✅ instead do this: pass a new object to setState
    this.setState({ clicked: true });
  }
  render() {
    return (
      <div>
        <p>{this.state.clicked && "clicked"}</p>
        <button onClick={this.onClick}>click</button>
      </div>
    );
  }
}
```

如果你使用像`useReducer`或 [Redux](https://redux.js.org/) 这样的全局状态管理工具，你应该像这样更新状态。

```
const newState = {
  ...state, // creates a copy of the current state
  darkMode: action.darkMode, // just override to reflect the changes
};
```

## 回调函数

作为参数传递给另一个函数的函数被称为[回调](https://en.wikipedia.org/wiki/Callback_(computer_programming)，如果该函数在以后调用该参数。

`[setTimeout](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout)`获取一个回调函数作为第一个参数，在定时器超时后调用(第二个参数)。

```
window.setTimeout(() => console.log("I'm a callback function"), 1000); // after 1s: "I'm a callback function"
```

在 React 的上下文中，[回调函数通常作为道具](https://reactjs.org/docs/faq-functions.html)传递给 React 组件。因此，子组件可以在以后以父组件可以对其作出反应的方式执行传递的回调(例如，更新状态，从而更新 UI)。

回调函数在[反应钩子](https://reactjs.org/docs/hooks-intro.html)的上下文中也很重要，例如，用`[useEffect](https://reactjs.org/docs/hooks-effect.html)`触发副作用。

## 结论

由于 React 开发主要包括编写普通的 JavaScript 代码，我建议在学习 React 之前对 JavaScript 基础有一个很好的理解。有了这条学习道路，你会有一个更顺利的开始——我向你保证。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。