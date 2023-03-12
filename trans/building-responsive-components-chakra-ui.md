# 在 Chakra UI 中构建响应组件

> 原文：<https://blog.logrocket.com/building-responsive-components-chakra-ui/>

## 介绍

用户通过不同的设备与网络互动，从电话、笔记本电脑到智能手表和 AR/VR 耳机。作为开发人员，我们必须确保我们创建的网站和 web 应用程序不仅仅是高性能的和功能性的，而且对所有屏幕尺寸都有响应。

[Chakra UI](https://blog.logrocket.com/advanced-techniques-chakra-ui/) 是一个简单的、模块化的、易于扩展的组件库，由基本构建模块组成，使我们能够构建 web 应用程序的前端。查克拉用户界面是可定制的，完全可访问的，可重复使用的，易于使用。它还附带了一些有用的钩子，比如`useColorMode`钩子，我们可以用它来给我们的应用程序添加黑暗模式。总的来说，Chakra UI 具有许多令人难以置信的功能，使其成为工作的合适工具。

在本文中，我们将学习如何使用 Chakra UI 构建响应式组件，并使用这些知识来构建[这个仪表板应用程序](https://next-chakra-dashboard.vercel.app/)。

## 先决条件

本文的重点不是向读者介绍 Chakra UI，而是展示如何用这个伟大的工具构建响应式组件。虽然没有严格要求，但有使用 Chakra UI 的经验将是有利的。

## Chakra UI 的响应式网页设计方法

在编写响应式 CSS 时，开发人员可以在移动优先和桌面优先两种方法之间进行选择。Chakra UI 采用移动优先的方法，使用`@media(min-width)`媒体查询。

Chakra UI 中的响应样式依赖于在`theme`对象中定义的断点。默认情况下，Chakra UI 的`theme`对象带有以下断点:

```
 //Breakpoints for responsive design
{
  sm: "30em",
  md: "48em",
  lg: "62em",
  xl: "80em",
  "2xl": "96em",
}

```

我们可以使用`[createBreakpoints](https://chakra-ui.com/docs/features/responsive-styles)`主题工具从默认断点切换到符合应用程序规范的断点，如下所示:

```
import { createBreakpoints } from "@chakra-ui/theme-tools"

const breakpoints = createBreakpoints({
  sm: "320px",
  md: "768px",
  lg: "960px",
  xl: "1200px",
})

```

虽然我们可以使用`createBreakpoints`来创建自定义断点，但它将在未来中[被弃用。Chakra UI 团队建议我们将断点定义为我们创建的自定义主题对象中的一个对象:](https://github.com/chakra-ui/chakra-ui/blob/main/packages/theme-tools/src/create-breakpoints.ts)

```
import { extendTheme } from "@chakra-ui/react";

const customeTheme = extendTheme({
  colors: {},
  fonts: {},
  fontSizes: {},
  breakpoints: {
    sm: "320px",
    md: "768px",
    lg: "960px",
    xl: "1200px",
  },
});

const theme = extendTheme({ customeTheme });

export default customeTheme;

```

Chakra UI 为创建响应样式提供了两种语法:数组语法和对象语法。这些语法抽象了编写媒体查询的复杂性，并在开发响应式组件时提供了出色的开发人员体验。

以下是这些语法的一个示例:

```
//unresponsive width styles
<Box bg="red.200" w="400px">
  This is a box
</Box>

//responsive width styles using the Array syntax
<Box bg="red.200" w={[300, 400, 500]}>
  This is a box
</Box>

//responsive width style susing the Object syntax
<Box bg="red.200" w={{ base: "300px", md: "400px", lg: "500px" }}>
  This is a box
</Box>

```

对于数组语法，`Box`的宽度转换为:

*   0em 以上 300 像素
*   30em 以上 400 像素
*   48em 以上 500 像素

对于对象语法，`Box`的宽度转换为:

*   0em 以上的“基本”300 像素
*   “md”从 48em 向上
*   62em 以上的“lg”

我们可以看到，除了语法上的差异，数组和对象响应样式定义执行相同的功能。

## 理解`useMediaQuery`挂钩

`[useMediaQuery](https://chakra-ui.com/docs/hooks/use-media-query)`是一个自定义挂钩，用于帮助检测单个媒体查询或多个媒体查询是否单独匹配。它根据我们定义的媒体查询返回一个布尔值。

让我们看看`useMediaQuery`挂钩是如何工作的:

```
import { useMediaQuery } from "@chakra-ui/react"

function Home() {
 const [isMobile] = useMediaQuery("(max-width: 768px)") 
 return (
   <Text>
    {isMobile ? "This is a mobile device" : "This is a desktop device"}
   </Text>
  )
}

```

在上面的代码片段中，我们定义了一个`max-width: 768px`媒体查询并访问了`isMobile`布尔值。接下来，我们根据`isMobile`的值有条件地呈现一些文本。

既然我们已经了解了如何在 Chakra UI 中创建响应风格以及`useMediaQuery`是如何工作的，让我们开始构建我们的仪表板应用程序。

## 创建仪表板布局

对于仪表板，我们将从创建布局开始。仪表板布局由`Sidebar`和`Header`组件组成。

让我们来分解布局的功能和组件，如下所示:

```
import Header from "@components/Header";
import Sidebar from "@components/Sidebar";
import { Box, Drawer, DrawerContent, useDisclosure } from "@chakra-ui/react";

export default function Layout({ children }) {
  const { isOpen, onOpen, onClose } = useDisclosure();
  return (
    <Box minH="100vh" bg="gray.100">
      <Sidebar
        onClose={() => onClose}
        display={{ base: "none", md: "block" }}
      />
      <Drawer
        autoFocus={false}
        isOpen={isOpen}
        placement="left"
        onClose={onClose}
        returnFocusOnClose={false}
        onOverlayClick={onClose}
        size="full"
      >
        <DrawerContent>
          <Sidebar onClose={onClose} />
        </DrawerContent>
      </Drawer>

      {/* Header */}
      <Header onOpen={onOpen} />
      <Box ml={{ base: 0, md: 60 }} p="4">
        {children}
      </Box>
    </Box>
  );
}

```

从[的演示](https://next-chakra-dashboard.vercel.app/)中可以注意到一些关于`Header`和`Sidebar`组件的事情。

首先，有一个汉堡菜单出现在移动设备的标题上。当汉堡被点击时，侧边栏就会滑入视野。最后，当侧边栏滑入视图时，有一个“关闭”图标按钮，单击该按钮会导致侧边栏滑回视图之外。

我们可以使用 Chakra UI 的 [`useDisclosure`](https://chakra-ui.com/docs/hooks/use-disclosure) 钩子来设置这个功能。如上面的代码片段所示，我们从`useDisclosure`访问`isOpen`、`onOpen`和`onClose`。

接下来，我们将`onOpen`函数传递给`Header`，这样我们就可以在汉堡包菜单中使用它。汉堡只能在手机上看到；我们将在本文后面开发`Header`组件时看到这一点。

现在我们来分解一下`Sidebar`组件。我们从上面的代码中看到`Sidebar`用了两次。

第一个`Sidebar`是桌面工具条。我们希望这个侧边栏只在大屏幕设备上显示，所以我们在移动设备上将它的显示设置为“无”，在更大的设备上设置为“阻止”。我们还将`onClose`函数传递给侧边栏，这样我们就可以用它来关闭移动设备上的侧边栏。

第二个`Sidebar`是移动侧边栏。我们使用 Chakra UI 的`[Drawer](https://chakra-ui.com/docs/overlay/drawer)`组件进行设置。侧边栏将会是抽屉的子控件，只有当`isOpen`为`true`时，抽屉才会打开。`isOpen`只有当表头的汉堡被点击时才会为真。

通过这些步骤，我们已经创建了仪表板的布局，并使侧栏具有响应性。

我们可以用来创建响应式侧边栏的另一种方法是利用`useMediaQuery`钩子。通过这种方法，我们创建了一个桌面和一个移动侧边栏，并根据当前屏幕大小有条件地显示它们，如下所示:

```
import { Box, Stack } from "@chakra-ui/layout";
import Header from "./navbar";
import DesktopSidebar from "./DesktopSidebar";
import MobileSidebar from "./DesktopSidebar";
import { useMediaQuery } from "@chakra-ui/media-query";

export default function Layout({ children }) {
  const [isSmallScreen] = useMediaQuery("(max-width: 768px)");
  return (
    <Box>
      <Header />
      <Box>
        <Stack>
          {isSmallScreen ? <MobileSidebar /> : <DesktopSidebar />}
          {children}
        </Stack>
      </Box>
    </Box>
  );
}

```

现在我们已经创建了仪表板布局，让我们在我们的`_app.js`文件中使用它:

```
import Head from "next/head";
import { ChakraProvider } from "@chakra-ui/react";
import Layout from "@layout/index";

function MyApp({ Component, pageProps }) {
  return (
    <>
      <Head>
        <title>Chakra UI Dashboard</title>
      </Head>
      <ChakraProvider>
        <Layout>
          <Component {...pageProps} />
        </Layout>
      </ChakraProvider>
    </>
  );
}
export default MyApp;

```

## 创建`Header`组件

`Header`组件由一个徽标、`UserProfile`组件和一个汉堡菜单组成，我们将用它来切换移动设备上的侧边栏。`UserProfile`由用户的名字、头像和角色组成。`UserProfile`点击时触发下拉菜单。

先说`UserProfile`:

```
import {
  IconButton, Avatar, Box, Flex, HStack, VStack, Text, Menu, MenuButton, MenuDivider,
  MenuItem, MenuList,
} from "@chakra-ui/react";
import { FiChevronDown, FiBell } from "react-icons/fi";

export default function UserProfile() {
  return (
    <HStack spacing={{ base: "0", md: "6" }}>
      <IconButton
        size="lg"
        variant="ghost"
        aria-label="open menu"
        icon={<FiBell />}
      />
      <Flex alignItems="center">
        <Menu>
          <MenuButton
            py={2}
            transition="all 0.3s"
            _focus={{ boxShadow: "none" }}
          >
            <HStack spacing="4">
              <Avatar
                size="md"
                src={
                  "https://images.unsplash.com/photo-1619946794135-5bc917a27793?ixlib=rb-0.3.5&q=80&fm=jpg&crop=faces&fit=crop&h=200&w=200&s=b616b2c5b373a80ffc9636ba24f7a4a9"
                }
              />
              <VStack
                display={{ base: "none", md: "flex" }}
                alignItems="flex-start"
                spacing="1px"
                ml="2"
              >
                <Text fontSize="lg">Ademola Jones</Text>
                <Text fontSize="md" color="gray.600">
                  Admin
                </Text>
              </VStack>
              <Box display={{ base: "none", md: "flex" }}>
                <FiChevronDown />
              </Box>
            </HStack>
          </MenuButton>
          <MenuList fontSize="lg" bg="white" borderColor="gray.200">
            <MenuItem>Profile</MenuItem>
            <MenuItem>Settings</MenuItem>
            <MenuItem>Billing</MenuItem>
            <MenuDivider />
            <MenuItem>Sign out</MenuItem>
          </MenuList>
        </Menu>
      </Flex>
    </HStack>
  );
}

```

这里，我们使用对象语法设置`HStack`组件的间距。对于小屏幕和移动设备，我们移除了`HStack`的间距，对于较大的设备，我们将其设置为`6`。Net 中，我们将`VStack`和`Box`组件的显示在小型设备上设置为`"none"`，在大型设备上设置为`"flex"`。

现在来看一下`Header`:

```
import { IconButton, Flex, Text } from "@chakra-ui/react";
import { FiMenu } from "react-icons/fi";
import UserProfile from "./UserProfile";

export default function Header({ onOpen }) {
  return (
    <Flex
      ml={{ base: 0, md: 60 }}
      px="4"
      position="sticky"
      top="0"
      height="20"
      zIndex="1"
      alignItems="center"
      bg="white"
      borderBottomWidth="1px"
      borderBottomColor="gray.200"
      justifyContent={{ base: "space-between", md: "flex-end" }}
    >
      <IconButton
        display={{ base: "flex", md: "none" }}
        onClick={onOpen}
        variant="outline"
        aria-label="open menu"
        icon={<FiMenu />}
      />
      <Text
        display={{ base: "flex", md: "none" }}
        fontSize="2xl"
        fontFamily="monospace"
        fontWeight="bold"
      >
        Logo
      </Text>
      <UserProfile />
    </Flex>
  );
}

```

通过使用`Flex`组件，我们将`Header`的显示设置为`"flex"`。我们希望根据断点调整`Flex`组件的左边距`ml`。我们在小型设备上将左边距设置为`0`，在大型设备上将左边距设置为`60`。你可以在[文档](https://chakra-ui.com/docs/theming/theme#spacing)中了解更多关于脉轮空间的知识。

我们希望汉堡和标志只在移动设备上可见，所以我们通过他们的`display`道具将它们设置为只在移动设备上显示。

最后，我们将`onClose`函数传递给汉堡菜单的`onClick`事件，以触发移动设备上的侧边栏，就像我们之前解释的那样。

## 创建`Sidebar`组件

组件由一个徽标、一个链接列表和一个“关闭”按钮组成，我们将用它来关闭移动设备上的侧边栏。

让我们开始设置侧栏:

```
import { Box, CloseButton, Flex, Text } from "@chakra-ui/react";
import {
  FiHome,
  FiTrendingUp,
  FiCompass,
  FiStar,
  FiSettings,
} from "react-icons/fi";
import NavLink from "./NavLink";

const LinkItems = [
  { label: "Home", icon: FiHome, href: "/" },
  { label: "Trending", icon: FiTrendingUp, href: "/" },
  { label: "Explore", icon: FiCompass, href: "/" },
  { label: "Favourites", icon: FiStar, href: "/" },
  { label: "Settings", icon: FiSettings, href: "/" },
];

export default function Sidebar({ onClose, ...rest }) {
  return (
    <Box
      transition="3s ease"
      bg="white"
      borderRight="1px"
      borderRightColor="gray.200"
      w={{ base: "full", md: 60 }}
      pos="fixed"
      h="full"
      {...rest}
    >
      <Flex h="20" alignItems="center" mx="8" justifyContent="space-between">
        <Text fontSize="2xl" fontFamily="monospace" fontWeight="bold">
          Logo
        </Text>
        <CloseButton display={{ base: "flex", md: "none" }} onClick={onClose} />
      </Flex>
      {LinkItems.map((link, i) => (
        <NavLink key={i} link={link} />
      ))}
    </Box>
  );
}

```

我们希望侧边栏在手机上采用全屏宽度，所以我们将其基本宽度设置为`"full"`。接下来，我们在更大的设备上将它的宽度设置为`60`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们希望`CloseButton`只在移动设备上可见，所以我们在移动设备上将它的显示设置为`"flex"`，在更大的设备上设置为`"none"`。

我们还将`onClose`函数传递给`CloseButton`，以便在需要时关闭侧边栏。需要使用`CloseButton`来关闭我们之前在`Layout`组件中设置的抽屉。

我们已经创建了侧边栏，它可以响应，但是有一个问题。当我们点击手机上的侧边栏链接时，侧边栏不会滑回视图之外。我们如何解决这个问题，并确保在链接被点击时隐藏侧边栏？我们用 next . js '[router . events API](https://nextjs.org/docs/api-reference/next/router)解决了这个问题。

### Next.js 中的路由器.事件

这个 API 允许我们监听 Next.js 路由器内部发生的不同事件，并对它们做出反应。

让我们更新`Sidebar`组件的代码:

```
import { useEffect } from "react";
import { useRouter } from "next/router";

export default function Sidebar({ onClose, ...rest }) {
  const router = useRouter();

  useEffect(() => {
    router.events.on("routeChangeComplete", onClose);
    return () => {
      router.events.off("routeChangeComplete", onClose);
    };
  }, [router.events, onClose]);

  return (
    <Box
      transition="3s ease"
      bg="white"
      borderRight="1px"
      borderRightColor="gray.200"
      w={{ base: "full", md: 60 }}
      pos="fixed"
      h="full"
      {...rest}
    >
      //other elements of the sidebar go here....
    </Box>
  );
}

```

这里，我们使用`useEffect`钩子来注册`routeChangeComplete`路由器事件。`routeChangeComplete`当路线完全改变时触发，在我们的例子中，当我们点击侧边栏链接时。当`routeChangeComplete`发生时，我们调用`onClose`，这导致侧边栏关闭。

最后，您会注意到我们定义了一个`LinkItems`数组。我们遍历数组，对于数组中的每个元素，我们呈现一个`NavLink`组件。`NavLink`将包含侧栏中每个链接的数据。

让我们来创造`NavLink`:

```
import NextLink from "next/link";
import { Flex, Icon, Text } from "@chakra-ui/react";

export default function NavLink({ link, ...rest }) {
  const { label, icon, href } = link;
  return (
    <NextLink href={href} passHref>
      <a>
        <Flex
          align="center"
          p="4"
          mx="4"
          borderRadius="lg"
          role="group"
          cursor="pointer"
          _hover={{
            bg: "cyan.400",
            color: "white",
          }}
          {...rest}
        >
          {icon && (
            <Icon
              mr="4"
              fontSize="16"
              _groupHover={{
                color: "white",
              }}
              as={icon}
            />
          )}
          <Text fontSize="1.2rem">{label}</Text>
        </Flex>
      </a>
    </NextLink>
  );
} 

```

`NavLink`没有响应样式。在这里，我们传入标签、图标和 URL，这些都是我们在映射`LinkItems`数组时得到的。

## 创建主页视图

现在我们已经设置了仪表板布局及其组件，让我们来看看主页的构建模块:

```
import { useState } from "react";
import { cardVariant, parentVariant } from "@root/motion";
import ProductModal from "@components/ProductModal";
import { motion } from "framer-motion";
import data from "@root/data";
import ProductCard from "@components/ProductCard";
import { Box, SimpleGrid } from "@chakra-ui/react";

const MotionSimpleGrid = motion(SimpleGrid);
const MotionBox = motion(Box);

export default function Home() {
  const [modalData, setModalData] = useState(null);
  return (
    <Box>
      <MotionSimpleGrid
        mt="4"
        minChildWidth="250px"
        spacing="2em"
        minH="full"
        variants={parentVariant}
        initial="initial"
        animate="animate"
      >
        {data.map((product, i) => (
          <MotionBox variants={cardVariant} key={i}>
            <ProductCard product={product} setModalData={setModalData} />
          </MotionBox>
        ))}
      </MotionSimpleGrid>
      <ProductModal
        isOpen={modalData ? true : false}
        onClose={() => setModalData(null)}
        modalData={modalData}
      />
    </Box>
  );
}

//sample of product cards array
const data = [
  {
    title: "First Product",
    price: 250,
    img: "https://res.cloudinary.com/nefejames/image/upload/v1593631406/market%20square/clothes/cloth1.jpg",
  },
  {
    title: "Second Product",
    price: 250,
    img: "https://res.cloudinary.com/nefejames/image/upload/v1593631406/market%20square/clothes/cloth2.jpg",
  },
//other product objects below

```

主页视图由两部分组成，`ProductCard`和`ProductModal`。

我们遍历产品数据数组并创建一个`ProductCard`网格。您可以在上面的代码中看到该数组的示例。

当单击产品卡时，我们希望弹出一个填充了产品数据的模态。为此，我们定义了一个`modalData`状态，它将保存被点击产品的数据。

我们将包含产品数据的`product`对象和`setModalData`方法传递给`ProductCard`。

接下来，我们将`isOpen`、`onClose`和`modalData`状态传递给`ProductModal`。当`modalData`保存一个产品的数据时`isOpen`为真。`onClose`方法将`modalData`设置为空。

现在我们已经了解了`ProductCard`和`ProductModal`组件是如何工作的，让我们来创建它们。

## 创建`ProductCard`组件

`ProductCard`组件由上下两部分组成。上半部分是产品图片，下半部分包含产品信息，包括标题、价格和评论数量:

```
import Image from "next/image";
import { Box, Flex, chakra } from "@chakra-ui/react";
import { AiTwotoneStar } from "react-icons/ai";
const ChakraStar = chakra(AiTwotoneStar);

export default function ProductCard({ product, setModalData }) {
  const { img, title, price } = product;

  return (
    <Flex
      w="full"
      h="full"
      alignItems="center"
      justifyContent="center"
      cursor="pointer"
      bg="white"
      rounded="xl"
      shadow="lg"
      borderWidth="1px"
      onClick={() => setModalData(product)}
    >
      <Box w="full" h="full">
        <Box
          w="100%"
          height="200px"
          position="relative"
          overflow="hidden"
          roundedTop="lg"
        >
          <Image
            src={img}
            objectFit="cover"
            alt="picture of a house"
            layout="fill"
          />
        </Box>
        <Box p="6">
          <Box fontWeight="semibold" as="h4" lineHeight="tight" isTruncated>
            {title}
          </Box>
          <Box>${price}</Box>
        </Box>
      </Box>
    </Flex>
  );
}

```

就像我们之前看到的，我们将`product`对象和`setModalData`方法传递给`ProductCard`。当产品被点击时，我们调用`setModalData`方法用产品的数据更新`modalData`状态。

## 创建`ProductModal`组件

`ProductModal`组件由产品的图片、标题和价格组成。它还拥有一个购买按钮来模拟实际电子商务应用程序的购买流程:

```
import Image from "next/image";
import { Box, Modal, ModalOverlay, ModalContent, ModalHeader, ModalFooter, ModalBody,
  ModalCloseButton, Button, useToast, Flex,
} from "@chakra-ui/react";

export default function ProductModal({ isOpen, onClose, modalData }) {
  const { title, price, img } = modalData || {};
  const toast = useToast();

  const handleModalClose = () => {
    toast({
      title: "Purchase successsful.",
      description: "Fashion ++",
      status: "success",
      duration: 3000,
      isClosable: true,
    });
    setTimeout(() => {
      onClose();
    }, 1000);
  };

  return (
    <Modal isOpen={isOpen} onClose={onClose} size="xl">
      <ModalOverlay />
      <ModalContent>
        <ModalCloseButton />
        <ModalHeader>Product Details</ModalHeader>
        <ModalBody>
          <Box w="full" h="full">
            <Flex w="full" h="300px" position="relative">
              <Image src={img} alt="a house" objectFit="cover" layout="fill" />
            </Flex>
            <Box pt="3">
              <Box mt="3" fontWeight="semibold" as="h4" lineHeight="tight" isTruncated>
                {title}
              </Box>
              ${price}
            </Box>
          </Box>
        </ModalBody>
        <ModalFooter>
          <Button
            bg="cyan.700" color="white" w="150px" size="lg" onClick={handleModalClose}
            _hover={{ bg: "cyan.800" }}
          >
            Purchase
          </Button>
        </ModalFooter>
      </ModalContent>
    </Modal>
  );
}

```

就像我们之前看到的，我们将`isOpen`、`onClose`和`modalData`状态传递给`ProductModal`。我们在这里使用查克拉界面的`[Modal](https://chakra-ui.com/docs/overlay/modal)`。

`modalData`包含被点击产品的数据。我们从`modalData`状态访问产品的标题、图片和价格。

我们还定义了一个`handleModalClose`函数，并将其传递给购买按钮。当点击按钮时，显示一个 toast，并调用`onClose`方法。

## 结论

我喜欢使用 Chakra UI，因为它帮助我在构建应用程序的前端时保持高效。不仅 style props 模式使 Chakra UI 成为一种乐趣，而且我也不必处理为不同大小设置媒体查询的复杂性。Chakra UI 旨在提供出色的开发人员体验，并且始终是我的首选组件库。

在本文中，我们学习了如何在 Chakra UI 中编写响应组件。我们学习了`useMediaQuery`和`useDisclosure`钩子是如何工作的，并使用它们来创建一个响应式仪表板。

我们构建的 dashboard 应用程序的源代码可以在 [GitHub](https://github.com/nefejames/next-chakra-dashboard) 上获得。