# 如何测试 React 钩子

> 原文：<https://blog.logrocket.com/test-react-hooks/>

***编者按*** :这篇文章最后一次更新是在 2022 年 6 月 30 日，目的是纠正之前版本中的不一致之处，并包含更多关于测试 React 挂钩的最新信息。

React 的 16.8.0 版本发布意味着 [React 钩子](https://blog.logrocket.com/react-hooks-context-redux-state-management/)特性的稳定发布。React Hooks 于 2018 年推出，并获得了 React 生态系统的好评。它本质上是一种创建具有状态等特性的组件的方法，而不需要类组件。

前端测试的重要性怎么强调都不为过，因为每个团队和公司都采用测试驱动的开发来给他们的软件注入信心。本文的目标是提供一个实用的指南，指导你如何使用 React 测试库、Jest 和 Enzyme 等工具来测试 React 钩子。

### 内容

## 什么是 React 钩子？

挂钩特性是一个受欢迎的改变，因为它解决了 React 开发人员多年来面临的许多问题。其中一个问题是 React 不支持`class`组件之间的可重用状态逻辑。这有时会导致巨大的组件、构造函数中重复的逻辑以及[生命周期方法](https://blog.logrocket.com/using-react-useeffect-hook-lifecycle-methods/)。

不可避免地，这迫使我们使用一些复杂的模式，如渲染道具和高阶组件，这可能导致复杂的代码库。

钩子旨在通过使您能够编写可重用的组件来访问状态、生命周期方法和引用，从而解决所有这些问题。

## 如何使用 React 挂钩构建 React 应用程序

在我们继续看如何为 React 钩子编写测试之前，让我们看看如何使用钩子构建一个 React 应用程序。我们将建立一个应用程序，显示 2018 年 F1 比赛和每年的获胜者。

在 CodeSandbox 可以看到整个应用程序并与之交互。

在上面的应用中，我们使用了`useState`和`useEffect`钩子。如果您导航到`index.js`文件，在`App`函数中，您会看到一个使用了`useState`的实例:

```
// Set the list of races to an empty array
let [races, setRaces] = useState([]);
// Set the winner for a particular year
let [winner, setWinner] = useState();
useState returns a pair of values, that is the current state value and a function that lets you update it. It can be initialized with any type of value (string, array e.t.c) as opposed to state in classes where it had to be an object.

```

这里使用的另一个钩子是`useEffect`钩子。`useEffect`钩子增加了从一个函数组件执行副作用的能力；它本质上允许您执行通常在`componentDidMount`、`componentDidUpdate`和`componentWillUnmount`生命周期中执行的操作:

```
// On initial render of component, fetch data from API.
useEffect(() => {
  fetch(`https://ergast.com/api/f1/2018/results/1.json`)
    .then(response => response.json())
    .then(data => {
      setRaces(data.MRData.RaceTable.Races);
    });
  fetch(`https://ergast.com/api/f1/2018/driverStandings.json`)
    .then(response => response.json())
    .then(data => {
      let raceWinner = data.MRData.StandingsTable.StandingsLists[0].DriverStandings[0].Driver.familyName + " " + data.MRData.StandingsTable.StandingsLists[0].DriverStandings[0].Driver.givenName;
      setWinner(raceWinner);
    });
}, []);

```

在应用程序中，我们使用`useEffect`钩子进行 API 调用并获取 F1 比赛数据，然后使用`setRaces`和`setWinner`函数将它们各自的值设置为状态。

这只是一个如何结合使用钩子来构建应用程序的例子。我们使用`useEffect`钩子从某个源获取数据，使用`useState`设置进入状态的数据。

## 如何测试 React 钩子

## 用 Jest 和酶测试 React 钩子

[Jest](https://jestjs.io/) 和[酵素](https://airbnb.io/enzyme/)是用来测试 React apps 的工具。Jest 是用于测试 JavaScript 应用程序的 JavaScript 测试框架，Enzyme 是 React 的 JavaScript 测试实用程序，它使断言、操作和遍历 React 组件的输出变得更加容易。

让我们看看如何用它们来测试 React 钩子。

首先，让我们使用 Create React App 创建一个项目，如下所示:

```
npx create-react-app my-app
cd my-app

```

接下来，我们将安装酶测试库和 React 适配器，如下所示:

```
npm i --save-dev enzyme enzyme-adapter-react-16

```

现在，在`src`文件夹中创建一个名为`setupTests.js`的文件。添加以下代码片段来配置 Enzyme 的适配器:

```
import Enzyme from "enzyme";
import Adapter from "enzyme-adapter-react-16";
Enzyme.configure({ adapter: new Adapter() });

