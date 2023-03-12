# React 重选:更好的内存化和缓存管理

> 原文：<https://blog.logrocket.com/react-re-reselect-better-memoization-cache-management/>

尽管 React 的组件具有快速的生命周期，但它们[会因过度的重新渲染](https://blog.logrocket.com/react-tracked-manage-state-prevent-excessive-re-rendering/)而受损，从而影响制作时间和整体性能。而在较小的应用程序中，这些不必要的重新渲染通常不会被注意到，因为 UI 随着组件树上更重的组件渲染而增长，开发人员和用户都会感受到负面的副作用。

为了解决这个问题，开发人员引入了第三方性能优化库，如 [re-reselect](https://github.com/toomuchdesign/re-reselect) ，这是一个流行库[re-select](https://github.com/reduxjs/reselect)的包装器，主要用于 Redux，通过编写记忆化选择器来提高性能。在本教程中，我们将通过创建一个简单的待办事项列表应用程序来探索重新选择。

要跟随本教程，您应该熟悉反应和重新选择。我们开始吧！

## 选择器

本质上，选择器是将 Redux 状态作为输入并返回从该状态派生的值的函数。在 React Redux 中，当调度`useSelector()`钩子的 reducer 动作时，它为每个挂载的组件运行，每次调度都要重新计算选择器，导致性能问题。

我们经常在选择函数中执行昂贵的计算。有些情况下，尽管调度了 reducer 的动作或者重新呈现了组件，但是我们的选择器看不到任何值的变化。理想情况下，选择器应该不经过任何计算就返回相同的值。

但是，在 React 中重新渲染一个组件后，所有内容都是用新的引用创建的。因此，选择器函数必须再次执行昂贵的计算。

## 构建我们的待办事项列表应用程序

让我们用代码来看一个例子；假设我们有一个待办事项列表应用程序，其中心状态在 reducer 中，文件夹结构如下:

![React To Do App Folder Structure](img/29bae91e2e8074543b74c77369f42a82.png)

我们将在项目中安装以下依赖项:

![React To Do App Dependencies](img/79875d3152d61561da1b8484a28635dc.png)

要创建应用程序商店并设置入口点元素节点`root`，请将以下代码添加到`index.js`:

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import { Provider } from 'react-redux';
import { createStore } from 'redux';
import rootReducer from './reducers/rootReducer';
import App from './App';
import * as serviceWorker from './serviceWorker';

const store = createStore(rootReducer)

ReactDOM.render(
<Provider store={store}><App /></Provider>, document.getElementById('root'));

serviceWorker.unregister();

```

接下来，我们将生成用于呈现应用程序布局的主要表示组件。将以下代码添加到`app.js`:

```
import React from 'react';
import TodoList from './views/TodoList';

function App() {

   return (
       <div className="App">
           <main className="container">
               <div className="divider"></div>
               <TodoList/>
           </main>
       </div>
   );
}

export default App;

```

`todoReducer.js`包含我们应用程序的中央状态，由选择器函数提取:

```
const initState = {
   todoList: [
       {
           id: 1,
           content: 'Play video game',
           weight:1
       }, {
           id: 2,
           content: 'Learn nodejs & python',
           weight:2

       }, {
           id: 3,
           content: 'Join meetup event',
           weight:3

       }
   ]
}

const todoReducer = (state = initState, action) => {
   switch (action.type) {
       case 'ADD_TODO':
           return {
               ...state,
               todoList: [
                   ...state.todoList,
                   action.payload
               ]
           }
       case 'REMOVE_TODO':
           return {
               ...state,
               todoList: action.payload
           }
       default:
           return state
   }
}

export default todoReducer;

```

在`rootReducer.js`中，我们将结合应用中的所有减速器:

```
import todoReducer from './todoReducer';
import {combineReducers} from 'redux';

//Combine all the sub reducers
const rootReducer = combineReducers({
   todos:todoReducer
})

export default rootReducer

```

我们有一个`TodoItem`来呈现减速器的`todo`状态。每个项目都有一个十字图标，该图标将调度一个事件，根据其 ID 删除该特定项目:

```
import React from 'react';
import {useSelector, useDispatch} from 'react-redux';

//Single todo item component
const TodoItem = (props) => {
   const todoList = useSelector(state => state.todos.todoList)
   const dispatch = useDispatch();

   const removeTodoItem = (todoId) => {
       let newTodoList = todoList.filter(item => item.id !== todoId);
       dispatch({type: 'REMOVE_TODO', payload: newTodoList})

   }

   return (
       <li className="collection-item" key={props.item.id}>{props.item.content}
           <span
               onClick={() => {
               removeTodoItem(props.item.id)
           }}
               className="secondary-content">
               <i className="remove-btn material-icons blue-text">clear</i>
           </span>
       </li>
   );
}

export default TodoItem;

```

最后，我们将创建一个名为`TodoList.js`的组件，在这里我们调用我们的选择器以及一个空节点列表，使得该组件对于 DOM 是可扩展的:

```
import React,{useState} from 'react';
import { useSelector,useDispatch } from 'react-redux';
import TodoItem from '../components/TodoItem';

const TodoList = () => {

 const todoList = useSelector(state => state.todos.todoList.filter(todo => todo.content.includes('a')));

 const dispatch = useDispatch();

 const [inputTodo,setInputTodo] =  useState('');

 const handleInput = (e)=>{
   setInputTodo(e.target.value);
 }

 //Handle onClick event
 const addNewTodo = ()=>{
   //Valid input value
        let newTodoObject={
           id: Math.random(),
           content:inputTodo,
           weight:Math.random(),

       }
        //Add new todo item into List with the action
       dispatch({type:'ADD_TODO',payload:newTodoObject});
       setInputTodo('');
         function returnBlankNodes(){
           let nodes = [];
                   for (let i=0;i<10000;i++) {
                   nodes.push(<p></p>)
   }
                   return nodes
 }
 }

   return (   
       <section id="section-todo">
       <h3 className="center-align white-text blue">Todo List</h3>
       {
           todoList.length>0?
           (<ul className="collection">
           {
             todoList.map(item => {
               return <TodoItem key={item.id} item={item} />
             })
           }
         </ul>):
         (<p className="center-align">You don't have anything to do! Awesome!</p>)
       }

       <div className="row">
       <p className="red-text err-msg col s12 center-align">
       </p>
       <div className="input-field col s10">
       <input onChange={handleInput} value={inputTodo} placeholder="Add todo..." id="todo-input" type="text" />
       <label htmlFor="todo-input" className="active">New Todo</label>
       </div>

       <button className="btn col s2 blue" onClick={addNewTodo} >Add</button>
       </div>
       {returnBlankNodes()}

     </section>
     );
}
export default TodoList;

```

我们从我们的商店中取出`todoList`,并在浏览器上呈现组件。然后由`todoList`选择器提取列表，并对其进行过滤。

目前，`todoList`会在每次渲染时重新计算。每当我们在文本字段中输入任何内容时，组件的状态都会改变。理想情况下，选择器应该只在我们对 Redux 存储进行更改时运行，例如，当我们添加一个新的待办事项时。

如果我们的 Redux store 中有数百个商品，那么`todoList`选择器将是 CPU 密集型的。在这些情况下，我们必须使用 memoization，只有当我们的存储中的`todoList`的值发生变化时，它才会运行选择器。

## 为什么要重新选择？

我们可以使用重选来创建记忆选择器。结果将被存储在内存中，只有在值不变的情况下才会被提取。每个选择器都有自己的内存，这意味着它不能缓存多个选择器。

### 重新选择示例

为了更好地理解这一点，让我们考虑下面的另一个例子:

```
import { createSelector } from 'reselect'

const getTodosSelector = state => state.todos.todoList

const getTodosWithLetter = createSelector(
   getTodosSelector,
   (state,Letter)=>Letter,
   (toDos,Letter) => toDos.filter(todo.content.includes(Letter))
)
const a=getTodosWithLetter(state,’a’) //cache created
const e=getTodosWithLetter(state,’e’)  //cache invalidated
const a_again=getTodosWithLetter(state,’a’) //cache created again.

a!===a_again.

```

在上面的代码片段中，我们使用 Reselect 创建了一个选择器，它接受一个字母表并基于它过滤结果。目前，所有英文字母共享一个选择器，但是，选择器只能缓存一个字母。

我们可以为每个字母创建选择器，但是这种方法很繁琐。正如我们在上面看到的，我们有`a`、`e`和`a_again`。首先，选择器为字母`a`创建一个缓存，但是在提供了不同的字母之后，缓存失效，并为字母`e`创建一个新的缓存。理想情况下，用不同的字母创建一个选择器应该会添加一个新的缓存，而不会覆盖以前的缓存。

### 重新选择示例

相反，让我们用重新选择重写上面的代码，如下所示:

```
import {createCachedSelector} from 're-reselect';

const getTodosSelector = state => state.todos.todoList

const getTodosWithLetter = createCachedSelector(
   getTodosSelector,
   (state,Letter)=>Letter,
   (toDos,Letter) => toDos.filter(todo.content.includes(Letter))
)(
 (state, Letter) => Letter // Cache selectors by Letter name
);

const a=getTodosWithLetter(state,'a') //cache created
const e=getTodosWithLetter(state,'e')  //new cache created
const a_again=getTodosWithLetter(state,'a') //fetched from cache
// memoization is preserved among different components

```

要使用上面的代码，我们必须简单地导入`createCachedSelector`。请注意，语法几乎类似于 Reselect。

此外，我们必须告诉选择器关于缓存的标准。在上面的代码中，我们用字母名称指定了它。不是销毁以前的缓存，而是为单个选择器创建一个新的缓存。

重选已经很有性能了，但是重选通过引入深度缓存管理更进了一步。在上面的例子中，我们创建了三个选择器。

当我们之前使用 Reselect 时，我们的过滤器运行了三次，因为新的缓存不断被重新创建。然而，使用重选，我们的过滤器只运行了两次。用`a_again` ，它从我们的缓存中取出数据对照字母。

## 将重新选择添加到我们的待办事项列表应用程序

为了将重新选择与 React Redux 集成，让我们创建一个名为`todoSelector.js`的新文件，并添加以下代码:

```
// todoSelector.js

import {createCachedSelector} from 're-reselect';

export const getTodosSelector = state => state.todos.todoList

export const  getTodosWithLetter = createCachedSelector(

    getTodosSelector,
    (state,Letter)=>Letter,
    (todoList,Letter) => todoList.filter(todo=>todo.content.includes(Letter))
 )(
  (state, Letter) => Letter // Cache selectors by Letter name
 );

```

在`TodoList.js`中导入，如下所示:

```
import { getTodosWithLetter } from './todoSelector';
const todoList = useSelector(state=>getTodosWithLetter(state,'a'));

```

## 输出

让我们在浏览器中运行该项目以查看结果。通过运行以下命令启动项目:

```
yarn start

```

瞧，我们已经成功地添加了一个带有重选功能的缓存选择器！

## 结论

在本文中，我们学习了如何创建具有深度管理缓存的选择器。我们构建了一个示例待办事项列表应用程序，其中包含一个在每次重新呈现时都会重新计算的组件。在集成了 re-reselect 之后，只有当我们在 Redux 存储中进行更改时，选择器才会运行。

如果您的项目中有昂贵的选择器，重新选择可以极大地提高您的应用程序的性能，特别是如果您有一个更大的应用程序。我希望你喜欢这个教程。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)