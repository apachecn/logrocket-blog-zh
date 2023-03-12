# 使用 React Native - LogRocket 博客从头开始构建电子商务应用程序

> 原文：<https://blog.logrocket.com/build-ecommerce-app-from-scratch-with-react-native/>

移动应用程序开发人员处理各种项目，包括客户可以用来在线购买产品的电子商务移动应用程序。世界上智能手机用户的数量每天都在急剧增加，因此，每个产品销售公司都可能希望通过电子商务移动应用程序提供便捷的购买服务。

这些应用程序由几个通用屏幕组成，如产品列表、产品详情页面、购物车、结账区和注册/登录页面。一些移动应用程序也可能有空间用于客户评论、个人设置和许多其他功能。像任何其他企业应用一样，移动应用也需要发布到 Google Play 和苹果应用商店，以获得广泛的客户受众。

## 电子商务反应 GitHub 上的本地应用程序与从头开始构建自己的应用程序

移动应用程序开发人员通常倾向于使用跨平台框架来构建他们的应用程序，以便快速交付项目。这也是 React Native 成为构建跨平台移动应用的流行框架的部分原因——它让开发人员可以在基于 React 的开发环境中轻松构建原生应用。

大多数自由开发者首先寻找开源的 React 原生电子商务应用模板，并根据他们用户的需求对其进行定制。但是 GitHub 上的 React Native 中大多数现有的电子商务应用程序模板都使用了一些复杂的 Redux 来进行集中的状态管理，其中许多项目都包含了您不需要的东西。

考虑到这一点，从头开始创建自己的 React 原生电子商务应用程序模板可能是一个更明智的决定。在本教程中，我将解释如何做到这一点。

## 我们要建造什么？

我们将建立一个具有几个通用(因此可定制)功能的电子商务移动应用程序。该应用程序有以下三个屏幕。

1.  **产品屏幕**
    产品屏幕列出了几个产品，作为应用的主页。每个产品项目都呈现一个标题、价格和图像。![The demo products screen](img/e8da8b61334c4b655bb265ca5cca05e3.png)
2.  **产品详情** **屏幕**
    当用户点击产品页面上的特定产品项目时，应用程序会将用户带到产品详情屏幕。产品详细信息屏幕显示当前产品的所有信息。还有一个按钮可以将产品添加到购物车中；当用户点击**添加到购物车**按钮时，购物车图标的商品计数会更新。![The product details screen](img/13b971ec42ef833e9651c0e8f32dcd4e.png)
3.  **购物车屏幕**
    该屏幕显示添加到购物车的所有产品的摘要。它将显示带有产品名称、数量、小计和总计的产品列表。
    ![The shopping cart screen](img/5cc89f3501340b889e961f10b0979c29.png)

## 使用 React Native 构建电子商务应用程序

让我们创建一个新的 React 本地项目。在本教程中，我将使用 Expo CLI 来构建电子商务应用程序。

您可以使用 React Native CLI 或 Expo CLI 来构建 React Native 应用程序，但如果您使用 React Native CLI，则需要自己设置开发人员环境。例如，如果您需要在 Android 设备上测试您的应用程序，您需要设置所需的 Android SDKs。

另一方面，Expo CLI 允许您开发 React 本地应用程序，而无需在您的计算机上安装任何移动开发 SDK。

### 设置新的 React 本地项目

运行以下命令安装 Expo CLI。确保 Node.js 和 npm 都是较新的 LTS 版本。

