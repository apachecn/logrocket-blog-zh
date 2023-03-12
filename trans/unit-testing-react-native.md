# React Native - LogRocket 博客中的单元测试指南

> 原文：<https://blog.logrocket.com/unit-testing-react-native/>

React Native 是当今构建移动应用最常用的库之一。在本指南中，我们将探索 React 本机应用程序中的单元测试，包括好处、最佳实践、测试框架等等。

## 什么是单元测试？

单元测试是测试小的、孤立的代码片段的实践。[反应原生本身](https://blog.logrocket.com/testing-and-error-handling-patterns-in-next-js/)这样说:

> 单元测试覆盖了代码的最小部分，比如单独的函数或类。

所以单元测试包括测试函数、类、组件——我们项目中所有的小部分。我们可以单独测试其中的每一项，以确保它们正常工作。

考虑下面的例子，一个 JavaScript 项目中的一个`utils.js`文件的片段:

```
// utils.js
function toJSON() {...}
function convertToSLAs(args) {...}
function hourToMins(hour) {...}
export {toJSON, convertToSLAs, hourToMins}

```

该文件包含执行不同操作的函数。我们可以对这个文件中的每个函数进行单元测试，这意味着我们可以在不同的条件下独立测试每个函数，以确保它们都能按预期工作。

```
// test/_tests_.js
describe("toJSON tests", () => {
  it("should return object", () => {
    expect(toJSON(..)).toBe(...)
    ...
  });
  ...
});
describe("convertToSLAs tests", () => {
  it("should return true", () => {
    expect(convertToSLAs(...)).toBe(true)
    ...
  });
  ...
});
describe("hourtoMins", () => {
  it("should be truthy", () => {
      expect(hourToMins(...)).toBe(true)
      ...
  });
  ...
});

```

如您所见，我们为每个功能编写了测试。每个测试套件包含大量的测试，这些测试完全覆盖了功能的工作。

Jest、Mocha、Jasmine 等测试框架提供了详细的测试结果。任何不符合条件的测试都被标记为失败，表明它们需要进一步的检查；当然，那些通过的测试被标记为通过。

把一次失败的考试当成一件好事。当测试失败时，通常意味着事情不对劲；这让我们有机会在问题影响到我们的用户之前解决它。单元测试对于给出快速反馈和提供我们的功能将在生产中按预期工作的信心是很棒的。

## 结构化测试的最佳实践

测试应该是可读和可维护的。测试应该简短，一次测试一个条件。基于函数`it`，考虑以下测试:

```
it("should be truthy", () => {
  expect(hourToMins(3)).toBe(180);
  expect(hourToMins(5)).toBe(300);
  expect(hourToMins(null)).toBe(null);
});

```

上面的测试在一个测试中有多个条件，这使得它不明确。这三个条件是:

*   经过 3 小时后，`it`返回 180 分钟
*   经过 5 小时后，`it`返回 300 分钟
*   当`null`通过时，`it`返回`null`

相反，上述每个条件都应该是单独的测试:

```
it("should return 180", () => {
  expect(hourToMins(3)).toBe(180);
});
it("should return 300", () => {
  expect(hourToMins(5)).toBe(300);
});
it("should return null", () => {
  expect(hourToMins(null)).toBe(null);
});

```

这就清楚多了。

测试也应该尽可能具有描述性。在上面的测试中，描述并不清楚；描述应该足够清晰，以传达在某种条件下测试的内容。

React 本地文档[提供了一些最佳实践](https://reactnative.dev/docs/testing-overview#structuring-tests):

> 尽最大努力涵盖以下内容:
> 
> 1.  给定—一些前提条件。
> 2.  when——由您正在测试的函数执行的一些操作。
> 3.  然后——预期的结果。
> 
> 这也被称为 AAA(安排、动作、断言)。

因此，让我们的测试更具描述性:

```
it("given 3 hours, hourToMins should return 180", () => {
  expect(hourToMins(3)).toBe(180);
});
it("given null, hourToMins should return null", () => {
  expect(hourToMins(null)).toBe(null);
});

```

这就对了——清楚多了。

## 使用 Jest 和 Enzyme 的单元测试

我们可以使用许多测试框架来测试 React 本机应用程序，包括但不限于:

*   摩卡
*   玩笑
*   茉莉
*   可怕的
*   web 驱动程序

所有这些都是测试基于 JS 的应用程序的好选择。Jest 和 Enzyme 特别好，它们都被强烈推荐用于测试基于 React 的应用程序。但是因为 React Native 不是针对 web 应用的，所以 Jest 和 Enzyme 都没有 React Native 适配器。

幸运的是，有几个库可以帮助我们对 React 本机应用程序进行单元测试:

1.  [反应测试渲染器](https://reactjs.org/docs/test-renderer.html)
2.  [React 原生测试库](https://callstack.github.io/react-native-testing-library/)

Jest 提供测试环境，React 原生测试库提供测试 React 原生组件的轻量级解决方案。React 测试渲染器提供了一个 React 渲染器，我们可以使用它将 React 组件渲染为纯 JavaScript 对象，而不依赖于 DOM 或原生移动环境。

React Native 中的单元测试也涵盖了组件测试。组件是任何 React 原生应用的基本单元；每个组件呈现应用程序中自己的部分，用户直接与其输出进行交互。

我们在组件中测试两件事:

1.  **交互**，表示组件正确响应用户交互
2.  **输出**，意味着组件呈现正确的输出

## 设置我们的测试环境

从这里开始，我们将探索如何向我们的 React 本机应用程序添加测试功能。首先，让我们通过全局安装 expo-cli 工具来搭建一个新的 React 本地项目:

```
yarn global add expo-cli

```

接下来，我们将创建一个名为 react-native-test 的新 React Native 项目:

```
expo init react-native-test
cd react-native-test
yarn start # you can also use: expo start

```

这将启动一个开发服务器。使用 expo-cli 来搭建我们的 React 本地项目将自动为我们设置测试环境:

```
// package.json
...
  "scripts": {
    ...
    "test": "jest --watchAll"
  },
  "jest": {
    "preset": "jest-expo"
  }
  ...

```

一切都为我们准备好了。我们所要做的就是在我们的终端中运行`yarn test`来运行测试文件。

现在，我们将在 VS 代码中打开项目，打开集成终端，并运行`yarn test`。我们将看到 Jest 运行测试文件，并在观察模式下逗留，观察文件，以便在进入时重新运行文件。

```
PASS  components/__tests__/StyledText-test.js (5.018s)
  ✓ renders correctly (3108ms)
 › 1 snapshot written.
Snapshot Summary
 › 1 snapshot written from 1 test suite.
Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   1 written, 1 total
Time:        5.1s
Ran all test suites.
Watch Usage
 › Press f to run only failed tests.
 › Press o to only run tests related to changed files.
 › Press p to filter by a filename regex pattern.
 › Press t to filter by a test name regex pattern.
 › Press q to quit watch mode.
 › Press Enter to trigger a test run.

```

现在我们可以安装 React 本机测试库:

```
yarn add --dev @testing-library/react-native

```

就这样——我们准备好开始编写单元测试了。

## 测试组件

假设我们在`components`文件夹中有一个`Button.tsx`组件:

```
import React from "react";
import { Text, TouchableOpacity } from "react-native";
function Button({ styles, children }) {
  return (
    <TouchableOpacity style={[styles.button]}>
      <Text>{children}</Text>
    </TouchableOpacity>
  );
}
export default Button;

```

我们可以编写一个测试来确定`Button`组件是否正确呈现:

```
// __tests__
import * as React from "react";
import Button from "../Button";
import renderer from "react-test-renderer";
it(`renders correctly`, () => {
  const tree = renderer.create(<Button>Login</Button>);
  expect(tree).toMatchSnapshot();
});

```

现在让我们为`App`组件编写一个测试:

```
import * as React from "react";
import renderer from "react-test-renderer";
import App from "../App";
it(`renders correctly`, () => {
  const tree = renderer.create(<App />).toJSON();
  expect(tree.children.length).toBe(1);
});

```

`App`呈现一个只有一个孩子的 DOM，所以这个测试通过了。

### 嘲弄的

我们可以用一个模拟真实实现的虚拟版本来模拟实际的实现。模仿让测试速度更快，尤其是那些涉及互联网活动的测试。Jest 允许我们添加模拟实现——让我们看看它是如何实现的。

让我们测试一个表示组件:

```
function TodoItem({ todo, editTodoItem, deleteTodoItem }: TodoProps) {
  return (
    <>
      <View className="todoItem">
        <View className="todoItemText">
          <Text>{todo.todoText}</Text>
        </View>
        <View className="todoItemControls">
          <TouchableHighlight
            className="bg-default"
            onPress={() => editTodoItem(todo)}
          >
            <Text>Edit</Text>
          </TouchableHighlight>
          <TouchableHighlight
            className="bg-danger"
            onPress={() => deleteTodoItem(todo)}
          >
            <Text>Del</Text>
          </TouchableHighlight>
        </View>
      </View>
    </>
  );
}

```

`TodoItem`组件呈现一个`todo`。注意，`TodoItem`组件需要一个`todo`对象和当点击`Del`和`Edit`按钮时调用的函数。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

为了测试这一点，我们必须模拟函数道具。

```
it("delete and edit a todo", () => {
  const mockEditTodoItemFn = jest.fn();
  const mockDeleteTodoItemFn = jest.fn();
  const { getByText } = render(
    <TodoItem
      todo={{ todoText: "go to church" }}
      editTodoItem={mockEditTodoItemFn}
      deleteTodoItem={mockDeleteTodoItemFn}
    />
  );
  fireEvent.press(getByText("Edit"));
  fireEvent.press(getByText("Del"));
  expect(mockEditTodoItemFn).toBeCalledWith({
    1: { todoText: "go to church" },
  });
  expect(mockDeleteTodoItemFn).toBeCalledWith({
    1: { todoText: "go to church" },
  });
});

```

我们使用 Jest 的`fn()`来创建模拟功能`mockDeleteTodoItemFn`和`mockEditTodoItemFn`，然后我们将它们分配给`editTodoItem`和`deleteTodoItem`道具。当用户按下`Del`或`Edit`按钮时，模拟功能被调用。这确保了当真正的函数被传递时，按钮将在生产中工作。

我们也可以模拟返回值:

```
it("delete and edit a todo", () => {
  const mockEditTodoItemFn = jest.fn();
  mockEditTodoItemFn.mockReturnValue({ edited: true });
  const mockDeleteTodoItemFn = jest.fn();
  mockDeleteTodoItemFn.mockReturnValue({ deleted: true });
  const { getByText } = render(
    <TodoItem
      todo={{ todoText: "go to church" }}
      editTodoItem={mockEditTodoItemFn}
      deleteTodoItem={mockDeleteTodoItemFn}
    />
  );
  fireEvent.press(getByText("Edit"));
  fireEvent.press(getByText("Del"));
  expect(mockEditTodoItemFn.mock.results[0].value).toBe({
    edited: true,
  });
  expect(mockDeleteTodoItemFn.mock.results[0].value).toBe({
    deleted: true,
  });
});

```

我们在模拟函数上调用了`.mockReturnValue(...)`方法，并在函数被调用时返回了我们想要的值。这有助于我们测试函数返回值的类型。

属性`.mock.results`保存了我们调用的模拟函数的结果数组。由此，我们能够检索返回值，并根据预期值对它们进行测试。

我们也可以模仿 API 调用。我们将使用 Axios 来执行对我们的 API 的 HTTP 调用。下面是我们如何模仿 Axios 方法，如`get`、`post`、`delete`和`put`:

```
...
import axios from "axios";
export default function Home() {
  const [todos, setTodos] = useState([]);
  useEffect(async () => {
    const result = await axios.get("http://localhost:1337/todos");
    setTodos(result?.data);
  }, []);
  const addTodo = async (todoText) => {
    if (todoText && todoText.length > 0) {
      const result = await axios.post("http://localhost:1337/todos", {
        todoText: todoText,
      });
      setTodos([...todos, result?.data]);
    }
  };
  return (
    <View>
        {todos.map((todo,i) => {
            <Text key={i}>{todo}</Text>
        })}
    </View>
  );
}

```

我们有一个组件，加载并发布待办事项到服务器。为了测试这个组件，我们需要在 Axios 中模拟 HTTP 调用:

```
jest.mock("axios");
it("component can load todos, when mounted.", () => {
  const resp = {
    data: [{ todoText: "go to church" }, { todoText: "go to market" }],
  };
  axios.get.mockResolvedValue(resp);
  const { getByText } = render(<Home />);
  const todos = [getByText("go to church"), getByText("go to market")];
  expect(todos.length).toBe(2);
});

```

在这里，我们嘲笑了 Axios `get`方法。当`Home`组件通过`axios.get`加载待办事项时，我们的模拟被调用，并以模拟响应进行响应。更多关于嘲讽的内容可以在这里找到[。](https://jestjs.io/docs/en/mock-functions)

### 测试用户交互

我们还应该在 React 本地组件中测试用户交互——比如按下按钮、输入文本、滚动列表等交互。

React 本机测试库是测试 React 本机组件中用户交互的一个很好的资源。它提供了类似于`getByPlaceholder`、`getByText`和`getAllByText`的方法，我们可以使用这些方法从作为节点的渲染输出中获取元素或文本。然后，我们可以与渲染的节点进行交互，就好像我们实际上是在浏览器中进行交互一样。

让我们以之前的例子为例:

```
it("delete and edit a todo", () => {
  const mockEditTodoItemFn = jest.fn();
  mockEditTodoItemFn.mockReturnValue({ edited: true });
  const mockDeleteTodoItemFn = jest.fn();
  mockDeleteTodoItemFn.mockReturnValue({ deleted: true });
  const { getByText } = render(
    <TodoItem
      todo={{ todoText: "go to church" }}
      editTodoItem={mockEditTodoItemFn}
      deleteTodoItem={mockDeleteTodoItemFn}
    />
  );
  fireEvent.press(getByText("Edit"));
  fireEvent.press(getByText("Del"));
  expect(mockEditTodoItemFn.mock.results[0].value).toBe({
    edited: true,
  });
  expect(mockDeleteTodoItemFn.mock.results[0].value).toBe({
    deleted: true,
  });
});

```

`TodoItem`呈现了两个带有文本`Del`和`Edit`的按钮。我们使用`getByText`来访问这些按钮的节点，并使用`fireEvent.press(...)`方法来按下按钮，就像在实际的浏览器中一样。这将触发附加到按钮的`onPress`事件的事件处理程序。这会改变呈现的输出，我们可以测试单击按钮时会发生什么。

```
function SearchFruit() {
  const [result, setResult] = useState([]);
  const [searchItem, setSearchItem] = useState();
  const fruits = ["orange", "apple", "guava", "lime", "lemon"];
  useEffect(() => {
    if (searchtiem.length > 0) {
      setResult(fruits.includes(searchItem));
    }
  }, [searchItem]);
  return (
    <>
      <TextInput
        placeholder="Search fruits"
        onChangeText={(text) => setSearchItem(text)}
      />
      {result.map((fruit, i) => (
        <Text key={i}>{fruit}</Text>
      ))}
    </>
  );
}

```

上面，我们有一个从数组中搜索水果的组件，如果找到了就显示结果。我们可以编写测试来确保当用户与事件处理程序交互时，事件处理程序被触发，并呈现正确的结果。

```
it("search for a fruit when the fruit name is entered/typed", () => {
  const { getByPlaceholder } = render(<SearchFruit />);
  fireEvent.changeText(getByPlaceholder("Search fruits"), "guava");
  expect(getAllByText("guava")).toHaveLength(1);
});

```

上面，我们测试了当用户输入“番石榴”时，正确的结果会显示出来，因为我们在`fruits`数组中有番石榴。

`fireEvent.changeText()`方法改变输入框的文本，从而触发`onChange`事件处理程序。`getAllByText`获取数组中出现的文本“番石榴”。既然我们知道番石榴在`fruits`数组中，那么就应该渲染文本“番石榴”。

### 点火事件

为了触发 React 本机组件中的事件，我们使用 React 本机测试库的`fireEvent` API 中的方法。

#### `press`

这引发了元素上的`press`事件，该事件调用其附加的处理程序。

为了触发`press`事件，React 本地测试库导出`fireEvent`对象并调用`press()`方法。它接收要按下的元素的实例作为参数。

```
const onPressMock = jest.fn();
const { getByText } = render(
  <View>
    <TouchableOpacity onPress={onPressMock}>
      <Text>Press Me</Text>
    </TouchableOpacity>
  </View>
);
fireEvent.press(getByText("Press Me"));
expect(onPressMock).toHaveBeenCalled();

```

我们在`TouchableOpacity`元素中设置了一个`press`事件，该事件附带了一个模拟函数处理程序。我们使用`fireEvent.press()`来“按压”元素，因此调用被模仿的处理程序。我们希望这将调用被嘲笑的处理程序。

#### `changeText`

该事件在元素中输入文本数据，并在元素上触发`changeText`事件，该事件调用元素的处理程序。

```
const onChangeTextMock = jest.fn();
const { getByPlaceholder } = render(
  <View>
    <TextInput placeholder="Search fruits" onChangeText={onChangeTextMock} />
  </View>
);
fireEvent.changeText(getByPlaceholder("Search fruits"), "guava");
expect(onChangeTextMock).toHaveBeenCalled();

```

我们在`TextInput`框的`onChangeText`事件中设置了一个模拟处理程序。我们使用`fireEvent.changeText(...)`来启动事件。现在，我们期望当在`TextInput`盒子上调用`fireEvent.changeText(...)`时，被模仿的处理程序被调用。

更多关于 React 本地测试库中事件的信息可以在[这里](https://callstack.github.io/react-native-testing-library/docs/api/)找到

## 结论

当然，关于 React Native 中的单元测试，我们可以说的还有很多，但这应该能让您入门并开始运行。

我们已经看到 Jest 作为基于 React 的应用程序的测试框架是多么强大，尤其是在 React Native 中。我们还看到了 React Native Testing Library 有多棒，有太多的查询和事件方法。

如果你对这个话题有任何问题，或者如果你觉得我应该添加，纠正或删除什么，请随时评论，发电子邮件或给我发短信。谢谢！

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)