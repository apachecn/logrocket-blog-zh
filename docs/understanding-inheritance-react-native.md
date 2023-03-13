# 了解 React Native 中的继承

> 原文：<https://blog.logrocket.com/understanding-inheritance-react-native/>

代码可重用性是现代应用程序开发的最强支柱之一；如果不能在保持代码完整性的同时在多个地方重用代码，开发大规模的应用程序将是不切实际的。继承是一个软件编程概念，它支持大范围的代码重用。

在本文中，我们将探索继承，并学习如何在 React Native 中实现它。在本文的后面，我们将讨论一些其他流行的代码重用方法，并看看它们如何对抗继承。我们开始吧！

## 什么是继承？

继承是面向对象编程的基本概念之一。它使一个类能够从另一个类派生并继承其属性和方法。对基类的属性和方法的访问可以通过访问修饰符来控制，并且可以很容易地添加新的属性和方法来实现定制的业务逻辑。

为了更好地理解它，我们来看一个例子。假设您有以下 JavaScript 类:

```
class Car {

 constructor() {
   this.wheels = 4
   this.color = "RED"
 }
  accelerate () {
   // Logic to accelerate here
   console.log("accelerate called") 
 }
  stop () {
   // Logic to stop vehicle
   console.log("stop called")
 }

 honk () {
   // Logic to honk the horn
   console.log("honk!")
 }

 printInfo() {
   console.log("Wheels: " + this.wheels + " Color:  " + this.color)
 }
}

```

假设您想为一个`bus`创建一个类似的类。现在，你知道它可能有相同的加速、停车和鸣笛功能。然而，它会有不同数量的轮子，比如说`8`，可能还有不同的颜色。它还可以执行额外的功能，比如打开乘客门。

下面的代码展示了如何通过继承`Car`的所有属性和方法来轻松实现它:

```
class Bus extends Car {
 constructor() {
   super()
   this.wheels = 8
   this.color = "BLUE"
 }

 openPassengerDoors () {
   // Logic to open passenger doors here
   console.log("openPassengerDoors called")
 }
}

```

`extends`关键字使`Bus`类能够扩展`Car`类的特性并添加自己的特性。你可以自己去看看:

```
// Create new instances of both classes
const car = new Car()
const bus = new Bus()

// Print their properties' values
car.printInfo()
// Output: Wheels: 4 Color:  RED
bus.printInfo()
// Output: Wheels: 8 Color:  BLUE

// Call the accelerate method
car.accelerate()
// Output: accelerate called
bus.accelerate()
// Output: accelerate called

// Call the newly created method
bus.openPassengerDoors()
// Output: openPassengerDoors called

```

使用这种方法，继承使得重用现有逻辑变得简单，而不必重新编写。使用继承的其他一些好处包括:

*   在一个地方编写和更新基本代码的灵活性
*   节省了重新编写相同代码所花费的时间和精力
*   提供类之间关系的清晰结构
*   允许访问修饰符控制子类如何使用继承的数据

## 什么是 React Native？

