# React Native 的 Tamagui:创建更快的设计系统

> 原文：<https://blog.logrocket.com/tamagui-react-native-create-faster-design-systems/>

React Native 系统正逐步向 React Native 和 React Native web 应用程序之间共享代码的解决方案发展。

React Native 生态系统的一个最新成员是 [Tamagui](https://tamagui.dev) ，这是一个 ui 工具包，包括一系列主题、媒体查询和类型化内联样式，以及一个优化编译器。Tamagui 旨在缩小 React Native 和 React Native web 应用程序之间的差距，方法是涵盖应用程序的基本元素，如样式、主题和提供跨平台组件，同时牢记应用程序的性能。

在本文中，我们将了解如何在 React 本机应用程序和 React 本机 Web 应用程序中配置 Tamagui，并在撰写本文时探索它在当前状态下提供的组件。我们开始吧！

## 创建新的 React 本机应用程序

我们将从使用 [Expo CLI](https://docs.expo.dev/workflow/expo-cli/) 创建一个新的 React Native 项目开始，它增强了开发人员在 React Native 生态系统中使用工具的体验，例如，各种模板。选择其中一个模板是我们将在本教程中构建的演示应用程序的一个很好的起点。打开终端窗口并执行以下命令:

```
npx expo init tamagui-app
# after the project directory is created, navigate inside it
cd tamagui-app

```

运行该命令时，会提示您选择一个模板。选择`blank (TypeScript)`，这将用最少的 TypeScript 配置创建一个项目。

## 安装依赖项

在项目目录中导航之后，我们将安装在 React 本地项目中配置 Tamagui 所需的库。首先，从终端运行以下命令:

```
yarn add [email protected] @tamagui/[email protected]

```

由于 Tamagui 仍处于 alpha 版本，所以我使用特定的包版本。但是，当将来发布更稳定的版本时，您可能就不必这样做了。请务必参考[官方文档](https://tamagui.dev/docs/intro/installation)以获取关于安装依赖项的最新信息。

`tamagui`是 UI 工具包，`@tamagui/babel-plugin`是 Babel 插件，它加载在另一个名为`tamagui.config.ts`的文件中定义的设计系统属性。您需要在项目的根目录下创建这个文件，但是现在您可以将它留空。我们稍后将回到它。

Tamagui UI kit 需要使用`@unimodules/core`依赖项来处理 Expo 项目。打开终端窗口并执行以下命令:

```
expo install @unimodules/core

```

接下来，我们需要安装开发依赖项来使用 Tamagui 和 Expo for web。打开终端窗口并执行以下命令:

```
yarn add -D @expo/[email protected] [email protected] [email protected] [email protected]

```

和以前一样，我们已经安装了特定版本的软件包。在`package.json`文件中，您将找到我们安装的依赖项和开发依赖项的以下摘要:

```
{
  "dependencies": {
   "@tamagui/babel-plugin": "^1.0.0-alpha.37",
    "@unimodules/core": "~7.2.0",
    "expo": "~43.0.2",
    "expo-status-bar": "~1.1.0",
    "react": "17.0.2",
    "react-dom": "17.0.2",
    "react-native": "0.64.3",
    "react-native-web": "0.17.5",
    "tamagui": "^1.0.0-alpha.37"
  },
  "devDependencies": {
    "@babel/core": "^7.12.9",
    "@types/react": "~17.0.21",
    "@types/react-native": "~0.66.6",
    "typescript": "~4.5.2",
    "@expo/webpack-config": "^0.16.14",
    "esbuild-loader": "^2.17.0",
    "tamagui-loader": "^1.0.0-alpha.37",
    "thread-loader": "^3.0.4"
  }
}

```

现在我们的依赖项已经安装好了，将`@tamagui/babel-plugin`添加到`babel.config.js`文件中:

```
module.exports = function (api) {
  api.cache(true);
  return {
    presets: ['babel-preset-expo'],
    plugins: [
      [
        '@tamagui/babel-plugin',
        {
          components: ['tamagui'],
          config: './tamagui.config.ts'
        }
    ]
    ]
  };
};

```

在上面的代码片段中，确保为`config`定义的路径是 Tamagui `config`文件的相对路径。`components`包含一系列包含 Tamagui 组件的 npm 模块。对于这个示例应用程序，我们使用 Tamagui 基础组件；因此，我们不需要添加任何进一步的配置。

## 设置 Tamagui 配置

Tamagui 允许您创建主题、定义标记、添加速记等等。但是，它要求您描述所有属性，并在开始为 React 本机应用程序构建组件之前建立设计系统的基础。

为了设置所需的 Tamagui 配置，我们将使用`createTamagui`函数。我们需要定义以下内容:

*   `tokens`:在`theme`和`app`中生成变量
*   `media`:定义可重复使用的响应媒体查询
*   `themes`:定义你的设计主题
*   `shorthands`:允许您定义扩展`style value`道具的按键。例如，您可以为 flex 定义`f`，为`alignItems`定义`ai`，为`justifyContent`定义`jc`，等等。

您可以使用`size`和`space`属性开始设置配置。我们还需要使用带有配置对象的`createFont`函数来设置`defaultFont`，该对象包含字体`family`、`size`、`lineHeight`、`fontWeight`和`letter spacing`的值。

上面所有的值都在`createTokens`函数中使用，它允许您创建令牌，或者在构建时映射到 CSS 变量的变量。`createTokens`函数需要其配置对象中的`size`、`space`、`font`、`color`、`radius`和`zIndex`属性。

下面的代码块包含我为演示应用程序创建的最小配置，具有所有必需的属性:

```
import { createFont, createTokens, createTamagui } from 'tamagui';
const size = {
  0: 0,
  1: 4,
  2: 8
};
const space = {
  ...size,
  '-0': -0,
  '-1': -5
};
const defaultFont = createFont({
  family: 'Arial',
  size: {
    1: 14,
    2: 18,
    3: 22
  },
  lineHeight: {
    1: 15,
    2: 20
  },
  weight: {
    4: '300',
    7: '600'
  },
  letterSpacing: {
    4: 0,
    7: -1
  }
});
const tokens = createTokens({
  size,
  space,
  font: {
    title: defaultFont,
    body: defaultFont
  },
  color: {
    lightPurple: '#EDD2F3',
    darkPurple: '#544179'
  },
  radius: {
    0: 0,
    1: 3,
    2: 5,
    3: 10,
    4: 15,
    5: 20
  },
  zIndex: {
    0: 0,
    1: 100,
    2: 200,
    3: 300,
    4: 400,
    5: 500
  }
});
const shorthands = {
  ai: 'alignItems',
  bg: 'backgroundColor',
  br: 'borderRadius',
  f: 'flex',
  h: 'height',
  jc: 'justifyContent',
  m: 'margin',
  p: 'padding',
  w: 'width',
 lh: 'lineHeight',
  ta: 'textAlign'
} as const;
const media = {
  xs: { maxWidth: 660 },
  gtXs: { minWidth: 660 + 1 },
  sm: { maxWidth: 860 },
  gtSm: { minWidth: 860 + 1 },
  md: { minWidth: 980 },
  gtMd: { minWidth: 980 + 1 },
  lg: { minWidth: 1120 },
  gtLg: { minWidth: 1120 + 1 },
  xl: { minWidth: 1280 },
  xxl: { minWidth: 1420 },
  short: { maxHeight: 820 },
  tall: { minHeight: 820 },
  hoverNone: { hover: 'none' },
  pointerCoarse: { pointer: 'coarse' }
};
const config = createTamagui({
  defaultTheme: 'light',
  shorthands,
  media,
  tokens,
  themes: {
    light: {
      bg: tokens.color.lightPurple
    }
  }
});
type Conf = typeof config;
declare module 'tamagui' {
  interface TamaguiCustomConfig extends Conf {}
}
export default config;

```

## 使用`Tamagui.Provider`

Tamagui 配置提供了一个`[Tamagui.Provider](https://tamagui.dev/docs/intro/configuration#add-provider)`组件，它包装了应用程序中的所有其他组件:

```
import React from 'react';
import Tamagui from './tamagui.config';
export default function App() {
  return <Tamagui.Provider>{/* The rest of your app here */}</Tamagui.Provider>;
}

```

## Tamagui 视图、实用道具和人手不足

在 Tamagui 中，堆栈是创建基于 flex 的布局的核心视图元素。有三种不同类型的堆栈可用，`XStack`、`YStack`和`ZStack`，每一种都意味着不同的轴。

在下面的例子中，`defaultTheme`接受您在`config`文件中定义的主题的值。`XStack`用了几个简称，比如`f`代表`flex`、`ai`代表`alignItems`、`jc`代表`justifyContent`、`bg`代表`backgroundColor`。

`$bg`属性的值也来自于`config`文件，我们已经明确定义了`light`主题的`bg`属性应该有一个特定的颜色值。`YStack`上的`space`的值从`config`文件本身设置为`$2`:

```
import { StatusBar } from 'expo-status-bar';
import React from 'react';
import { YStack, Text, XStack } from 'tamagui';
import Tamagui from './tamagui.config';
export default function App() {
  return (
    <Tamagui.Provider defaultTheme='light'>
      <StatusBar style='dark' />
      <XStack f={1} ai='center' jc='center' bg='$bg'>
        <YStack space='$2'>
          <Text fontSize={20} marginBottom={20}>
            Tamagui
          </Text>
          <Text fontSize={20}>Tamagui</Text>
        </YStack>
      </XStack>
    </Tamagui.Provider>
  );
}

```

您可以进一步为`margin`、`marginBottom`和`padding`等属性定义快捷键，并将它们用作组件中的实用工具，这样您就可以更好地控制自己的样式和主题，如下例所示:

![React Native Tamagui Shorthand Properties](img/016bc52353c035fa3d7060597bf39141.png)

## 让 Tamagui 在网络上工作

现在，我们将利用我们之前安装的`@expo/webpack-config`包，它用于创建定制的 webpack 配置。当运行`expo start --web`命令时，Expo CLI 检查项目在根目录中是否有自定义 webpack 配置。如果项目没有定制的 webpack 配置，Expo 将使用默认配置。

为了创建我们的定制 webpack 配置，我们将首先从终端窗口运行以下命令，这将为您创建一个`config`文件来定制 webpack 配置。当您运行这个命令时，会提示您从几个选项中进行选择。选择`webpack.config.js`选项:

```
expo customize:web

```

接下来，按照 [Tamagui 文档](http://(https://tamagui.dev/docs/intro/installation)的建议，将定制配置添加到`webpack.config.js`文件中。该配置将允许我们在网络上运行`tamagui-loader`:

```
const createExpoWebpackConfigAsync = require('@expo/webpack-config');
module.exports = async function (env, argv) {
  const config = await createExpoWebpackConfigAsync(env, argv);
  // Customize the config before returning it.
  // add TAMAGUI_TARGET = web to defines
  const DefinePlugin = config.plugins.find(
    x => x.constructor.name === 'DefinePlugin'
  );
  DefinePlugin.definitions\['process.env'\]['TAMAGUI_TARGET'] = `"web"`;
  // replace babel-loader with our snackui + esbuild loaders
  const rules = config.module.rules[1].oneOf;
  const ruleIndex = rules.findIndex(x =>
    x.use?.loader?.includes('babel-loader')
  );
  rules[ruleIndex] = {
    test: /\.(mjs|[jt]sx?)$/,
    use: [
      'thread-loader',
      {
        loader: require.resolve('esbuild-loader'),
        options: {
          loader: 'tsx',
          target: 'es2019',
          keepNames: true
        }
      },
      {
        loader: require.resolve('tamagui-loader'),
        options: {
          config: './tamagui.config.ts',
          components: ['tamagui']
        }
      }
    ]
  };
  return config;
};

```

添加配置后，执行命令`yarn web`或`expo start --web`。您将在`[http://localhost:19006/](http://localhost:19006/)`获得以下输出:

![Tamagui Output Display Localhost](img/5a62c2830858305d176349d6e80f06e3.png)

您可以将媒体查询直接整合到应用程序的 UI 元素中。在`tamagui.config.ts`文件中定义媒体查询后，您将使用库提供的一个名为`useMedia`的钩子:

```
import { YStack, Text, XStack, useMedia } from 'tamagui';

```

现在，让我们给`YStack`添加一个背景色。使用媒体查询`md: { minWidth: 980 }`，颜色值将在屏幕的最小宽度上改变。在下面的代码片段中，如果屏幕宽度小于`980`，则`backgroundColor`值设置为`yellow`，否则设置为`red`:

```
export default function App() {
  const media = useMedia();
  return (
    <Tamagui.Provider defaultTheme='light'>
      <StatusBar style='dark' />
      <XStack f={1} ai='center' jc='center' bg='$bg'>
        <YStack
          space='$2'
          width={200}
          height={100}
          p={10}
          bg={media.md ? 'red' : 'yellow'}
        >
          <Text fontSize={media.md ? 32 : 18} marginBottom={20}>
            Tamagui
          </Text>
          <Text fontSize={20}>React Native</Text>
     </YStack>
      </XStack>
    </Tamagui.Provider>
  );
}

```

您可以在下面的示例中看到这方面的直观演示:

![Tamagui Background Color Example](img/01c05b7fbf7209e463c320a3d704fdf9.png)

类似地，`fontSize`属性也基于媒体查询而改变，如示例所示:

![Tamagui Final Example](img/75ae48b58ffadb46f5714c3da63b23f6.png)

## 结论

尽管 Tamagui 仍处于其 [alpha 版本](https://tamagui.dev/docs/intro/releases)，但它提供了超越 React 本地空间中其他 ui 套件的[基准。在本教程中，我们首先看了一下 Tamagui，了解了为可能的不同用例配置它的最佳方式。](https://tamagui.dev/docs/intro/benchmarks)

作为一名开发者，我将密切关注 Tamagui 的发展和成长。从头开始创建一个支持本地和 web 平台的定制设计系统的想法既有趣又有用。我希望你喜欢这个教程！

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)