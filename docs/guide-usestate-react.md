# React 中的使用状态:一个完整的指南

> 原文：<https://blog.logrocket.com/guide-usestate-react/>

***编者按:*** *本 React `useState` Hook 教程最后一次更新于 2023 年 2 月 7 日，以包含 React `useState`的更多信息，并反映 React 的更新。查看我们的 [React 钩子参考指南](https://blog.logrocket.com/react-reference-guide-hooks-api/)和[备忘单](https://blog.logrocket.com/react-hooks-cheat-sheet-unlock-solutions-to-common-problems-af4caf699e70/)以获得更多关于 React 钩子的信息。*

React `useState`钩子允许你在函数组件中拥有状态变量。您将初始状态传递给这个函数，它返回一个包含当前状态值(不一定是初始状态)的变量和另一个更新该值的函数。

本教程是 React 中`[useState Hook](https://blog.logrocket.com/usestate-vs-useref/)`的完整指南，相当于功能组件的`this.state` / `this.setSate`。

我们将详细介绍以下内容:

如果您刚刚开始使用 React Hooks，并且正在寻找可视化指南，请查看下面的视频教程:

 [https://www.youtube.com/embed/4qVNaohzDWU?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/4qVNaohzDWU?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

## React 中的类和功能组件

React 中有两种类型的组件:类组件和功能组件。

类组件是扩展内置 [`Component`](https://reactjs.org/docs/react-component.html) 类的 [ES6 类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)，可以有`[state](https://beta.reactjs.org/learn/state-a-components-memory)`和`[lifecycle](https://beta.reactjs.org/learn/lifecycle-of-reactive-effects)`方法:

```
import { Component } from 'react';
class Message extends Component {
  constructor(props) {
    super(props);
    this.state = {
      message: ''    
    };
  }

  componentDidMount() {
    /* ... */
  }

  render() {
    return <div>{this.state.message}</div>;
  }
}

```

> 注意:React 团队建议将组件定义为函数而不是类。这里有一份[移民指南](https://beta.reactjs.org/reference/react/Component#alternatives)。

函数组件是接受参数作为组件属性并返回有效 JSX 的函数，如下所示:

```
function Message(props) {
  return <div>{props.message}</div>
}
// Or as an arrow function
const Message = (props) =>  <div>{props.message}</div>

```

如您所见，没有状态或生命周期方法。但是，从 React v16.8 开始，我们可以使用钩子。 [React Hooks](https://blog.logrocket.com/react-reference-guide-hooks-api/) 是将状态变量添加到功能组件中的函数，并检测类的生命周期方法。他们倾向于从`use`开始。

## 什么是使用状态挂钩？

`useState`是 React 钩子，它允许你给一个[功能组件](https://blog.logrocket.com/fundamentals-functional-programming-react/)添加状态。它返回一个带有两个值的数组:当前状态和一个更新它的函数。钩子将一个初始状态值作为参数，并在调用`setter`函数时返回一个更新的状态值。它可以这样使用:

```
const [state, setState] = useState(initialValue);

```

这里，`initialValue`是您想要开始的值，`state`是可以在您的组件中使用的当前状态值。`setState`函数可以用来更新`state`，触发组件的重新渲染。

### `useState`能装什么？

在 React 中，`useState`可以存储任何类型的值，而类组件中的状态仅限于对象。这包括像`string`、`number`和`Boolean`这样的原始数据类型，以及像`array`、`object`和`function`这样的复杂数据类型。它甚至可以涵盖自定义数据类型，如类实例。

基本上，任何可以存储在 JavaScript 变量中的东西都可以存储在由`useState`管理的状态中。

### 更新`useState`中的对象和数组

切勿直接修改存储在`useState`中的对象或数组。相反，您应该创建对象或数组的新的更新版本，并用新版本调用`[setState](https://blog.logrocket.com/using-setstate-react-components/)`。例如:

```
// Objects
const [state, setState] = useState({ name: 'John', age: 30 });

const updateName = () => {
  setState({ ...state, name: 'Jane' });
};

const updateAge = () => {
  setState({ ...state, age: state.age + 1 });
};

// Arrays
const [array, setArray] = useState([1, 2, 3, 4, 5]);

const addItem = () => {
  setArray([...array, 6]);
};

const removeItem = () => {
  setArray(array.slice(0, array.length - 1));
};

```

## `React.useState`钩子是做什么的？

如前所述，`useState`使您能够向功能组件添加状态。在函数组件内部调用`React.useState`会生成一个与该组件相关联的状态。

尽管类中的状态总是一个对象，但是通过钩子，状态可以是任何类型。每种状态都有一个值:一个`object`、一个`array`、`Boolean`或任何你能想到的其他类型。

那么，什么时候应该使用`useState`钩呢？嗯，这对本地组件状态是有益的，但是更大的项目可能需要额外的[状态管理](https://blog.logrocket.com/guide-choosing-right-react-state-management-solution/)解决方案。

## 在反应中声明状态

`useState`是来自`react`的命名导出。要使用它，可以写`React.useState`或者写`useState`导入:

```
import React, { useState } from 'react';

```

可以在类中声明的`state`对象，它允许你声明多个状态变量，如下所示:

```
import React from 'react';

class Message extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      message: '',
      list: [],    
    };
  }
  /* ... */
}

```

然而，与`state`对象不同的是，`useState`钩子允许你一次只声明一个状态变量(任何类型的),就像这样:

```
import React, { useState } from 'react';

const Message= () => {
   const messageState = useState( '' );
   const listState = useState( [] );
}

```

所以，`useState`把状态变量的初始值作为参数，你可以直接传递，就像前面的例子。你也可以使用一个函数来[延迟初始化](https://blog.logrocket.com/lazy-loading-components-in-react-16-6-6cea535c0b52/)变量。当初始状态是一个昂贵的计算的结果时，这是有用的，如下所示:

```
const Message= () => {
   const messageState = useState( () => expensiveComputation() );
   /* ... */
}

```

初始值将仅在初始渲染时分配。如果它是一个函数，它将只在初始渲染时执行。在随后的渲染中(由于组件或父组件的状态变化)，将忽略`useState`钩子的参数，并将检索当前值。

记住这一点很重要，因为，例如，如果您想要基于组件接收的新属性更新状态，单独使用`useState`是行不通的。这是因为它的参数只在第一次使用时使用——而不是每次属性改变时都使用(查看[这里](https://stackoverflow.com/questions/54625831/how-to-sync-props-to-state-using-react-hook-setstate)了解正确的方法)。如下所示:

```
const Message= (props) => {
   const messageState = useState( props.message );
   /* ... */
}

```

但是，`useState`并不像前面的例子所暗示的那样，只返回一个变量。它返回一个数组，其中第一个元素是状态变量，第二个元素是更新变量值的函数:

```
const Message= () => {
   const messageState = useState( '' );
   const message = messageState[0]; // Contains ''
   const setMessage = messageState[1]; // It's a function
}

```

通常，您将使用数组析构来简化上面所示的代码，如下所示:

```
const Message= () => {
   const [message, setMessage]= useState( '' );
}

```

这样，您可以像使用任何其他变量一样在功能组件中使用状态变量:

```
const Message = () => {
  const [message, setMessage] = useState( '' );

  return (
    <p>
      <strong>{message}</strong>
    </p>
  );
};

```

但是，为什么`useState`会返回一个数组呢？这是因为，与对象相比，数组更加灵活和易于使用。如果该方法返回一个具有固定属性集的对象，您将无法轻松地分配自定义名称。

相反，您必须这样做(假设对象的属性是`state`和`setState`):

```
// Without using object destructuring
const messageState = useState( '' );
const message = messageState.state;
const setMessage = messageState

// Using object destructuring
const { state: message, setState: setMessage } = useState( '' );
const { state: list, setState: setList } = useState( [] );

```

## 使用 React 挂钩更新状态

`useState`返回的第二个元素是一个函数，它采用一个新值来更新状态变量。下面是一个使用`text`框在每次改变时更新状态变量的例子:

```
const Message = () => {
  const [message, setMessage] = useState( '' );

  return (
    <div>
      <input
         type="text"
         value={message}
         placeholder="Enter a message"
         onChange={e => setMessage(e.target.value)}
       />
      <p>
        <strong>{message}</strong>
      </p>
    </div>
  );
};

```

你可以在这里试试:

但是，这个更新函数不会立即更新值。相反，它将更新操作排入队列。然后，重新渲染组件后，`useState`的参数将被忽略，这个函数将返回最近的值。

当根据先前的值更新状态时，需要将一个函数传递给更新状态的 setter 函数。该函数接收以前的状态值作为参数，并返回新的状态值，如下所示:

你可以在这里试试:

```
const Message = () => {
  const [message, setMessage] = useState( '' );

  return (
    <div>
      <input
        type="text"
        value={message}
        placeholder="Enter some letters"
        onChange={e => {
          const val = e.target.value;
          setMessage(prev => prev + val)
        } }
      />
      <p>
        <strong>{message}</strong>
      </p>
    </div>
  );
};

```

用`useState`钩子实现一个对象作为状态变量

使用对象时，关于更新，您需要记住两件事:

## 关于第一点，如果使用与当前状态相同的值更新状态(React 使用`[Object.is()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is#Description)`进行比较)，React 不会触发重新渲染。

当处理对象时，很容易犯下面的错误:

下面是代码沙箱:

上面的例子没有创建一个新的对象，而是改变了现有的状态对象。反应过来，那是同一个物体。要使它工作，我们必须创建一个新对象，就像我们前面讨论的那样:

```
const Message = () => {
  const [messageObj, setMessage] = useState({ message: '' });

  return (
    <div>
      <input
        type="text"
        value={messageObj.message}
        placeholder="Enter a message"
        onChange={e => {
          messageObj.message = e.target.value;
          setMessage(messageObj); // Doesn't work
        }}
      />
      <p>
        <strong>{messageObj.message}</strong>
      </p>
  </div>
  );
};

```

这就引出了你需要记住的第二件重要的事情。当你更新一个状态变量时，与类组件中的`this.setState`不同，由`useState`返回的函数不会自动合并更新对象，而是替换它们。

按照前面的例子，如果我们向消息对象(`id`)添加另一个属性，如下所示:

而且我们只更新了`message`属性，就像上面的例子一样，React 会用`onChange`事件中使用的对象替换原来的`{ message: '', id: 1 }`状态对象，它只包含`message`属性:

```
onChange={e => {
  const newMessageObj = { message: e.target.value };
  setMessage(newMessageObj); // Now it works
}}

```

{ message:“输入的消息”} // id 属性丢失

您可以在这里看到`id`属性是如何丢失的:

```
const Message = () => {
  const [messageObj, setMessage] = useState({ message: '', id: 1 });

  return (
    <div>
      <input
        type="text"
        value={messageObj.message}
        placeholder="Enter a message"
        onChange={e => {
          const newMessageObj = { message: e.target.value };
          setMessage(newMessageObj); 
        }}
      />
      <p>
        <strong>{messageObj.id} : {messageObj.message}</strong>
      </p>
  </div>
  );
};

```

And we only update the `message` property like in the above example, React will replace the original `{ message: '', id: 1 }` state object with the object used in the `onChange` event, which only contains the `message` property:

您可以通过使用包含要替换的对象和对象扩展语法的函数参数来复制`setState()`的行为:

`...prevState`部分将获得对象的所有属性，`message: val`部分将覆盖`message`属性。这将与使用`[Object.assign()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)`产生相同的结果(记住创建一个新对象):

在此尝试:

但是，spread 语法简化了这种操作，它也适用于数组。基本上，当应用于数组时，spread 语法会删除括号，以便您可以用原始数组的值创建另一个数组:

```
onChange={e => {
  const val = e.target.value;
  setMessage(prevState => {
    return { ...prevState, message: val }
  });
}}

```

下面的例子展示了如何对数组使用`useState`:

```
onChange={e => {
  const val = e.target.value;
  setMessage(prevState => {
    return Object.assign({}, prevState, { message: val });
  });
}}

```

Try it here:

将 spread 语法应用于多维数组时必须小心，因为它不会像您预期的那样工作。这让我们想到了另一件事，那就是当把对象作为状态使用时要考虑的事情。

如何用钩子更新嵌套对象的状态

```
[
  ...['a', 'b', 'c'],
  'd'
]
// Is equivalent to
[
  'a', 'b', 'c',
  'd'
]

```

在 JavaScript 中，多维数组是数组中的数组，如下所示:

```
const MessageList = () => {
  const [message, setMessage] = useState("");
  const [messageList, setMessageList] = useState([]);

  return (
    <div>
      <input
        type="text"
        value={message}
        placeholder="Enter a message"
        onChange={e => {
          setMessage(e.target.value);
        }}
      />
      <input
        type="button"
        value="Add"
        onClick={e => {
          setMessageList([
            ...messageList,
            {
              // Use the current size as ID (needed to iterate the list later)
              id: messageList.length + 1,
              message: message
            }
          ]);
          setMessage(""); // Clear the text box
        }}
      />
      <ul>
        {messageList.map(m => (
          <li key={m.id}>{m.message}</li>
        ))}
      </ul>
    </div>
  );
};

```

你可以用它们把所有的状态变量放在一个地方。但是，出于这个目的，最好使用这样的嵌套对象:

但是，使用多维数组和嵌套对象时的问题是，`Object.assign`和 spread 语法将创建一个[浅拷贝](https://blog.logrocket.com/copy-objects-in-javascript-complete-guide/#shallow-copy:~:text=Structured%20cloning-,Shallow%20copy,-A%20shallow%20copy)，而不是[深拷贝](https://blog.logrocket.com/copy-objects-in-javascript-complete-guide/#shallow-copy:~:text=disrupt%20our%20program.-,Deep%20copy,-A%20deep%20copy)。

## 来自[展开语法文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax):

*在复制数组时，扩展语法有效地深入一级。因此，它可能不适合复制多维数组，如下例所示。(对于`[Object.assign()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)`和 spread 语法也是如此。)*

```
[
  ['value1','value2'],
  ['value3','value4']
]

```

这个[堆栈溢出查询](https://stackoverflow.com/questions/43421704/why-is-a-spread-element-unsuitable-for-copying-multidimensional-arrays)为上面的例子提供了很好的解释，但重要的一点是，当使用嵌套对象时，我们不能只使用 spread 语法来更新状态对象。例如，考虑以下状态对象:

```
{
  'row1' : {
    'key1' : 'value1',
    'key2' : 'value2'
  },
  'row2' : {
    'key3' : 'value3',
    'key4' : 'value4'
  }
}

```

以下代码片段显示了更新`text`字段的一些不正确方法:

为了正确更新`text`字段，我们必须将原始对象的整个字段/嵌套对象集合复制到一个新对象:

> 同样，下面是如何更新`state`对象的`author`字段:

```
let a = [[1], [2], [3]];
let b = [...a];

b.shift().shift(); //  1
//  Array 'a' is affected as well: [[], [2], [3]]

```

然而，这是假设`message`对象没有改变。如果它确实发生了变化，您必须以这种方式更新对象:

```
const [messageObj, setMessage] = useState({
  author: '',
  message: {
    id: 1,
    text: ''
  }
});

```

使用多个状态变量或一个状态对象

```
// Wrong
setMessage(prevState => ({
  ...prevState,
  text: 'My message'
}));

// Wrong
setMessage(prevState => ({
  ...prevState.message,
  text: 'My message'
}));

// Wrong
setMessage(prevState => ({
  ...prevState,
  message: {
    text: 'My message'
  }
}));

```

当使用多个字段或值作为应用程序的状态时，您可以选择使用多个状态变量来组织状态:

```
// Correct
setMessage(prevState => ({
  ...prevState,           // copy all other field/objects
  message: {              // recreate the object that contains the field to update
    ...prevState.message, // copy all the fields of the object
    text: 'My message'    // overwrite the value of the field to update
  }
}));

```

但是，在使用具有复杂结构的状态对象(嵌套对象)时，您必须小心。考虑这个例子:

```
// Correct
setMessage(prevState => ({
  author: 'Joe',          // overwrite the value of the field to update
  ...prevState.message    // copy all other field/objects
}));

```

如果必须更新嵌套在对象深处的特定字段，则必须复制所有其他对象以及包含该特定字段的对象的键值对:

```
// Correct
setMessage(prevState => ({
  author: 'Joe',          // update the value of the field
  message: {              // recreate the object that contains the field to update
    ...prevState.message, // copy all the fields of the object
    text: 'My message'    // overwrite the value of the field to update
  }
}));

```

## 在某些情况下，[克隆深度嵌套的对象](https://blog.logrocket.com/methods-for-deep-cloning-objects-in-javascript/)可能会很昂贵，因为 React 可能会重新呈现应用程序中依赖于甚至没有改变的字段的部分。

出于这个原因，你需要考虑的第一件事是试图展平你的状态对象。特别是，React 文档建议[根据哪些值会一起改变，将状态分成多个状态变量](https://reactjs.org/docs/hooks-faq.html#should-i-use-one-or-many-state-variables)。

```
const [id, setId] = useState(-1);
const [message, setMessage] = useState('');
const [author, setAuthor] = useState('');
Or an object state variable:
const [messageObj, setMessage] = useState({ 
  id: 1, 
  message: '', 
  author: '' 
});

```

如果这是不可能的，建议使用帮助您处理不可变对象的库，例如 [immutable.js 或 immer](https://blog.logrocket.com/immer-and-immutable-js-how-do-they-compare/) 。

```
const [messageObj, setMessage] = useState({
  input: {
    author: {
      id: -1,
      author: {
        fName:'',
        lName: ''
      }
    },
    message: {
      id: -1,
      text: '',
      date: now()
    }
  }
});

```

使用`useState`的规则

```
setMessage(prevState => ({
  input: {
    ...prevState.input,
    message: {
      ...prevState.input.message, 
      text: 'My message'
    }
  }
}));

```

`useState`遵守所有钩子都遵循的[规则](https://reactjs.org/docs/hooks-rules.html):

只调用顶层的钩子

仅从 React 函数调用挂钩

## 第二条规则很容易遵循。不要在类组件中使用`useState`:

或者常规 JavaScript 函数(不在功能组件内部调用):

*   您将得到一个[错误](https://reactjs.org/warnings/invalid-hook-call-warning.html)。第一条规则意味着，即使在函数组件内部，也不应该在循环、条件或嵌套函数中调用`useState`，因为 React 依赖于调用`useState`函数的顺序来获得特定状态变量的正确值。
*   在这方面，最常见的错误是将`useState`调用包装在[条件语句](https://blog.logrocket.com/react-conditional-rendering-9-methods/)中(它们不会一直被执行):

一个功能组件可以有许多对`useState`或其他钩子的调用。每个钩子都存储在一个列表中，有一个变量跟踪当前执行的钩子。

```
class App extends React.Component {
  render() {
    const [message, setMessage] = useState( '' );

    return (
      <p>
        <strong>{message}</strong>
      </p>
    );
  }
}

```

执行`useState`时，读取当前钩子的状态(或在第一次渲染时初始化)，然后，变量改为指向下一个钩子。这就是为什么总是保持钩子调用的顺序不变是很重要的。否则，可能会返回属于另一个状态变量的值。

```
function getState() {
  const messageState = useState( '' );
  return messageState;
}
const [message, setMessage] = getState();
const Message = () => {
 /* ... */
}

```

总的来说，下面是一个逐步实现的例子:

React 初始化钩子列表和跟踪当前钩子的变量

```
if (condition) { // Sometimes it will be executed, making the order of the useState calls change
  const [message, setMessage] = useState( '' );
  setMessage( aMessage );  
}
const [list, setList] = useState( [] );
setList( [1, 2, 3] );

```

React 首次调用您的组件

React 找到对`useState`的调用，创建一个新的钩子对象(带有初始状态)，改变当前钩子变量指向这个对象，将对象添加到钩子列表，返回带有初始状态的数组和更新它的函数

React 找到对`useState`的另一个调用，并重复上一步的动作，存储一个新的钩子对象并改变当前的钩子变量

1.  组件状态发生变化
2.  React 将状态更新操作(由`useState`返回的函数执行)发送到一个队列进行处理
3.  React 确定它需要重新呈现组件
4.  React 重置当前钩子变量并调用你的组件
5.  React 找到对`useState`的调用，但是这一次，因为在钩子列表的第一个位置已经有了一个钩子，所以它只改变当前的钩子变量并返回当前状态的数组，以及更新它的函数
6.  React 找到对`useState`的另一个调用，因为在第二个位置存在一个钩子，所以它再次改变当前的钩子变量并返回当前状态的数组和更新它的函数
7.  如果你喜欢读代码，参考`[ReactFiberHooks](https://github.com/facebook/react/blob/fd557d453d37eab29eca18f0507750ab2093669d/packages/react-reconciler/src/ReactFiberHooks.js)`类来学习钩子如何在幕后工作。
8.  `useState`对`useEffect`
9.  `useState`和`useEffect`是 React 中最重要的两个钩子。它们允许您管理功能组件中的状态和副作用。然而，它们有不同的用途，应该以不同的方式使用。
10.  正如我们前面所讨论的，`useState`是一个钩子，它允许您向功能组件添加状态。它返回一个带有两个值的数组:当前状态和一个用于更新状态的 setter 函数。

`useEffect`是一个钩子，用于管理功能组件中的副作用。副作用是在组件呈现之外影响组件的任何操作，如进行 API 调用或设置计时器。例如，考虑一个从 API 获取数据并将其显示在列表中的组件:

## 在这个例子中，`useEffect`钩子用于进行 API 调用，并在组件被渲染时更新`data`状态。该钩子将一个`callback`函数作为参数，该函数将在组件的每次渲染后执行。`useEffect`的第二个参数是一个依赖关系数组，它决定了效果应该何时运行。在这种情况下，空数组意味着当组件被挂载时效果将只运行一次。

`useState`用于管理组件中基于用户交互或其他事件需要更新和重新呈现的状态。另一方面，`useEffect`用于管理组件每次渲染后需要运行的副作用，或者在组件卸载时执行任何清理。理解这些钩子之间的区别，可以让您对何时使用每个钩子做出明智的决定，并构建健壮的、可伸缩的 React 应用程序。

理解`useReducer`挂钩

对于高级用例，您可以使用`[useReducer](https://blog.logrocket.com/react-reference-guide-hooks-api/#usereducer)`钩子作为`useState`的替代。当您有使用多个子值的复杂状态逻辑或者一个状态依赖于前一个状态时，这尤其有用。

```
const [data, setData] = useState([]);

useEffect(() => {
  fetch('https://api.example.com/data')
    .then(res => res.json())
    .then(data => setData(data));
}, []);

return (
  <ul>
    {data.map(item => (
      <li key={item.id}>{item.name}</li>
    ))}
  </ul>
);

```

以下是如何使用`useReducer`挂钩:

`useReducer`返回保存当前状态值和调度方法的数组。如果你有使用 Redux 的经验，这应该是一个熟悉的领域。使用`useState`，您调用状态更新器函数来更新状态，而使用`useReducer`，您调用`dispatch`函数并传递给它一个动作。例如，至少有一个`type`属性的对象，如下所示:

## 按照惯例，一个动作对象也可以有一个`payload`，例如`{action: 'increase', payload: 10}`。虽然遵循这种模式传递一个动作对象并不是绝对必要的，但这是一种非常常见的模式，由 [Redux](https://blog.logrocket.com/understanding-redux-tutorial-examples/) 推广。

结论

`useState`是一个钩子，允许你在功能组件中有状态变量。您将初始状态传递给这个函数，它返回一个包含当前状态值(不一定是初始状态)的变量和另一个更新该值的函数。

```
const [state, dispatch] = useReducer(reducer, initialArgument, init)

```

以下是一些需要记住的要点:

```
dispatch({type: 'increase'})

```

更新函数不会立即更新值

## 如果使用前一个值来更新状态，则必须传递一个接收前一个值并返回更新值的函数，例如`setMessage(previousVal => previousVal + currentVal)`

如果使用与当前状态相同的值来更新状态，React 不会触发重新渲染

与类组件中的`this.setState`不同，`useState`不会在状态更新时合并对象。它取代了它们

*   所有钩子都遵循同样的规则。特别要注意这些函数被调用的顺序(有一个 [ESLint 插件](https://www.npmjs.com/package/eslint-plugin-react-hooks)会帮助你执行这些规则)
*   If you use the previous value to update state, you must pass a function that receives the previous value and returns an updated value, for example, `setMessage(previousVal => previousVal + currentVal)`
*   If you use the same value as the current state to update the state, React won’t trigger a re-render
*   Unlike `this.setState` in class components, `useState` doesn’t merge objects when the state is updated. It replaces them
*   `useState` follows the same [rules](https://reactjs.org/docs/hooks-rules.html) that all Hooks do. In particular, pay attention to the order in which these functions are called (there’s an [ESLint plugin](https://www.npmjs.com/package/eslint-plugin-react-hooks) that will help you enforce these rules)