```

在我们的测试执行之前，`setupTests.js`文件中的代码被执行。

### 用酶测试`useState`挂钩

为了测试`useState`钩子，让我们用下面的代码更新`app.js`文件:

```
import React from "react";
const App= () => {
  const [name, setName] = React.useState("");

  return (
      <form>
        <div className="row">
          <div className="col-md-6">
            <input
              type="text"
              placeholder="Enter your name"
              className="input"
              onChange={(e) => {
                setName(e.target.value);
              }}
            />
          </div>
        </div>
        <div className="row">
          <div className="col-md-6">
            <button
              type="submit"
              className="btn btn-primary"
            >
              Add Name
            </button>
          </div>
        </div>
      </form>
  );
};
export default App;

```

这里我们有一个基本的输入字段和一个按钮元素。注意我们是如何使用`React.useState()`而不是`useState()`的。我们需要这个来支持模拟酶测试中的`useState`钩:

```
import React from "react";
import { shallow } from "enzyme";
import App from "./App";

const setState = jest.fn();
const useStateSpy = jest.spyOn(React, "useState");
useStateSpy.mockImplementation((initialState) => [initialState, setState]);
const wrapper = shallow(<App />);

```

这里，我们已经成功模拟了`useState`钩子，我们可以继续测试输入变化的状态更新，如下所示:

```
it("should update state on input change", () => {
  const newInputValue = "React is Awesome";
  wrapper
    .find(".input")
    .simulate("change", { target: { value: newInputValue } });
  expect(setState).toHaveBeenCalledWith(newInputValue);
});

```

Enzyme 支持 React 挂钩，尽管由于 React 的浅层渲染器中的上游问题,`.shallow()`中有一些缺点。使用 React 浅层渲染器，`useEffect()`和`useLayoutEffect()`不会被调用。

## 用 React 测试库测试 React 挂钩

[React 测试库](https://github.com/testing-library/react-testing-library)是一个测试 React 组件的轻量级解决方案。它在`react-dom`和`react-dom/test-utils`上扩展，以提供照明实用功能。它鼓励您编写与 React 组件的使用非常相似的测试。

React Testing Library 的主要目标是通过以用户使用组件的方式测试组件来增强开发人员对测试的信心。它已经安装在 CRA，并且是 React 的默认测试库。

让我们看一个使用 React 测试库为钩子编写测试的例子。

在上面的应用程序中，使用了三种类型的钩子，`useState`、`useEffect`和`useRef`，我们将为它们编写测试。

对于`useRef`钩子实现，我们本质上是使用`useRef`创建一个`ref`实例，并将其设置为一个输入字段，这意味着现在可以通过 ref 访问输入的值。

`useEffect`钩子的实现本质上是将`name`状态的值设置为`localStorage`。

让我们继续为上面的所有实现编写测试。我们将为以下内容编写测试:

*   初始`count`状态为零
*   `increment`和`decrement`按钮工作
*   通过输入字段提交名称会改变`name`状态的值
*   `name`状态保存在`localStorage`中

导航到`__tests__`文件夹，查看包含测试套件和下面代码导入行的`hooktest.js`文件:

```
// hooktest.js
import { render, fireEvent, getByTestId} from "react-testing-library";

```

将有助于渲染我们的组件。它呈现在一个附加到`document.body`的容器中。`getByTestId`通过`data-TestId`获取一个 DOM 元素。`fireEvent`用来“开除”DOM 事件。它在文档上附加了一个事件处理程序，并通过事件委托处理一些 DOM 事件，例如，单击一个按钮。

接下来，在`hooktest.js`文件中添加下面的测试套件:

```
// hooktest.js

it("App loads with initial state of 0", () => {
  const { container } = render(<App />);
  const countValue = getByTestId(container, "countvalue");
  expect(countValue.textContent).toBe("0");
});

```

该测试通过首先用`getByTestId`助手获取元素来检查初始计数状态是否设置为零。然后使用`expect()`和`toBe()`功能检查内容是否为零。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

接下来，我们将编写测试来查看递增和递减按钮是否有效:

```
// hooktest.js

it("Increment and decrement buttons work", () => {
  const { container } = render(<App />);
  const countValue = getByTestId(container, "countvalue");
  const increment = getByTestId(container, "incrementButton");
  const decrement = getByTestId(container, "decrementButton");
  expect(countValue.textContent).toBe("0");
  fireEvent.click(increment);
  expect(countValue.textContent).toBe("1");
  fireEvent.click(decrement);
  expect(countValue.textContent).toBe("0");
});

