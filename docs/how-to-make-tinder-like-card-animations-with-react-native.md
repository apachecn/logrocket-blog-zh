# 如何用 React Native 制作类似 Tinder 的卡片动画

> 原文：<https://blog.logrocket.com/how-to-make-tinder-like-card-animations-with-react-native/>

由于其独创的刷卡机制，Tinder 无疑改变了人们对在线约会的看法。Tinder 是首批大量使用滑动动作来选择最佳匹配的“滑动应用”之一。今天我们将在 [React Native](https://blog.logrocket.com/how-to-build-a-progress-bar-with-react-native/) 中构建一个类似的解决方案。

## 装置

复制这种滑动机制最简单的方法是使用 [`react-native-deck-swiper`](https://github.com/alexbrillant/react-native-deck-swiper) 。这是一个令人敬畏的 npm 包打开了许多可能性。让我们从安装必要的依赖项开始:

```
yarn add react-native-deck-swiper
yarn add react-native-view-overflow
yarn add react-native-vector-icons
```

尽管最新的 React 原生版本(0.60.4，我们在本教程中使用)引入了[自动链接](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md)，但这三个依赖项中的两个仍然需要手动链接，因为在撰写本文时，他们的维护人员还没有将它们更新到最新版本。所以我们必须用传统的方式把它们联系起来:

```
react-native link react-native-view-overflow && react-native-link react-native-vector-icons
```

此外，React Native 版本 0.60.0 和更高版本在 iOS 中默认使用 CocoaPods，因此需要一个额外的步骤来正确安装所有内容:

```
cd ios && pod install && cd ...
```

安装完成后，我们现在可以运行应用程序:

```
react-native run-ios
```

如果您在使用 CLI 运行应用程序时遇到问题，请尝试打开 XCode 并通过它构建应用程序。

## 构建`Card.js`组件

安装完成后，我们有一个模拟器上运行的应用程序，我们可以开始写一些代码！我们将从一个卡片组件开始，它将显示照片和人名。

```
import React from 'react'
import { View, Text, Image, ImageSourcePropType } from 'react-native'
import { shape, string, number } from 'prop-types'
import styles from './Card.styles'
const Card = ({ card }) => (
  <View
    activeOpacity={1}
    style={styles.card}
  >
    <Image
      style={styles.image}
      source={card.photo}
      resizeMode="cover"
    />
    <View style={styles.photoDescriptionContainer}>
      <Text style={styles.text}>
        {`${card.name}, ${card.age}`}
      </Text>
    </View>
  </View>
)

Card.propTypes = { 
  card: shape({
    photo: ImageSourcePropType,
    name: string,
    age: number,
  }).isRequired,
}
export default Card
```

我在这个和我在 React Native 做的每个项目中都使用了`propTypes`。`propTypes`对传递给我们组件的`props`的类型安全帮助很大。每一个错误类型的道具(例如，`string`而不是`number`)都会在我们的模拟器中产生`console.warn`警告。

当对特定的`propType`使用`isRequired`时，我们会在调试控制台中得到一个关于丢失`props`的`error`，这有助于我们更快地识别和修复错误。我真的推荐在我们编写的每个组件中使用来自`prop-types`库的`propTypes`，对正确渲染组件所需的每个道具使用`isRequired`选项，并在`defaultProps`中为每个不需要的道具创建一个默认道具。

### 设计我们的卡片

让我们继续设计`Card`组件的样式。下面是我们的`Card.styles.js`文件的代码:

```
import { StyleSheet, Dimensions } from 'react-native'
import { colors } from '../../constants'
const { height } = Dimensions.get('window')
export default StyleSheet.create({
  card: {
    /* Setting the height according to the screen height, it also could be fixed value or based on percentage. In this example, this worked well on Android and iOS. */
    height: height - 300,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: colors.white,
    borderRadius: 5,
    shadowColor: colors.black,
    shadowOffset: {
      width: 0,
      height: 2,
    },
    shadowRadius: 6,
    shadowOpacity: 0.3,
    elevation: 2,
  },
  image: {
    borderRadius: 5,
    flex: 1,
    width: '100%',
  },
  photoDescriptionContainer: {
    justifyContent: 'flex-end',
    alignItems: 'flex-start',
    flexDirection: 'column',
    height: '100%',
    position: 'absolute',
    left: 10,
    bottom: 10,
  },
  text: {
    textAlign: 'center',
    fontSize: 20,
    color: colors.white,
    fontFamily: 'Avenir',
    textShadowColor: colors.black,
    textShadowRadius: 10,
  },
})
```

我们的卡现在是这样的:

![Styled Card](img/91fad31ba1f08466d69c74c615361dc6.png)

## `IconButton.js`组件

我们的应用程序的第二个组件在一个彩色的圆形按钮内呈现图标，该按钮负责处理用户交互，而不是滑动手势(**如**、**星**和**非**)。

```
import React from 'react'
import { TouchableOpacity } from 'react-native'
import { func, string } from 'prop-types'
import Icon from 'react-native-vector-icons/AntDesign'
import styles from './IconButton.styles'
import { colors } from '../../constants'
const IconButton = ({ onPress, name, backgroundColor, color }) => (
  <TouchableOpacity
    style={[styles.singleButton, { backgroundColor }]}
    onPress={onPress}
    activeOpacity={0.85}
  >
    <Icon
      name={name}
      size={20}
      color={color}
    />
  </TouchableOpacity>
)
IconButton.defaultProps = {
  color: colors.white,
  backgroundColor: colors.heartColor,
}
IconButton.propTypes = {
  onPress: func.isRequired,
  name: string.isRequired,
  color: string,
  backgroundColor: string,
}
export default IconButton
```

### 设计我们的按钮

现在让我们开始造型:

```
import { StyleSheet } from 'react-native'

export default StyleSheet.create({
  singleButton: {
    backgroundColor: 'transparent',
    borderRadius: 50,
    alignItems: 'center',
    justifyContent: 'center',
    shadowColor: 'black',
    shadowOffset: {
      width: 0,
      height: 2,
    },
    shadowRadius: 6,
    shadowOpacity: 0.3,
    elevation: 2,
    padding: 15,
  },
})
```

这三个按钮如下所示:

![Styled Buttons](img/a41396d6fc3d08b901b54a23c722742a.png)

## `OverlayLabel.js`组件

`OverlayLabel`组件是一个简单的`Text`组件，位于具有预定义样式的`View`组件内。

```
import React from 'react'
import { View, Text } from 'react-native'
import { string } from 'prop-types'
import styles from './OverlayLabel.styles'

const OverlayLabel = ({ label, color }) => (
  <View style={[styles.overlayLabel, { borderColor: color }]}>
    <Text style={[styles.overlayLabelText, { color }]}>{label}</Text>
  </View>
)

OverlayLabel.propTypes = {
  label: string.isRequired,
  color: string.isRequired,
}

export default OverlayLabel
```

### 造型`OverlayLabel`

现在是造型:

```
import { StyleSheet } from 'react-native'

export default StyleSheet.create({
  overlayLabel: {
    justifyContent: 'center',
    alignItems: 'center',
    padding: 10,
    borderWidth: 2,
    borderRadius: 10,
  },
  overlayLabelText: {
    fontSize: 25,
    fontFamily: 'Avenir',
    textAlign: 'center',
  },
})
```

这是结果:

![Styled Overlay Demo](img/a8f58cf76dcb8ba4bfcbea7462c9e2d2.png)

## 数据

在创建了这些基本组件之后，我们必须创建一个数组，用对象填充`Swiper`组件，然后才能构建它。我们将使用在 [Unsplash](https://unsplash.com/t/people) 上找到的一些免费的随机照片，我们将把它们放在项目根目录下的`assets`文件夹中。

**T2`photoCards.js`**

```
const photoCards = [
  {
    name: 'Austin Wade',
    age: 22,
    photo: require('../assets/austin-wade-ex6qfO4TPMY-unsplash.jpg'),
    key: 'caseex6qfO4TPMYyhorner',
  },
  {
    name: 'Aleksander Borzenets',
    age: 28,
    photo: require('../assets/aleksander-borzenets-ozda-XbeP0k-unsplash.jpg'),
    key: 'ozda-XbeP0k',
  },
  {
    name: 'Don Delfin Espino',
    age: 29,
    photo: require('../assets/don-delfin-espino-nBywXevf_jE-unsplash.jpg'),
    key: 'nBywXevf_jE-',
  },
  {
    name: 'Eduardo Dutra',
    age: 30,
    photo: require('../assets/eduardo-dutra-ZHy0efLnzVc-unsplash.jpg'),
    key: 'ZHy0efLnzVc',
  },
  {
    name: 'Wesley Tingey',
    age: 21,
    photo: require('../assets/wesley-tingey-TvPCUHten1o-unsplash.jpg'),
    key: 'TvPCUHten1o',
  },
  {
    name: 'Gift Habeshaw',
    age: 26,
    photo: require('../assets/gift-habeshaw-dlbiYGwEe9U-unsplash.jpg'),
    key: 'dlbiYGwEe9U',
  },
  {
    name: 'Henri Pham',
    age: 30,
    photo: require('../assets/henri-pham-Ml4tr2WO7JE-unsplash.jpg'),
    key: 'Ml4tr2WO7JE',
  },
  {
    name: 'Nico Marks',
    age: 24,
    photo: require('../assets/nico-marks-mFcc5b_t74Q-unsplash.jpg'),
    key: 'mFcc5b_t74Q',
  },
  {
    name: 'Sirio',
    age: 28,
    photo: require('../assets/sirio-Ty4f_NOFO60-unsplash.jpg'),
    key: "Ty4f_NOFO60'",
  },
  {
    name: 'Teymi Townsend',
    age: 30,
    photo: require('../assets/teymi-townsend-AvLHH8qYbAI-unsplash.jpg'),
    key: "AvLHH8qYbAI'",
  },
  {
    name: 'Caique Silva',
    age: 20,
    photo: require('../assets/caique-silva-3ujVzg9i2EI-unsplash.jpg'),
    key: "3ujVzg9i2EI'",
  },
  {
    name: 'David Yanutenama',
    age: 21,
    photo: require('../assets/david-yanutama-5AoO7dBurMw-unsplash.jpg'),
    key: "5AoO7dBurMw'",
  },
]
export default photoCards
```

## 最后是`Swiper`组件

一旦我们有了可以使用的带有卡数据的数组，我们就可以实际使用`Swiper`组件了。

首先，我们导入必要的元素并初始化`App`函数。然后，我们使用一个 [`useRef`](https://reactjs.org/docs/hooks-reference.html#useref) 钩子，一部分新的和令人敬畏的 [React 钩子 API](https://blog.logrocket.com/frustrations-with-react-hooks/) 。我们需要这样做，以便通过按下其中一个`handles`功能来强制引用`Swiper`组件。

```
import React, { useRef } from 'react'
import { View, Text } from 'react-native'
import Swiper from 'react-native-deck-swiper'
import { photoCards } from './constants'
import { Card, IconButton, OverlayLabel } from './components'
import styles from './App.styles'
const App = () => {
  const useSwiper = useRef(null).current
  const handleOnSwipedLeft = () => useSwiper.swipeLeft()
  const handleOnSwipedTop = () => useSwiper.swipeTop()
  const handleOnSwipedRight = () => useSwiper.swipeRight()
```

当使用`useRef`钩子时，确保调用实际`ref`(例如，这里的`useSwiper.swipeLeft()`)的函数被包装在一个先前声明的函数(例如，这里的`handleOnSwipedLeft`)中，以避免调用`null object`时出现`error`。

接下来，在返回函数中，我们使用设置为`useSwiper`钩子的 ref 来呈现`Swiper`组件。在`cards`道具中，我们插入我们之前创建的`photoCards`数据数组，并用`renderCard`道具呈现单个项目，将单个`item`传递给`Card`组件。

在`overlayLabels`道具中，当我们向左或向右滑动时，有一些物体可以显示`LIKE`和`NOPE`标签。它们以不透明动画显示，越靠近边缘，越明显。

```
return (
  <Swiper
    ref={useSwiper}
    animateCardOpacity
    containerStyle={styles.container}
    cards={photoCards}
    renderCard={card => <Card card={card} />}
    cardIndex={0}
    backgroundColor="white"
    stackSize={2}
    infinite
    showSecondCard
    animateOverlayLabelsOpacity
    overlayLabels={{
      left: {
        title: 'NOPE',
        element: <OverlayLabel label="NOPE" color="#E5566D" />,
        style: {
          wrapper: styles.overlayWrapper,
        },
      },
      right: {
        title: 'LIKE',
        element: <OverlayLabel label="LIKE" color="#4CCC93" />,
        style: {
          wrapper: {
            ...styles.overlayWrapper,
            alignItems: 'flex-start',
            marginLeft: 30,
          },
        },
      },
    }}
  />
```

在`App.js`组件的最后一部分，我们强制性地呈现了三个处理滑动手势的按钮。通过将名称道具传递给`IconButton`组件，我们正在使用令人敬畏的 [`react-native-vector-icons`](https://github.com/oblador/react-native-vector-icons) 库来呈现好看的 SVG 图标。

```
  <View style={styles.buttonsContainer}>
    <IconButton
      name="close"
      onPress={handleOnSwipedLeft}
      color="white"
      backgroundColor="#E5566D"
    />
    <IconButton
      name="star"
      onPress={handleOnSwipedTop}
      color="white"
      backgroundColor="#3CA3FF"
    />
    <IconButton
      name="heart"
      onPress={handleOnSwipedRight}
      color="white"
      backgroundColor="#4CCC93"
    />
  </View>
```

## 摘要

最终结果是这样的:

你可以在我的 GitHub 中找到本教程的[完整代码。这个 react-native-deck-swiper 组件的使用非常流畅，而且——它确实帮助我们节省了很多时间。此外，如果我们试图从头实现它，我们很可能会使用库作者使用的 React Native 的 PanResponder API。。这就是为什么我真的推荐使用它。我希望你能从这篇文章中学到一些东西！](https://github.com/Karniej/TinderLike)

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)