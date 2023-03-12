# 如何在 React Native 中使用 Redux Persist

> 原文：<https://blog.logrocket.com/use-redux-persist-react-native/>

有时，开发人员需要在本地为用户保存信息。例如，假设您构建了一个电子商务平台，用户无需登录即可将商品添加到购物车中。您需要为应用程序实现一种方法来记住用户添加到购物车中的商品。对于用户来说，关闭应用程序一段时间，却发现他们添加到购物车的商品已经不在那里，这是一种糟糕的体验。

另一种情况是，当用户登录你的应用程序后，他们关闭应用程序一分钟，然后被带到登录页面再次登录。

在本文中，我们将学习如何使用 [Redux Persist](https://github.com/rt2zz/redux-persist#readme) 来持久化 Redux 存储。首先，我们将构建一个小的待办应用程序，并用 Redux 设置它的状态管理。然后，我们将设置 Redux Persist 库，以便在应用程序关闭的情况下保存数据。我们开始吧！

## Redux 保持和反应本机

在 React 本地应用程序中，可以使用`AsyncStorage`在本地持久化数据。`AsyncStorage`是一个异步、持久、键值存储系统，对整个应用程序是全局的。

[Redux Persist 是一个用来无缝保存应用的 Redux 状态对象](https://blog.logrocket.com/persist-state-redux-persist-redux-toolkit-react/)到`AsyncStorage`的工具。在应用程序启动时，Redux Persist 检索这个持久状态，并将其保存回 Redux。

通常，Redux 会在启动时设置应用的初始状态。不久之后，Redux Persist 获取您的持久化状态，在 Redux Persist 团队称为再水合的过程中覆盖任何初始状态。

[Redux](https://redux.js.org/) 是一个开源的 JavaScript 库，用于管理和集中应用程序状态。它在一个不可变的状态对象中维护整个应用程序的状态，这个对象不能被直接改变。当发生变化时，会使用动作和缩减器创建一个新的对象。

## 设置项目

在本教程中，我们将演示在 [Expo 小吃平台](https://snack.expo.dev/)上实现 Redux Persist。

让我们创建一个名为`screens`的文件夹。在其中，创建一个名为`Todo.js`的文件，该文件将包含要添加到待办事项的输入字段，并显示添加的待办事项:

```
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';

export default function BooksListApp() {
  return (
    <View style={styles.container}>
        <Text style={styles.paragraph}>React Native ToDo App with Redux Persist </Text>
          <Text style={styles.title}> Add ToDo Here</Text>
          <TextInput
            style={styles.input}
            mode="outlined"
            label="Task"
            value={task}
             onChangeText={task => setTask(task)}
          />
          <Button title='Add' color="#841584"  />
      <FlatList
        data={todos}
        keyExtractor={(item) => item.id}
        renderItem={({item, index}) => {
          return (
              <Text style={styles.list}>{item.task}</Text>
          );
        }}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: Constants.statusBarHeight,
    backgroundColor: '#ecf0f1',
    padding: 10,
  },
  paragraph: {
    margin: 24,
    fontSize: 18,
    fontWeight: 'bold',
    textAlign: 'center',
  },
  title: {
    margin: 10,
    fontSize: 16,
    fontWeight: 'bold',
    textAlign: 'center',
  },
  list: {
    fontSize: 18,
    fontWeight: 'bold',
    textAlign: 'center',
  },
  input: {
    height: 40,
    margin: 12,
    borderWidth: 1,
    padding: 10,
  },
});

```

## 设置 Redux

要设置 Redux，首先用以下代码安装它:

```
npm install redux

```

### 创建动作创建者

使用 Redux，整个应用程序的状态由一个 JavaScript 对象管理。要对该对象进行更改，我们可以调度操作。

让我们为状态管理创建另一个名为`redux`的文件夹。在其中，让我们创建一个`action.js`文件来设置动作类型和动作创建者:

```
export const ADD_TODO = "ADD_TODO";

let todoId = 0;

export const addTodo = task => ({
  type: ADD_TODO,
  payload: {
    id: ++todoId,
    task
  }
});

```

动作类型`ADD_TODO`不言自明。当我们需要通过添加新的 todo 来更新状态时，我们调用它。

### 创建减速器

当一个动作已经被分派时，缩减器通过对状态对象进行改变来更新状态。reducer 是一个纯函数，它接受两个输入，即状态和动作，并且必须返回默认状态。

让我们在`redux`文件夹中创建另一个文件，并导入我们之前创建的动作类型。我们还将创建一个初始应用程序状态:

```
import { ADD_TODO } from "./action";

const initialState = {
  todos: []
};

const todoReducer = (state = initialState, action) => {
  switch (action.type) {
    case ADD_TODO: {
      const { id, task } = action.payload
      return {
        ...state,
        todos: [ ...state.todos, { id, task }]
      };
    }
    default:
      return state;
  }
}

export default todoReducer;

```

在上面的代码中，我们定义了一个 reducer 函数`todoReducer`，它将`initialState`作为第一个参数的默认值，将`action`作为第二个参数。

### 设置 Redux 商店

Redux 存储在应用程序级别保存状态，以便可以从任何组件访问它。Redux store 是一个将动作和 reducers 集合在一起的对象。

我们将使用 Redux 中的`createStore`方法来配置 Redux 存储。它将缩减器作为参数。

在`redux`文件夹中创建一个`store.js`文件，并初始化 Redux 存储，如下所示:

```
import { createStore } from "redux";
import todoReducer from './reducers';

export default createStore(todoReducer);

```

接下来，我们将通过将整个应用程序包装在一个名为`Provider`的高阶组件中，并将商店传递给它，从而使 Redux 商店在全球可用。

在`App.js`文件中，从 React Redux 导入`Provider`并将 app 包装在其中:

```
import store from './redux/store';
import { Provider } from 'react-redux';

const App = () => {
  return (
    <Provider store={store}>
      <MyTodo/>
    </Provider>
  );
}

export default App;

```

在上面的代码中，我们在 React 组件和 Redux 存储和状态之间建立了一个连接。因此，该应用程序中的任何组件都可以随时访问应用程序的状态。

### 调度操作

设置好 Redux 后，让我们编写一个函数，当用户添加待办事项时，该函数将调度一个动作。在`Todo.js`中，添加以下代码:

```
import { useSelector, useDispatch } from 'react-redux';
import { addTodo } from './redux/action';

const [task, setTask] = React.useState('');
  const  todoList  = useSelector(state => state.todos);
  const dispatch = useDispatch();

  const handleAddTodo = () => {
    dispatch(addTodo(task))
    setTask('')
  }

```

我们从 React Redux 导入了`useSelector`钩子来获取组件的状态，并导入了`useDispatch`钩子来分派动作。然后，当点击`Add`按钮时，我们调用该函数:

```
<Button title='Add' color="#841584" onPress={handleAddTodo} />

```

现在，当我们在输入字段中添加一个项目并单击按钮时，会调用`addTodo`动作创建器，它又会将`ADD_TODO`动作分派给 reducer，以便添加项目并更新应用程序的状态。

在这一点上，你会注意到减速器正在工作，我们有我们的待办事项应用程序启动和运行。但是，您会注意到，当我们关闭应用程序一段时间并再次打开它时，我们添加的所有项目都不见了，这表明 Redux 状态已被重置为初始状态。

我们可以使用 Redux Persist 持久化我们添加的项目。因此，即使当我们关闭应用程序并再次打开它时，我们也不会丢失我们的待办事项。

## 集成 Redux persist

让我们用下面的命令安装 Redux Persist 包和 React Native `AsyncStorage`包:

```
npm i redux-persist @react-native-async-storage/async-storage

```

我们将从 Redux Persist 导入`persistStore, persistReducer`,并添加以下配置:

```
import AsyncStorage from '@react-native-async-storage/async-storage';
import { persistStore, persistReducer } from 'redux-persist'
import storage from 'redux-persist/lib/storage' 

const persistConfig = {
  key: 'root',
  storage: AsyncStorage,
}

```

要为 React Persist 创建配置，您需要传递密钥和存储引擎，这是必需的。还有其他可选配置，如`whitelist`、`blacklist`、`version`、`stateReconciler`、`debug`。如果你不想保留你状态的一部分，你可以把它放在`blacklist`中。或者，`whitelist`定义了您希望保持的状态部分。

现在，我们将调用`persistReducer`，传入配置对象和我们的`todoReducer`。
我们还导出了`persistor`，这是一个由`persistStore`返回的对象，它包装了原始存储:

```
const persistedReducer = persistReducer(persistConfig, todoReducer)

export const store = createStore(persistedReducer)
export const persistor = persistStore(store)

```

在配置结束时，我们的`store.js`将看起来像下面的代码:

```
import AsyncStorage from '@react-native-async-storage/async-storage';
import { persistStore, persistReducer } from 'redux-persist'

import todoReducer from './reducers';
import { createStore } from 'redux'
import storage from 'redux-persist/lib/storage' 

const persistConfig = {
  key: 'root',
  storage: AsyncStorage,
}

const persistedReducer = persistReducer(persistConfig, todoReducer)

export const store = createStore(persistedReducer)
export const persistor = persistStore(store)

```

然后，我们通过导入`PersistGate`并在其中包装应用程序来更新`App.js`:

```
import * as React from 'react';
import { Text } from 'react-native';
import { PersistGate } from 'redux-persist/integration/react'
import MyTodo from './Todo';

import {store, persistor} from './redux/store';
import { Provider } from 'react-redux';

const App = () => {
  return (
    <Provider store={store}>
    <PersistGate loading={<Text>Loading...</Text>} persistor={persistor}>
      <MyTodo/>
    </PersistGate>
    </Provider>
  );
}

export default App;

```

Redux Persist 在`AsyncStorage`中为我们持久化 Redux 存储。即使我们离开应用程序，稍后再回来，它也会从`AsyncStorage`获取数据，并用它初始化 Redux store。

## 结论

在本文中，我们回顾了 Redux Persist 是什么以及如何在 React 本机应用程序中设置它。当您需要在没有数据库的情况下持久化登录会话和其他信息时，Redux Persist 非常有用。它允许用户在不丢失数据的情况下退出和返回应用程序，从而极大地改善了用户体验。我希望你喜欢这篇文章，如果你有任何问题，一定要留下评论。编码快乐！

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)