```

在上面的测试中，我们检查了如果点击了`onButton`，状态被设置为 1，当点击了`offButton`时，状态被设置为零。

下一步，我们将编写一个测试来断言通过输入字段提交一个名字是否真的改变了`name`状态的值，并且它被成功地保存到了`localStorage`:

```
// hooktest.js

it("Submitting a name via the input field changes the name state value", () => {
  const { container, rerender } = render(<App />);
  const nameValue = getByTestId(container, "namevalue");
  const inputName = getByTestId(container, "inputName");
  const submitButton = getByTestId(container, "submitRefButton");
  const newName = "Ben";
  fireEvent.change(inputName, { target: { value: newName } });
  fireEvent.click(submitButton);
  expect(nameValue.textContent).toEqual(newName);
  rerender(<App />);
  expect(window.localStorage.getItem("name")).toBe(newName);
});

```

在上面的测试断言中，`fireEvent.change`方法用于在`input`字段中输入一个值，然后单击提交按钮。

然后测试检查按钮被点击后 ref 的值是否等于`newName`。最后，使用`rerender`方法，模拟应用程序的重新加载，并检查之前设置的名称是否存储在`localStorage`中。

## 测试异步挂钩函数

为了实现异步钩子的测试，我们可以使用 React 钩子测试库中的`waitForNextUpdate`函数。

异步方法返回承诺，所以一定要用`await`或`.then`来调用它们。React Hooks 测试库提供了许多用于测试异步钩子的异步方法，包括:

*   `waitFor`
*   `waitForValueToChange`
*   `waitForNextUpdate`

我们将要测试的异步钩子接受一个 API URL 作为参数，用 Axios 发出一个异步请求，并返回一个响应对象。

在`src`文件夹中创建一个`useFetchData.js`文件，并添加以下内容:

```
import React from 'react';
import axios from 'axios';

export default (endpoint) => {
  const [data, setData] = React.useState({
    state: "",
    error: '',
    data: [],
  });

  const fetchData = () => {
    setData({
      state: "LOADING",
      error: '',
      data: [],
    });
    axios(endpoint)
      .then((resp) => resp.json())
      .then((respData) => {
        setData({
          ...data,
          state: "SUCCESS",
          data: respData,
        });
      })
      .catch((err) =>
        setData({
          ...data,
          state: "ERROR",
          error: 'Fetch failed',
        })
      );
  };

  React.useEffect(() => {
    fetchData();
  }, []);

  return data;
};

```

现在，让我们测试我们的异步钩子。

使用以下命令安装 React 挂钩的测试库:

```
Yarn add @testing-library/react-hooks

```

现在，在`src`文件夹中创建一个`useFetchData.test.js`文件，并添加以下内容:

```
import React from 'react';
import { renderHook } from '@testing-library/react-hooks';
import useFetchData from './useFetchData.js';
import axios from 'axios';

jest.mock('axios');

const useApiMockData = [{ id: 1,
name: "Leanne Graham", }, { id: 2,
name: "Ervin Howell" }];

```

这里，我们使用一个`jest.mock`方法模拟 Axios 来测试异步动作。此外，我们还模拟了测试的典型响应数据。

接下来，我们将为成功的 API 请求和失败的 API 请求编写一个测试:

```
describe('useFetchData Hook', () => {
  it('initial and success state', async () => {
    axios.get.mockResolvedValue(useApiMockData);
    const { result, waitForNextUpdate } = renderHook(() =>
      useFetchData('lorem')
    );
    expect(result.current).toMatchObject({
      data: [],
      error: '',
      state: 'LOADING',
    });

    await waitForNextUpdate();

    expect(result.current).toMatchObject({
      data: useApiMockData,
      error: '',
      state: 'SUCCESS',
    });
  });

  it('error state', async () => {
    const errorMessage = 'Network Error';
    axios.get.mockImplementationOnce(() =>
      Promise.reject(new Error(errorMessage))
    );

    const { result, waitForNextUpdate } = renderHook(() =>
      useFetchData('lorem')
    );

    await waitForNextUpdate();

    expect(result.current).toMatchObject({
      data: [],
      error: 'Fetch failed',
      state: 'ERROR',
    });
  });
});

```

注意`waitForNextUpdate()`。它模拟一个异步更新的状态更新，并返回一个承诺，该承诺在钩子下次呈现时解决。

## 结论

在本文中，我们看到了如何使用 react-testing-library 为 React 挂钩和 React 组件编写测试。我们还学习了如何使用 React 钩子的简短入门。想了解更多？看看这篇关于如何避免 React 钩子常见错误的文章。如果你有任何问题或意见，可以在下面分享。