[React Native](https://reactnative.dev/) 是 2022 年[最流行的混合应用开发框架](https://blog.logrocket.com/optimize-react-native-performance/)之一。基于 JavaScript，React Native 可以帮助您使用相同的代码库快速创建 Android 和 iOS 应用程序。

受另一个用于开发 web 应用程序的流行 JavaScript 框架 React 的启发，React Native 由脸书在 2015 年创建，此后一直开源。React Native 使用组件的概念来搭建和组成复杂的 UI 屏幕。

由于每个应用程序可以包含多达数百个组件，因此将它们组织好并绘制有意义的关系非常重要，这有助于降低维护工作量，并便于将来推送更新。

这就是继承的由来。现在，让我们学习如何在 React Native 中实现继承。

## 在 React Native 中实现继承

与上面的 JavaScript 示例类似，可以使用`extends`关键字在 React Native 中实现继承。为了更好地理解它，我们先举一个例子。下面是一个组件，它显示一个带有所选文本、文本颜色和背景颜色的文本框:

```
import * as React from 'react';
import { Text, View, StyleSheet } from 'react-native';

export default class TextPrompt extends React.Component {
  constructor(props) {
   super()

   // Store the text from props in state
   this.text = props.text

   // Create and store the styles object in state
   this.styles = StyleSheet.create({
       container: {
         alignItems: 'center',
         justifyContent: 'center',
         padding: 12,
         borderRadius: 4,
         backgroundColor: "white"
       },
       paragraph: {
         margin: 2,
         fontSize: 14,
         color: "black",
         fontWeight: 'bold',
         textAlign: 'center',
       },
     }
   );
 }

 render() {
   return (
       <View style={this.styles.container}>
         <Text style={this.styles.paragraph}>
           {this.text}
         </Text>
       </View>
     );
 }
 }

```

您可以在您的`App.js`文件中使用这个组件，如下所示:

```
export default function App() {
 return (
   <View>
     <TextPrompt text="This is a text box"/>
   </View>
 );
}

```

在移动屏幕上，`TextPrompt`看起来像下面的图像:

![Appjs TextPrompt Component View](img/9f8f117adf7acdb53206c52c2584161d.png)

现在，假设您想使用这个文本框向您的用户显示一条错误消息。一个简单的重新设计包括将背景颜色改为红色，将文本颜色改为白色，如下图所示:

![Textprompt Error Message View](img/baa090371a8bf2d4d3d0f26aeab259c9.png)

您可以使用以下代码扩展`TextPrompt`组件来创建另一个名为`ErrorPrompt`的组件:

```
import TextPrompt from "./TextPrompt"
import { StyleSheet } from "react-native"

export default class ErrorPrompt extends TextPrompt {

 constructor (props) {
   super(props)

   // Create and store the styles object in state
   this.styles = StyleSheet.create({
       container: {
         alignItems: 'center',
         justifyContent: 'center',
         padding: 12,
         borderRadius: 4,
         backgroundColor: "red"
       },
       paragraph: {
         margin: 2,
         fontSize: 14,
         color: "white",
         fontWeight: 'bold',
         textAlign: 'center',
       },
     }
   );
}
}

```

您会注意到这个组件没有 render 方法；它从父类继承 render 方法。因此，您不能对原始呈现方法进行更改；您可以完全重用它，也可以从头重写它。

然而，从头重写 render 方法是没有意义的，因为你必须知道`TextPrompt`类的内部设计和结构。因为重写 render 方法是不实际的，所以添加新的状态变量或者对它们进行更改也是没有用的。

这给了我们一个启示，React Native 中的继承并不是一个推荐的或者有用的实践。虽然在大多数编程语言中，继承通过重用代码提供了极大的便利，但由于 React Native 组件的构造方式，它在 React Native 中不起作用。

## 将继承与其他代码重用方法进行比较

那么，如果继承不是重用 React 本机代码的正确方式，那么什么才是呢？让我们看看 React Native 中重用代码的其他一些方法。

### 小道具

Props 是将组件转化为独立的、可重用的 UI 构建块的默认方式。React Native 及其超类 React 建议尽可能使用 props 向组件传递参数，并根据组件的值改变其行为。

属性可以是任何数据类型、数字、字符串、对象等。事实上，我们已经在我们的`TextPrompt`例子中使用了 props 来传递文本值给它。

下面的代码展示了如何配置在您的`TextPrompt`中传递`promptType`，以便在需要时将它变成错误提示:

```
import * as React from 'react';
import { Text, View, StyleSheet } from 'react-native';

export default class TextPrompt extends React.Component {
  constructor(props) {
   super()

   // Store the text from props in state
   this.text = props.text

   // Create and store the styles object in state
   this.styles = StyleSheet.create({
       container: {
         alignItems: 'center',
         justifyContent: 'center',
         padding: 12,
         borderRadius: 4,
         backgroundColor: props.promptType === "error" ? "red": "white"
       },
       paragraph: {
         margin: 2,
         fontSize: 14,
         color: props.promptType === "error" ? "white" : "black",
         fontWeight: 'bold',
         textAlign: 'center',
       },
     }
   );
 }

 render() {
   return (
       <View style={this.styles.container}>
         <Text style={this.styles.paragraph}>
           {this.text}
         </Text>
       </View>
     );
 }
 }

```

您可以在您的`App.js`组件中使用它，如下所示:

```
export default function App() {
 return (
   <View style={styles.container}>
    <TextPrompt text="This is a normal message"/>
  <TextPrompt text="This is an error message!" promptType="error"/>
   </View>
 );
}

```

现在，它看起来将如下所示:

![PromptType TextPrompt Error Result](img/719399820c439c8868c47893d828fd85.png)

Props】使用简单，可以根据你的需要传播到多层组件,并且不会像继承那样将你置于复杂的环境中。

### 作文

解决 React Native 中代码重用问题的另一种方法是使用组合。通过[将实际组件作为道具](https://blog.logrocket.com/solving-prop-drilling-react-apps/)传递给其他组件，合成将道具游戏提升了一个档次。这允许动态创建新的复杂组件。

有一个叫做`children`的特殊属性，允许你将一个组件的标签从空变为非空，这意味着你可以直接在组件的 JSX 标签中传递数据或组件。有两种方法可以使用这个特殊的`children`道具。

### 包含

使用`children` prop，您可以传入一个预先格式化的组件，而不仅仅是文本数据，并使用您现有的组件作为容器。下面的代码显示了如何使用 containment 方法重写上面的示例:

```
import * as React from 'react';
import { Text, View, StyleSheet } from 'react-native';

export default class TextPrompt extends React.Component {
  constructor(props) {
   super()

   // Store the children from props in state
   this.children = props.children

   // Create and store the styles object in state
   this.styles = StyleSheet.create({
      container: {
         alignItems: 'center',
         justifyContent: 'center',
        padding: 12,
         margin: 4,
         borderRadius: 4,
         backgroundColor: "white"
      },
       paragraph: {
         margin: 2,
         fontSize: 14,
         color: "black",
        fontWeight: 'bold',
         textAlign: 'center',
       },
     }
   );
 }

 render() {
   return (
       <View style={this.styles.container}>
         <Text style={this.styles.paragraph}>
           {this.children}
         </Text>
       </View>
     );
 }
 }

```

下面是你如何在你的`App.js`中使用它:

```
export default function App() {
 return (
   <View style={styles.container}>
   <TextPrompt>
       This is a normal message
    </TextPrompt>
    <TextPrompt>
       <Text style={styles.error}>
         This is an error message!
       </Text>
    </TextPrompt>
   </View>
 );
}

```

您会注意到第二个`TextPrompt`现在接受了一个`Text`组件，而不是一个简单的字符串。这使您能够将一段有样式的文本传递给`TextPrompt`组件，如下所示:

![Textprompt Takes Text Component](img/7e2d1af1725c054486fc5bd8194a05dc.png)

我们必须重新设计错误信息，使其在白色背景上显示红色文本，而不是在红色背景上显示白色文本。当通过组合传递自定义组件时，您无法控制接收组件的属性。

它使用`props.children`将你的组件呈现在你要求的地方。这是这种方法的一个显著特征，建议不要在需要基于其子代修改容器行为的地方使用 containment。然而，如果你想构建可重用的、多用途的容器，包容是一个很好的方法。

### 专门化

使用组合的另一种方法是通过重用现有的组件来构建专门化的组件。

在本文中，我们讨论了文本提示和错误提示。虽然文本提示是一个通用组件，可用于显示任何类型的文本，但错误提示是它的一个专用版本，仅用于显示错误。因此，错误提示是专门化组件的完美例子。下面是如何在上面的例子中实现专门化。

`TextPrompt`将如下所示:

```
import * as React from 'react';
import { Text, View, StyleSheet } from 'react-native';
export default class TextPrompt extends React.Component {
 constructor(props) {
  super()
  // Store the children from props in state
  this.children = props.children
   // Create and store the styles object in state
  this.styles = StyleSheet.create({
      container: {
        alignItems: 'center',
        justifyContent: 'center',
        padding: 12,
        margin: 4,
        borderRadius: 4,
        // Instead of relying on any internal calculations, the background color is now directly controlled via props
        backgroundColor: props.backgroundColor || "white"
      },
      paragraph: {
        margin: 2,
        fontSize: 14,
        // Instead of relying on any internal calculations, the text color is now directly controlled via props
        color: props.textColor || "black,
        fontWeight: 'bold',
        textAlign: 'center',
      },
    }
  );
}
render() {
  return (
      <View style={this.styles.container}>
        <Text style={this.styles.paragraph}>
          {/* We're still using composition, so children will be rendered as they're passed in*/}
          {this.children}
        </Text>
      </View>
    );
}
}

```

现在，您将创建一个名为`ErrorPrompt`的新的专门化组件，它在内部重用`TextPrompt`:

```
import React from "react"
import TextPrompt from "./TextPrompt"

export default class ErrorPrompt extends React.Component {

 constructor (props) {
   super()
   // Store the children in state
   this.children = props.children
 }

 render() {
   // Reuse the TextPrompt component to render a specialized ErrorPrompt
   return <TextPrompt textColor="white" backgroundColor="red">{this.children}</TextPrompt>
 }
}

```

现在，它变得非常容易使用:

```
export default function App() {
 return (
   <View style={styles.container}>
    <TextPrompt>
      This is a normal message
    </TextPrompt>
    <ErrorPrompt>
       This is an error message!
    </ErrorPrompt>
   </View>
 );
}

```

注意现在使用`error`组件是多么简单！调用环境不需要担心`error`提示符如何工作的内部细节，它看起来恰到好处:

![Specialized Errorprompt Component Reuse Text](img/4f33e21e7708fc0d4c887b88b88de8f5.png)

当涉及到为某些固定场景重用代码时，专门化的组件是很棒的。然而，确定何时以及何时不选择专用组件需要开发人员的一些经验和专业知识。

## 最后的想法

继承是一个伟大的 OOP 概念，可以在保持代码完整性的同时重用代码。然而，由于 React 原生组件的结构，当谈到 React 原生时，继承并不是完美的选择。

在本指南中，您了解了如何在 React Native 中实现继承。我们还探讨了它的缺点，然后回顾了重用代码同时避免继承带来的复杂性的一些替代方法，如道具、组合、包容和专门化。

我希望你喜欢这篇文章。请留下评论，让我知道您更喜欢使用哪种方法来重用 React 本机代码。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)