注意:如果你正在构建 Ubuntu 操作系统， [nvm](https://github.com/nvm-sh/nvm) 是一个很好的工具，安装它可以跟踪和更新到最新的节点 LTS 版本。

```
 npm install -g expo-cli

```

以下命令将创建一个新的 React 本机项目。

```
expo init e-commerce-app

```

如果它要求模板，请选择空白模板选项。之后，运行以下命令开始开发应用程序。

```
npm start
# or
yarn start 

```

现在，您可以使用 [Expo Go 移动应用](https://expo.dev/client)实时查看您的代码更改。(这个在 Play Store 和 App Store 上都有。)我将使用 Android 设备演示该应用程序。

用手机扫描上述命令给出的二维码，打开你的应用。

### 创建产品列表

我们正在创建一个多屏移动应用程序，产品列表是应用程序屏幕之一。我们可以使用 React 原生导航扩展来构建多屏应用。

用下面的命令安装流行的 [React 导航包](https://github.com/react-navigation)。

```
npm install @react-navigation/native
expo install react-native-screens react-native-safe-area-context
npm install @react-navigation/native-stack

```

产品信息通常是通过 web API 检索的。但是，现在，让我们创建一个模拟 API 服务来获得几个产品并让我们的移动应用程序运行起来。将以下代码添加到`./services/ProductsService.js`文件中。

```
const PRODUCTS = [
    {
        id: 100,
        name: 'ReactProX Headset',
        price: 350,
        image: require('../assets/products/headset-100.jpg'),
        description: 'A headset combines a headphone with microphone. Headsets are made with either a single-earpiece (mono) or a double-earpiece (mono to both ears or stereo).'
    },
    {
        id: 101,
        name: 'FastLane Toy Car',
        price: 600,
        image: require('../assets/products/car-101.jpg'),
        description: 'A model car, or toy car, is a miniature representation of an automobile. Other miniature motor vehicles, such as trucks, buses, or even ATVs, etc. are often included in this general category.'
    },
    {
        id: 102,
        name: 'SweetHome Cupcake',
        price: 2,
        image: require('../assets/products/cake-102.jpg'),
        description: 'A cupcake (also British English: fairy cake; Hiberno-English: bun; Australian English: fairy cake or patty cake[1]) is a small cake designed to serve one person.'
    }
];
export function getProducts() {
    return PRODUCTS;
}
export function getProduct(id) {
    return PRODUCTS.find((product) => (product.id == id));
}

```

正如您所看到的，上述服务将充当模拟 API 服务器来检索产品信息。`getProducts`函数列出所有现有的产品，而`getProduct`函数返回给定产品标识符的产品细节。确保将你的样品图片保存在`assets/products`目录中。

现在我们有了产品数据，我们需要为单个产品条目创建一个可重用的组件，因为产品列表有多个产品。将以下代码添加到`./components/Product.js`中。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
import React from 'react';
import {Text, Image, View, StyleSheet, TouchableOpacity} from 'react-native';
export function Product({name, price, image, onPress}) {
  return (
    <TouchableOpacity style={styles.card} onPress={onPress}>
      <Image
        style={styles.thumb}
        source={image}
      />
      <View style={styles.infoContainer}>
        <Text style={styles.name}>{name}</Text>
        <Text style={styles.price}>$ {price}</Text>
      </View>
    </TouchableOpacity>
  );
}
const styles = StyleSheet.create({
  card: {
    backgroundColor: 'white',
    borderRadius: 16,
    shadowOpacity: 0.2,
    shadowRadius: 4,
    shadowColor: 'black',
    shadowOffset: {
      height: 0,
      width: 0,
    },
    elevation: 1,
    marginVertical: 20,
  },
  thumb: {
    height: 260,
    borderTopLeftRadius: 16,
    borderTopRightRadius: 16,
    width: '100%',
  },
  infoContainer: {
    padding: 16,
  },
  name: {
    fontSize: 22,
    fontWeight: 'bold',
  },
  price: {
    fontSize: 16,
    fontWeight: '600',
    marginBottom: 8,
  },
});

```

当提供了名称、价格和图像属性时，产品组件负责呈现产品。该组件接受打开产品详细信息屏幕的触摸事件回调。您可能已经注意到，还应用了一些样式规则来使产品条目显示为略微圆角的矩形。

### 构建我们的产品列表

既然我们已经完成了产品组件，我们现在可以通过重用产品组件开始构建我们的产品列表屏幕。将以下代码添加到`./screens/ProductsList.js`中。

```
import React, {useEffect, useState} from 'react';
import { View, Text, FlatList, StyleSheet } from 'react-native';
import { Product } from '../components/Product.js';
import { getProducts } from '../services/ProductsService.js';
export function ProductsList ({navigation}) {
function renderProduct({item: product}) {
    return (
      <Product {...product} 
      onPress={() => {
        navigation.navigate('ProductDetails', {
          productId: product.id,
        });
      }}
      />
    );
  }

  const [products, setProducts] = useState([]);

  useEffect(() => {
    setProducts(getProducts());
  });

  return (
    <FlatList
      style={styles.productsList}
      contentContainerStyle={styles.productsListContainer}
      keyExtractor={(item) => item.id.toString()}
      data={products}
      renderItem={renderProduct}
    />
  );
}
const styles = StyleSheet.create({
  productsList: {
    backgroundColor: '#eeeeee',
  },
  productsListContainer: {
    backgroundColor: '#eeeeee',
    paddingVertical: 8,
    marginHorizontal: 8,
  },
});

```

产品列表组件从我们之前创建的模拟 API 服务中获取产品列表数据。之后，它将通过呈现多个产品组件实例来显示产品项目。

在呈现每个产品之前，我们通过`onPress` prop 传递一个导航回调。选择特定产品项目后，导航回调会显示产品详细信息屏幕。

现在，我们的产品列表屏幕准备好了。

### 开发购物车图标和上下文

用户应该能够从产品详细信息屏幕将当前打开的产品添加到购物车。现在，我们需要在产品细节屏幕之前实现购物车的逻辑。

在这个场景中，当用户更新购物车时，我们需要更新购物车摘要图标(在屏幕的右上角)。此外，购物车摘要屏幕还列出了购物车中的商品。

我们必须将购物车数据存储在一个全球位置，并且我们需要从不同的地方更新/检索它们。React 的[上下文 API](https://reactjs.org/docs/context.html) 对于这种场景是一个很好的解决方案，因为它提供了一种简单的方式来拥有全局状态，这与其他状态管理解决方案不同。

用下面的代码为`CartContext.js`中的购物车创建一个上下文。

```
import React, {createContext, useState} from 'react';
import { getProduct } from './services/ProductsService.js';
export const CartContext = createContext();
export function CartProvider(props) {
  const [items, setItems] = useState([]);

  function addItemToCart(id) {
    const product = getProduct(id);
    setItems((prevItems) => {
      const item = prevItems.find((item) => (item.id == id));
      if(!item) {
          return [...prevItems, {
              id,
              qty: 1,
              product,
              totalPrice: product.price 
          }];
      }
      else { 
          return prevItems.map((item) => {
            if(item.id == id) {
              item.qty++;
              item.totalPrice += product.price;
            }
            return item;
          });
      }
    });
}
function getItemsCount() {
      return items.reduce((sum, item) => (sum + item.qty), 0);
  }

  function getTotalPrice() {
      return items.reduce((sum, item) => (sum + item.totalPrice), 0);
  }  

  return (
    <CartContext.Provider 
      value={{items, setItems, getItemsCount, addItemToCart, getTotalPrice}}>
      {props.children}
    </CartContext.Provider>
  );
}

```

上面的`CarProvider`类通过公开购物车的动作为购物车定义了一个 React 上下文。现在，我们可以使用这个上下文实例来添加新的购物车商品，获得商品列表，并获得商品总数。

### 创建产品详细信息屏幕

产品详细信息部分显示当前所选产品的完整信息。此外，它还有**添加到购物车**按钮，用于更新购物车。

将以下代码添加到`./screens/ProductDetails.js`文件中。

```
import React, {useEffect, useState, useContext} from 'react';
import {
  Text, 
  Image, 
  View, 
  ScrollView, 
  SafeAreaView, 
  Button, 
  StyleSheet
  } from 'react-native';
import { getProduct } from '../services/ProductsService.js';
import { CartContext } from '../CartContext';
export function ProductDetails({route}) {
  const { productId } = route.params;
  const [product, setProduct] = useState({});

  const { addItemToCart } = useContext(CartContext);

  useEffect(() => {
    setProduct(getProduct(productId));
  });

  function onAddToCart() {
    addItemToCart(product.id);
  }

  return (
    <SafeAreaView>
      <ScrollView>
        <Image
          style={styles.image}
          source={product.image}
        />
        <View style={styles.infoContainer}>
          <Text style={styles.name}>{product.name}</Text>
          <Text style={styles.price}>$ {product.price}</Text>
          <Text style={styles.description}>{product.description}</Text>
            <Button
            onPress={onAddToCart}
            title="Add to cart"
            / >
        </View>
      </ScrollView>
    </SafeAreaView>
  );
}
const styles = StyleSheet.create({
  card: {
    backgroundColor: 'white',
    borderRadius: 16,
    shadowOpacity: 0.2,
    shadowRadius: 4,
    shadowColor: 'black',
    shadowOffset: {
      height: 0,
      width: 0,
    },
    elevation: 1,
    marginVertical: 20,
  },
  image: {
    height: 300,
    width: '100%'
  },
  infoContainer: {
    padding: 16,
  },
  name: {
    fontSize: 22,
    fontWeight: 'bold',
  },
  price: {
    fontSize: 16,
    fontWeight: '600',
    marginBottom: 8,
  },
  description: {
    fontSize: 16,
    fontWeight: '400',
    color: '#787878',
    marginBottom: 16,
  },
});

```

上面的屏幕根据从导航参数接收到的产品标识符从模拟 API 服务加载产品细节。**添加到购物车**按钮的`onPress`动作然后通过调用`addItemToCart`上下文函数更新购物车上下文。最后，屏幕的内容用`SafeAreaView`和`ScrollView`包装，以支持垂直滚动条来显示不太适合设备视窗的内容。

### 创建购物车摘要屏幕

每位顾客都希望在结账前看到订单摘要。这个电子商务应用程序有一个购物车汇总屏幕，将列出所有购物车的产品名称，数量，小计和最终总额。用下面的代码实现`./screens/Cart.js`中的购物车屏幕。

```
import React, { useEffect, useState, useContext } from 'react';
import { View, Text, Button, FlatList, StyleSheet } from 'react-native';
import { CartContext } from '../CartContext';
export function Cart ({navigation}) {
const {items, getItemsCount, getTotalPrice} = useContext(CartContext);

  function Totals() {
    let [total, setTotal] = useState(0);
    useEffect(() => {
      setTotal(getTotalPrice());
    });
    return (
       <View style={styles.cartLineTotal}>
          <Text style={[styles.lineLeft, styles.lineTotal]}>Total</Text>
          <Text style={styles.lineRight}>$ {total}</Text>
       </View>
    );
  }
function renderItem({item}) {
    return (
       <View style={styles.cartLine}>
          <Text style={styles.lineLeft}>{item.product.name} x {item.qty}</Text>
          <Text style={styles.lineRight}>$ {item.totalPrice}</Text>
       </View>
    );
  }

  return (
    <FlatList
      style={styles.itemsList}
      contentContainerStyle={styles.itemsListContainer}
      data={items}
      renderItem={renderItem}
      keyExtractor={(item) => item.product.id.toString()}
      ListFooterComponent={Totals}
    />
  );
}
const styles = StyleSheet.create({
  cartLine: { 
    flexDirection: 'row',
  },
  cartLineTotal: { 
    flexDirection: 'row',
    borderTopColor: '#dddddd',
    borderTopWidth: 1
  },
  lineTotal: {
    fontWeight: 'bold',    
  },
  lineLeft: {
    fontSize: 20, 
    lineHeight: 40, 
    color:'#333333' 
  },
  lineRight: { 
    flex: 1,
    fontSize: 20, 
    fontWeight: 'bold',
    lineHeight: 40, 
    color:'#333333', 
    textAlign:'right',
  },
  itemsList: {
    backgroundColor: '#eeeeee',
  },
  itemsListContainer: {
    backgroundColor: '#eeeeee',
    paddingVertical: 8,
    marginHorizontal: 8,
  },
});

```

上面的代码从购物车上下文中获取购物车商品，并将它们显示在一个列表中。

### 创建购物车图标

每个电子商务应用程序通常在屏幕右上角的小图标的帮助下显示当前购物车的商品数量。让我们用下面的代码在`./components/CartIcon.js`中创建一个小组件来显示当前购物车的商品总数。

```
import React, { useContext } from 'react';
import { View, Text, StyleSheet } from 'react-native';
import { CartContext } from '../CartContext';
export function CartIcon({navigation}) {
  const {getItemsCount} = useContext(CartContext);
  return (
    <View style={styles.container}>
      <Text style={styles.text} 
        onPress={() => {
          navigation.navigate('Cart');
        }}
      >Cart ({getItemsCount()})</Text>
    </View>
  );
}
const styles = StyleSheet.create({
  container: {
    marginHorizontal: 8,
    backgroundColor: 'orange',
    height: 32,
    padding: 12,
    borderRadius: 32 / 2,
    alignItems: 'center',
    justifyContent: 'center',
  },
  text: {
    color: 'white',
    fontWeight: 'bold',
  },
});

```

### 组装主应用程序的所有组件

现在，所有的应用程序组件都准备好组装成一个电子商务移动应用程序。主应用程序组件需要包含一个导航控制器，因为我们有多个屏幕。此外，我们确实需要用 cart 上下文提供者包装所有组件，因为我们已经使用了 React 上下文 API。

最后一步，用下面的代码替换您的`./App.js`文件的代码。

```
import React from 'react';
import { StyleSheet } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { ProductsList } from './screens/ProductsList.js';
import { ProductDetails } from './screens/ProductDetails.js';
import { Cart } from './screens/Cart.js';
import { CartIcon } from './components/CartIcon.js';
import { CartProvider } from './CartContext.js';
const Stack = createNativeStackNavigator();
function App() {
  return (
    <CartProvider>
      <NavigationContainer>
        <Stack.Navigator>
          <Stack.Screen name='Products' component={ProductsList} 
          options={({ navigation }) => ({
            title: 'Products',
            headerTitleStyle: styles.headerTitle,
            headerRight: () => <CartIcon navigation={navigation}/>
          })}/>
          <Stack.Screen name='ProductDetails' component={ProductDetails} 
          options={({ navigation }) => ({
            title: 'Product details',
            headerTitleStyle: styles.headerTitle,
            headerRight: () => <CartIcon navigation={navigation}/>,
          })} />
          <Stack.Screen name='Cart' component={Cart} 
          options={({ navigation }) => ({
            title: 'My cart',
            headerTitleStyle: styles.headerTitle,
            headerRight: () => <CartIcon navigation={navigation}/>,
          })} />
        </Stack.Navigator>
      </NavigationContainer>
    </CartProvider>
  );
}
const styles = StyleSheet.create({
  headerTitle: {
    fontSize: 20
  }
});
export default App;

```

## 结论和展望

本教程指导您使用 React Native 从头开始构建一个跨平台的电子商务应用程序。我们构建了一个最小的电子商务应用程序模板，没有使用任何状态管理库。因此，很容易用本教程的演示应用程序源代码构建一个个人应用程序模板，并放弃在 GitHub 上查看所有准备好的可能不适合您特定需求的 ecommerce React 原生模板。(另外，如果你需要，你可以把你喜欢的状态管理库集成到这个里面。)

在我的 GitHub 上有完整的源代码。

至于后续步骤，您可以实现诸如身份验证、客户评论、产品评论、库存可用性检查等需求。您还需要规划您的移动应用程序的后端，因为我们在本教程中使用了模拟 API 服务。

如果您计划使用现有的电子商务 API，您可以编写一个服务来进行 API 调用。如果你打算建立一个新的电子商务 API，基于节点的 NoSQL 后端很容易上手。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)

## [LogRocket](https://lp.logrocket.com/blg/ecommerce-signup) :看看用户为什么不完成你的电子商务流程中的一个步骤的技术和 UX 原因。

[![](img/d60d88871d85e76e0dcca90f4bbaf78c.png)](https://lp.logrocket.com/blg/ecommerce-signup)

LogRocket 就像是一个网络和移动应用程序和网站的 DVR，记录你的电子商务应用程序上发生的一切。LogRocket 没有猜测用户不转化的原因，而是主动揭示了阻止你转化的根本原因，比如 JavaScript 错误或死点击。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

开始主动监控您的电子商务应用程序— [免费试用](https://lp.logrocket.com/blg/ecommerce-signup)。