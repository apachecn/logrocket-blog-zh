# React Native 与 Vue 和 Capacitor 的比较

> 原文：<https://blog.logrocket.com/comparing-react-native-vs-vue-capacitor/>

***编者按:**这篇文章于 2022 年 8 月 30 日更新，以解决 Vue Native 已被弃用的移动应用程序开发问题，并反映 React Native 的最新版本。*

随着可用移动应用开发框架数量的增加，2018 年 Vue Native 的引入使开发人员能够使用 Vue.js 代码创建 React 原生移动应用。

然而，Vue 原生团队[在 2021 年 11 月宣布 Vue 原生被弃用](https://github.com/GeekyAnts/vue-native-core/discussions/342)。使用 Vue Native 的开发人员需要决定是通过将他们的应用迁移到 React Native 来继续使用 React Native 生态系统，还是继续在不同的框架中使用 Vue。

在本文中，我们将比较使用 React Native 和使用 Vue 和 Capacitor 进行移动应用开发。我们将涵盖:

## 什么是电容？

[Capacitor 是由 Ionic 团队开发的跨平台工具](https://capacitorjs.com/)。它基本上可以让你把你的网络应用变成一个 iOS 或 Android 应用。

使用 Capacitor，您可以使用 JavaScript 代码创建移动应用程序。然后，它会使用手机的原生 WebView 呈现应用程序。使用 Capacitor 的插件和 API，您可以访问本机功能，如摄像头、扬声器等。

电容器兼容不同的 JavaScript 框架；React、Vue、Angular 和 vanilla JS。了解更多关于[使用 Capacitor 和 Vue](https://blog.logrocket.com/building-cross-platform-apps-with-capacitor-and-vue-js/) 构建跨平台应用的信息。

## 什么是 React Native？

[React Native](https://reactnative.dev/) 基本上就是针对移动应用的 React。它能让你用 React 语法为 Android 和 iOS 创建应用。

您编写的 React 代码与移动设备上的本机 API 进行交互。React Native 为开发人员提供了像`Text`、`Image`和`View`这样的原生组件，作为原生 UI 的构建模块。

React Native 是开源的，由脸书创建和维护。

## 这两个框架有什么共同点？

像 React Native 和 Capacitor 这样的跨平台工具可以为你节省大量的时间和金钱。

这两个框架都消除了学习 Java、Kotlin、Swift 和 Objective C 等本地语言来构建特定平台的移动应用程序的需要。您可以使用相同的代码库为两个平台创建移动应用，而不是用一个代码库构建 Android 应用，用另一个代码库构建 iOS 应用。

这也意味着，构建跨平台应用程序的公司可以只雇佣一个 React Native 或 Capacitor 团队来构建两个版本，而不是需要两个不同的团队——一个负责 iOS，一个负责 Android——从而减少开发人员的数量。

Capacitor 和 React Native 共享一种通用的方法，将定制的本地代码作为模块或插件集成到他们的项目中。在这两个框架中，您都可以用 Java、Kotlin、Objective C 或 Swift 编写定制的本机代码，以访问这些框架没有现成提供的本机特性。

和 React Native 一样，Capacitor 使用了手机的原生功能。主要区别在渲染上。React 本地移动应用程序使用每个设备的本地视图，而 Capacitor 使用设备的本地 WebView 呈现应用程序。

这两个框架都是开源的，任何人都可以贡献和使用它们的源代码。

## 反应原生与电容:功能

在 React Native 中工作时，开发人员可以使用 React 的语法和核心原则来构建原生应用。它通常被称为非个人化的框架，这意味着它附带了[非常少的官方库和功能](https://blog.logrocket.com/react-native-component-libraries/)。

React Native 的创造者更喜欢在构建应用程序和解决不同问题时给予开发人员[自由](https://reactjs.org/docs/add-react-to-a-website.html)，让不想从头编写代码的开发人员使用社区开发的第三方库来构建不同的功能。

这些库包括:

然而，即使有了可以被视为优势的第三方库，这些库也往往会变得过时。如果社区对某个特定库的支持不够强大，并且不经常更新，就会出现不兼容的问题。

[电容器建立在 Cordova](https://blog.logrocket.com/framework7-vs-ionic-comparing-cordova-frameworks/) 之上，并且向后兼容大多数 Cordova 插件。电容器，但是，更现代，更好地维护，而科尔多瓦已被否决。电容器也支持 PWA，比科尔多瓦更快，给你的应用程序更好的启动时间。

即使[电容器是由 Ionic 团队](https://blog.logrocket.com/react-native-vs-ionic/)开发的，你实际上并不需要将 Ionic 与电容器一起使用。电容器兼容任何 JavaScript 框架以及普通 JavaScript。

也就是说，使用 Ionic 和 Capacitor 可以使您的工作更容易，因为 Ionic 可以帮助您实现原生 UI，并为移动开发配置一些必要的工具。

Capacitor 是 web 开发人员构建移动应用程序的最佳选择。它甚至可以从使用 MUI 和 Chakra 等 [React 框架构建的 web 应用程序中生成移动应用程序。你不能用 React Native 做同样的事情；你必须从头开始构建你的应用程序。](https://blog.logrocket.com/definitive-guide-react-material/)

与 React Native 相比，Capacitor 的一个优势是它可以用来创建渐进式 web 应用程序，因为它可以从 web 访问本机 API。与 Xamarin、Cordova 和 NativeScript 等其他跨平台工具相比，Capacitor 也非常轻量级。

如果你是科尔多瓦的粉丝，你应该考虑使用电容器。Ionic 团队对它进行了良好的维护，定期提供问题修复。

## 反应原生与电容:性能

让我们来看看这两个工具的设计理念以及它们之间的区别。

Capacitor 采用基于 web 的方法进行移动开发。它使用设备的本机网络视图在手机上呈现应用程序，并且它带有现成的插件，可以将您的网络代码转换为与设备本机功能交互的 API。

另一方面，使用 React Native，开发人员可以跳过 web 代码，直接进入移动领域。

与使用 WebViews 的混合应用程序不同，React 本机应用程序直接与平台的本机组件进行交互。正因为如此，像 React Native 这样的原生应用通常更快、更高效，因为它们是为运行它们的平台量身定制的。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

使用 WebView 渲染应用程序的工具(如 Capacitor)的一个常见问题是难以渲染动画、CSS 效果和具有渐变的复杂布局——任何复杂或沉重的东西。显示视频也是一个问题。

电容应用可能会在低端设备或旧硬件设备上苦苦挣扎。这是因为通常情况下，在应用程序的用户界面被渲染之前，一些资源必须从网络上加载。

此外，当应用程序不在设备的本机视图上呈现时，它们无法充分利用设备的硬件功能，从而导致性能低下。

使用 Capacitor 进行测试更容易，因为它允许在 web 浏览器中运行应用程序。使用 React Native，[编译、运行和测试应用程序需要安装 Xcode](https://blog.logrocket.com/xcode-for-react-native-developers-tutorial-and-best-practices/) 或 Android Studio，这为编译过程增加了另一个步骤。

虽然你可以通过 Expo 跳过 Xcode/Android Studio 这一步，但 Expo[并非没有限制](https://docs.expo.dev/introduction/why-not-expo/)。

像 Capacitor 这样的混合 WebView 工具可以节省您的成本和大量时间。但是，如果高性能对您非常重要，或者如果您正在构建一个复杂的应用程序，该应用程序可能在廉价设备和带有旧硬件的设备上运行，那么 React Native 可能是一个更好的选择。

React 原生应用可能会更快，性能更好，因为它们被转换为设备的原生语言，并直接与这些设备的原生功能一起工作，而不是在 WebView 中运行。

凭借 GitHub 上超过 2000 名贡献者和不到 700，000 名用户，以及 Stack Overflow 上的大型社区 T2，React Native 拥有开发人员在框架中学习和成长所需的支持。

此外，因为 React Native 基于 JavaScript，是一个跨平台的框架，所以它易于访问，在开发人员中很受欢迎。

React Native 也因为脸书的创造而变得流行起来。脸书目前在其许多应用程序中使用 React Native，并对该框架进行了大量投资。

其他使用 React 原生框架的公司包括:

*   沃尔玛
*   微软
*   特斯拉
*   不调和
*   购物化
*   照片墙

由于电容器仍然是相当新的，没有太多的资源和材料在线供开发人员消费。它在 GitHub 上只有不到 300 名贡献者[，在 Stack Overflow](https://github.com/ionic-team/capacitor) 上只有[一个小社区。不过，它确实有](https://stackoverflow.com/questions/tagged/capacitor)[综合文档](https://capacitorjs.com/)。

目前使用电容器的公司包括:

*   汉堡王
*   大力水手
*   西南的

由于 React Native 存在的时间更长，并且得到了脸书的支持，更多的开发者和大公司使用它，所以它显然在这里取得了胜利。

与其他离子工具一样，电容器是开源的，并得到了 MIT 的许可。然而，Ionic 团队为电容器的企业用户提供付费支持。

通过 Capacitor 的付费支持服务，您可以与 Ionic 团队(包括工程师)进行电话交谈，以解决您的问题，通常在几个小时或几天内，甚至在周末。

如果特优支持是您和您的团队的首要任务，那么电容器可能是您更好的选择。

## 反应原生与电容:学习曲线

React Native 使用 JSX 作为它的模板语言。React Native 不是通过将标记和逻辑放在不同的文件中来分离它们，而是使用单独的组件，这些组件包含标记和属于同一文件中组件的逻辑，这是通过 JSX 实现的。

这种面向组件的方法允许开发人员创建一次组件，并通过结合标记、样式和逻辑，根据需要多次重用它们。

JSX 使创建这些组件变得简单，而且由于它是静态类型的，开发人员可以及早发现错误，提高调试和开发质量。

它还在编译时优化了代码，因此 JSX 生成的 JavaScript 代码比直接用 JavaScript 编写的代码运行得更快。

然而，正因为如此，开发者不能使用 CSS 进行样式化，而[只能使用 JavaScript](https://blog.logrocket.com/react-native-styling-tutorial-with-examples/) 进行样式化。

虽然 JSX 并不特别难，但大多数开发人员使用 HTML 和 CSS 进行标记和样式化，适应这种新范式可能需要一些时间。

下面是 React Native 中 JSX 和造型的一个例子:

```
import React from 'react';
import { Text } from 'react-native';

const Example = () => {
  return (
    <Text style={[ color: 'red' ]}>Example text in React Native!</Text>
  );
}

export default Example;

```

要使用 JSX，开发者必须导入`React`库和任何他们想要使用的 React 原生组件；在这个例子中，用来显示文本的 React 本地组件是`Text`。

`Example`函数返回一行应用了一些基本样式的文本，使文本变成红色，而`Text`标签标记被嵌入到 JavaScript `Example`函数中。这种样式是 JavaScript，不是 CSS。

用`export default`导出组件允许你在任何地方使用组件。

正如你所看到的，开发人员通常必须学习 JSX 和其他工具来开发应用程序。

他们还必须从头开始开发那些移动应用程序。如果已经有可用的网站，则该网站的代码库不能用于创建具有 React Native 的移动应用程序。

事实上，与 React Native 相比，React 网站可以更快地部署到市场上，而 React Native 则需要从头开始构建应用程序。

另一方面，电容器是框架无关的；您可以在任何阶段将它引入到您的项目中。

这意味着您可以从一开始就使用 Capacitor 构建您的 JavaScript 应用程序，并将移动体验作为您的主要关注点，或者您可以将 Capacitor 引入到现有的 web 应用程序中，为现有用户创建移动体验。

您可以通过运行以下命令向现有应用程序添加电容器:

```
npm install @capacitor/cli @capacitor/core

```

接下来，用下面的代码初始化它:

```
npx cap init

```

接下来，添加您正在构建的平台:

```
npm install @capacitor/android
npx cap add android

```

现在，如果您运行`npm run build`，Capacitor 将使用生成的文件来创建您的移动应用程序。

要将 web 应用程序的资源与移动应用程序同步，请运行以下命令:

```
npx cap sync

```

最后，要在您的设备上运行应用程序，请运行以下命令:

```
npx cap open android

```

如果你还没有 web 开发技能，并且你的重点是移动应用而不是网站，那么你可能想跳过电容器，选择 React Native。

当开发者已经拥有 web 开发技能并计划创建一个网站，或者已经为他们正在构建的项目建立了一个网站时，Capacitor 就会大放异彩。使用 React Native，您的网站和移动应用程序不能共享相同的代码库，最终可能不会像您希望的那样一致。

## 反应原生与电容:技能需求

由于 React 在开发人员和企业中的受欢迎程度、上市时间以及脸书的大力支持，开发人员市场对 React 本地技能的需求会大大增加。

## 应该用 Vue 和电容还是 React Native？

这两个跨平台移动应用程序开发框架使用相同的代码，可以由 Android 和 iOS 使用，最终节省时间、金钱和精力，因为不需要开发人员学习跨不同操作系统的新编程语言。

值得注意的是，像就业机会、社区和使用可靠的框架等因素都有利于 React Native。因此，如果你不看重效率，而是看重成熟度和就业市场，那么 React Native 很容易胜出。

然而，如果你只是想用最简单的学习曲线框架，电容器可能更适合你。

您不一定需要将这两个框架视为彼此的替代物。相反，视情况需要，把这两者都看作是你可以使用的工具。

如果你已经有了一个网络应用程序，并且你希望你的移动应用程序能够尽快的发布到商店，那么电容可能是你更好的选择。如果您计划从头开始构建，并且您专注于移动，那么 React Native 可能是您的一个好选择。

关于性能，你的代码的效率和结构在很大程度上决定了你的应用有多快，而不仅仅是你选择哪个框架。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。