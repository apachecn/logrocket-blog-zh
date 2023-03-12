# Chakra UI 中的高级技术

> 原文：<https://blog.logrocket.com/advanced-techniques-chakra-ui/>

Chakra UI 是 web 开发人员最好的朋友。Chakra UI 是一个基于组件的库，由可用于构建 web 应用程序的基本构建模块组成，可帮助您“快速构建可访问的 React 应用程序”

Chakra UI 的组件是可访问的并且符合 WAI-ARIA 标准，它的 API 使得定制和主题化组件变得容易。但是这还没有触及它给开发者带来的好处的表面。

我们在本文中的重点是看看我们可以用 Chakra UI 做的一些超越基础的很酷的事情，以及它们如何提高我们作为开发人员的生产力。我准备了一个我们要看的东西的演示。如果你想跟进的话，这里有到文章演示和 [GitHub 回购](https://github.com/nefejames/chakra-logroket-article)的链接。

这篇文章是用 Next.js 编写的。在 React 生态系统中，Chakra UI 的实现是相似的，但是，当涉及到路由等概念时，会有一些差异，当我们在教程中谈到它们时，我会指出这一点。

这是我们将要讨论的内容:

## 创建动态 SVG

在为你的网站创建不同的主题时，你可能想要动态地改变你的 SVG 来适应当前的主题。这意味着为每个主题创建不同版本的 SVG。

虽然这在技术上可行，但 Chakra UI 提供了一种更简洁的方法。我们可以使用 Chakra UI 的`useColorMode`钩子以编程方式改变 SVG 的填充，并使它们动态化。

在我们可以使用`useColorMode`之前，我们需要实现黑暗模式。如果你还没有使用黑暗模式的查克拉用户界面，这个指南可以帮助你。

一旦配置了黑暗模式，我们就可以使用`useColorMode`:

```
import { LightBulb, Moon, Sun, Vercel } from "../svgs";
import { Box, Button, Center, Stack, useColorMode } from "@chakra-ui/react";

export default function SVG() {
  const { colorMode, toggleColorMode } = useColorMode();

  return (
    <Center>
      <Box>
        <Center>
          <Button onClick={toggleColorMode}>
            Switch to {colorMode === "light" ? "Dark" : "Light"}
          </Button>
        </Center>
        <Stack direction={["column", "row"]} spacing="24px" mt={14}>
          <LightBulb colorMode={colorMode} />
          <Moon colorMode={colorMode} />
          <Sun colorMode={colorMode} />
          <Vercel colorMode={colorMode} />
        </Stack>
      </Box>
    </Center>
  );
}

```

在上面的代码片段中，我们导入了`useColorMode`钩子和我们想要动态化的 SVG。我们可以从`useColorMode`访问`colorMode`和`toggleColorMode`。`colorMode`是当前颜色模式，`toggleColorMode`是切换颜色模式的功能。

我们将`toggleColorMode`函数传递给按钮的`onClick`事件处理程序，将`colorMode`传递给 SVG 组件:

```
function lightBulb({ colorMode }) {
  return (
    <svg

      x="0"
      y="0"
      enableBackground="new 0 0 480.8 480.8"
      version="1.1"
      viewBox="0 0 480.8 480.8"
      xmlSpace="preserve"
      width="200px"
    >
      <path
        fill={colorMode === "light" ? "#FFD517" : "#111"}
        d="M317.112 314.4c-22.4 22.4-19.6 67.6-19.6 67.6h-113.6s2.4-45.2-19.6-67.6c-24.4-21.6-40-52.8-40-87.6 0-64 52-116 116-116s116 52 116 116c0 34.8-15.2 66-39.2 87.6z"
      ></path>
      <g fill={colorMode === "light" ? "#210B20" : "#E5E5E5"}>
        <path d="M300.712 417.6c0 6-4.8 10.8-10.8 10.8h-98.8c-6 0-10.8-4.8-10.8-10.8 0-6 4.8-10.8 10.8-10.8h98.4c6 0 11.2 4.8 11.2 10.8zM285.912 462.4c0 6-4.8 10.8-10.8 10.8h-69.2c-6 0-10.8-4.8-10.8-10.8 0-6 4.8-10.8 10.8-10.8h69.2c6 0 10.8 4.8 10.8 10.8z"></path>
      </g>
      <g fill={colorMode === "light" ? "#FFD517" : "#210B20"}>
        <path d="M323.112 318.4c26-23.6 40.8-56.8 40.8-91.6 0-68-55.6-123.6-123.6-123.6s-123.6 55.6-123.6 123.6c0 35.6 15.6 69.6 42

```

在 SVG 组件中，我们访问`colorMode`并根据当前模式有条件地改变路径的填充。这是设置动态 SVG 的一种更干净、更有效的方式。

这样，我们就成功地将 SVGs 动态化了，如下图所示:

![Create Dynamic SVGs with Chakra UI](img/3495d11673b6947c733c53ddb347727f.png)

## 扩展和覆盖查克拉的默认风格

Chakra UI 中的主题遵循[风格的系统主题规范](https://system-ui.com/theme/)方法。我们使用一个主题对象来定义应用程序的调色板、字体、断点、间距等等。

为了扩展或覆盖默认主题中的令牌，我们导入了`extendTheme`函数并添加了我们想要覆盖的键。让我们看看如何扩展主题对象。

首先，我们创建一个`Theme.js`文件，然后在那里为我们的应用程序创建样式定义:

```
import { extendTheme } from "@chakra-ui/react";

const themes = {
  colors: {
    brand: {
      100: "#ff0000",
      80: "#ff1a1a",
    },
  },
};
const theme = extendTheme(overrides);

export default theme;

```

我们通过在主题对象中添加两种新颜色(我们的品牌颜色)来扩展颜色。根据应用程序的设计需求，我们还可以为字体、断点、字体大小、行高等定义样式标记。

为了使用这个添加了扩展样式的新主题对象，我们转到应用程序的根目录，在那里我们设置了`ChakraProvider`:

```
import { ChakraProvider } from "@chakra-ui/react";
import theme from "theme";
import Nav from "Nav";

function MyApp({ Component, pageProps, router }) {
  return (
    <ChakraProvider theme={theme}>
      <Nav />
      <Component {...pageProps} />
    </ChakraProvider>
  );
}
export default MyApp;

```

接下来，我们将定义的主题对象传递给`ChakraProvider`。现在，Chakra 组件可以在整个应用程序中访问品牌颜色。

可能会有这样的情况，你的项目不需要风格扩展，而是覆盖 Chakra 的默认组件风格。

脉轮组件样式包括`baseStyle`、`sizes`、`variants`，以及一个可选的`defaultProps`来表示默认的`size`或`variant`。

下面是组件样式对象的外观:

```
const ComponentStyle = {
  // style object for base or default style
  baseStyle: {},
  // styles for different sizes ("sm", "md", "lg")
  sizes: {},
  // styles for different visual variants ("outline", "solid")
  variants: {},
  // default values for `size` and `variant`
  defaultProps: {
    size: "",
    variant: "",
  },
}

```

让我们覆盖`Button`和`Heading`组件的基本样式。

就像我们在扩展样式时所做的那样，我们创建一个`theme.js`文件:

```
const overrides = {
  components: {
    Button: {
      baseStyle: {
        borderRadius: "none",
      },
      sizes: {
        small: {
          px: 5,
          h: "50px",
          fontSize: "20px",
        },
        medium: {
          px: 7,
          h: "60px",
          fontSize: "25px",
        },
        large: {
          px: 8,
          h: "70px",
          fontSize: "30px",
          borderRadius: "10px",
        },
      },
      variants: {
        primary: {
          bg: "primary",
          color: "#fff",
        },
        secondary: {
          bg: "secondary",
          color: "#fff",
        },
        ghost: {
          bg: "transparent",
          border: "1px solid red",
        },
        primaryGhost: {
          bg: "transparent",
          border: "1px solid",
          borderColor: "primary",
        },
        secondaryGhost: {
          bg: "transparent",
          border: "1px solid",
          borderColor: "secondary",
          _hover: {
            color: "#fff",
            bg: "#BB1415",
          },
        },
      },
    },

    Heading: {
      baseStyle: {
        fontFamily: "Inter",
        fontWeight: "600",
      },
      sizes: {
        small: {
          fontSize: "20px",
        },
        medium: { fontSize: "25px" },
        large: { fontSize: "30px" },
      },
    },
  },
};

const theme = extendTheme(overrides);
export default theme;

```

对于`Button`的`baseStyle`，我们移除了默认的边框半径。对于`Heading`的基本样式，我们改变了它的字体类型和粗细。这些例子用来展示我们如何能超越脉轮组件的默认风格。

能够扩展或覆盖脉轮组件的风格给了我们对界面外观和感觉的精细控制。

我们可以在下图中看到移除了边框半径的按钮:

![Button Border Radius Chakra UI](img/8f5a6507eca72f7d422783603c7105c0.png)

默认情况下，标题现在的字体粗细为 600:

![Default Font Weight in Chakra UI](img/ef2354e38adcf48a50f3b7aeef3a77d7.png)

## Chakra 工厂和第三方组件

[查克拉工厂](https://chakra-ui.com/docs/features/chakra-factory)使第三方组件能够接收查克拉的风格道具。这减少了在将第三方组件与 Chakra UI 集成时创建定制组件包装器的需要。

[该引用](https://github.com/chakra-ui/chakra-ui/blob/main/packages/system/src/system.utils.ts#L9)显示了 Chakra factory 支持的 HTML 元素列表:

```
import Image from "next/image";
import { Box, Center, chakra, HStack, VStack } from "@chakra-ui/react";
import woman from "../public/woman.jpg";

const ChakraNextImage = chakra(Image);

export default function factory() {
  return (
    <Center>
      <HStack spacing={10}>
        <Box width="400px" h="400px" position="relative">
          <ChakraNextImage
            src={woman}
            alt="an img"
            layout="fill"
            objectFit="cover"
            borderRadius="full"
          />
        </Box>
      </HStack>
    </Center>
  );
}

```

在上面的代码片段中，我们将 Next.js `Image`组件与 Chakra UI 集成在一起。然后，我们设置一个`ChakraNextImage`组件，通过它，我们可以将 Chakra 的风格道具传递给`Image`。

## 动画片

你可以使用 Chakra UI 的`keyframes`助手来定义动画。`keyframes` [接受一个 CSS 关键帧](https://blog.logrocket.com/guide-to-css-animation-for-javascript-developers/)定义并返回一个可以在样式中使用的对象:

```
import {
  Box,
  Button,
  Center,
  VStack,
  keyframes,
} from "@chakra-ui/react";
import { LightBulb } from "svgs";

const spin = keyframes`
  from {transform: rotate(0deg);}
  to {transform: rotate(360deg)}
`;

export default function Transition() {
  const spinAnimation = `${spin} infinite 2s linear`;
  return (
    <Center>
      <VStack spacing={20}>
        <Box animation={animation}>
          <LightBulb />
        </Box>
      </VStack>
    </Center>
  );
}

```

上面，我们用`keyframes`助手设置了一个`spin`动画。我们可以通过`animation`道具给脉轮 UI 元素添加动画。

接下来，我们将`spinAnimation`传递给`Box`组件，以便使用 Chakra UI 向我们的应用程序添加动画。

## 带有过渡组件的页面过渡

我们可以在应用程序中添加页面过渡，以增强用户从一个页面导航到另一个页面的体验。我将在这个演示中使用 Next.js，它的路由系统与 React 不同。如果您打算用 React 重新创建这个页面转换，请注意这一点。

现在让我们来看看如何使用 Chakra UI 实现这一点:

```
import { ChakraProvider, ScaleFade } from "@chakra-ui/react";
import theme from "theme";
import Nav from "Nav";

function MyApp({ Component, pageProps, router }) {
  return (
    <ChakraProvider theme={theme}>
      <Nav />
      <ScaleFade
        key={router.route}
        initialScale={0.9}
        in="true"
      >
        <Component {...pageProps} />
      </ScaleFade>
    </ChakraProvider>
  );
}
export default MyApp;

```

为了添加页面过渡，我们使用了 Chakra UI 的过渡组件。在上面的代码片段中，我们使用了`ScaleFade`转换。

为了在用户导航应用程序时让过渡工作，我们需要通知`ScaleFade`当前的路线。我们从 Next.js 路由器对象访问关于当前路由的信息，然后将`route`传递给`ScaleFade`的`key`道具。我们通过`initialScale`道具设置过渡的初始比例，并通过将`in`道具设置为`true`来设置组件渲染时发生的过渡。

## 编写可扩展的脉轮代码

知道 Chakra UI 如何工作是一回事，以可伸缩的方式实现 UI 是另一回事。让我们看看开发人员在工作空间中发现的一个常见场景。

下面的截图是你需要实现的一个项目的 UI 设计的一部分。我们可以用一种不可扩展的方式和一种可扩展的方式来实现这一点。

![Image of three basic blog cards](img/8d31aa6f4e9567f9fc33121350d420a8.png)

让我们从不可伸缩的实现开始。我们从创建一个`BlogCard`组件开始:

```
import { Box, Heading, Text, Button } from "@chakra-ui/react";

export default function BlogCard() {
  return (
    <Box
      borderRadius="sm"
      background="#38383d"
      boxShadow="md"
      _hover={{background: "#42414d" }}
    >
     <Box p={5}>
        <Heading pb={2} color="#00DDFF">
          Blog Title
        </Heading>
        <Text fontSize={["lg", "xl"]}>
          A webshop with focus on storytelling and the high quality products
          created by Andersen-Andersen, a Danish work-wear clothing
          manufacturer.
        </Text>
        <Button mt="5" color="black" bg="#00DDFF" borderRadius="none">
          Read more
        </Button>
      </Box>
    </Box>
  );
}

```

这就完成了任务。但是，它不会随着时间的推移而扩展，尤其是在对初始设计进行更改的情况下。

为什么不会扩展？因为像`Heading`和`Button`的颜色和背景颜色定义这样的东西已经被直接传入，而不是通过主题对象来定义它们。

如果你是一个单独工作的开发人员或者开发一个小的应用程序，你可能能够很容易地跟踪`BlogCard.js`文件并改变颜色和背景，但是随着项目变得越来越复杂，文件越来越多，并且你在你的团队中与多人一起工作，这就不再适用了。

让我们来看看这种设计的可扩展实现。在使用 Chakra UI 时，编写可伸缩代码的一个出发点是始终在`theme.js`文件中为您的项目定义设计标记。

我们已经在本文前面看到了如何扩展和定制主题:

```
const Theme = extendTheme({
  colors: {
    brand: {
      primary: "#00DDFF",
      greyPrimary: "#38383d",
      greySecondary: "#42414d",
    },
  },
});
export default Theme;

```

接下来，我们在`Heading`和`Button`组件中使用品牌颜色:

```
import { Box, Heading, Text, Button } from "@chakra-ui/react";
export default function Card() {
  return (
    <Box
      borderRadius="sm"
      background="brand.greyPrimary"
      boxShadow="md"
      _hover={{ background: "brand.greySecondary" }}
    >
      <Box p={5}>
        <Heading pb={2} color="brand.primary">
        //more code below
        <Button mt="5" color="black" bg="brand.primary" borderRadius="none">
          Read more
        </Button>
      </Box>
    </Box>
  );
}

```

有了这种实现，无论你的代码库变得多么复杂，或者参与项目的人数有多少，你的代码仍然是可伸缩的，因为改变一个设计就像进入你的风格定义所在的`theme.js`文件一样简单。

另一个场景是处理字体大小。假设我们有一个在所有页面上都相同的 H1。我们可以定义样式并将其粘贴到每个页面中，如下所示:

```
<Heading
  fontWeight={600}
  fontSize={"3xl"}
  lineHeight={"110%"}
>
  I am the main h1 page heading 
</Heading>

```

然而，这不仅是不可伸缩的，它也不是干的。

我们还可以将代码抽象成一个`PageHeading`组件，并通过 props 传递标题文本:

```
import { Heading } from "@chakra-ui/react";

export default function PageHeading({ text }) {
  return (
    <Heading
      fontWeight={600}
      fontSize={"3xl"}
      lineHeight={"110%"}
    >
      {text}
    </Heading>
  );
}

```

虽然这是一个更具可伸缩性的实现，但我们最终创建了一个不必要的组件包装器。

最好的方法是在`theme.js`文件中为`h1`定义一个全局样式:

```
const Theme = extendTheme({
  styles: {
    global: {
      h1: {
        fontWeight: "600",
        lineHeight: "110%",
        fontSize: " 3xl",
      },
    },
  },
});

```

为`h1`定义一个全局样式可以让你的代码保持干爽，防止在你的代码库中出现可避免的抽象。

根据项目的特性，您的实现可能会有所不同。但是，您编写的代码应该是可伸缩的。

## 有趣的脉轮组件

### 范围滑块

滑块允许用户从一系列值中进行选择。

Chakra UI 最近发布了一个[范围滑块组件](https://chakra-ui.com/docs/form/range-slider)，并提供了以下组件来组成滑块:

*   `RangeSlider`:为子组件提供功能的根组件
*   `RangeSliderTrack`:表示滑块的最小值和最大值之间的数值范围
*   `RangeSliderFilledTrack`:显示选择值的范围
*   `RangeSliderThumb`:沿滑块轨道拖动时，用于选择滑块值的轨道手柄

现在我们知道了范围滑块是什么，让我们来看看一个基本的实现。

首先，我们实现所需的组件:

```
import {
  RangeSlider,
  RangeSliderTrack,
  RangeSliderFilledTrack,
  RangeSliderThumb,
} from '@chakra-ui/react'

const Home = () => {
  return (
    <RangeSlider
      aria-label={["min", "max"]}
      colorScheme="pink"
      defaultValue={[10, 30]}
    >
      <RangeSliderTrack>
        <RangeSliderFilledTrack />
      </RangeSliderTrack>
      <RangeSliderThumb index={0} />
      <RangeSliderThumb index={1} />
    </RangeSliderTrack>
  );
};

```

`RangeSlider`组件接受以下属性:

*   `aria-label`:为滑块提供一个可访问的标签
*   `defaultValue`:未控制模式下滑块的初始值
*   `min`:滑块的最小允许值。默认设置为`0`
*   `max`:滑块的最大允许值。默认设置为`100`
*   `step`:滑块使用默认步长间隔`1`。我们可以用这个道具改变音程
*   `colorScheme`:改变滑块的颜色
*   `orientation`:将滑块的方向改变为水平或垂直。默认方向是水平的

我已经准备了一个[代码沙盒演示](https://codesandbox.io/s/range-slider-demo-3zh4z)来展示我们如何使用范围滑块。

### 语义标记

Chakra UI 团队最近发布了[语义令牌特性](https://twitter.com/NikolovLazar/status/1486445592729591818?s=20&t=OQByXW29lk3vXpkuzLjeVg)。

语义标记使我们能够定义具有特定名称的主题标记，根据颜色模式、方向改变和其他 CSS 选择器等外部因素，主题标记可以具有不同的值。

在语义标记之前，我们需要在处理黑暗模式时使用`useColorModeValue`钩子:

```
const theme = extendTheme({
    colors: {
        red: {
            200: "#e57373",
            500: "#f44336"
        }
    }
});

function ErrorText() {
    const textColor = useColorModeValue("red.500", "red.200");
    return <Text color={textColor}>An error occured</Text>;
}

```

有了语义标记，我们有了一个更简洁的实现:

```
const theme = extendTheme({
    colors: {
        red: {
            200: "#e57373",
            500: "#f44336"
        }
    },
    semanticTokens: {
        colors: {
            errorTextColor: { default: "red.500", _dark: "red.200" }
        }
    }
});

function ErrorText() {
    return <Text color={errorTextColor}>An error occured</Text>;
}

```

语义标记意味着更少更干净的代码，更少的重复，和改进的 DX。

看看这个沙箱中的[来看看语义标记的作用。](https://codesandbox.io/s/semantic-tokens-41xdw?file=/src/index.tsx)

## 结论

在本文中，我们已经看到了 Chakra UI 提供的一些功能和工具，以及它如何帮助我们做到最好。我们还研究了定义主题对象和创建全局样式如何帮助保持代码的可伸缩性。

我希望这篇文章已经向你展示了一些技巧和诀窍，使你的 Chakra UI 开发体验更加容易和简单。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

## [LogRocket](https://lp.logrocket.com/blg/nextjs-signup) :全面了解生产 Next.js 应用

调试下一个应用程序可能会很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/nextjs-signup)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)](https://lp.logrocket.com/blg/nextjs-signup)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/nextjs-signup)

LogRocket 就像是网络和移动应用的 DVR，记录下你的 Next.js 应用上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

让您调试 Next.js 应用的方式现代化— [开始免费监控](https://lp.logrocket.com/blg/nextjs-signup)。