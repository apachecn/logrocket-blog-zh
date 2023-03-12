# 通过 XState 和 React - LogRocket 博客使用状态机

> 原文：<https://blog.logrocket.com/using-state-machines-with-xstate-and-react/>

当我们的应用程序变得更大时，我们需要一种方法来保持处理应用程序状态的复杂性。状态是由我们正在运行的应用程序操作和反映的数据。在 React 中有许多方法来处理应用程序的状态。一些最流行的状态管理库是 [Redux](https://redux.js.org/) 、[上下文 API](https://reactjs.org/docs/context.html) 、 [React Query](https://react-query.tanstack.com/) 、反冲和 [XState](https://xstate.js.org/docs/) 。

在本文中，我们将使用 XState，因为它是在任何 JavaScript 应用程序中处理状态的最简单和最有效的方法之一，而不是与框架无关的。当使用 XState 时，我们可以很容易地利用状态机来管理和设计全局或组件状态。

## 什么是有限状态机？

[有限状态机](https://brilliant.org/wiki/finite-state-machines/)是一种计算的数学模型，最初开发于 20 世纪 40 年代初，几十年来一直用于为各种技术构建硬件和软件。它帮助我们减少可能状态的数量，并控制移动状态的转换，从而产生可预测和可靠的应用程序。

使用状态机的另一个好处是，在我们构建之前和/或之后，他们还有一个可视化工具来查看图中的状态，称为 [XState Visualizer](https://xstate.js.org/viz/) 。

## 先决条件

*   熟悉 JavaScript 并对 ES6 语法有基本了解。
*   安装在计算机上的 Node.js
*   [NPM 安装了](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)

在本教程中，我们将构建一个简单的书店，并实现构建该商店所需的所有必要状态。这个存储将涉及构建一般应用程序所需的所有 CRUD(创建、读取、更新、删除)功能。

对于 API 端点，我们将利用 Airtable 提供的特性，用它为我们的应用程序提供数据，并用 [Tailwind CSS](https://tailwindcss.com/) 来设计我们的应用程序。

### 添加图书状态机

在我们的 XState 可视化工具中，我们将首先编写一个简单的状态机来为我们的书店创建一本新书:

```
export const addbookMachine = {
 id: 'addBooks',
 initial: 'addNew',
 states: {
   addNew: {},
   adding: {
     invoke: {
       id: 'addingBook',
       src: addingBook,
       onDone: {
         target: 'success',
         actions: assign({ fields: (_context, event) => event.data }),
       },
       onError: {
         target: 'failed',
         actions: assign({ error: (_context, event) => event.data }),
       },
     },
   },
   success: {},
   failed: {},
 },
};

```

可视化这个简单的状态机，我们可以看到什么时候我们能够填充`add a book`表单，它将它的状态从`addNew`转换到`adding`，并且，基于那个特定的承诺，我们改变到成功和错误。这是一个简单的状态机，当函数调用时，它可以根据成功和失败来转换状态。

![XState Visualizer](img/9f31b2c793ff5c2031cc7fbfe9001fef.png)

XState Visualizer generated from [XState Visualizer](https://xstate.js.org/viz/).

我们还将创建一个函数来确定我们的调用是成功还是失败。我们之前已经在我们的状态机中把它命名为`invoke`里面的`addingBook`。

```
const addingBook = (context, event) =>
 new Promise(async (resolve, reject) => {
   let result = await addTheBooks(context, event);
   if (result.status === 200) {
     resolve(result);
   } else {
     reject('books');
   }
 });

```

这个承诺解决或拒绝我们提供的等待其值的函数调用。如果响应是`200`，我们就去`onDone`；否则，我们将过渡到`OnError`状态。

让我们也连接到我们的 Airtable API 来进行这个调用。我们将创建一个新文件夹，并将其命名为 **API** ，在这里我们将放置应用程序所需的所有 API 调用。在里面，我们将创建一个新文件，并将其命名为 **addBook** 。

```
const addTheBooks = async (_context, event) => {
 const { Name, Author, Published, Currency, Category } = event;
 const formater = {
   records: [
     {
       fields: { Name, Author, Published, Currency, Category },
     },
   ],
 };
 const res = await fetch(process.env.REACT_APP_BASE_URL + 'Books', {
   method: 'POST',
   headers: new Headers({
     Authorization: process.env.REACT_APP_API_KEY,
     'Content-Type': 'application/json',
   }),
   body: JSON.stringify(formater),
 });
 return res;
};

export default addTheBooks;

```

在这里，我们从我们的 Airtable 调用了一个 API 端点来`POST`我们添加的书籍。我们还添加了一个格式化常量来构造 API 采用的数据格式。表单字段将作为 JSON 有效负载发送给 formatter 常量，该常量将字段值作为键值对。

最后，为了在书店中添加书籍，我们创建了一个组件来添加书籍。这与状态机通信，以便在应用程序的指定状态中流动。

```
import React, { useContext, useRef } from 'react';
import { Link } from 'react-router-dom';
import { MachineContext } from '../state/index';

// eslint-disable-next-line
function Addbook({}) {
 const book = useRef();
 const authorName = useRef();
 const date = useRef();
 const price = useRef();
 const category = useRef();
 const [machine, sendToMachine] = useContext(MachineContext);

 const addAbook = async () => {
   const Name = book.current.value;
   const Author = authorName.current.value;
   const Published = date.current.value;
   const Currency = parseFloat(price.current.value);
   const Category = category.current.value;
   sendToMachine('ADD_BOOK', { Name, Author, Published, Currency, Category });
   book.current.value = '';
   authorName.current.value = '';
   date.current.value = '';
   price.current.value = '';
   category.current.value = '';
 };

 return (
   <div>
     <div className="flex items-left justify-left">
       <div className="max-w-md w-full">
         <div>
           <div>
             <Link
               to="/"
               className="text-indigo-500 inline-flex items-center font-bold"
             >
               <svg
                 className="w-6 h-6 mr-2"
                 fill="none"
                 stroke="currentColor"
                 viewBox="0 0 24 24"

               >
                 <path
                   strokeLinecap="round"
                   strokeLinejoin="round"
                   strokeWidth="2"
                   d="M7 16l-4-4m0 0l4-4m-4 4h18"
                 ></path>
               </svg>
               Go back
             </Link>
           </div>
           <h4 className="text-left text-2xl font-bold text-gray-500">
             Fill details to add new book.
           </h4>
         </div>
         <form className="mt-10 space-y-6">
           <input type="hidden" name="remember" value="true" />
           <div className="rounded-md shadow-sm">
             <div className="mb-3">
               <label htmlFor="book-name" className="sr-only">
                 Book Name
               </label>
               <input
                 id="book-name"
                 name="book"
                 type="text"
                 autoComplete="bookname"
                 className="w-full bg-white rounded border border-gray-300 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-200 text-base outline-none text-gray-700 py-1 px-3 leading-8 transition-colors duration-200 ease-in-out"
                 placeholder="book name"
                 ref={book}
               />
             </div>
             <div className="mb-3">
               <label htmlFor="author-name" className="sr-only">
                 Author Name
               </label>
               <input
                 id="author-name"
                 name="author"
                 type="text"
                 autoComplete="authorname"
                 className="w-full bg-white rounded border border-gray-300 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-200 text-base outline-none text-gray-700 py-1 px-3 leading-8 transition-colors duration-200 ease-in-out"
                 placeholder="author name"
                 ref={authorName}
               />
             </div>
             <div className="mb-3">
               <label htmlFor="published-date" className="sr-only">
                 Published date
               </label>
               <input
                 id="published-date"
                 name="date"
                 type="date"
                 autoComplete="published date"
                 className="w-full bg-white rounded border border-gray-300 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-200 text-base outline-none text-gray-700 py-1 px-3 leading-8 transition-colors duration-200 ease-in-out"
                 placeholder="published date"
                 ref={date}
               />
             </div>
             <div className="mb-3">
               <label htmlFor="currency" className="sr-only">
                 Price
               </label>
               <input
                 id="currency"
                 name="currency"
                 type="text"
                 autoComplete="currencyname"
                 className="w-full bg-white rounded border border-gray-300 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-200 text-base outline-none text-gray-700 py-1 px-3 leading-8 transition-colors duration-200 ease-in-out"
                 placeholder="price of book"
                 ref={price}
               />
             </div>

             <div className="relative">
               <select
                 className="rounded border appearance-none border-gray-300 py-2 w-full placeholder-gray-500 text-gray-900 focus:outline-none focus:ring-2 focus:ring-indigo-200 focus:border-indigo-500 text-base pl-3 pr-10 sm:text-sm"
                 ref={category}
               >
                 <option default value="select one">
                   select one
                 </option>
                 <option>Psychology</option>
                 <option>Related</option>
                 <option>Others</option>
                 <option>Research</option>
                 <option>Testing</option>
                 <option>Usability</option>
               </select>
               <span className="absolute right-0 top-0 h-full w-10 text-center text-gray-600 pointer-events-none flex items-center justify-center">
                 <svg
                   fill="none"
                   stroke="currentColor"
                   strokeLinecap="round"
                   strokeLinejoin="round"
                   strokeWidth="2"
                   className="w-4 h-4"
                   viewBox="0 0 24 24"
                 >
                   <path d="M6 9l6 6 6-6"></path>
                 </svg>
               </span>
             </div>
           </div>

           <div className="inline-flex rounded-md shadow">
             <input
               type="button"
               value="Add My Book"
               className="inline-flex items-center justify-center px-5 py-3 border border-transparent text-base font-medium rounded-md bg-indigo-600 text-white hover:bg-indigo-700"
               onClick={addAbook}
             ></input>
           </div>

           {machine.matches('addbookMachine.adding') && (
             <span>
               <svg

                 width="24"
                 height="24"
                 viewBox="0 0 24 24"
                 fill="none"
                 stroke="currentColor"
                 strokeWidth="2"
                 strokeLinecap="round"
                 strokeLinejoin="round"
                 className="feather feather-loader"
               >
                 <line x1="12" y1="2" x2="12" y2="6"></line>
                 <line x1="12" y1="18" x2="12" y2="22"></line>
                 <line x1="4.93" y1="4.93" x2="7.76" y2="7.76"></line>
                 <line x1="16.24" y1="16.24" x2="19.07" y2="19.07"></line>
                 <line x1="2" y1="12" x2="6" y2="12"></line>
                 <line x1="18" y1="12" x2="22" y2="12"></line>
                 <line x1="4.93" y1="19.07" x2="7.76" y2="16.24"></line>
                 <line x1="16.24" y1="7.76" x2="19.07" y2="4.93"></line>
               </svg>
             </span>
           )}
         </form>
       </div>
     </div>
     {machine.matches('addbookMachine.success') && (
       <div className="bg-green-200 px-2 py-2 text-green-800 mt-2 inline-flex">
         Your book has been added!
       </div>
     )}
     {machine.matches('addbookMachine.failed') && (
       <div className="bg-red-200 px-2 py-2 text-red-800 mt-2 inline-flex">
         Sorry we cannot add new book!
       </div>
     )}
   </div>
 );
}

export default Addbook;

```

很明显，我们需要我们的应用程序包装一个 XState 提供者。为此，您仍然可以查看下面共享的代码库。

### 更新图书状态机

为了更新我们的图书详细信息，我们需要创建两个 API 调用:第一个调用获取一本图书的详细信息，第二个调用提交一个补丁请求来编辑该特定的图书。

让我们首先创建一个 fetchsinglebook.js 文件，并在那里编写我们的机器代码:

```
export const fetchOneBookMachine = {
 id: 'fetchonebook',
 initial: 'start',
 states: {
   start: {},
   fetching: {
     invoke: {
       id: 'getOneBook',
       src: getOneBook,
       onDone: {
         target: 'success',
         actions: assign({ list: (_context, event) => event.data }),
       },
       onError: {
         target: 'failed',
         actions: assign({ error: (_context, event) => event.data }),
       },
     },
   },
   editing: {
     invoke: {
       id: 'editOneBook',
       src: editOneBook,
       onDone: {
         target: 'success',
         actions: assign({ list: (_context, event) => event.data }),
       },
       onError: {
         target: 'failed',
         actions: assign({ error: (_context, event) => event.data }),
       },
     },
   },
   success: {},
   failed: {},
 },
};

```

这就是我真正喜欢状态机的地方。只要看一眼这些嵌套的对象结构，我们就能明白发生了什么。

我们现在简单地添加了五个状态，最初是起始状态。当我们调用函数`FETCH_A_BOOK`时，它将自己转换为在`fetchOneBookMachine`中获取。

同样，一旦进入编辑状态，我们也可以添加 PUT API 请求。所有的状态天气移动到成功或失败状态。我们还需要创建一个承诺来确定函数是`onDone`还是`onError`状态。

```
const getOneBook = (context, event) =>
 new Promise(async (resolve, reject) => {
   let result = await fetchSingleBook(context, event);
   if (result.status === 200) {
     resolve(result);
   } else {
     reject('book');
   }
 });

const editOneBook = (context, event) => {
 new Promise(async (resolve, reject) => {
   let result = await editTheBook(context, event);
   if (result.status === 200) {
     resolve(result);
   } else {
     reject('book');
   }
 });
};

```

让我们也为编辑我们的书写我们的 API 调用。这里，我们还需要两个函数:一个是获取单本书的值，另一个是以非破坏性的方式将值添加到数据库中。

```
const fetchSingleBook = async (props) => {
 const { id } = props;
 const res = await fetch(process.env.REACT_APP_BASE_URL + 'Books/' + id, {
   method: 'GET',
   headers: new Headers({
     Authorization: process.env.REACT_APP_API_KEY,
     'Content-Type': 'application/json',
   }),
 }).then((x) => x.json());
 return res;
};

export default fetchSingleBook;

```

最后，我们将创建我们的`editbook`组件。在它的内部，我们将使用`useEffect`钩子来获取一本书。这本书是我们在我们的路线上点击和访问的同一 ID，我们将从我们的状态中过滤下来(当我们获得所有书籍的列表时更新)。

```
import React, { useContext, useEffect, useState } from 'react';
import { MachineContext } from '../state/index';
import { useHistory } from "react-router-dom";

import { Link } from 'react-router-dom';

function Editbook(route) {
 const [machine, sendToMachine] = useContext(MachineContext);
 const id = route.match.params.id;
 const [selectedBook, setSeletedBook] = useState({
   id: '',
   fields: {
     Category: '',
     Name: '',
     Published: '',
     Currency: '',
     Author: '',
   },
   createdTime: '',
 });
 let history = useHistory();

 useEffect(() => {
   sendToMachine('FETCH_A_BOOK', { id });
   const { books, error } = machine.context;
   const list = books.records;
   const filterObj = list.filter((list) => list.id === id);
   setSeletedBook(filterObj[0]);
 }, []);

 const handleOnChange = (userKey, value) => {
   setSeletedBook({ ...selectedBook, fields: { [userKey]: value } });
 };

 const editAbook = async () => {
   sendToMachine('EDIT_A_BOOK', selectedBook);
   history.push("/");
 };

 return (
   <div>
     <div className="flex items-left justify-left">
       <div className="max-w-md w-full">
         <div>
           <div>
             <Link
               to="/"
               className="text-indigo-500 inline-flex items-center font-bold"
             >
               <svg
                 className="w-6 h-6 mr-2"
                 fill="none"
                 stroke="currentColor"
                 viewBox="0 0 24 24"

               >
                 <path
                   strokeLinecap="round"
                   strokeLinejoin="round"
                   strokeWidth="2"
                   d="M7 16l-4-4m0 0l4-4m-4 4h18"
                 ></path>
               </svg>
               Go back
             </Link>
           </div>
           <h4 className="text-left text-2xl font-bold text-gray-500">
             Fill details to edit book details.
           </h4>
         </div>
       </div>
     </div>
     <section>
       {machine.matches('fetchOneBookMachine.fetching') && (
         <>
           <svg

             width="24"
             height="24"
             viewBox="0 0 24 24"
             fill="none"
             stroke="currentColor"
             strokeWidth="2"
             strokeLinecap="round"
             strokeLinejoin="round"
             className="feather feather-loader animate-spin text-2xl"
           >
             <line x1="12" y1="2" x2="12" y2="6"></line>
             <line x1="12" y1="18" x2="12" y2="22"></line>
             <line x1="4.93" y1="4.93" x2="7.76" y2="7.76"></line>
             <line x1="16.24" y1="16.24" x2="19.07" y2="19.07"></line>
             <line x1="2" y1="12" x2="6" y2="12"></line>
             <line x1="18" y1="12" x2="22" y2="12"></line>
             <line x1="4.93" y1="19.07" x2="7.76" y2="16.24"></line>
             <line x1="16.24" y1="7.76" x2="19.07" y2="4.93"></line>
           </svg>
         </>
       )}
       <div>
         <form className="mt-10 space-y-6">
           <input type="hidden" name="remember" value="true" />
           <div className="rounded-md shadow-sm">
             <div className="mb-3">
               <label htmlFor="book-name" className="sr-only">
                 Book Name
               </label>
               <input
                 id="book-name"
                 name="book"
                 type="text"
                 autoComplete="bookname"
                 value={selectedBook.fields.Name}
                 onChange={(e) => handleOnChange('Name', e.target.value)}
                 className="w-full bg-white rounded border border-gray-300 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-200 text-base outline-none text-gray-700 py-1 px-3 leading-8 transition-colors duration-200 ease-in-out"
               />
             </div>
             <div className="mb-3">
               <label htmlFor="author-name" className="sr-only">
                 Author Name
               </label>
               <input
                 id="author-name"
                 name="author"
                 type="text"
                 autoComplete="authorname"
                 className="w-full bg-white rounded border border-gray-300 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-200 text-base outline-none text-gray-700 py-1 px-3 leading-8 transition-colors duration-200 ease-in-out"
                 placeholder="author name"
                 value={selectedBook.fields.Author}
                 onChange={(e) => handleOnChange('Author', e.target.value)}
               />
             </div>
             <div className="mb-3">
               <label htmlFor="published-date" className="sr-only">
                 Published date
               </label>
               <input
                 id="published-date"
                 name="date"
                 type="date"
                 autoComplete="published date"
                 className="w-full bg-white rounded border border-gray-300 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-200 text-base outline-none text-gray-700 py-1 px-3 leading-8 transition-colors duration-200 ease-in-out"
                 placeholder="published date"
                 value={selectedBook.fields.Published}
                 onChange={(e) => handleOnChange('Published', e.target.value)}
               />
             </div>
             <div className="mb-3">
               <label htmlFor="currency" className="sr-only">
                 Price
               </label>
               <input
                 id="currency"
                 name="currency"
                 type="text"
                 autoComplete="currencyname"
                 className="w-full bg-white rounded border border-gray-300 focus:border-indigo-500 focus:ring-2 focus:ring-indigo-200 text-base outline-none text-gray-700 py-1 px-3 leading-8 transition-colors duration-200 ease-in-out"
                 placeholder="price of book"
                 value={selectedBook.fields.Currency}
                 onChange={(e) => handleOnChange('Currency', e.target.value)}
               />
             </div>

             <div className="relative">
               <select
                 onChange={(e) => handleOnChange('Category', e.target.value)}
                 className="rounded border appearance-none border-gray-300 py-2 w-full placeholder-gray-500 text-gray-900 focus:outline-none focus:ring-2 focus:ring-indigo-200 focus:border-indigo-500 text-base pl-3 pr-10 sm:text-sm"
               >
                 <option default value={selectedBook.fields.Category}>
                   {selectedBook.fields.Category}
                 </option>
                 <option>Psychology</option>
                 <option>Related</option>
                 <option>Others</option>
                 <option>Research</option>
                 <option>Testing</option>
                 <option>Usability</option>
               </select>
               <span className="absolute right-0 top-0 h-full w-10 text-center text-gray-600 pointer-events-none flex items-center justify-center">
                 <svg
                   fill="none"
                   stroke="currentColor"
                   strokeLinecap="round"
                   strokeLinejoin="round"
                   strokeWidth="2"
                   className="w-4 h-4"
                   viewBox="0 0 24 24"
                 >
                   <path d="M6 9l6 6 6-6"></path>
                 </svg>
               </span>
             </div>
           </div>

           <div className="inline-flex rounded-md shadow">
             <input
               type="button"
               value="Edit My Book"
               className="inline-flex items-center justify-center px-5 py-3 border border-transparent text-base font-medium rounded-md bg-indigo-600 text-white hover:bg-indigo-700"
               onClick={editAbook}
             ></input>
           </div>
         </form>
       </div>
     </section>
   </div>
 );
}

export default Editbook;

```

### 删除图书状态机

让我们从删除我们的书的机器开始。这将类似于我们以前创建的许多其他状态机。为了删除一本书，我们需要一个获取的书的列表，所以我们将创建两个文件。

第一个用于获取所有的书，另一个用于删除一本书。我们将为我们的机器添加一些状态来实现这一点。

```
export const removebookMachine = {
 id: 'removebook',
 initial: 'start',
 states: {
   start: {},
   deleting: {
     invoke: {
       id: 'deletingBooks',
       src: deletingBooks,
       onDone: {
         target: 'success',
         actions: assign({ fields: (_context, event) => event.data }),
       },
       onError: {
         target: 'failed',
         actions: assign({ error: (_context, event) => event.data }),
       },
     },
   },
   success: {},
   failed: {},
 },
};

```

这个代码片段用于删除图书机。我们还将添加获取所有机器的机器代码。

```
export const appMachine = Machine({
 id: 'app',
 initial: 'init',
 context: {
   books: [],
   error: undefined,
   fields: '',
 },
 states: {
   init: {},
   addbookMachine,
   removebookMachine,
   fetchOneBookMachine,
   list: {
     states: {
       loading: {
         invoke: {
           id: 'fetchAllBooks',
           src: fetchAllBooks,
           onDone: {
             target: 'success',
             actions: assign({ books: (_context, event) => event.data }),
           },
           onError: {
             target: 'failed',
             actions: assign({ error: (_context, event) => event.data }),
           },
         },
       },
       success: {},
       failed: {},
     },
   },
 },
 on: {
   LOAD_BOOKS: {
     target: 'list.loading',
   },
   DELETE_BOOK: {
     target: 'removebookMachine.deleting',
     actions: assign((_ctx, evt) => ({
       id: evt.id,
     })),
   }
 },
});

```

正如您可能已经注意到的，我们还用它的命名导出导入了其他机器代码。这有助于我们将状态机彼此分开。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

现在，让我们转到`Booklist`组件，在那里我们可以调用我们的机器来为我们调用一个动作。删除相应动作的`book`和`load books`。

```
import React, { useContext, useEffect } from 'react';
import { MachineContext } from '../state/index';

import { Deleteicon } from './Deleteicon';
import { Link } from 'react-router-dom';

function Booklist() {
 const [machine, sendToMachine] = useContext(MachineContext);
 const { books, error } = machine.context;
 const list = books.records;

 useEffect(() => {
   sendToMachine('LOAD_BOOKS');
   // eslint-disable-next-line
 }, []);

 const removeBook = (id) => {
   sendToMachine('DELETE_BOOK', { id });
 };

 return (
   <>
     {machine.matches('list.loading') && (
       <span className="w-full">
         <svg

           width="24"
           height="24"
           viewBox="0 0 24 24"
           fill="none"
           stroke="currentColor"
           strokeWidth="2"
           strokeLinecap="round"
           strokeLinejoin="round"
           className="feather feather-loader animate-spin text-2xl"
         >
           <line x1="12" y1="2" x2="12" y2="6"></line>
           <line x1="12" y1="18" x2="12" y2="22"></line>
           <line x1="4.93" y1="4.93" x2="7.76" y2="7.76"></line>
           <line x1="16.24" y1="16.24" x2="19.07" y2="19.07"></line>
           <line x1="2" y1="12" x2="6" y2="12"></line>
           <line x1="18" y1="12" x2="22" y2="12"></line>
           <line x1="4.93" y1="19.07" x2="7.76" y2="16.24"></line>
           <line x1="16.24" y1="7.76" x2="19.07" y2="4.93"></line>
         </svg>
       </span>
     )}
     <section>
       {list && list.length > 0 && (
         <div>
           {list.map((b) => (
             <div key={b.id}>
               {b.fields.Name}
               {b.fields.Published}
               {b.fields.Author}
               {b.fields.Currency}
               {b.fields.Category}
               <Deleteicon clickDelete={() => removeBook(b.id)} />
               <Link to={`/editbook/${b.id}`}>
                 <span>
                   <svg
                     className="w-6 h-6"
                     fill="currentColor"
                     viewBox="0 0 20 20"

                   >
                     <path d="M13.586 3.586a2 2 0 112.828 2.828l-.793.793-2.828-2.828.793-.793zM11.379 5.793L3 14.172V17h2.828l8.38-8.379-2.83-2.828z"></path>
                   </svg>
                 </span>
               </Link>
             </div>
           ))}
         </div>
       )}
     </section>
     <div>
       {machine.matches('list.failed') && (
         <span>Data cannot be loaded {error.toString()}</span>
       )}
     </div>
     <div>
       {machine.matches('removebookMachine.deleting') && (
         <span>
           <svg

             width="24"
             height="24"
             viewBox="0 0 24 24"
             fill="none"
             stroke="currentColor"
             strokeWidth="2"
             strokeLinecap="round"
             strokeLinejoin="round"
             className="feather feather-loader animate-spin text-2xl"
           >
             <line x1="12" y1="2" x2="12" y2="6"></line>
             <line x1="12" y1="18" x2="12" y2="22"></line>
             <line x1="4.93" y1="4.93" x2="7.76" y2="7.76"></line>
             <line x1="16.24" y1="16.24" x2="19.07" y2="19.07"></line>
             <line x1="2" y1="12" x2="6" y2="12"></line>
             <line x1="18" y1="12" x2="22" y2="12"></line>
             <line x1="4.93" y1="19.07" x2="7.76" y2="16.24"></line>
             <line x1="16.24" y1="7.76" x2="19.07" y2="4.93"></line>
           </svg>
         </span>
       )}
     </div>
       {machine.matches('removebookMachine.success') && (
                 <div className="bg-green-200 px-2 py-2 text-green-800 mt-2 inline-flex">
                 Your book has been removed!
               </div>
       )}
       {machine.matches('removebookMachine.failed') && (
                   <div className="bg-green-200 px-2 py-2 text-green-800 mt-2 inline-flex">
                   Your book has not been removed!
                 </div>
       )}
   </>
 );
}

export default Booklist;

```

使用这种方法，我们可以创建一个简单的、功能齐全的 CRUD 书店应用程序。在其中，我们可以存储、编辑和删除关于 Airtable 中书籍的信息。

利用 XState，我们可以确定应用程序的有限数量的状态，并从整体上进行更好的控制。

## 结论

作为开发人员，我们需要外部库来维护应用程序的状态。使用`useState`仅仅使用 React 的内部状态不是一个解决方案，[因为这带来了一种基于事件的方法来编码，并且我们在使用它时会面临许多缺陷](https://blog.logrocket.com/state-driven-interfaces-with-xstate/)。

毕竟，我们让状态机负责转换，是为了让应用程序的性能更加健壮。这确保了您一次只能有一个状态，这是您预定义的状态之一，并且只有在我们明确启用的情况下，才可能从某个状态转换到另一个状态。所以，我们称之为有限状态机。

你可以在 github 库[这里](https://github.com/ishan-me/xstate_airtable_crudApp/tree/main)找到文章的源代码。编码快乐！

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