# 在 React 中创建一个自定义的去抖钩子

> 原文：<https://blog.logrocket.com/create-custom-debounce-hook-react/>

React v16.8 引入了 React 钩子和编写功能组件的新一波可能性。有了 React 钩子，我们可以单独创建可重用的逻辑，这有助于我们[写出更好、更易管理的代码](https://blog.logrocket.com/react-hooks-context-redux-state-management/)。

在本文中，我们将在 React 应用程序中编写一个自定义的反跳钩子，它将延迟几秒钟来处理我们传递的值。我们将创建一个简单的 React 应用程序来搜索 [Rick 和 Morty](https://rickandmortyapi.com/) 角色，并使用自定义的反跳 React 钩子优化我们的应用程序。

*向前跳转:*

## 什么是去抖？

去抖是对任何昂贵函数的结果的优化技术。这些函数可能需要大量的执行工作，因此我们将通过模拟一段时间的延迟执行来创建这种效果。使用反跳可以通过分块执行的动作、添加延迟响应和执行最后一次调用来改进 React 应用程序。

React 内置了很多钩子，包括 use、`useState`、`useEffect`、`useMemo`、`useContext`等等。我们甚至可以组合多个钩子，为我们的 React 应用程序创建[定制钩子。定制的 React 钩子是以`use`关键字开始的函数，后跟我们正在制作的钩子的名字。](https://blog.logrocket.com/create-your-own-custom-react-hooks/)

在创建去抖挂钩之前，我们需要了解以下内容:

*   在循环、条件或嵌套函数中不调用挂钩
*   多个钩子可以用来建立新的钩子
*   钩子只能从 React 函数中调用
*   钩子是为功能部件制造的。
*   以单词“use”开头的名称挂钩

我们正在构建一个应用程序，它将模拟通过按键发送到后端的过多 API 调用。为了减少过多的调用，我们将引入`[useDebounce React Hook](https://blog.logrocket.com/how-and-when-to-debounce-or-throttle-in-react/)`。这个钩子将消耗我们传递的值和计时器。

如果动作被连续调用，我们将只执行最近的用户动作。使用反跳 React 钩子将减少对服务器不必要的 API 调用，并优化我们的应用程序以获得更好的性能。

我们开始吧！

## 创建我们的 React 应用

对于这个应用程序，我们将使用一个简单的 React、Vite.js 和 JavaScript 应用程序。我们将在[查克拉界面](https://blog.logrocket.com/advanced-techniques-chakra-ui/)中添加一些造型点缀。

让我们用这个命令创建一个 Vite.js 应用程序:

```
npm create [email protected] react-useDebounce
cd react-useDebounce
npm i @chakra-ui/react @emotion/react @emotion/styled framer-motion

```

添加完所有包后，我们可以开始创建`components`文件夹。我们将从一个简单的`TextField`开始，它将从 Chakra 导入:

```
export default function Inputfield({onChange, value}) {
 return (
   <div>
     <Input onChange={onChange} value={value} placeholder='Search your character' size='md' />
   </div>
 )
}

```

这个组件是一个简单的输入，它接受一个`onChange`函数和一个作为道具的`value`。我们将使用来自 API 的响应来列出我们找到的所有字符。

我们需要调用我们的端点并接收来自它的响应，因此我们将创建一个`Utils`文件夹并使用浏览器本地`[fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)` API 获取数据:

```
export async function getCharacter(value) {
 const data = await fetch(
   `https://rickandmortyapi.com/api/character/?name=${value}`
 )
 const response = await data.json()
 if (response === undefined || response.error) {
   throw new Error(`HTTP error! status: ${response.error}`);
 }
 return response
}

```

这里，我们创建了一个函数，它对服务器进行 API 调用，并将数据解析到 JSON。我们还添加了一个基本的错误处理，以防我们从服务器收到一个`error`或`undefined`。

## 编写我们的自定义谴责反应钩

现在，我们可以继续创建一个`Hooks`文件夹，在其中添加我们为应用程序创建的挂钩。你可以在这里重温使用 React 钩子的最佳实践。

在`useDebounce.jsx`内部，我们将编写我们的`useDebounce`函数:

```
import { useState, useEffect } from 'react'

export const useDebounce = (value, milliSeconds) => {
 const [debouncedValue, setDebouncedValue] = useState(value);

 useEffect(() => {
   const handler = setTimeout(() => {
     setDebouncedValue(value);
   }, milliSeconds);

   return () => {
     clearTimeout(handler);
   };
 }, [value, milliSeconds]);

 return debouncedValue;
};

```

在这个函数中你看不到什么变化，但是不要担心，我们会在运行过程中修正这个问题。如果你熟悉`setTimeOut`和`clearTimeOut`函数，这并不新鲜。

该函数将值和毫秒作为第二个参数，以特定的时间间隔扩展其执行。我们还通过一个`cleanup return`调用清空了时间，并添加了`value`和`milliSeconds`作为一个`dependency`数组。以下是关于这些函数的更多信息:

*   这个钩子帮助我们存储需要的值
*   `useEffect()`:用清除功能更新去抖值
*   `setTimeOut()`:创建超时延迟
*   `clearTimeOut`:清理、拆卸与用户输入相关的组件

我们可以在应用程序中实现反跳 React 挂钩:

```
import { useState, useEffect } from 'react'
import { ChakraProvider, Heading, Text, Box } from '@chakra-ui/react'
import Inputfield from './components/input-field'
import { useDebounce } from './hooks/useDebounce'
import { getCharacter } from './utils/getCharacter'
import './App.css'

function App() {
 const [query, setQuery] = useState('')
 const [listing, setListing] = useState('')
 const [loading, setLoading] = useState(false)

 const searchQuery = useDebounce(query, 2000)

 useEffect(() => {
   setListing('')
   if (searchQuery || query.length < 0) searchCharacter();
   async function searchCharacter() {
     setListing('')
     setLoading(true)
     const data = await getCharacter(searchQuery)
     setListing(data.results)
     setLoading(false)
   }
 }, [searchQuery])

 return (
   <div className="App">
     <ChakraProvider>
       <Heading mb={4}>Search Rick and Morty Character</Heading>
       <Text fontSize='md' textAlign="left" mb={10}>
         With a debouce hook implemented
       </Text>
       <Inputfield mb={10} onChange={(event) => setQuery(event.target.value)} value={query} />
       {loading && <Text mb={10} mt={10} textAlign="left">Loading...</Text>}
       {listing && <Box mt={10} display={'block'}>{listing.map(character => (
         <Box key={character.id} mb={10}>
           <img src={character.image} alt={character.name} />
           {character.name}
         </Box>
       ))}</Box>}
     </ChakraProvider>
   </div>
 )
}

export default App

```

到目前为止，我们已经完成了基本的实现，并使用了`useState`来存储我们的`searchQuery`字的状态。

找到结果后，我们将使用数据设置我们的`listing`状态。因为这是一个异步动作，所以我们添加了`loading`来继续跟踪`data loading`状态。

尽管这是一个简单的反跳钩子的实现，我们将改进和重构我们的代码。让我们开始改进我们的代码。

## 提高我们的反应谴责挂钩

为了改进 React 中的去抖钩子，我们将使用`[AbortController](https://developer.mozilla.org/en-US/docs/Web/API/AbortController)`，这是一个所有现代浏览器都内置的 WebAPI。这个 API 帮助我们停止任何正在进行的 Web 请求。

要开始使用该控制器，请用以下代码实例化它:

`const controller = new AbortController();`

使用控制器，我们可以访问两个属性:

*   `abort()`:执行时，取消正在进行的请求
*   `Signal`:保持`controller`和请求取消之间的连接

我们现在可以对我们的`debounce`钩子做进一步的调整。当我们没有收到一个`milliSeconds`值时，我们将提供一个可选值:

```
const timer = setTimeout(() => setDebouncedValue(value), milliSeconds || 1000)

```

在`getCharacter`函数中，我们将传入`controller`的`signal`属性。现在，我们将对我们的主文件进行一些重要的修改。

让我们来看一下引入的变化:

```
import { useState, useEffect, useRef } from 'react'
import { ChakraProvider, Heading, Text, Box, Button, SimpleGrid } from '@chakra-ui/react'
import Inputfield from './components/input-field'
import { useDebounce } from './hooks/useDebounce'
import { getCharacter } from './utils/getCharacter'
import './App.css'

function App() {
 const [query, setQuery] = useState('')
 const [listing, setListing] = useState('')
 const [loading, setLoading] = useState(false)
 const controllerRef = useRef()

 const searchQuery = useDebounce(query, 2000)
 const controller = new AbortController();
 controllerRef.current = controller;
  const searchCharacter = async () => {
   setListing('')
   setLoading(true)
   const data = await getCharacter(searchQuery, controllerRef.current?.signal)
   controllerRef.current = null;
   setListing(data.results)
   setLoading(false)
 }

 useEffect(() => {
   if (searchQuery || query.trim().length < 0) searchCharacter()
   return cancelSearch()
 }, [searchQuery])

 const cancelSearch = () => {
   controllerRef.current.abort();
 }

 return (
   <div className="App">
     <ChakraProvider>
       <Heading mb={4}>Search Rick and Morty  Character</Heading>
       <Text fontSize='md' textAlign="left" mb={10}>
         With a debounce hook implemented
       </Text>

       <SimpleGrid columns={1} spacing={10}>
         <Box>
           <Inputfield mb={10} onChange={(event) => setQuery(event.target.value)} value={query} />
         </Box>
       </SimpleGrid>

       {loading && <Text mb={10} mt={10} textAlign="left">Loading...</Text>}
       {listing && <Box mt={10} display={'block'}>{listing.map(character => (
         <Box key={character.id} mb={10}>
           <img src={character.image} alt={character.name} />
           {character.name}
         </Box>
       ))}</Box>}
       {!listing && !loading && <Box mt={10} display={'block'} color={'#c8c8c8'}>You have started your search</Box>}
     </ChakraProvider>
   </div>
 )
}

export default App

```

这里，我们在应用程序中引入了一个额外的挂钩。我们使用`controller`构造函数来创建一个新的`AbortSignal`实例，并将`controller`分配给`useRef`。`useRef`帮助我们从 DOM 中获取元素来监视状态变化。

在我们的 API 调用过程中，我们用`controllerRef.current.signal`传递了当前的`signal option`。我们添加了一个`cancel`控制器，用于在`searchQuery`值改变时调用[清理函数](https://blog.logrocket.com/understanding-react-useeffect-cleanup-function/):

*   `Aborted`:表示信号已经中止的`Boolean`值，初始为`false`，触发时，初始为`null`
*   `abortController.abort()`:这有助于我们停止`fetch`请求

我们还可以多次调用服务器，并根据需要中止请求。这在处理网络流量和优化技术时很方便。

## 结论

在本文中，我们成功地创建了一个反跳 React 挂钩，以限制 React 应用程序中对服务器的不必要调用和处理。使用这种技术有助于改进 React 应用程序。

我们可以将这种去抖优化技术用于昂贵的动作，如调整事件大小、拖动事件、击键监听器和滚动事件。这可以帮助我们运行具有已知性能优势的应用程序。要找到完整的工作代码，请查看 GitHub 库。

编码快乐！

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