# React Native 领域入门

> 原文：<https://blog.logrocket.com/realm-react-native/>

长期以来，SQLite 一直是移动和跨平台开发人员的首选数据库。它是轻量级的，无服务器的，并且为任何熟悉 SQL 的人提供了一个简单的学习曲线。SQLite 用 C 编写，支持 30 多种其他编程语言和平台，包括 Android 和 React Native。

近年来，一个新的竞争对手 Realm 出现了。Realm 是一个面向对象的移动优先数据库，旨在用于跨平台和移动应用程序。

在本帖中，我们将讨论以下主题:

## 什么是境界？

Realm 于 2014 年推出，是一个开源数据库平台，具有实时、双向同步和 Node.js SDK 支持。这款传统产品已经被下载了超过 20 亿次。

2019 年，Realm 被 MongoDB 收购，现在由 MongoDB 维护。MongoDB Realm(或者现在称为 Realm)将旧的 Realm 数据库和 MongoDB 的无服务器平台 MongoDB Stitch 结合成一个统一的解决方案，以便在移动设备上进行更轻松的开发。

根据 Realm.io 的说法，“从高速增长的初创公司到财富 500 强公司，数以千计的应用程序都建立在 Realm 之上。”

到目前为止，Realm 已得到各种编程语言的支持，包括:

*   目标-C
*   雨燕
*   Java 和 Kotlin(都只在 Android 设备上)
*   C#(适用于 Xamarin、Win32 和 UWP)
*   JavaScript/TypeScript(使用 Node.js 和 React Native)

## 领域特征

Realm 提供跨平台支持，并且易于使用。对于常规操作，它也比 SQLite 快，并且执行常规操作所需的代码比 SQLite 或核心数据少。

Realm 的一些功能包括:

*   移动优先:Realm 是第一个从头开始构建的数据库，目标是在手机、平板电脑和可穿戴设备中执行
*   轻量级和快速:Realm 是轻量级的，包括延迟加载和零拷贝架构，这意味着开发人员可以直接处理对象
*   易于扩展:Realm 以类似 JSON 的结构形式使用面向对象的数据模型，因此它不需要 orm 或 DAO，提供了更好的 DX
*   现代特性:Realm 包括加密、JSON 支持和数据更改通知
*   实时数据:Realm 提供实时的移动到云的数据同步，这有助于保持设备、用户等之间的数据同步

开发人员还可以使用 Realm 将数据保存在本地磁盘存储上，或者根据应用程序的特定要求将数据临时存储在内存中。

## Realm 与其他数据库有何不同？

与其他数据库不同，领域并不代表单一的应用程序范围的数据库。它甚至不是一个关系表；它可以包含给定域所必需的任意数量的对象类型。领域数据库保存在应用程序中的数据将存储在名为`default.realm`的默认领域文件中。

数据库还可以使用其他文件，例如以`.lock`为后缀的文件，即`default.realm.lock`。锁文件的目的是监视正在使用的领域中的数据版本。

### 领域模式结构

领域对象的结构是通过模式定义的。对象不能包含其架构未描述的属性。因为我们将使用 TypeScript 作为例子，所以模式将在一个`.ts`文件中定义。

每个领域都使用版本化模式。当模式发生变化时，比方说，如果您添加了一个新列(或领域术语中的“属性”)或修改了一个列名，那么您必须定义一个新版本的迁移，以便在模式版本之间移动对象数据。

## 为 React Native 设置领域

