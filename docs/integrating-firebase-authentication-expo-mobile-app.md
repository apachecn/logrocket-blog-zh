# 将 Firebase 认证集成到世博会移动应用程序中

> 原文：<https://blog.logrocket.com/integrating-firebase-authentication-expo-mobile-app/>

在本教程中，我们将使用 [Expo](https://expo.dev/) 构建一个移动应用程序，它将与 [Firebase](https://firebase.google.com/) 认证模块集成。最终，我们将拥有一款具备以下功能的移动应用:

*   注册和登录屏幕(使用电子邮件和密码)
*   仅限于登录用户的用户主屏幕
*   使用[反应导航](https://reactnavigation.org/)的动态屏幕路由

本教程的源代码可以在 [GitHub](https://github.com/bajcmartinez/expo-with-firebase-auth-demo) 上获得。请通过下面的链接随意跳转到您最感兴趣的部分:

## 使用 Expo 创建 React 本地应用程序

在我们开始 Firebase 集成之前，我们需要用 Expo CLI 设置我们的项目。

如果您的系统上没有安装它，您必须运行:

```
npm install --global expo-cli

```

安装完成后，您可以使用下面的命令创建一个新项目。注意我们的项目名称是`expo-with-firebase-auth-demo`，但是你可以用不同的名称。

```
expo init expo-with-firebase-auth-demo

```

在项目准备就绪之前，Expo 将要求您提供您想要的模板，并将提供以下选项:

![Expo Template](img/afd5f40fb0887381a4499b361a6919b4.png)

我个人一直用`blank (TypeScript)`。你当然可以选择`blank`，但是[为什么不试试 TypeScript](https://livecodestream.dev/post/what-why-and-how-of-typescript-for-javascript-developers/) ？太牛逼了。

做出选择后，您就可以继续安装我们需要的附加软件包了。

对于这些附加库，运行:

```
expo install @react-navigation/native @react-navigation/stack firebase dotenv react-native-elements expo-constants

```

注意，我们特意安装了包`firebase`，它是 JavaScript SDK，而不是`react-native-firebase`。这是由于图书馆对 Expo Go 应用程序的限制。你可以在[世博会官方文件](https://docs.expo.dev/guides/using-firebase/#usage-with-expo)或[消防基地文件](https://rnfirebase.io/#expo)上了解更多信息。

接下来，我们将在 Firebase 上建立云项目。

## 在 Firebase 上设置云项目

首先，您需要一个 Firebase 项目。如果您还没有 Firebase 帐户，请访问[https://firebase.google.com/](https://firebase.google.com/)创建一个。之后，登录到你的 [Firebase 控制台](https://console.firebase.google.com/)，使用“创建项目”按钮创建一个新项目。

![Create Firebase Project](img/2f2a59747178d996cf66944060d17edd.png)

接下来，添加新 Firebase 项目的名称。请注意，它不必与世博会项目名称相同，然后单击**继续**。

![Add Project Name](img/1af66abe12d0d26231400ece1af09469.png)

在这一步，你可以决定是否要加入谷歌分析。尽管它为项目提供了一些有趣的功能，但我们的演示并不需要它，所以我将禁用它。

完成后，点击**创建项目**。

![Google Analytics Note](img/968eed022c8cb5129dda40ced81400fd.png)

进入项目屏幕后，你需要设置一个应用程序。请记住，一个 Firebase 项目可以托管多个应用，包括网站、unity 和移动应用。

点击 **Web** 项目应用程序，创建一个新的 Web 项目。我们必须选择 web 选项而不是本地选项，因为我们使用了 Firebase JS SDK。

![Web Project](img/39bd05a628521d765123d29ec18f9e33.png)

输入应用程序详细信息，并确保 Firebase 主机已禁用。完成后，点击**注册应用**。

![Add Firebase Web App](img/565fffda71a4cb8fabbc2d1f3eacc063.png)

您将在屏幕上收到有关设置配置的说明，包括您的应用程序密钥。这些[密钥不是您的私有密钥](https://firebase.google.com/docs/projects/api-keys)，但是它们是访问后端服务所必需的，因为它们允许您将代码与 Firebase cloud 集成。

让它们在代码中保持清晰不是一个好主意，但是现在，我们不会关注这个。复制给定的代码并保存它。当我们设置环境变量时，我们将回到这个主题。

![Firebase SDK](img/8164a967079a86d1bf733f7bd1ab3aca.png)

现在应用程序已经准备好了，我们可以开始添加 Firebase 服务，比如身份验证。

![Authentication](img/ebf4ddc7302e5fd7f096d0bef6f99bb7.png)

您可以从左侧的 **Build** 菜单或主屏幕上的 **Products** 部分访问**认证**服务。进入后，点击**开始**并选择您喜欢的登录方式。

我们今天的演示将使用电子邮件/密码提供程序，但您可以随意探索更多选项。Firebase 在这里提供了广泛的选择。

![Authentication Options](img/e88d5ea9b4b9c00d9ed70ad5ebbf32b9.png)

一旦选择了一个提供者，只需启用它，如果需要，按照说明进行设置并保存您的更改。

![Sign-in Providers](img/7b7f1ce8ab76f10c423b28f4d4bb6f8d.png)

## 在 Expo 应用程序中配置 Firebase SDK

是时候设置 Firebase SDK 了。为此，我们将把上面提供的 Firebase 设置代码添加到我们的应用程序中。让我们在应用程序源上创建一个名为`config`的文件夹，并添加一个名为`firebase.ts`的文件。

在那里，我们将粘贴配置代码。

```
// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
const firebaseConfig = {
  apiKey: "AIzaSyBSKowWPBCynHTYSFBBnJUqP2pI-pr2GJI",
  authDomain: "expo-with-firebase-auth-demo.firebaseapp.com",
  projectId: "expo-with-firebase-auth-demo",
  storageBucket: "expo-with-firebase-auth-demo.appspot.com",
  messagingSenderId: "719336217671",
  appId: "1:719336217671:web:efd7e14078045dc9a72562"
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);

export default app;

```

但是，如果我们保持原样，我们的密钥和敏感设置可能会暴露。相反，我们将使用环境变量从我们的提交中排除这些信息。

### 使用环境变量保护 Firebase 设置

首先，将配置移动到位于项目根目录下的`.env`文件中:

```
FIREBASE_API_KEY=AIzaSyBSKowWPBCynHTYSFBBnJUqP2pI-pr2GJI
FIREBASE_AUTH_DOMAIN=expo-with-firebase-auth-demo.firebaseapp.com
FIREBASE_PROJECT_ID=expo-with-firebase-auth-demo
FIREBASE_STORAGE_BUCKETt=expo-with-firebase-auth-demo.appspot.com
FIREBASE_MESSAGING_SENDER_ID=719336217671
FIREBASE_APP_ID=1:719336217671:web:efd7e14078045dc9a72562

```

当然，您应该提供您自己项目的值，而不是使用我的。

不要忘记将这个文件添加到您的`.gitignore`中。否则有可能是不小心犯的，推的。

不像我们用 [CRA](https://create-react-app.dev/docs/adding-custom-environment-variables/) 所能做的，在访问这些值之前，Expo 中需要一些额外的配置。

1.  将您的应用程序设置从`app.json`重命名为`app.config.js`。这样，我们就可以访问变量`process`。
2.  导出文件中的 JSON 对象，在它的开头添加`export default`
3.  在文件开头添加`import 'dotenv/config`
4.  添加一个额外的部分来映射我们的环境变量和代码中可访问的常量。新的部分如下:

```
"extra": {
      firebaseApiKey: process.env.FIREBASE_API_KEY,
      firebaseAuthDomain: process.env.FIREBASE_AUTH_DOMAIN,
      firebaseProjectId: process.env.FIREBASE_PROJECT_ID,
      firebaseStorageBucket: process.env.FIREBASE_STORAGE_BUCKET,
      firebaseMessagingSenderId: process.env.FIREBASE_MESSAGING_SENDER_ID,
      firebaseAppId: process.env.FIREBASE_APP_ID
    }

```

最后，您的文件应该如下所示:

```
import 'dotenv/config';

export default {
  "expo": {
    "name": "expo-with-firebase-auth-demo",
    "slug": "expo-with-firebase-auth-demo",
    "version": "1.0.0",
    "orientation": "portrait",
    "icon": "./assets/icon.png",
    "splash": {
      "image": "./assets/splash.png",
      "resizeMode": "contain",
      "backgroundColor": "#ffffff"
    },
    "updates": {
      "fallbackToCacheTimeout": 0
    },
    "assetBundlePatterns": [
      "**/*"
    ],
    "ios": {
      "supportsTablet": true
    },
    "android": {
      "adaptiveIcon": {
        "foregroundImage": "./assets/adaptive-icon.png",
        "backgroundColor": "#FFFFFF"
      }
    },
    "web": {
      "favicon": "./assets/favicon.png"
    },
    "extra": {
      firebaseApiKey: process.env.FIREBASE_API_KEY,
      firebaseAuthDomain: process.env.FIREBASE_AUTH_DOMAIN,
      firebaseProjectId: process.env.FIREBASE_PROJECT_ID,
      firebaseStorageBucket: process.env.FIREBASE_STORAGE_BUCKET,
      firebaseMessagingSenderId: process.env.FIREBASE_MESSAGING_SENDER_ID,
      firebaseAppId: process.env.FIREBASE_APP_ID
    }
  }
}

```

现在，由于已经安装了包`expo-constants`，对象的`extra`部分中的所有键都可以在应用程序范围内访问。

最后一步是修改`firebase.ts`配置文件以使用新的常量，而不是硬编码那些键。更改非常简单，只需用配置文件中定义的名称更改常量的值。

新的 `firebase.ts`文件应该如下所示:

```
// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
import Constants from 'expo-constants';

// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
const firebaseConfig = {
  apiKey: Constants.manifest?.extra?.firebaseApiKey,
  authDomain: Constants.manifest?.extra?.firebaseAuthDomain,
  projectId: Constants.manifest?.extra?.firebaseProjectId,
  storageBucket: Constants.manifest?.extra?.firebaseStorageBucket,
  messagingSenderId: Constants.manifest?.extra?.firebaseMessagingSenderId,
  appId: Constants.manifest?.extra?.firebaseAppId,
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);

export default app;

```

## 添加导航

考虑到应用程序中的两种不同状态:登录和注销状态，现在是时候设计应用程序导航和用户流了。

下面是我们导航的工作方式:

*   注销用户
    *   欢迎屏幕
        *   登录屏幕
        *   注册屏幕
*   登录用户

让我们把重点放在编程助手和导航上。我们将首先创建屏幕占位符，以便将所有内容放在正确的位置。

让我们创建一个名为`screens`的新文件夹，并为每个屏幕创建一个文件。目前，它们都将采用相同的设计。我们稍后会解决这个问题。

您的文件夹结构如下所示:

![Folder Structure Expo](img/8d8e69acfe416732141fa17bcba8e084.png)

每个文件将包含以下代码:

```
import { StatusBar } from 'expo-status-bar';
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';

export default function HomeScreen() {
  return (
    <View style={styles.container}>
      <Text>Home screen!</Text>
      <StatusBar style="auto" />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});

```

只要确保适当地重命名组件。

### 创建身份验证挂钩

首先，我们必须确定一个用户是否经过身份验证，因此我们将构建一个自定义钩子来执行该信息以及用户信息，以防有用户登录。

创建一个名为`utils`的文件夹，在里面有一个新文件夹`hooks`，在它下面我们将放置一个新文件`useAuthentication.ts`。

该文件的代码如下:

```
import React from 'react';
import { getAuth, onAuthStateChanged, User } from 'firebase/auth';

const auth = getAuth();

export function useAuthentication() {
  const [user, setUser] = React.useState<User>();

  React.useEffect(() => {
    const unsubscribeFromAuthStatuChanged = onAuthStateChanged(auth, (user) => {
      if (user) {
        // User is signed in, see docs for a list of available properties
        // https://firebase.google.com/docs/reference/js/firebase.User
        setUser(user);
      } else {
        // User is signed out
        setUser(undefined);
      }
    });

    return unsubscribeFromAuthStatuChanged;
  }, []);

  return {
    user
  };
}

```

通过调用`onAuthStateChanged`，我们订阅了每次授权状态改变时触发的事件，比如当用户登录或注销应用程序时。

我们使用这个事件来捕获用户信息，并将它正确地设置为钩子状态，然后将它提供给可能需要它的组件。

### 创建导航路由器

一旦身份验证工具准备就绪，我们就可以为经过身份验证和未经身份验证的用户构建导航栈。

我们将所有导航逻辑分成三个文件:

*   一个`index`将处理所有状态的应用范围导航
*   一个`authStack`,包含所有未认证用户的堆栈
*   登录用户的一个`userStack`

让我们从`authStack.tsx.`开始，在项目根目录下的`navigation`文件夹下创建这个新文件，这也需要创建那个文件夹。在那里，放置以下代码:

```
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';

import WelcomeScreen from '../screens/Welcome';
import SignInScreen from '../screens/SignInScreen';
import SignOutScreen from '../screens/SignUpScreen';

const Stack = createStackNavigator();

export default function AuthStack() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name="Welcome" component={WelcomeScreen} />
        <Stack.Screen name="Sign In" component={SignInScreen} />
        <Stack.Screen name="Sign Up" component={SignOutScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

```

接下来，让我们算出 u `serStack.tsx`文件:

```
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';

import HomeScreen from '../screens/Home';

const Stack = createStackNavigator();

export default function UserStack() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name="Home" component={HomeScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

```

最后，我们使用 `index.tsx` 文件将两者连接起来。

```
import React from 'react';
import { useAuthentication } from '../utils/hooks/useAuthentication';
import UserStack from './userStack';
import AuthStack from './authStack';

export default function RootNavigation() {
  const { user } = useAuthentication();

  return user ? <UserStack /> : <AuthStack />;
}

```

这个最新的组件使用`useAuthentication`钩子来确定我们是否有一个登录的用户，并基于此加载两个应用程序栈中的一个。

最后，我们需要将我们的主`App.tsx`与导航连接起来，虽然我们还没有屏幕，但是我们应该会看到默认的`Welcome`屏幕被加载，因为我们还没有登录。

```
import React from 'react';
import './config/firebase';
import RootNavigation from './navigation';

export default function App() {
  return (
    <RootNavigation />
  );
}

```

到目前为止，我们可以通过运行以下命令来运行应用程序，以确保一切正常工作:

```
expo start

```

在您的设备、模拟器或 web 上运行项目后，您应该会看到类似这样的内容:

![Welcome Screen](img/49232ea55a36724c143d50a1bcb9a671.png)

印象不深，但我们接下来会在屏幕上工作。

## 构建屏幕

构建屏幕无疑是应用程序最重要的方面之一。出于本教程的考虑，我们将构建基础，较少关注整体设计。

我们需要设置我们的 UI 库，我们已经在教程开始时安装了这个库，但是我们需要考虑一些缺失的配置步骤。

### 设置反应原生元素

我们不会在这里创建自定义主题，但是我们需要导入库的默认主题。返回`App.tsx` 并添加主题提供商，详见[官方文档](https://reactnativeelements.com/docs/overview)。

现在，简单地将`App.tsx`中的所有组件包装到主题提供程序中，如下所示:

```
import React from 'react';
import { ThemeProvider } from 'react-native-elements';
import './config/firebase';
import RootNavigation from './navigation';

export default function App() {
  return (
    <ThemeProvider>
      <RootNavigation />
    </ThemeProvider>
  );
}

```

### 构建欢迎屏幕

我们的欢迎屏幕看起来很棒，但它不起作用。我们需要添加一种方法，让用户使用 React Native elements 中的[按钮](https://reactnativeelements.com/docs/button)组件跳转到登录屏幕或注册屏幕。

我们需要添加按钮，这些按钮将导航到堆栈上的不同屏幕。

以下是其中一个按钮的外观示例:

```
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';
import { StackScreenProps } from '@react-navigation/stack';
import { Button } from 'react-native-elements';

const WelcomeScreen: React.FC<StackScreenProps<any>> = ({ navigation }) => {
  return (
    <View style={styles.container}>
      <Text>Welcome screen!</Text>

      <View style={styles.buttons}>
        <Button title="Sign in" buttonStyle={styles.button} onPress={() => navigation.navigate('Sign In')} />
        <Button title="Sign up" type="outline" buttonStyle={styles.button} onPress={() => navigation.navigate('Sign Up')} />
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: 20,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },

  buttons: {
    flex: 1,
  },

  button: {
    marginTop: 10
  }
});

export default WelcomeScreen;

```

结果如下:

![Sign-in Demonstration](img/bcae1fec0da2e46a104c1d4d39381e8e.png)

### 构建注册屏幕

注册界面有点复杂和有趣，因为我们需要集成逻辑来在 Firebase 上创建一个用户。

我们将开始设计屏幕，然后我们将添加逻辑，使它像魔术一样工作。该屏幕包含两个输入元素，一个用于电子邮件，一个用于密码。它还有一个注册按钮，可以在出错时显示错误信息。

以下是屏幕设计:

```
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';
import Icon from 'react-native-vector-icons/FontAwesome';
import { Input, Button } from 'react-native-elements';
import { StackScreenProps } from '@react-navigation/stack';

const SignUpScreen: React.FC<StackScreenProps<any>> = ({ navigation }) => {
  const [value, setValue] = React.useState({
    email: '',
    password: '',
    error: ''
  })

  function signUp() {
    if (value.email === '' || value.password === '') {
      setValue({
        ...value,
        error: 'Email and password are mandatory.'
      })
      return;
    }

    setValue({
      ...value,
      error: ''
    })
  }

  return (
    <View style={styles.container}>
      <Text>Signup screen!</Text>

      {!!value.error && <View style={styles.error}><Text>{value.error}</Text></View>}

      <View style={styles.controls}>
        <Input
          placeholder='Email'
          containerStyle={styles.control}
          value={value.email}
          onChangeText={(text) => setValue({ ...value, email: text })}
          leftIcon={<Icon
            name='envelope'
            size={16}
          />}
        />

        <Input
          placeholder='Password'
          containerStyle={styles.control}
          value={value.password}
          onChangeText={(text) => setValue({ ...value, password: text })}
          secureTextEntry={true}
          leftIcon={<Icon
            name='key'
            size={16}
          />}
        />

        <Button title="Sign up" buttonStyle={styles.control} onPress={signUp} />
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: 20,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },

  controls: {
    flex: 1,
  },

  control: {
    marginTop: 10
  },

  error: {
    marginTop: 10,
    padding: 10,
    color: '#fff',
    backgroundColor: '#D54826FF',
  }
});

export default SignUpScreen;

```

接下来，让我们从代码中开始使用 Firebase 的 auth 服务。

我们需要更新我们的`firebase.ts`配置文件，因为我们从未指定我们想要使用认证。我们所要做的就是在文件的开头导入 `firebase/auth`，就在导入`Constants`之前。文件的导入部分如下所示:

```
// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
import 'firebase/auth';
import Constants from 'expo-constants';

```

接下来，让我们更新`SignUpScreen`的代码来使用这个库。

让我们从导入和本地对象开始。每次我们想要访问 Firebase 服务时，我们都需要导入它的配置和我们想要使用的任何模块。在我们的案例中:

```
import { getAuth, createUserWithEmailAndPassword } from 'firebase/auth';

const auth = getAuth();

```

接下来，我们将利用函数`createUserWithEmailAndPassword`在注册过程中在 Firebase 上创建一个用户。如果成功，我们会将用户发送到登录屏幕。如果没有，我们将显示一条错误消息，并提供详细信息。

按如下方式更新`signUp`功能:

```
async function signUp() {
  if (value.email === '' || value.password === '') {
    setValue({
      ...value,
      error: 'Email and password are mandatory.'
    })
    return;
  }

  try {
    await createUserWithEmailAndPassword(auth, value.email, value.password);
    navigation.navigate('Sign In');
  } catch (error) {
    setValue({
      ...value,
      error: error.message,
    })
  }
}

```

### 构建登录屏幕

登录屏幕看起来与我们的注册屏幕非常相似。代码 95%是相同的，除了我们不调用`createUserWithEmailAndPassword,`而是调用`signInWithEmailAndPassword`。

代码如下:

```
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';
import Icon from 'react-native-vector-icons/FontAwesome';
import { Input, Button } from 'react-native-elements';
import { getAuth, signInWithEmailAndPassword } from 'firebase/auth';

const auth = getAuth();

const SignInScreen = () => {
  const [value, setValue] = React.useState({
    email: '',
    password: '',
    error: ''
  })

  async function signIn() {
    if (value.email === '' || value.password === '') {
      setValue({
        ...value,
        error: 'Email and password are mandatory.'
      })
      return;
    }

    try {
      await signInWithEmailAndPassword(auth, value.email, value.password);
    } catch (error) {
      setValue({
        ...value,
        error: error.message,
      })
    }
  }

  return (
    <View style={styles.container}>
      <Text>Signin screen!</Text>

      {!!value.error && <View style={styles.error}><Text>{value.error}</Text></View>}

      <View style={styles.controls}>
        <Input
          placeholder='Email'
          containerStyle={styles.control}
          value={value.email}
          onChangeText={(text) => setValue({ ...value, email: text })}
          leftIcon={<Icon
            name='envelope'
            size={16}
          />}
        />

        <Input
          placeholder='Password'
          containerStyle={styles.control}
          value={value.password}
          onChangeText={(text) => setValue({ ...value, password: text })}
          secureTextEntry={true}
          leftIcon={<Icon
            name='key'
            size={16}
          />}
        />

        <Button title="Sign in" buttonStyle={styles.control} onPress={signIn} />
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: 20,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },

  controls: {
    flex: 1,
  },

  control: {
    marginTop: 10
  },

  error: {
    marginTop: 10,
    padding: 10,
    color: '#fff',
    backgroundColor: '#D54826FF',
  }
});

export default SignInScreen;

```

## 构建主屏幕

让我们保持简单，只提供一个退出的方法。

类似于注册屏幕，首先我们将构建屏幕，然后是注销功能。

以下是`HomeScreen`组件设计的代码:

```
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';
import { useAuthentication } from '../utils/hooks/useAuthentication';
import { Button } from 'react-native-elements';

export default function HomeScreen() {
  const { user } = useAuthentication();

  return (
    <View style={styles.container}>
      <Text>Welcome {user?.email}!</Text>

      <Button title="Sign Out" style={styles.button} />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
  button: {
    marginTop: 10
  }
});

```

现在，让我们通过替换以下按钮代码来编写`signOut`函数:

```
<Button title="Sign Out" style={styles.button} onPress={() => signOut(auth)} />
```

就是这样！

![Sign-up Demonstration](img/8fc250c082f55d48ceb9eff1baf32b44.png)

## 结论

将 Firebase 身份验证集成到 Expo 应用程序中的代码相当简单，因为大部分工作都是设计屏幕。有了 [Firebase](https://firebase.google.com/) ，开始构建安全的应用程序变得很容易。

今天，我们刚刚构建了一个最小的原型，但是 Firebase 提供了更多的身份验证选项和服务，所以请进一步探索它以构建伟大的产品——它是免费的！感谢阅读！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)