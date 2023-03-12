# React - LogRocket 博客中使用 TypeScript 的参考指南

> 原文：<https://blog.logrocket.com/your-reference-guide-to-using-typescript-in-react/>

JavaScript 的一个问题是它的动态类型特性，这意味着数据和变量类型在运行时之前是未知的。这可能会有很多副作用。例如，由于变量可以是任何东西，这可能会在您的代码库中造成混乱。

为了解决这个问题，微软发布了 [TypeScript](https://www.typescriptlang.org/) 。TypeScript 的首席架构师安德斯·海尔斯伯格说，“如果我们可以用大规模应用程序开发所缺少的东西来增强 JavaScript，比如静态类型、类[和]模块……会怎么样？这就是 TypeScript 的意义所在。”

TypeScript 立即成为最广泛使用的 JavaScript 静态类型版本。它使 JavaScript 开发人员能够静态地输入数据和变量。很快，它被引入到 [React.js](https://reactjs.org/) 中，使 React 开发人员能够用 TypeScript 编写他们的 React 应用程序。

但这是有代价的:TypeScript 的类型和语法可能很难跟上，尤其是在与 React 一起使用时。

React 有很多特性，比如 props，class 组件，function 组件，function params，组件生命周期钩子，成员属性。因为在 TypeScript 中输入这些内容并不容易，所以本文旨在作为初学者和高级 React 开发人员的快速参考和学习指南。

有了它，您将能够在 React 中快速查找最佳实践和通用 TS 类型。准备好了吗？让我们开始吧。

## 打字稿

TypeScript 有一个 typings 文件夹，用于保存扩展名为*.d.ts 的文件。这些文件包括推断值将采取什么形状的接口。这就是 TypeScript 将数据类型引入 JavaScript 的原因。

接口描述了值的样子:

```
type AppState {
    propOne: number;
    propTwo: string
}

```

`AppState`描述其数据类型的值看起来像什么。首先，我们推断它将是一个拥有属性`propOne`和`propTwo`的对象，前者是数字类型，后者是字符串类型。将 boolean 类型赋给`propOne`会导致 TypeScript 抛出 TypeError。

当我们在 React 项目中包含 TypeScript 时，每个 React 元素都有一个定义其形状的接口。让我们从功能组件开始。

## 功能组件

函数组件是在 React 中返回 JSX 元素的普通函数，用于创建视图。最初，它们是无状态的组件，但是随着 React 钩子的出现，它们可以变成有状态的和智能的/

定义一个 React 函数组件采用`React.FunctionComponent`形状:

```
function App: React.FunctionComponent<> {
    return (
        <>
            // ...
        </>
    )
}

```

我们也可以使用简写`React.FC:`

```
function App: React.FC<> {
    return (
        <>
            // ...
        </>
    )
}

```

`React.FunctionComponent`或`React.FC`明确描述了函数组件的返回类型。

我们可以在箭头`<>`中键入道具定义。

```
type AppProps = {
    message: string;
    age: number;
};

```

`AppProps`是传递给`App`的道具将接受的接口，因此我们可以编写下面的`App`组件，如果它将接受道具的话:

```
type AppProps {
    message: string;
    age: number;
}

function App: React.FC<AppProps>(props: AppProps) {
    return (
        <>
            // ...
        </>
    )
}

```

我们可以使用`?`在输入中设置可选值:

```
type AppProps {
    message: string;
    age?: number;
}

```

现在，年龄属性变成可选的。可以呈现`App`组件，省略其 props 对象中的 age 属性。

我们可以省略箭头`<>`中的类型声明。

```
function App<{message: string; age: number;}>({message: string; age: number;}: AppProps) {
    // ...
}

```

功能组件中的内部函数可以被类型化，如下所示:

```
function App<{message: string; age: number;}>({message: string; age: number;}: AppProps) {
    // ...

    function clickHandler (val: number) {
        // ...
    }

    return (
        <>
            <button onClick={() => clickHandler(45)}            
        </>
    )
}

```

## 类别组件

类组件用于在 React 中创建视图。它们本质上是积木。一个 React 应用程序可以包含许多这样的元素，它们定义了一个小的 UI 单元应该如何显示。

类组件有生命周期挂钩，我们可以在组件生命周期的任何状态下运行定制代码。

他们使用`React.Component<>`:

```
class App extends React.Component<> {
    // ...    
}

```

## 道具和状态

您可以为类组件提供属性和状态类型参数:

```
type AppProps = {
    message: string;
    age: number;
};

type AppState = {
    id: number;
};

class App extends React.Component<AppProps, AppState> {
    state: AppState = {
        id: 0
    };

    // ...    
}

```

道具类型`AppProps`插在状态类型`AppState`之前的`<>`箭头之间。

## 类方法

如果您有类方法的参数，这些参数可以是:

```
class App extends React.Component<AppProps, AppState> {
    state: AppState = {
        id: 0
    };

    clickHandler = (val: number) => {
        // ...
    }

    render() {
        return (
            <>
                <button onClick={() => this.clickHandler(90)}>Click</button>
            </>
        )
    }
}

```

# 功能组件和`defaultProps`打字

`defaultProps`用于定义传递给 React 组件的 props 参数的默认值。`defaultProps`有助于防止在运行时一个假定可用的道具丢失时出现错误。`defaultValues`内的值立刻成为道具的值。

在函数组件中包含`defaultProps`时，`React.Fc`被删除，因为类型推理中的一些边缘情况在 TypeScript 中仍然是一个问题。

这意味着道具像平常一样使用:

```
type AppProps = { message: string; age: number } & typeof defaultProps;
const defaultProps = {
    message: "",
    age: 0
};

function App(props: AppProps) {
    // ...
};

App.defaultProps = defaultProps;

```

## 表单和事件

表格一般用于收集内部状态的信息。它们主要用于登录和注册页面，以便表单可以收集提交的信息并发送到服务器进行处理。

`React.FormEvent`用于从元素中概括事件类型。

```
class App extends React.Component<> {
    clickHandler = (e: React.FormEvent<HTMLButtonElement>) => {
        // ...
    }

    changeHandler = (e: React.FormEvent<HTMLInputElement>) => {
        // ...
    }

    render() {
        return (
            <div>
                <button onClick={this.clickHandler}>Click</button>
                <input type="text" onChange={this.changeHandler} />
            </div>
        )
    }
}

```

`clickHandler`和`changeHandler`论元`e`都具有`React.FormEvent`类型。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们可以省略用`React.FormEvent`键入处理程序的参数，而是键入处理程序的返回值。这是通过使用`React.ChangeEventHandler`完成的:

```
class App extends React.Component<> {
    clickHandler: React.FormEvent<HTMLButtonElement> = (e) => {
        // ...
    }

    changeHandler: React.FormEvent<HTMLInputElement> = (e) => {
        // ...
    }

    render() {
        return (
            <div>
                <button onClick={this.clickHandler}>Click</button>
                <input type="text" onChange={this.changeHandler} />
            </div>
        )
    }
}

```

`React.ChangeEvent<T>`是可用于键入事件处理程序参数的事件类型:

```
class App extends React.Component<> {
    clickHandler = (e: React.ChangeEvent<HTMLButtonElement>) => {
        // ...
    }

    changeHandler = (e: React.ChangeEvent<HTMLInputElement>) => {
        // ...
    }

    render() {
        return (
            <div>
                <button onClick={this.clickHandler}>Click</button>
                <input type="text" onChange={this.changeHandler} />
            </div>
        )
    }
}

```

注意`T`是注册事件的元素的类型。

### 纽扣

这描述了 React。按钮元素的 ChangeEvent 类型。按钮是 HTMLButtonElement 类的实例。这里，`T`会是`HTMLButtonElement`，并做出反应。变化事件将是`React.ChangeEvent`。

### 输入

这描述了所有输入元素的`React.ChangeEvent`:“文本”、“密码”、“颜色”、“按钮”、“媒体”等。它们都是 HTMLInputElement 类的实例。对于所有类型=[“文本”、“密码”等]

`T`将是`HTMLTnputElement`。做出反应。变化事件将是`React.ChangeEvent`。

### 文本区域

此指反应过来。文本区域元素的 ChangeEvent 和文本区域元素是 HTMLTextAreaElement 的实例。

`T`将是`HTMLTextAreaElement`。
做出反应。变化事件将是`React.ChangeEvent`。

### 挑选

Select 元素用于创建一个带有选项的下拉列表。Select 元素是 HTMLSelectElement 的实例。

`T`将是`HTMLSelectElement`。做出反应。变化事件将是`React.ChangeEvent`。

### 形式

Form elements 从 DOM 中收集信息。表单元素是 HTMLFormElement 的实例。t 将`HTMLFormElement`。做出反应。ChangeEvent 将是`React.ChangeEvent`。

### 视频、音频

视频用于在浏览器上播放视频。video 元素是 HTMLVideoElement 的一个实例。这描述了如何设置 React。表单元素中的 ChangeEvent。

`T`将是`HTMLVideoElement`。
做出反应。变化事件将是`React.ChangeEvent`。

反过来，Audio 用来播放音频文件(mp3，aac 等。)在浏览器中。音频元素是 HTMLAudioElement 的实例。

`T`将是`HTMLAudioElement`。
做出反应。变化事件将是`React.ChangeEvent`。

### `React.SyntheticEvent`

当您不关心事件的类型时，可以使用这种类型:

```
class App extends React.Component<> {
    submitHandler = (e: React.SyntheticEvent) => {
        // ...
    }

    render() {
        return (
            <form onSubmit={this.submitHandler}>
                ...
            </form>
        )
    }
}

```

### 钩住

react 16.8+支持钩子。它们允许我们在功能组件中使用状态。

### `useState`

这用于设置功能组件中的状态，该状态在组件的整个生命周期中保持有效。

```
const [state, setState] = useState(0)

```

我们可以推断出`state`是一个数字，而`setState`是一个接受数字的函数。

```
const [state, setState] = useState<number, (v: boolean) => : void >(0)

```

如果国家接受一个对象:

```
type StateObject = {
    loading: boolean
}

const [state, setState] = useState<StateObject, (boolean) => : void >({loading: true})

```

很多时候，`useState`的初始值是`null`。我们可以使用联合类型显式声明类型，如下所示:

```
const [state, setState] = useState<StateObject | null, (boolean) => : void >(null)

```

这个`StateObject | null`告诉 TypeScript 状态可以是`StateObject`类型或者是`null`类型。

### **T2`useReducer`**

允许我们维护状态，同时从我们的功能组件向商店发送动作。它有一个初始状态和一个缩减函数:

```
const intialState = {
    loggedIn: false
}

function reducer(state, action) {
    // ...    
}

const [state, dispatch ] = useReducer(initialState, reducer)

```

我们可以定义状态的类型，然后根据返回类型键入 reducer 函数。可以基于 reducer 函数上的动作参数类型来确定分派的类型:

```
type LogginState = {
    loggedIn: boolean;
};

type ActionType = {
    type: string;
    payload: boolean;
};

const intialState: LogginState = {
    loggedIn: false
}

function reducer(state: LogginState, action: ActionType): LogginState {
    // ...    
}

const [state, dispatch ] = useReducer<LogginState, (v: ActionType) => : void>(initialState, reducer)

```

### `useRef`

`useRef`允许我们访问 React 节点中的引用，并在组件的整个生命周期中维护它们。

`null`类型大多被传递给`useRef`，所以类型应该是这样的:

```
const ref= useRef<HTMLElement | null>(null)

```

我们可以更具体地说明我们试图引用的元素的类型:

```
// this types useRef to button elements
const buttonRef= useRef<HTMLButtonElement | null>(null)

// this types useRef to input elements
const inputRef= useRef<HTMLInputElement | null>(null)

```

另外，`useRef`可以用在`React.Component`上:

```
const inputRef= useRef<React.Component | null>(null)

```

### **使用上下文**

useContext 允许我们在功能组件的整个生命周期中创建和维护上下文。上下文是使用 React 上下文 API 创建的。

下面是如何使用`useContext`和`createContext`来创建一个上下文:

```
const authContext = createContext({isAuth: true})
const context = useContext(authContext)

```

`createContext`的缺省值类型将构成其参数类型的基础:

```
type AuthType = {
    isAuth: boolean;
};

const authContext = createContext<AuthType>({isAuth: true})

```

另外，`useContext`参数类型将是`AuthType`:

```
const context = useContext<AuthType>(authContext)

```

## 结论

希望你发现这个指南信息丰富，有帮助。你还可以在 [this Github repo](https://github.com/typescript-cheatsheets/react) 中阅读更多关于 React TypeScript 类型推断的内容。

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

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.