# 撰写惯用类型脚本的 3 条规则

> 原文：<https://blog.logrocket.com/writing-idiomatic-typescript/>

打字稿:爱它或恨它，你不能否认它像野火一样蔓延的事实。事实上，根据 Stack Overflow 2019 开发者调查，它被列为[第三最](https://insights.stackoverflow.com/survey/2019#technology-_-most-loved-dreaded-and-wanted-languages)[—](https://insights.stackoverflow.com/survey/2019#technology-_-most-loved-dreaded-and-wanted-languages)[最喜爱的编程语言](https://insights.stackoverflow.com/survey/2019#technology-_-most-loved-dreaded-and-wanted-languages)和第四最想要的。

现在，这项调查是否准确地代表了真实世界的情绪还有待讨论，但事实仍然是，TypeScript 就在这里，而且已经存在。它现在如此普及，以至于它已经取代了普通的 JavaScript，成为 JS 生态系统中许多包的首选语言，有些像 [Yarn 甚至用 TypeScript](https://github.com/yarnpkg/yarn/issues/6953) 重写了他们的整个代码库。

我觉得这种迅速成功的原因之一是， [TypeScript](https://blog.logrocket.com/set-up-a-typescript-gatsby-app/) 本质上就是 JavaScript。这使得现有 JavaScript 开发人员的入门门槛降低了很多，而且它是类型化的这一事实也可能吸引其他喜欢类型化语言提供的特性的开发人员。

这也是有利有弊的，因为使用 TypeScript 的便利性导致了这种语言没有得到应有的有效利用。许多开发人员仍然像编写 JavaScript 一样编写 TypeScript，这带来了一些缺点。

我们将看到一些用 TypeScript 编写的真实世界的代码，它们可以被改进以更好地利用该语言的优势。这绝不是一个详尽的列表，我欢迎你在下面的评论部分列出一些你可能已经注意到的。

其中一些例子涉及 React，因为我已经注意到一些实例，其中 [React](https://blog.logrocket.com/10-mistakes-react-developers-make/) 代码可以通过简单地利用一些类型脚本特性来改进，但是这些原则决不局限于 React。让我们开始吧。

*注意:本文中的许多代码片段取自现实世界的项目，并被匿名化以保护* *ir* *所有者。*

## 1.不要忽视接口

让我们从 TypeScript 最有用的特性之一开始:接口。

在 TypeScript 中，[接口](https://www.typescriptlang.org/docs/handbook/interfaces.html)简单地指定了变量的预期形状。就这么简单。让我们看一个简单的界面来说明这一点。

```
interface FunctionProps {
  foo: string;
  bar: number;
}
```

现在，如果定义任何变量来实现`FunctionProps`，它必须是一个带有键`foo`和`bar`的对象。添加任何其他键都将导致 TypeScript 无法编译。让我们看看我的意思。

```
const fProps: FunctionProps = {
  foo: 'hello',
  bar: 42,
}
```

现在我们有了一个对象`fProps`，它正确地实现了`FunctionProps`接口。如果我偏离了界面中指定的形状，比如写`fProps.foo = 100`或删除`fProps.bar`，TypeScript 就会抱怨。`fProps`的形状必须与`FunctionProps`完全匹配，否则会有大麻烦。

```
fProps.foo = true ❌ // foo must be a string
```

既然我们已经解决了这个问题，让我们来看一个例子。采取这种反应功能成分的方法:

```
const renderInputBox = (props, attribute, index) => {
  return (
    <div key={index} className="form-group">
      {renderLabel(attribute)}
      <InputBox
        name={attribute.key}
        value={!!isAssetPropAvailable(props, attribute) && props.inputValue}
        onChange={props.handleInputChange}
        placeholder={`Enter ${attribute.label}`}
      />
    </div>
  );
};
```

如果您正在编写 JavaScript，这完全没问题，但是它没有利用接口。为什么这样不好？如果方法的参数是类型化的，您将无法获得任何 IntelliSense 功能。

此外，您可以很容易地向该方法传递一个不同预期形状的道具，但您并不知道，因为 TypeScript 不会对此有所抱怨。这只是普通的 JS，如果所有的东西都是这样写的话，你还不如从项目中完全消除 TypeScript。

我们如何改进这一点？好吧，看看论点本身，它们是如何被使用的，以及它们的预期形态。

先说`props`。看一下第 7 行，您可以看到它应该是一个带有名为`inputValue`的键的对象。在第 8 行，我们看到另一个名为`handleInputChange`的键被访问，根据上下文，它必须是输入的事件处理程序。我们现在知道了形状道具应该有什么，我们可以为它创建一个界面。

```
interface PropsShape {
  inputValue: string;
  handleInputChange: (event: React.FormEvent): void;
}
```

继续看`attribute`，我们可以用同样的方法为它创建一个接口。看第 6 行。我们正在从它那里访问一个名为`key`的键(提示:它是一个对象)。在第 9 行，我们从它那里访问另一个名为`label`的键，有了这个信息，我们可以继续为它创建一个接口。

```
interface AttributeShape {
  key: string;
  label: string;
}
```

我们现在可以将该方法重写为如下形式:

```
const renderInputBox = (props:PropsShape, attribute:AttributeShape, index:number) => {
  return (
    <div key={index} className="form-group">
      {renderLabel(attribute)}
      <InputBox
        name={attribute.key}
        value={!!isAssetPropAvailable(props, attribute) && props.inputValue}
        onChange={props.handleInputChange}
        placeholder={`Enter ${attribute.label}`}
      />
    </div>
  );
};
```

是不是要写更多的代码？是的。但是想想这样做的好处:

*   无论您在哪里使用这个方法，您都会得到 IntelliSense，并且您可以立即看到它的参数应该是什么样子，而不必看它。
*   您永远不能误用这个方法，因为 TypeScript 不允许您传入形状错误的参数。
*   对方法定义的任何更改——也许`index`现在是一个字符串 TypeScript 将阻止您的代码编译，直到您修复了使用该方法的所有实例。

如果您不关心这些好处，为什么您首先要使用 TypeScript 呢？

## 2.停止滥用`any`

type `any`是一种将现有 JavaScript 项目逐步迁移到 TypeScript 的绝佳方式。这是为什么呢？嗯，如果你输入一个变量为`any`，你是在告诉 TypeScript 跳过类型检查。现在，您可以为该变量分配和重新分配不同的类型，这允许您在必要时选择加入或退出类型检查。

虽然可能有其他使用`any`的情况，比如当你使用第三方 API 并且你不知道会返回什么的时候，但是很有可能过度使用它，实际上，在这个过程中否定了 TypeScript 的优势。

我们来看一个绝对被虐的案例。

```
export interface BudgetRequiredProps {
  categoryDetails?: any[];
  state?: any;
  onInputChange?: (event) => void;
  toggleSubCategory?: (type: any) => any;
  displaySubCategory?: () => any[];
}
```

这个界面让我心碎。`any`有合法的用例，但这不是其中之一。例如，看一下第 2 行，这里我们基本上指定了一个可以保存不同类型内容的数组。这是一个等待爆炸的炸弹，无论我们在哪里绘制`categoryDetails`的地图，我们都没有考虑到它可能包含不同类型的项目。

*注意:如果你需要处理一个包含不同类型元素的数组，考虑使用一个[元组](https://www.typescriptlang.org/docs/handbook/basic-types.html#tuple)。*

3 号线更差。没有理由不知道`state`的形状。就类型检查而言，这整个接口基本上做着和普通 JS 一样的事情，也就是说，什么都不做。这是一个接口误用的极好例子。

如果你曾经在产品代码中编写过这样的界面，我原谅你，但是请不要让它再次发生。现在，我浏览了从中提取该示例的代码库，以查看变量的预期形状，它应该是这样的:

```
export interface BudgetRequiredProps {
  categoryDetails?: CategoryShape[];
  state?: string | null;
  onInputChange?: (event: React.FormEvent) => void;
  toggleSubCategory?: (type: string) => boolean;
  displaySubCategory?: () => CategoryShape[];
}
```

好多了。您可以获得使用 TypeScript 的所有优点，而无需对接口进行太多的更改。现在让我们看看使用`any`实际上有什么意义。

```
export interface WeatherPageProps {
  getCurrentWeatherStatus: (city: string): Promise<any>;
  handleUserUpdate: (userContent: any): Promise<any>;
}
```

为什么这是`any`的有效用例？首先，我们正在使用外部 API。在第 2 行，我们指定了一个向天气 API 发出获取请求的函数，我们不知道响应应该是什么样子；也许它是一个基于特定条件返回动态数据的端点。在这种情况下，将返回类型指定为解析到`any`的承诺是可以接受的。

注意:这不是处理动态数据的唯一方法。您可以在接口中指定来自端点的所有可能值，然后将动态字段标记为可选。

在第 3 行，我们还使用了一个函数，它接收一个内容动态的道具。例如，假设`userContent`来自用户，我们不知道用户可能会键入什么。在这种情况下，将`userContent`写成`any`是完全可以接受的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

是的，`any`类型有一些有效的用例，但是出于对 TypeScript 的热爱，请在不破坏开发人员体验的情况下尽可能地避免它。

## 3.记住索引签名

这是一个非常微妙的错误，我在 React 代码中看到很多，你可能需要映射一个对象并动态访问它的属性。考虑这个例子:

```
const obj = {
  gasoline: 'flammable',
  sauce: 'hot',
  isTypeScriptCool: true,
}

Object.keys(obj).forEach(key => console.log(obj[key])) // 'flammable', 'hot', true
```

上面的例子不会引起普通 JavaScript 的问题，但是在 TypeScript 中却不是这样。

```
interface ObjectShape {
  gasoline: string;
  sauce: string;
  isTypeScriptCool: boolean;
}

const obj: ObjectShape = {
  gasoline: 'flammable',
  sauce: 'hot',
  isTypeScriptCool: true,
}

Object.keys(obj).forEach(key => console.log(obj[key])) // ❌ you can't just do this
```

你不能这么做的原因是因为类型索引。

在 TypeScript 中，您需要通过给接口一个索引签名(即描述我们可以用来索引接口的类型的签名，以及相应的返回类型)来指定接口应该如何被索引。

快速复习:索引一个对象看起来像`obj['sauce']`或`obj.gasoline`。

我们没有告诉 TypeScript 应该有什么索引签名`ObjectShape`,所以它不知道当你像我们在第 13 行那样索引到实现它的对象时该做什么。但是这种担忧会有什么反应呢？

有些情况下，您可能需要遍历组件的状态来获取某些值，如下所示:

```
interface ComponentState {
  nameError: string;
  ageError: string;
  numOfFields: number;
}

this.state: ComponentState = {
  nameError: 'your name is too awesome',
  ageError: 'you seem immortal',
  numOfFields: 2,
}

Object.keys(this.state).forEach(err => this.handleError(this.state[err]));
```

这是 React 中非常常见的操作，但是您可以在第 13 行看到我们是如何遇到问题的。我们正在索引到`this.state`，但是它实现的接口没有索引签名。哎呀。

但那甚至不是我正在谈论的错误，我马上会谈到它。为了修复 TypeScript 抛出的警告，一些开发人员可能会像这样更新状态的接口:

```
interface ComponentState {
  nameError: string;
  ageError: string;
  numOfFields: number;
  [x: string]: any;  // index signature added
}
```

在我们继续之前，值得注意的是，默认情况下，向接口添加索引签名也意味着您将能够向实现它的任何变量添加接口中不存在的新值。

这将成功地消除错误，但现在您引入了一个新的副作用。这相当于告诉 TypeScript，当用一个字符串对`ComponentState`进行索引时，它应该返回一个类型为`any`的值(基本上是所有可能的类型)。如果`this.handleError`除了字符串或数字之外不期望任何东西，这可能会导致问题。

但更重要的是，您现在可以向实现接口的任何变量添加任何类型的新属性，在我们的例子中，这个变量是`this.state`。所以这是有效的:

```
this.state['shouldNotBeHere'] = { bugs: 1, dev: 0 }
```

这就是我所说的错误。但是，我们如何解决它呢？实际上，我们需要注意两件事:

1.  我们希望在对象中指定所有可能的索引返回类型，但仅此而已(no `any`)
2.  我们不希望因为索引而能够向对象添加新值

因此，在大多数情况下，解决我们最初的问题(索引到一个对象，而没有类型脚本抱怨)的正确方法是这样做:

```
interface ComponentState {
  nameError: string;
  ageError: string;
  numOfFields: number;
  readonly [x: string]: string | number;
}
```

好了，这段代码是这么说的:

> 嘿，TypeScript，我希望能够用一个字符串索引这个接口，我应该得到一个字符串或者一个数字。哦，请不要让我给任何实现这个接口的对象添加任何我没有明确指定的东西。

通过简单地指定索引签名返回值，我们能够解决第一个问题，通过将其标记为 readonly，我们能够解决第二个问题。当编写类型脚本代码时，请注意这个微妙的问题。

## 结论

TypeScript 是编写类型安全 JavaScript 的一种很好的方式，但是你必须做得正确。编写 TypeScript 的方式可能只会带来麻烦，而不会带来真正的好处，但是谢天谢地，通过花时间学习这种语言的窍门，这可以很容易地解决。

我希望你能从这篇文章中学到一两件事，如果你有一些例子想分享，请在下面的评论区添加它们，以便其他人可以受益。

再见，编码快乐。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.