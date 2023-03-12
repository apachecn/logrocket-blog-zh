# React 中的风格:5 种风格 React 应用程序的方法

> 原文：<https://blog.logrocket.com/styling-react-5-ways-style-react-apps/>

***编者按:**React 帖子中的这一样式最近一次更新是在 2022 年 12 月 8 日，包括了关于使用 Sass 和 CSS 样式表进行样式化的部分，每种样式化方法的优缺点，以及关于在 React 中进行样式化的最佳方式的讨论。*

那么，你是来学习如何在 [React](https://blog.logrocket.com/tag/react/) 中进行造型的？嗯，你来对地方了。当你读完这篇文章时，我保证你会知道该怎么做。在本文中，我们将回顾使用内联样式、样式化组件、CSS 模块、顺风 CSS 以及 Sass 和 CSS 样式表对 React 组件进行样式化。

我们将使用待办事项应用程序的一个组件来解释这些方法。同时也要考虑本文中讨论的样式选项的优缺点。

如果你是新反应过来的，可以查一下[官方文档](https://reactjs.org)。

 [https://www.youtube.com/embed/d-7hWYBguSs?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/d-7hWYBguSs?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

*向前跳转:*

## 设置 React 应用程序

要设置一个应用程序，可以使用 [Create React App](https://blog.logrocket.com/getting-started-with-create-react-app-d93147444a27/) 。这是开始 React 项目最简单的方法。然而， [CRA 演示](https://blog.logrocket.com/tag/create-react-app/)超出了本文的范围，所以我们将跳过它，设计一个假想的待办事项应用程序。

这是 React 应用程序的演示:

让我们从第一种方法开始吧！

## 样式用内嵌样式反应组件

如果你熟悉基本的 HTML，你会知道添加你的 [CSS 内嵌](https://blog.logrocket.com/improve-site-performance-inlining-css/)是可能的。这类似于 React。

我们可以向任何想要呈现的 React 组件添加内联样式。这些样式作为属性编写，并传递给元素。让我们使用内联样式来设计组件的各个部分:

```
/** src/todo/AddTodo.js **/

//...code omitted for brevity
const AddTodo = () => {
 //...
  return (
    <div style={{ display: "flex", flexDirection: "column" }}>
      <h2 style={{ padding: "10px 20px", textAlign: "center", color: "white" }}>
        TODO
      </h2>
      <div
        style={{
          display: "flex",
          justifyContent: "center",
          alignItems: "center"
        }}
      >
        <label
          style={{ padding: "10px 20px", textAlign: "center" }}
          htmlFor="new-todo"
        >
          What needs to be done?
        </label>
      </div>
      <div
        style={{
          display: "flex",
          justifyContent: "center",
          alignItems: "center"
        }}
      >
        <form onSubmit={handleSubmit}>
          <input onChange={onChange} value={task} ref={inputRef} />
          <button>Add </button>
        </form>
      </div>
      {message && (
        <div
          style={{
            display: "flex",
            justifyContent: "center",
            alignItems: "center"
          }}
        >
          <h4 style={{ color: "red" }}>{message}</h4>
        </div>
      )}
    </div>
  );
};
export default AddTodo;

```

在上面的代码中，我们向`AddTodo()`中的 HTML 元素添加了内联样式。这里有一些你应该注意的事情。

首先，有两个大括号。我们正在渲染的是用 JSX 写的，对于要在 JSX 使用的纯 JavaScript 表达式，它们必须包含在花括号中。

第一个花括号将 JavaScript 注入 JSX。内部的花括号创建一个对象

字面意思。样式作为对象文字传递给元素。

> 💡JSX 是将 XML 语法添加到 JavaScript 的预处理步骤。没有 JSX，你当然可以使用 React，但是 JSX 让 React 更加优雅。就像 XML 一样，JSX 标签有标签名、属性和子标签。

下一件要注意的事情是属性由逗号分隔。这是因为我们传递的是一个对象。因为它是一个 JavaScript 属性，所以属性是用 camelCase 编写的，并且没有用破折号分隔。

在上面的代码中，我们只是给我们设计的元素添加了一些属性。然而，想象一下我们必须向元素添加越来越多的样式。这就是内联方法失败的地方，因为它看起来不干净。

不过，有一种方法可以解决这个问题。我们可以创建对象变量并将它们传递给元素。

### 创建样式对象变量

我们创建样式对象变量的方式与创建 JavaScript 对象的方式相同。然后，这个对象被传递给我们想要设置样式的元素的 style 属性。

因此，我们没有像上一个例子那样直接内联添加样式，而是传递`object`变量，如下所示:

```
/** src/todo/AddTodo.js **/
const AddTodo = () => {
//...
  return (
    <div style={Container}>
      <h2 style={Header}>TODO</h2>
      <div style={LabelContainer}>
        <label style={Label} htmlFor="new-todo">
          What needs to be done?
        </label>
      </div>
      <div style={FormContainer}>
        <form onSubmit={handleSubmit}>
          <input onChange={onChange} value={task} ref={inputRef} />
          <button>Add </button>
        </form>
      </div>
      {message && (
        <div style={Message}>
          <h4 style={{ color: "red" }}>{message}</h4>
        </div>
      )}
    </div>
  );
};

const Container = { display: "flex", flexDirection: "column" };
const Header = { padding: "10px 20px", textAlign: "center", color: "white" };
const LabelContainer = { display: "flex", justifyContent: "center", alignItems: "center"};
const Label = { padding: "10px 20px", textAlign: "center" };
const FormContainer = { display: "flex", justifyContent: "center", alignItems: "center"};
const ErrorMessage = { display: "flex", justifyContent: "center", alignItems: "center"};

```

在上面的代码中，我们创建了六个对象变量:`Container`、`Header`、`LabelContainer`、`Label`、`FormContainer,` 和`ErrorMessage`。

然后，我们将这些变量传递给元素，而不是直接键入它们。

> 💡我们不必在元素中使用双花括号，因为这些变量本身就是对象。

如果您查看对象属性，`camelCases`将在编译期间被转换为破折号分隔的 CSS 属性。例如，这个:

```
backgroundColor: "#44014C",
minHeight: "200px",
boxSizing: "border-box"
In plain CSS, these will be written as:
background-color: #44014C;
min-height: 200px;
box-sizing: border-box;

```

> ⚠️The `camelCase`对破折号分隔的字符串的更改只适用于属性名，而不适用于属性值。

可以将变量作为值传递给属性。所以，我们可以这样做:

```
/* in .js file */
const spacing = "10px 20px";
const Header = {
  margin: spacing,
  padding: spacing
  // ...
}

```

在许多 [JavaScript 环境](https://logrocket.com/for/debug-javascript-app/)中，创建一个全局对象变量可能是不好的做法，但是在 React 中却很好。因为除非导入，否则文件对其他文件是不可见的，所以我们可以创建尽可能多的对象变量，即使使用相同的名称也不会有冲突。

### 跨许多 React 组件共享样式

样式对象和组件不必在同一个文件中。我们可以为我们的样式创建一个单独的`.js`文件，导出这些样式，然后将它们导入到我们想要使用它们的组件中。这样做使得样式可以跨多个组件重用。让我们为我们的组件这样做。

首先，我们将创建一个名为`styles.js`的单独的`.js`文件。然后，我们将添加这些样式:

```
const Container = { display: "flex", flexDirection: "column" };
const Header = { padding: "10px 20px", textAlign: "center", color: "white" };
const LabelContainer = {
  display: "flex",
  justifyContent: "center",
  alignItems: "center"
};
const Label = { padding: "10px 20px", textAlign: "center" };
const FormContainer = {
  display: "flex",
  justifyContent: "center",
  alignItems: "center"
};
const ErrorMessage = {
  display: "flex",
  justifyContent: "center",
  alignItems: "center"
};

export const styles = {
  Container: Container,
  Header: Header,
  LabelContainer: LabelContainer,
  Label: Label,
  ErrorMessage: ErrorMessage,
  FormContainer: FormContainer
}

export const styles = {
  Container: Container,
  Header: Header,
  LabelContainer: LabelContainer,
  Label: Label,
  ErrorMessage: ErrorMessage,
  FormContainer: FormContainer
}

```

在上面的代码中，我们可以分别导出每个样式对象，这也意味着分别导入它们。如果文件中有许多样式对象，这可能会变得很乏味。

因此，创建包含所有样式的对象才有意义。该对象被导出和导入到将要使用它的组件中一次。所以，让我们这样做:

```
/** AddTodo.js file **/

// Import the styles
import {styles} from "./styles";

const AddTodo = () => {
//....
  return (
    <div style={styles.Container}>
      <h2 style={styles.Header}>TODO</h2>
      <div style={styles.LabelContainer}>
        <label style={styles.Label} htmlFor="new-todo">
          What needs to be done?
        </label>
      </div>
      <div style={styles.FormContainer}>
        <form onSubmit={handleSubmit}>
          <input onChange={onChange} value={task} ref={inputRef} />
          <button>Add </button>
        </form>
      </div>
      {message && (
        <div style={styles.ErrorMessage}>
          <h4 style={{ color: "red" }}>{message}</h4>
        </div>
      )}
    </div>
  );
};

```

在`AddTodo()`之上，我们将导入`styles`对象。然后，这个对象被用来设计 React 应用程序的组件，就像任何 JavaScript 对象一样使用。

### 利弊

从这一点可以看出，样式可以在多个组件中使用和重用。只需要将样式导入并添加到样式属性中。

当然，[有些情况下你不应该使用内联样式](https://blog.logrocket.com/why-you-shouldnt-use-inline-styling-in-production-react-apps/)，这就是我们接下来两个方法的用武之地。

### 优点:

使用内联样式可以帮助你快速原型化你的界面。

### 缺点:

使用内联样式，声明会很快变得杂乱无章。此外，直列式设计并不提倡干燥(不要重复自己)的原则。使用内联样式不适合包含大量代码的大型项目。

## 使用样式组件

使用样式化组件，我们可以在 JavaScript 文件中编写实际的 CSS。这意味着你可以在 JavaScript 中使用 CSS 的所有特性——比如[媒体查询](https://blog.logrocket.com/choose-between-media-container-queries/)、[伪选择器](https://blog.logrocket.com/css-modal-pseudo-selector/)、[嵌套](https://blog.logrocket.com/native-css-nesting/)等等。

[样式化组件](https://github.com/styled-components/styled-components)使用 ES6 的标记模板文字来样式化组件。有了它，组件和样式之间的映射被删除。这意味着当您定义您的样式时，您实际上是在创建一个普通的 React 组件，它将您的样式附加到它上面。

使用样式化组件，我们可以创建带有样式的可重用组件。创造和使用它是相当令人兴奋的。

首先，我们需要在 React 应用程序的目录下运行`$ npm install --save styled-components`来安装它。

让我们回到我们的待办事项应用程序，[让我们的组件使用样式化组件](https://blog.logrocket.com/using-styled-components-in-react/)。首先，用`import styled from 'styled-components';`导入`AddTodo.js`文件中的`styled-components`包。

现在，我们可以马上开始使用它。我们将首先创建一个样式化组件，然后看看我们将如何使用它:

```
/** AddTodo.js file **/
const Container = styled.div`
  display: flex;
  flex-direction: column;
`;
const Header = styled.div`
  padding: 10px 20px;
  text-align: center;
  color: white;
`;
//....

```

在上面的代码中，我们创建了一个可以像任何 React 组件一样使用的组件。但是，请注意，我们在 JavaScript 文件中使用的是纯 CSS。接下来，让我们把这个组件:

```
/** AddTodo.js file **/

function AddTodo() {
 //...

return (
    <Container>
      <Header>TODO</Header>
      <LabelContainer>
        <Label htmlFor="new-todo">What needs to be done?</Label>
      </LabelContainer>
      <FormContainer>
        <form onSubmit={handleSubmit}>
          <input onChange={onChange} value={task} ref={inputRef} />
          <button>Add </button>
        </form>
      </FormContainer>
      {message && (
        <ErrorContainer>
          <ErrorMessage>{message}</ErrorMessage>
        </ErrorContainer>
      )}
    </Container>
  );
}

```

在上面的代码中，我们使用了自己创建的样式化组件，并用定义的组件样式标签替换了 HTML 标签。styled-component 用作任何其他 HTML 元素。唯一的区别是它有自己的预定义样式。

您可以在 CodeSandbox playground 中访问代码:

利弊

### 要找到更多关于样式组件以及如何使用它们的信息，你可以在这里阅读。

优点:

### styled——组件以您认为合适的方式动态地设计您的元素的样式。他们鼓励用优秀的模式来组织代码的 DRY 原则，并且风格化组件与广泛的框架和库兼容。它们也非常适合开发和维护设计系统。

缺点:

### 将声明转换成普通 CSS 时，样式化组件会带来大量的计算开销。这可能会影响应用程序的性能。样式化组件也需要时间来熟悉语法和过程。

接下来，让我们讨论 React 中样式的第三种方式。

样式化 React 组件的 CSS 模块

## 一个 [CSS 模块](https://blog.logrocket.com/a-deep-dive-into-css-modules/)是一个 CSS 文件，默认情况下所有的类名和动画名都在本地范围内。记下局部作用域的单词。让我们稍微分解一下。

默认情况下，CSS 类名和动画名的作用域是全局的。这可能会导致冲突，尤其是在大型样式表中，因为一种样式可能会覆盖另一种样式。CSS 模块解决了这个问题。CSS 类只在使用它们的组件中可用。

CSS 模块基本上是一个被编译的`.css`文件。编译时，它产生两个输出。一个是 CSS，它是输入 CSS 的修改版本，具有重命名的类名。另一个是 JavaScript 对象，它将原来的 CSS 名称映射到重命名的名称。

好了，让我们在一个模块中为一个示例错误消息创建一个 CSS 类。我们模块的名字是`styles.module.css`:

编译时，将会产生如下内容:

```
.error-message {
  color: red;
  font-size: 16px;
}

```

添加的`jhys`只是一个样本键(我自己添加的)，用来唯一标识这个类。如前所述，它生成一个 JavaScript 对象，可以导入 React 文件并使用:

```
.error-message_jhys {
  color: red;
  font-size: 16px;
}

```

现在，让我们看看如何使用:

```
{
  error-message: error-message_jhys
}

```

利弊

```
/** .js file **/
import styles from './styles.css';

function Message() {
 return (
   <div className={styles.ErrorMessage}>I am an error message</div>
 );
}

```

### 请记住，CSS 模块的主要目的是使 CSS 类具有本地作用域，并避免命名冲突。这里有一些使用 CSS 模块的优点和缺点。

优点:

### CSS 模块可以很容易地与 CSS 或 SCSS 风格引擎集成。它们生成唯一的类名，没有冲突，并且在 React 库中有[内置支持](https://create-react-app.dev/docs/adding-a-css-modules-stylesheet/)。CSS 模块还修复了 CSS 声明的全局范围问题。

缺点:

### 用 CSS 模块引用类名在大多数时候会令人困惑。

使用顺风 CSS 的样式

## Tailwind CSS 提供了一种不同的方法，在这种方法中，不需要编写 CSS 来设计应用程序。相反，Tailwind CSS 为每个 CSS 属性使用实用程序类，您可以在 HTML 或 JSX 中直接使用它们。

也许您想知道是否每个 CSS 属性都映射到一个类；最终 CSS 的包大小是多少？事实上，包的大小非常小，大多数项目交付的包的大小都小于 10kB。但是怎么做呢？Tailwind CSS 处理您在构建时使用的类，并构建一个适合您的项目的 CSS 包。

Tailwind CSS 提供的[比映射到属性的 CSS 类](https://blog.logrocket.com/10-best-tailwind-css-component-template-collections/)多得多；这也是一个支持响应行为、网格、状态、黑暗模式、[和更多](https://blog.logrocket.com/guide-adding-gradients-tailwind-css/)的完整框架。此外，它是高度可配置的。

要在 CRA 项目上设置 Tailwind CSS，需要几个步骤(和库)。这是因为它需要修改应用程序的构建过程来生成 CSS 包。

首先，从安装 Tailwind CSS 并生成`tailwind.config.js`和`postcss.config.js`开始。

然后，运行`$ npm install -D tailwindcss postcss autoprefixer`和`$ npx tailwindcss init -p`命令。

注意，所有与 Tailwind CSS 相关的库都是作为`dev`包安装的，这意味着它们不会影响 JavaScript 包的大小。

在上面生成的`tailwind.config.js`中，将以下值添加到`content`数组中:

现在，我们需要设置我们的 CSS 基线。因为 Tailwind CSS 使用多个具有相对值的类，所以跨所有浏览器设置相同的 CSS 样式库非常重要。Tailwind CSS 提供了一些默认样式来实现这一点。

```
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./src/**/*.{js,jsx,ts,tsx}",],
  theme: {
    extend: {},
  },
  plugins: [],
}

```

为此，导航到您的`src/index.css`文件并粘贴代码:

💡请注意，导入这些类将使所有默认元素的行为与您预期的不同，因此标题和段落等元素将具有相同的样式属性，直到您向它们添加 Tailwind CSS 类。

```
/* ./src/styles.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

```

> Tailwind CSS 在其设置期间提供了许多配置选项，因此如果您想了解更多，请查看官方文档。

最后，在项目中使用 Tailwind CSS 类。设置就绪后，现在可以直接在项目中使用 CSS 类了。继续使用 Tailwind CSS 构建一个`ToDo`组件:

请注意，这些类是作为文本直接注入到`className`属性中的，并且有一个完整的引用，引用了所有可以使用的带有状态和响应属性的类名。

```
/** AddTodo.js file **/

function AddTodo() {
 //...

  return (
    <div className="flex flex-col">
      <h2 className="px-2.5 py-5 text-center text-white">TODO</h2>
      <div className="flex justify-center items-center">
        <label className="px-2.5 py-5 text-center" htmlFor="new-todo">
          What needs to be done?
        </label>
      </div>
      <div className="flex justify-center items-center">
        <form onSubmit={handleSubmit}>
          <input onChange={onChange} value={task} ref={inputRef} />
          <button>Add </button>
        </form>
      </div>
      {message && (
        <div className="flex justify-center items-center">
          <h4 className="text-red-800">{message}</h4>
        </div>
      )}
    </div>
  );
}

```

利弊

### 虽然一开始可能看起来违反直觉，但是一旦你使用 Tailwind CSS 几天，你很可能会爱上它。让我们来看看 Tailwind CSS 的一些优缺点。

优点:

### Tailwind CSS 很容易用于定制元素和构建简洁的用户界面。Tailwind CSS 大大减少了编写定制 CSS 代码的工作量，这使您可以更快地开发 UI 屏幕。

缺点:

### Tailwind CSS 经常要求你从头开始实现它，因为像按钮、导航条和标签这样的基本组件都没有提供。此外，学习 Tailwind CSS 需要一个最小的学习曲线。

React 中的 Sass 和 CSS 样式表

## Sass ( [语法上令人敬畏的样式表](https://blog.logrocket.com/a-beginners-guide-to-programming-for-css-with-sass/))是一个 CSS 预处理器，具有许多功能，用于[创建可重用的样式](https://blog.logrocket.com/how-to-write-reusable-css-with-sass/)，嵌套功能，以及组织 CSS 声明。使用 Sass 或 CSS 样式表是基于在外部文件中编写样式并将其导入到需要它的组件中。

总的来说，Sass 是关于编写具有额外好处的标准 CSS 的。同样，你可以使用 [Sass 或者 SCSS](https://blog.logrocket.com/the-definitive-guide-to-scss/) 语法来编写你的风格。

本指南使用 SCSS 语法来演示其在待办事项应用程序中的用法。

首先，使用`npm install node-sass`命令将`node-sass` dev 依赖项添加到项目中。另外，将你的`.css`文件更新为`.scss`文件扩展名。

在下面的代码中，你会看到 CSS 和 SCSS 语法的混合。SCSS 引擎允许编写普通的 CSS，同时还利用了它的附加功能:

接下来，将样式声明导入到组件中。因此，在`AddTodo`文件中，导入`todo.scss`文件的内容，然后您可以访问您的样式，如下所示:

```
/** todo.scss file **/

// declare global variables
$paddingVertical: 10px;
$paddingHorizontal: 20px;
$text-center: center;
$text-white: #ffffff;
$text-black: #000000;
$text-red: rgb(185 28 28);
$font-size: 16px;
.error {
  color: $text-red;
}
.container{
  display:flex;
  flex-direction: column;
}
.flex-center {
display: flex;
justify-content: center;
align-items: center;
}
%typo-large {
  padding: $paddingVertical $paddingHorizontal;
  text-align: $text-center;
}
.h2 {
 @extend %typo-large;
 color: $text-white;
}

.label {
 @extend %typo-large;
}

```

以下是上述示例的 CodeSandbox 游戏场:

```
/** AddTodo.js file**/

//...imports omitted for brevity

// Import the styles
import "./todo.scss";

function AddTodo() {
 //...code omitted for brevity
return (
    <div className="container">
      <h2 className="h2">TODO</h2>
      <div className="flex-center">
        <label className="label" htmlFor="new-todo">
          What needs to be done?
        </label>
      </div>
      <div className="flex-center">
        <form onSubmit={handleSubmit}>
          <input onChange={onChange} value={task} ref={inputRef} />
          <button>Add </button>
        </form>
      </div>
      {message && (
        <div className="flex-center">
          <h4 className="error">{message}</h4>
        </div>
      )}
    </div>
  );
}

```

利弊

让我们考虑一下 Sass 和 CSS 样式表的优缺点。

### 优点:

Sass 和 CSS 样式表具有广泛的动态设计用户界面的特性，并且拥有大量的用户和支持。Sass 和 CSS 样式表允许您轻松地与标准 CSS 代码集成。

### 缺点:

不幸的是，学习 Sass 和 CSS 样式表有一个陡峭的学习曲线，Sass 和 CSS 样式表需要在构造和组织您的 SCSS 代码方面付出一些努力。

### React 中风格的最佳方式是什么？

虽然许多库和 CSS 引擎都提供了在 React 应用程序中进行样式化的方法，但需要注意的是，这并不是一种适合所有人的最佳 React 样式化解决方案。

## 有鉴于此，在寻找设计 React 应用程序的最佳方式时，几个因素在不同的软件团队中有所不同。以下是一些注意事项:

性能指标是什么？

优化你的代码有多容易？

*   需要设计系统吗？
*   CSS 系统需要额外的努力来定制你的 UI 吗？
*   在总结 React 风格的最佳方式之前，您需要回答这些问题。最终，团队在选择最适合产品需求的 CSS 解决方案时扮演了重要角色，同时考虑了团队的专业技能和领域知识。
*   现在，显而易见的是，对于选择最好的库、框架或系统来设计用 React 构建的网站没有直接的答案。然而，本文概述了在 React 中进行样式化的五种优秀方法。

走下去，去探索适合你或者你的团队的；您可能会满足于本文中讨论的任何 CSS 解决方案。

结论

所以，我们有了它 React 中的五种样式。一般来说，所有的方法都是有用的，这取决于项目的大小；你可以用任何一个。

## 如果您有任何问题或反馈，请在下面留下评论。

[LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

## .

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。

The LogRocket Redux middleware package adds an extra layer of visibility into your user sessions. LogRocket logs all actions and state from your Redux stores.

Modernize how you debug your React apps — [start monitoring for free](https://lp.logrocket.com/blg/react-signup-general).