Realm 通过一个开发人员作为依赖项安装的包包含了 React 本机支持。然后，大约一年前， [`@realm/react`库](https://www.npmjs.com/package/@realm/react)发布了，这使得在 React Native 中使用 Realm 变得更加容易和更好。

发布这篇文章的理由可以从 [npm](https://www.npmjs.com/package/@realm/react/v/0.1.0) 网站上总结出来:

> 在 React 原生应用程序中设置领域历来都很复杂。当数据库中的对象发生变化时，重新呈现组件需要手动添加和删除侦听器，这会产生大量样板代码，并且容易出错(如果在卸载时正确删除了侦听器)。这个库通过提供 React 钩子来缓解这个问题，这些钩子返回状态感知的领域数据。因此，对领域数据的任何更改都会导致使用钩子的组件重新呈现。

该库要求在您的系统上安装 React 本机版本≥ 0.59 和 Realm v ≥ 11。

## 将领域与 React Native 一起使用

让我们探索在 React 本机应用程序中使用 Realm。有几种方法可供选择:

*   从头开始
*   创建一个标准的 TypeScript 模板化项目，并向其中添加领域
*   使用现成的 Realm-TypeScript 模板，然后对其进行调整以添加功能

我们将使用第三种方法。Realm 团队在他们的 GitHub 上提供了一个包含 React 和 TypeScript 的本地模板。

首先，您可以键入:

```
npx react-native init AwesomeRealmProject --template @realm/react-native-template-ts

```

您也可以给项目起一个自定义名称。我们就用`reactrealmdemo`做名字吧。

一旦创建了项目，您可以通过在项目文件夹中运行 Metro 或任何其他开发客户端来测试它，然后运行项目:

```
cd reactrealmdemo

npm start (or react-native start)

npm run android

```

模板化 Realm-React 本地项目旨在添加和删除待办事项列表中的任务。它使用 Realm 实现持久性，使用 React 钩子实现 React 集成。它还支持同步，允许用户在多个设备上登录并同步他们的待办事项列表。

如果您打开项目来检查它的文件，尤其是`package.json`，您将会看到已经为您自动安装的包。

```
{
  "name": "reactrealmdemo",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "android": "react-native run-android",
    "ios": "react-native run-ios",
    "start": "react-native start",
    "test": "jest",
    "lint": "eslint . --ext .js,.jsx,.ts,.tsx"
  },
  "dependencies": {
    "@realm/react": "^0.4.0",
    "react": "^18.1.0",
    "react-native": "^0.70.3",
    "react-native-get-random-values": "^1.8.0",
    "realm": "^11.0.0"
  },
  "devDependencies": {
    "@babel/core": "^7.12.9",
    "@babel/plugin-proposal-decorators": "^7.19.0",
    "@babel/runtime": "^7.12.5",
    "@react-native-community/eslint-config": "^2.0.0",
    "@realm/babel-plugin": "^0.1.0",
    "@types/jest": "^26.0.23",
    "@types/react-native": "^0.67.3",
    "@types/react-test-renderer": "^17.0.1",
    "@typescript-eslint/eslint-plugin": "^5.17.0",
    "@typescript-eslint/parser": "^5.17.0",
    "babel-jest": "^26.6.3",
    "eslint": "^7.32.0",
    "jest": "^26.6.3",
    "metro-react-native-babel-preset": "^0.72.3",
    "react-test-renderer": "^18.1.0",
    "typescript": "^4.4.4"
  },
  "resolutions": {
    "@types/react": "^17"
  },
  "jest": {
    "preset": "react-native",
    "moduleFileExtensions": [
      "ts",
      "tsx",
      "js",
      "jsx",
      "json",
      "node"
    ]
  }
}

```

模板应用程序包含几个关键文件，也有两个版本:一个用于同步数据，另一个不同步。关键文件包括:

*   `Task.ts`在`models`下——通过一个名为`Task`的类的对象模型定义模式
*   `AddTaskForm.tsx` —包含通过表单创建和添加新任务的代码
*   `TaskItem.tsx`
*   `TaskList.tsx` —显示任务列表
*   `components`下的`TaskManager.tsx`
*   `AppSync.tsx`
*   `app`下的`AppWrapperSync.tsx`

请注意，模型中给出的类型/类名在一个领域中的对象类型中必须是唯一的。

我在这个模板示例的基础上创建了一个应用程序，它允许我执行 CRUD 操作来添加、查看、更新和删除列表中的电影。我还想使用 React 原生导航来整合屏幕间的导航。

让我们初始化项目:

```
npx react-native init reactrealmdemo --template @realm/react-native-template-ts

```

接下来，我们将在`Movie.ts`中定义一个`Movie`对象模型:

```
import {Realm} from '@realm/react';
export class Movie extends Realm.Object<Movie> {
  description!: string;
  title!:string;
  releaseyear!: number; 
  userId!: string;
  constructor(realm: Realm, description: string, title:string, releaseyear: number, userId?: string) {
    super(realm, {description, title, releaseyear, userId: userId || '_SYNC_DISABLED_'});
  }  
}

```

我们在`models`下的`index.ts`是这样定义的:

```
import {createRealmContext} from '@realm/react';
import {Movie} from './Movie';
export const MovieRealmContext = createRealmContext({
  schema: [Movie]  
});

```

`createRealmContext()`方法用于创建一个[反应上下文](https://blog.logrocket.com/how-to-use-react-context-typescript/)

具有给定`Realm.Configuration`的领域的对象。`Context`对象包含内置的钩子和一个包装器组件，该组件为其子组件提供对钩子的访问，以便与领域和上下文提供者一起工作。

接下来，如果您观察模板应用程序的父组件`AppWrapperNonSync.tsx`，您将看到它被包含在`Context`对象中的`RealmProvider`组件所包装，该对象是从`createRealmContext()`返回的。

下面是我们的`AppWrapperNonSync.tsx`文件的样子:

```
...
<SafeAreaView style={styles.screen}>
      <RealmProvider>
        <AppNonSync />
      </RealmProvider>
    </SafeAreaView>

```

`RealmProvider`通过内置的钩子`useRealm()`、`useObject()`和`useQuery()`为您的主组件及其子组件提供对已配置领域的访问。

### 添加 React 本机导航

在模板应用程序中，实际写入领域和从领域中检索/删除的代码包含在`TaskManager`中。`ts`，这就是所谓的`AppNonSync`。`ts`文件。

```
return <TaskManager tasks={tasks} />;

```

然而，在摆弄了代码之后，我采用了一种不同的方法。在创建了`Movie`对象模型之后，我安装了导航和手势处理的各种依赖项。

```
npm install react-navigation react-native-gesture-handler react-native-safe-area-context @react-native-community/masked-view react-native-screens react-native-reanimated react-navigation-stack react-navigation/native-stack

```

因为我想在我的项目中使用 React 本机导航和 Realm，所以我创建了一个`RootStackParams.ts`文件来保存导航屏幕名称。

下面是我的`RootStackParams.ts`文件的样子:

```
export type RootStackParamList = {
    MainScreen: undefined;
    HomeScreen: undefined;
    NewAddMovieScreen: undefined;
    TestUpdateScreen: undefined;
    UpdateMovieScreen: undefined;
    DeleteMovieScreen:undefined;
    ViewMovieScreen:undefined};

```

我在我的项目的入口点`App.tsx`中调用了这些，并且我已经将我的主组件包装在`<RealmProvider>`标签之间，这样所有子组件都可以访问 Realm 的内置钩子。请注意我们的`App.tsx`文件中以粗体显示的行:

```
import React from 'react';
import {NavigationContainer} from '@react-navigation/native';
import {createStackNavigator} from '@react-navigation/stack';
import 'react-native-gesture-handler';
import {RootStackParamList} from './RootStackParams';
import HomeScreen from './screens/HomeScreen';
import NewAddMovieScreen from './screens/NewAddMovie';
import ViewMovieScreen from './screens/ViewMovieScreen';
import { MovieRealmContext } from './models';
import DeleteMovieScreen from './screens/DeleteMovieScreen';
const Stack = createStackNavigator<RootStackParamList>();

export default function App() {  
  const {RealmProvider} = MovieRealmContext;    
  return (
    <NavigationContainer>
      <RealmProvider>
      <Stack.Navigator>
      <Stack.Screen name="HomeScreen" component={HomeScreen} />
      <Stack.Screen name="NewAddMovieScreen" component={NewAddMovieScreen}/>          
      <Stack.Screen name="ViewMovieScreen" component={ViewMovieScreen} />
      <Stack.Screen name="DeleteMovieScreen" component={DeleteMovieScreen} />  
      </Stack.Navigator>
      </RealmProvider>
    </NavigationContainer>
  );
}

```

### 在领域中编写`add`操作

在`HomeScreen`中，我配置了 UI，这样当我们执行 CRUD 操作时，它会将我们带到不同的屏幕。

在下面的`AddMovieScreen.tsx`中，我重用了模板应用程序中不同文件中的一些代码，主要是`AddTaskForm.tsx`和`TaskManager.tsx`，创建了一个执行`add`操作的屏幕。

```
import { View, StyleSheet, ScrollView, KeyboardAvoidingView, TextInput, Text, Pressable, Platform } from 'react-native';
import { StackNavigationProp } from '@react-navigation/stack';
import { RootStackParamList } from '../RootStackParams';
import { Movie } from '../models/Movie';
import colors from '../styles/colors';
import { shadows } from '../styles/shadows';
import { buttonStyles } from '../styles/button';
import { useNavigation } from '@react-navigation/native';
import { MovieRealmContext } from '../models';
import React, { useState, useCallback } from 'react';

type addScreenProp = StackNavigationProp<RootStackParamList, 'NewAddMovieScreen'>;
const { useRealm } = MovieRealmContext;

const AddMovieScreen = () => {
  const realm = useRealm();
  const handleAddMovie = useCallback(
    (description: string, title: string, releaseyear: number, userId: string): void => {
      if (!description) {
        return;
      }
      realm.write(() => {
        return new Movie(realm, description, title, releaseyear, userId);
      });
    },
    [realm],
  );

  return (
    <View style={styles.content}>
      {<AddMovieForm onSubmit={handleAddMovie} />}
    </View>
  );
};
type AddMovieFormProps = {
  onSubmit: (description: string, title: string, releaseyear: number, userId: string) => void;
};

export const AddMovieForm: React.FC<AddMovieFormProps> = ({ onSubmit }) => {
  const [description, setDescription] = useState('');
  const [title, setTitle] = useState('');
  const [releaseyear, setYear] = useState('');
  const [userId] = useState('');
  const navigation = useNavigation<addScreenProp>();

  const convertdate = (textyear: string) => {
    var dt = new Date(textyear);
    return dt.getFullYear();
 }
  const handleSubmit = () => {
    onSubmit(description, title,     
    convertdate(releaseyear), userId);
    setDescription('');
    setTitle('');
    setYear('');
  };

  return (
    <View style={styles.listContainer}>
      <ScrollView keyboardShouldPersistTaps="handled" >
        <KeyboardAvoidingView
          behavior="padding"
          style={{ flex: 1, justifyContent: 'space-
between' }}>
          <TextInput
            value={title}
            placeholder="Enter new movie title"
            onChangeText={setTitle}
            autoCorrect={false}
            autoCapitalize="none"
            style={styles.textInput}
          />
          <TextInput
            value={description}
            placeholder="Enter new movie description"
            onChangeText={setDescription}
            autoCorrect={false}
            autoCapitalize="none"
            style={styles.textInput}
          />
          <TextInput
            value={releaseyear}
            placeholder="Enter new movie year"

            onChangeText={setYear}
            maxLength={4}
            keyboardType="numeric"
            style={styles.textInput}
          />
          <View >
            <Pressable onPress={handleSubmit} 
style={styles.submit}>
              <Text>Submit</Text>
            </Pressable>
          </View>
          <Pressable style={styles.button} onPress={() 
=> navigation.navigate('HomeScreen')}>
            <Text style={styles.buttontext}>Go 
Home</Text>
          </Pressable>
        </KeyboardAvoidingView>
      </ScrollView>
    </View>
  );
};

export default AddMovieScreen;

```

在这个文件中，我包含了代码来显示一个通过用户输入接受电影细节的表单。我们使用内置的`useRealm(`钩子来检索当前打开的领域实例。来自表单字段的数据然后被传递给方法`handleAddMovie()`，该方法又被包装在一个回调中。

### 将数据从表单写入领域

使用`realm.write()`将表单中的数据写入领域。Realm 中的`write`操作是传递给 Realm 实例函数的回调函数，使您能够创建、修改和删除 Realm 对象。这意味着，即使要删除一个领域中的数据，也要调用`realm.write()`。

在真实设备上测试我的项目后(因为它比 Android 模拟器快)，我在主屏幕上打开了应用程序。

![Our Homescreen view in an Android device](img/67e7392f1777ee6e9c833804ab016fea.png)

点击**添加电影**将我带到这个屏幕:

![Clicking the AddMovieScreen button brings us to this page](img/28e13cc06b52f4b147fc4bf6231841e2.png)

同样，在`DeleteMovieScreen.tsx`中，我添加了代码来执行领域中的`delete`操作。

```
import React from 'react';
import {View, StyleSheet,  Pressable, Text, FlatList, SafeAreaView} from 'react-native';
import {Movie} from '../models/Movie';
import {MovieRealmContext} from '../models';
import {StackNavigationProp} from '@react-navigation/stack';
import {RootStackParamList} from '../RootStackParams';
import { useNavigation } from '@react-navigation/native';
import colors from '../styles/colors';
const {useRealm, useQuery} = MovieRealmContext;
type delScreenProp = StackNavigationProp<RootStackParamList, 'DeleteMovieScreen'>;

function DeleteMovieScreen() {
  const navigation = useNavigation<delScreenProp>();
  const realm = useRealm();
  const movies = useQuery(Movie);

  return (
    <SafeAreaView style={styles.listContainer}>
      <View style={{ flexDirection: 'row', justifyContent: 'center', margin: 10 }}>     
       </View>
        <FlatList data={movies.sorted("releaseyear")} renderItem={({ item }) => {
        return (
          <View style={{ flexDirection: 'row', justifyContent: 'center', margin: 10 }}>            
            <Text style={styles.text} >{item.title}</Text>
            <Pressable style={styles.button}
              onPress={() => {
                realm.write(() => {
                  realm.delete(item)
                })
              }} ><Text style={styles.buttontext}>{"Delete 🗑️"}</Text></Pressable>
          </View>
        );
      }} ></FlatList>
      <Pressable style={styles.button} onPress={() => navigation.navigate('HomeScreen')}>
        <Text style={styles.buttontext}>Go Home</Text>
        </Pressable>
    </SafeAreaView >
  );
}
export default DeleteMovieScreen;

```

注意上面粗体显示的行。语句`realm.delete()`包含在一个`realm.write()`中。该屏幕的结果将是一个现有电影的排序列表，每个电影标题旁边有一个**删除**按钮和垃圾桶图标。

![The DeleteMovieScreen view](img/1bc822fe95c7be2c496ba7b7b6166bd8.png)

点击**删除**按钮将从领域中删除所选电影标题的特定记录。

### 显示领域中的列表

在`ViewMovieScreen.tsx`中，我写了代码来显示这个领域的电影列表。内置的`useQuery()`钩子将返回给定类型的领域对象的集合。它既可以用[对象模式类](https://www.mongodb.com/docs/realm/sdk/react-native/realm-database/define-a-realm-object-model/#realm-schema)调用，也可以用字符串形式的模型名调用。`useQuery()`钩子监视集合中任何对象的更新，并根据查询结果的任何变化重新呈现组件。

```
import React from 'react';
import { View, StyleSheet, Pressable, Text, FlatList } from 'react-native';
import { Movie } from '../models/Movie';
import { MovieRealmContext } from '../models';
import { StackNavigationProp } from '@react-navigation/stack';
import { RootStackParamList } from '../RootStackParams';
import { useNavigation } from '@react-navigation/native';
/* import { ViewMovieItem } from '../components/ViewMovieItem'; */
import { shadows } from '../styles/shadows';
import colors from '../styles/colors';

type updateScreenProp = StackNavigationProp<RootStackParamList, 'ViewMovieScreen'>;

function ViewMovieScreen() {
  const navigation = useNavigation<updateScreenProp>();
  const { useQuery } = MovieRealmContext;
  const result = useQuery(Movie);
  const movies = result.sorted("releaseyear");
  return (
    <View style={styles.listContainer}>
      <FlatList
        data={movies}
        renderItem={({ item }) => (
          <ViewMovieItem
            movie={item}
          />
        )}
      />
      <Pressable style={styles.button} onPress={() => navigation.navigate('HomeScreen')}>
        <Text style={styles.buttontext}>Go Home</Text>
      </Pressable>
    </View>
  );
}

type ViewMovieItemProps = {
  movie: Movie & Realm.Object;
};

export const ViewMovieItem = React.memo<ViewMovieItemProps>(
  ({ movie }) => {
    return (
      <View style={styles.movie}>
        <View style={styles.movieContainer}>
          <Text numberOfLines={1} style={styles.text}>
            {movie.title}
          </Text>
          <Text numberOfLines={1} style={styles.text}>
            {movie.description}
          </Text>
          <Text numberOfLines={1} >
            {movie.releaseyear}
          </Text>
        </View>
      </View>
    );
  },
);
export default ViewMovieScreen;

```

输出是按发行年份排序的电影细节列表。

![In the ViewMovieScreen, we can see a list of movies sorted by release date](img/d4baa2d52ceb51b3c329c397661ee9eb.png)

如果在执行应用程序后更改模式，并且数据库中已经存在数据，则必须迁移模式或增加模式版本。您可以在`index.ts`文件中这样做，如下所示:

```
import {createRealmContext} from '@realm/react';
import {Movie} from './Movie';
export const MovieRealmContext = createRealmContext({
  schema: [Movie],
  schemaVersion:2
});

```

模式更改的一个示例用例是更改属性的名称或类型。在这种情况下，增加模式版本是继续执行应用程序最简单的方法。如果不这样做，应用程序将无法运行，并引发一个关于迁移的错误。

### 使用 Realm Studio 在 Realm 中添加`update`功能

为了 CRUD 的任务，我决定打破常规。我使用 MongoDB 的 realm Studio v 13.0 来更轻松地管理我的 Realm 数据库，而不是创建一个类似于我们为操作 Realm 数据而构建的`View`、`Delete`和`Add`屏幕的屏幕。使用基于 GUI 的工具，打开现有领域并在其中添加和修改数据变得简单多了。

![Realm Studio is a GUI editor](img/6c678fedb8190f69f17f486eefc50670.png)

为了使用 Realm Studio 完成`Update`任务，让我们首先定位我们的 Realm 文件。您可以使用以下代码确定领域文件在您的设备或模拟器上的本地路径:

```
Realm.open({}).then(realm => {
    console.log("Realm is located at: " + realm.path);
});

```

例如，在当前项目中，路径将是:

```
/data/data/com.reactrealmdemo/files/default.realm
```

然后，我们可以通过 Android Studio 的[设备文件浏览器工具](https://developer.android.com/studio/debug/device-file-explorer)打开这个文件，但只能在应用程序运行时打开。确保您已经将 Android Studio 配置为项目的框架，而不管您使用哪个 IDE 来编辑项目文件。例如，我使用 VS 代码编辑我的项目文件，所以当我第一次在 Android Studio 中打开项目时，我被提示配置框架。

一旦您通过了这一步，并打开了设备文件浏览器，向下滚动，直到您找到您的领域文件。

![Find and open your realm file in Realm Studio](img/ad9d0f07d3e6ed74ac2af99d6c3832b2.png)

右键点击 **`default.realm`** 并点击**另存为**将领域文件保存到你的磁盘。完成后，您可以使用 Realm Studio 打开、查看和编辑该文件。

启动 Realm Studio 并在其主屏幕上点击**打开 Realm 文件**。选择您刚刚保存文件的位置。

这是我保存到`C:\Source Codes`的`default.realm`文件。我们的电影数据现在可以在 GUI 工具中看到。我可以单击任何属性(列)并进行编辑。

![Our movie data is visible and editable in Realm Studio](img/2d37cda5f4e11223703a6fb44d4a0c02.png)

我甚至可以添加新的属性，删除现有的记录，等等。

![Create a new movie listing within Realm Studio](img/ae89cb50bfb61c003018ae5dc6f8f1de.png)

请注意，这些数据现在存在于本地磁盘上，而不存在于模拟器或运行项目的设备上，因此原始的 realm 文件不会受到我们在这里所做的任何更改的影响，除非我们用更新的文件替换该文件。

也可以在第一次使用`Realm.Configuration`创建之前，将领域位置和名称设置为不同于默认的位置和名称。

### 使用 Realm Studio 导入和导出数据

Realm Studio 还允许您导入和导出领域数据。您可以从任何 CSV 导入数据，也可以将现有领域数据导出到 CSV。

![You can import data into Realm via .csv file](img/cbe5bbd46bf6ef7c5efd3513f6df6da2.png)

您还可以将模型定义保存为特定的格式，并在其他应用程序中重用它们。

![You can save your model definitions for reuse in other realms](img/4eb21ececc5ee48ede44d4f40ae369a2.png)

您可以将数据保存为 JSON 格式或其他本地领域文件。

## 结论

因此，在 React Native 中使用 Realm 非常容易，在@realm/react 库中更是如此。开发人员可以充分利用这个库、其现成的模板和 Realm Studio，在移动应用程序中快速高效地处理数据存储和操作任务。

使用 React Native 领域时的一些最终注意事项:

*   请注意领域数据库(现在是遗留/过时的)和 MongoDB 领域(当前和最新的领域)之间的区别
*   使用@react/realm 库使得使用领域变得更加容易和快速
*   Realm Studio 是一个 GUI 工具，可以让您快速查看、更新和删除 Realm 中的数据等等
*   不要在应用程序运行时删除领域文件

### 有用的链接

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)

Start proactively monitoring your React Native apps — [try LogRocket for free](https://lp.logrocket.com/blg/react-native-signup).