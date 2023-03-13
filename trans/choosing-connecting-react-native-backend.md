# 选择和连接 React 本机后端

> 原文：<https://blog.logrocket.com/choosing-connecting-react-native-backend/>

Supabase 和 Firebase 是两个最著名的后端即服务(BaaS)产品。Firebase 现在归谷歌所有，它为开发者提供了在网络和移动设备上构建和发布应用程序的工具。另一方面，Supabase 是 Firebase 的开源替代方案，采用基于文档的数据存储结构构建。

Supabase 提供关系数据库管理，称为 [PostgreSQL](https://blog.logrocket.com/create-a-react-native-app-with-postgresql-and-graphql-part-1/) ，它允许你用 SQL 对你的数据库进行[查询。](https://blog.logrocket.com/detailed-look-basic-sqljs-features/)

在本文中，我们将学习如何选择和连接 [React Native](https://blog.logrocket.com/tag/react-native/) 后端，然后学习如何使用 [Node.js](https://blog.logrocket.com/tag/node/) 创建我们自己的后端服务。

*向前跳转:*

## 对本机后端的利弊做出反应

Supabase 和 [Firebase](https://blog.logrocket.com/storing-retrieving-data-react-native-apps-firebase/) 提供了构建和管理网络和移动应用的工具套件。这两个平台都提供了一系列功能，包括数据库管理、用户认证、托管和实时数据同步。以下是使用 Firebase 和 Supabase 的一些优点和缺点。

### 使用 Firebase 的利与弊

Firebase 为构建和管理 web 和移动应用程序提供了一套全面的工具和服务，包括实时数据库、用户认证、托管、分析等。Firebase 的另一个优点是 Firebase 提供实时数据同步，这意味着对数据库的任何更改都会实时自动传播到所有连接的客户端。这对于需要实时更新的应用程序非常有用，例如[聊天或协作应用程序](https://blog.logrocket.com/how-to-build-chatroom-app-react-firebase/)。

Firebase 与其他谷歌服务无缝集成，如谷歌云存储和谷歌云功能，这对已经在使用这些服务的开发者来说可能是有帮助的。最后，Firebase 相对容易设置和使用，并且有清晰的文档和直观的工具。

### 骗局

使用 Firebase，您对数据库的控制是有限的，因为 Firebase 平台管理它。如果您需要对数据进行更多的控制，或者如果您对数据管理有特定的要求，这可能是一个缺点。Firebase 为其工具和服务提供了有限的定制选项，这可能会使需要更多控制应用程序功能的开发人员处于不利地位。

因为谷歌拥有 Firebase，所以你依赖于该公司的基础设施和政策。对于关心数据隐私或希望对应用程序的托管环境有更多控制的开发人员来说，这可能是一个问题。

### 使用 Supabase 的利弊

像 Firebase 一样，Supabase 提供实时数据同步，这对于需要实时更新的应用程序很有帮助。 [Supabase 是一个开源平台](https://blog.logrocket.com/exploring-supabase-the-open-source-firebase-alternative/)，这意味着您可以完全控制源代码，并可以对其进行定制以满足您的特定需求。

Supabase 是建立在 PostgreSQL 之上的，这对于已经熟悉 PostgreSQL 或者需要对其数据库有更多控制的开发人员来说很有帮助。

### 骗局

因为 Supabase 是一个相对较新的平台，与 Firebase 相比，它的功能范围有限。此外，作为一个新平台，与 Firebase 等更成熟的平台相比，Supabase 的文档和社区支持有限。Supabase 是一个开源平台，这意味着你需要更多的技术知识来设置和有效地使用它。

## 选择 React 本机后端

当在 Firebase 和 Supabase 之间选择 React 本地后端时，有几个关键因素需要考虑:

### 目的

你想用数据库做什么？Firebase 是一个通用数据库，可以用于各种应用，包括实时数据同步、[认证](https://blog.logrocket.com/user-authentication-firebase-react-apps/)和[云功能](https://blog.logrocket.com/firebase-cloud-storage-firebase-v9-react/)。Supabase 是一个基于 Postgres 的数据库，具有实时功能和内置 API 支持，这使得它非常适合[构建现代 web 应用](https://blog.logrocket.com/using-edge-functions-supabase-complete-guide/)。

### 数据结构

您希望如何组织您的数据？Firebase 使用了一个 [NoSQL 数据模型](https://blog.logrocket.com/nosql-wide-column-stores-demystified/)，这意味着数据被存储为一个可以包含任意数量字段的文档集合。Supabase 使用一个 [SQL 数据模型](https://blog.logrocket.com/product-management/sql-skills-product-managers/)，将数据存储在具有行和列的表格中。如果您熟悉 SQL 或者需要处理结构化数据，Supabase 可能是更好的选择。

### 生态系统

您还需要哪些工具和服务来与您的数据库集成？Firebase 有一个庞大而活跃的开发人员社区，它与其他谷歌云服务整合在一起，包括机器学习和分析。Supabase 有一个[较小的社区](https://github.com/supabase-community)，但提供了与 Postgres 生态系统的强大集成，包括地理空间数据的 PostGIS 和时间序列数据的时标等工具。

最终，React 本机后端的最佳选择将取决于您的特定需求和要求。评估这两个选项，看看哪一个更符合您的项目目标可能会有所帮助。

## 连接到 Firebase

我们将使用 [Expo](https://blog.logrocket.com/native-routing-expo-react-native/) 作为 React 本地开发环境。我们将通过在我们的项目文件夹终端中运行`npx create-expo-app`来搭建 Expo starter 项目文件。

要开始创建我们的 Firebase 应用程序，请访问 [Firebase 网站](https://firebase.google.com/)，使用您的 Google 帐户登录，然后转到控制台选项卡。在那里，您将看到一个创建新项目的按钮。命名项目并完成创建新项目的步骤后，您将看到以下内容:

![Firebase React Native Project Start Example](img/ae0343399ba877dae98ceebe77319c4e.png)

因为我们想要使用一个 [Firestore 数据库](https://blog.logrocket.com/storing-retrieving-data-react-native-apps-firebase/)，我们将首先创建一个应用程序，点击 **`</>`** 按钮将您重定向到此页面:

![Adding Firebase For Your React Native Backend](img/7aeece3c3d9e089ed794e38a390d7d0b.png)

首先，使用`npm i firebase`安装 Firebase，并将代码块复制到我们的项目中。我们将在其中创建一个`firebase`文件夹和一个`firebase.js`文件:

```
// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
const firebaseConfig = {
 apiKey: "AIzaSyCGpnbYR-rubx9qC5XrGcQA3XMW6t0JdH0",
 authDomain: "todo-app-52801.firebaseapp.com",
 projectId: "todo-app-52801",
 storageBucket: "todo-app-52801.appspot.com",
 messagingSenderId: "961721982303",
 appId: "1:961721982303:web:ae37cc46b718ccf8cfaaea",
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);

export { app };

```

### 在数据库中存储数据

现在，我们将创建一个数据库来存储我们的待办事项。首先，点击**构建**按钮并选择 **Firestore 数据库**:

![Adding Cloud Firestore ](img/7411f04f8ac6819af2c111dcd743b7a6.png)

创建数据库后，我们将有一个仪表板，可以在其中创建我们的`todos`集合并存储任务:

![Creating New Tasks For Your Firebase React Native Backend](img/7552d160eb39c736d4bfdcfbb74b0f32.png)

然后，点击**开始收集**，并给它起一个名字或一个`collectionID`。我们的迷你项目将有三个字段:`ID`、`Title`和`Completed Field`。

### 创建待办任务

为了创建一个新的待办任务，我们将在`App.js`中有一个带有文本输入和行动按钮的界面。请记住，在上一步中，我们在 Firebase 应用程序中导出了应用程序。

现在，我们将导入并使用`App.js`中的 app。首先，我们将创建两个状态:`title`和`completed`。`TextInput`值将被设置为`title`的值，即用户输入的值，并且将在`button`组件上调用`addTodo`函数。

它应该是这样的:

```
import{ 
StyleSheet,
 Text,
 TextInput,
 View,
 Button,
 FlatList,
} from "react-native";
import { app } from "./firebase/firebase";
import {
 getFirestore,
 addDoc,
 getDocs,
 collection,
} from "firebase/firestore";
import { useEffect, useState } from "react";

export default function App() {
 const [title, setTitle] = useState("");
 const [completed, setCompleted] = useState(false);
 const db = getFirestore(app);

 const addTodo = async () => {
   try {
     const docRef = await addDoc(collection(db, "todos"), {
       title,
       completed,
     });
     setTitle("");
     console.log("Document written with ID: ", docRef.id);
   } catch (error) {
     console.log(error);
   }
 };

 return (
   <View style={styles.container}>
     <Text>Open up App.js to start working </Text>
     <TextInput
       value={title}
       onChangeText={(text) => setTitle(text)}
       placeholder="Enter a todo"
       style={styles.input}
     />
     <Button title="Add Todo" onPress={addTodo} />
   </View>
 );
}

const styles = StyleSheet.create({
 container: {
   flex: 1,
   backgroundColor: "#fff",
   marginTop: 400,
   alignItems: "center",
   justifyContent: "center",
 },
 input: {
   height: 40,
   width: 200,
   borderWidth: 1,
   borderColor: "black",
   margin: 10,
 },
});

```

现在，让我们检查一下模拟器中的内容:

![React Native Firebase App](img/2c193ae0ebe55cf7a048928f97e230a7.png)

创建后，我们可以检查我们的数据库，看看它是否在那里:

![Checking the Firebase Database](img/b1611ac0bf044c04b9d48d1c37b9a499.png)

干得好！我们已经成功地将 React 本机应用程序连接到 Firestore 数据库，并在创建待办事项应用程序时执行了`create`操作。你可以在我的库[这里](https://github.com/Taofiqq/rn-todo-supabase)找到这个项目的完整代码(用于`read`、`create`和`delete`动作)。

## 创建和连接 Supabase 应用程序

你可以完全使用 Supabase，或者只使用你的项目需要的[特性](https://supabase.com/)。本教程将向您展示如何连接 React Native 和 Supabase 来构建一个简单的 [CRUD 待办应用程序](https://blog.logrocket.com/building-the-simplest-crud-out-there-with-entity-management/)。

我们将使用 Expo 作为 React 本地开发环境。通过在终端的项目文件夹中运行`npx create-expo-app`开始。这将搭建构建所需的 Expo starter 项目文件。

要开始创建我们的 Supabase 应用程序，请导航至 [Supabase 网站](https://app.supabase.com)并使用您的 GitHub 帐户登录或手动使用您的电子邮件登录。成功登录后，您可以从“组织”选项卡创建新项目。然后，插入项目名称和密码，并选择**创建新项目**:

![Creating a Supabase Project](img/c3336cf5306c57a353174747a56e13a7.png)

这将设置您的项目，您可以在其中获得您的 API 密钥和项目 URL。为此，导航到**设置**选项卡并点击 **API 选项卡**。它应该是这样的:

![Setting the Supabase API ](img/d3b1ee43e6eb2fda8898c0e0393596f4.png)

现在，在我们的`project`文件夹中，我们将创建一个`lib`文件夹和一个`supabase.js`文件来启动 Supabase 实例。我们将在项目的后期使用它来提出我们的`HTTP`请求。您的代码应该如下所示:

```
// this is a javascript file
import "react-native-url-polyfill/auto";
import AsyncStorage from "@react-native-async-storage/async-storage";
import { createClient } from "@supabase/supabase-js";

const supabaseUrl = "https://ojbnhwfgozggjfjetrmu.supabase.co";
const supabaseAnonKey =
 "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Im9qYm5od2Znb3pnZ2pmamV0cm11Iiwicm9sZSI6ImFub24iLCJpYXQiOjE2Njg0ODIzNDcsImV4cCI6MTk4NDA1ODM0N30.Y3_C-gzEHZ-Nt9dpqmbbhoXIHEz0vdQ4W7BOmGKyoZw";

export const supabase = createClient(supabaseUrl, supabaseAnonKey, {
 auth: {

   storage: AsyncStorage,
   autoRefreshToken: true,
   persistSession: true,
   detectSessionInUrl: false,
 },
});

```

### 在 React Native 中使用 Supabase

为了让 Supabase 实例与 React Native 一起工作，我们将安装两个包:[React-Native-URL-poly fill/auto](https://www.npmjs.com/package/react-native-url-polyfill)和[@ React-Native-async-storage/async-storage](https://www.npmjs.com/package/@react-native-async-storage/async-storage)。

现在，我们将在 Supabase 项目应用程序中创建一个`todo`表格，方法是在表格编辑器选项卡中单击**创建一个新表格**。我们将有两列:`tasks`和`created_at`。它应该是这样的:

![React Native Backend Tasks One](img/7b0771cb23578a51ad8062a333699586.png)

![React Native Backend Tasks Two](img/d8b63dc9ed2f9bcaffbcda9cafb6c78f.png)

此外，禁用`RLS`策略来无限制地调用数据库。你可以在这里阅读更多关于行级安全[的内容。可以从表中手动创建新的数据行，如下所示:](https://supabase.com/docs/learn/auth-deep-dive/auth-row-level-security)

![React Native Backend Part Three](img/57092c1986926a14c91ffa4d58ad3acf.png)

![React Native With Supabase](img/ed4961f725b1e547f3890816ff18f2f2.png)

### 创建待办任务

为了创建一个新任务，我们将创建一个新的`AddTodo.js`文件，并从我们的应用程序接口调用端点来创建一个新任务。创建一个`todo`状态来存储在`TextInput`中输入的文本。

在`Supabase`实例上将调用`insert`方法，在`TextInput`中输入的`todo`文本将作为`tasks`字段的值被传递，如下所示:

```
// this is a javascript file
import { StyleSheet, Text, TextInput, Button, View } from "react-native";
import React, { useState } from "react";
import { supabase } from "../../lib/supabase";

const AddTodo = () => {
 const [todo, setTodo] = useState("");

 const handleSubmit = async (e) => {
   e.preventDefault();

   setLoading(true);
   const { data, error } = await supabase.from("todo").insert([
     {
       tasks: todo,
     },
   ]);

   setTodo("");
 };
 return (
   <View>
     <TextInput
       placeholder="Add Todo Tasks"
       value={todo}
       onChangeText={(e) => setTodo(e)}
       style={styles.input}
     />
     <Button title="Add Todo Tasks" onPress={handleSubmit} />
   </View>
 );
};

export default AddTodo;

const styles = StyleSheet.create({
 input: {
   height: 40,
   width: 200,
   margin: 12,
   borderWidth: 1,
   padding: 10,
 },
});

```

![React Native and Superbase App on Mobile](img/5c62eea6adfaaf07eda793eba8b3d462.png)

## 构建自定义 React 本机后端

在教程的第一部分，我们学习了如何将 React Native 连接到 Supabase 这样的自托管解决方案。现在，我们将学习如何创建自己的后端服务器，并在前端将其与 React Native 连接。对于这一部分，我们将使用 [Node.js](https://blog.logrocket.com/node-js-vs-python-how-to-choose-the-best-technology-develop-backend/) 作为后端，使用 [MongoDB](https://www.mongodb.com/) 作为数据库，创建一个简单的锻炼应用程序。

首先，我们将创建前端`client`和后端`server`文件夹。接下来，我们将导航到`client`文件夹，并通过在项目文件夹的终端中运行`npx react-native init RNWorkout`来初始化一个 React Native CLI 应用程序。这将搭建构建所需的起始项目文件。

对于后端，我们将使用`cd/server`命令进入目录，并在终端中运行`npm init -y`命令。这将启动我们的 Node.js 后端服务，我们将创建一个`app.js`基础文件。现在，我们将安装所需的依赖项:`npm i nodemon express mongoose cors dotenv`。

在`app.js`中，我们将设置一个基本的 [Express.js](https://blog.logrocket.com/organizing-express-js-project-structure-better-productivity/) 服务器来监听`port 3000`。这就是我们的`server/ap.js`的样子:

```
// this is a javascript file
const express = require("express");
const app = express();
// listen on port 3000
app.listen(3000, () => {
 console.log("Server is listening on port 3000");
});

```

然后，在我们的`package.json`文件中，我们将设置一个新的`script`，当我们运行下面的命令时，它将启动服务器:

```
 "scripts": {
   "test": "echo \"Error: no test specified\" && exit 1",
   "start": "nodemon app.js"
 },

```

基本上，我们设置了一个`start script`来运行`app.js`文件，每当我们在终端中运行命令`npm run start`时，如下所示:

![React Native Server Port](img/749b497ce484dfb70bed79df5fc9b611.png)

现在，我们已经成功地设置了我们的服务器，它正在监听`port 3000`。

### 将数据库连接到后端

要开始，导航到 [MongoDB Cloud](https://cloud.mongodb.com) 登录或注册。登录后，创建并命名一个新的组织和项目，然后选择一个**空闲集群**:

![React Native Backend With MongoDB Part One](img/eb6c8052de499daebbc34a8d8fc50d32.png)

![React Native Backend With MongoDB Part Two](img/9e0f5146fa7d56e1af29752390e5819a.png)

一旦您成功创建了一个新项目，您将有一个仪表板，您可以在其中获取连接字符串以将您的数据库连接到您的应用程序。点击**连接**按钮并选择**连接您的应用程序**以获得您的连接字符串。

然后，复制这个字符串并创建一个`.env`文件和变量名`MONGODB_URI`。接下来，像这样存储这个字符串:

![Connecting MongoDB to the React Native Backend](img/e54bcf3116d12f12d4fd5ee710e7ce57.png)

### `server/.env`

```
MONGODB_URI=mongodb+srv:******************

```

> 注意:记得创建一个`.gitignore`文件，并包含`.node_modules`和`.env`文件，这样它就不会被推送到存储库中

### `server/.gitignore`

```
node_modules
.env

```

现在，我们将在`app.js`文件中将数据库连接到我们的应用程序。首先，我们将需要`mongoose`模块并使用`mongoose.connect`方法连接我们的`MONGODB_URI`。如果我们的数据库已经成功连接，我们还将在`console`中记录一个`text`，如下所示:

```
// this is a javascript file
const express = require("express");
const mongoose = require("mongoose");
const app = express();

// implement dot env to read env variables
require("dotenv").config();

// connect mongodb with mongoose
mongoose
 .connect(process.env.MONGODB_URI, {
   useNewUrlParser: true,
   useUnifiedTopology: true,
 })
 .then(() => {
   console.log("Connected to MongoDB");
 })
 .catch((err) => console.log(err));

```

一旦我们启动服务器，我们的服务器应该在`port 3000`成功运行，并且应该在我们的终端上显示一个显示`"Connected to MongoDB"`的文本:

![React Native Backend Connected to MongoDB](img/70bbafcf8a006973efa7f19fedd311bd.png)

不错！我们已经成功地将服务器与数据库连接起来。

### 创建锻炼计划

现在，我们将创建一个名为`models`的新文件夹和一个`Workout.js`文件来定义我们所有的`schemas`。我们会有三个`schemas`道具:`title`、`reps`和`load`。

所有的`schema`道具(`title` : `String`，`reps`，`load` : `Number`)都会设置`Properties`类型和`required`。

每当我们想要执行任何 CRUD 操作时，都会调用这里创建的`Schema`:

```
// this is a javascript file
const mongoose = require("mongoose");

const WorkoutSchema = new mongoose.Schema({
 title: {
   type: String,
   required: true,
 },
 reps: {
   type: Number,
   required: true,
 },
 load: {
   type: Number,
   required: true,
 },
 // add created at
 createdAt: {
   type: Date,
   default: Date.now,
 },
});

module.exports = mongoose.model("Workout", WorkoutSchema);

```

### 设计 API 端点

下面是设计 API 端点的`server/app.js`:

```
// this is a javascript file
app.post("/create", async (req, res) => {
 const title = req.body.title;
 const reps = req.body.reps;
 const load = req.body.load;
 const workout = new WorkoutModel({
   title: title,
   reps: reps,
   load: load,
 });
 try {
   await workout.save();
   res.status(201).json({
     message: "Workout created",
     workout,
   });
 } catch (error) {
   console.log(error);
 }
});

```

对于`create workout`端点，我们将把`schema`数据存储在一个变量中，并在端点被调用时创建一个新的`schema`模型。

然后，当`request body`完成后，我们保存数据库中设计的健身程序，并返回一个已创建健身程序详细信息的`JSON`对象和一条成功消息。下面是邮递员中的[测试:](https://blog.logrocket.com/how-automate-api-tests-postman/)

![React Native Postman Testing](img/f3e09b6eeac846448399dc5b40cef41a.png)

现在，当我们对所有的`workouts`发出一个`get`请求时，我们应该有一个数据的`Array`:

![React Native Postman Testing Part Two](img/880610b14c8539184662fd16af8e917a.png)

## 将后端服务器连接到 React 本地应用程序

我们已经成功创建了 React 本地后端服务，现在可以请求在前端执行操作了。首先，我们将创建两个文件夹:`src`和`components`。

我们的`App.js`会是这样的:

```
// this is a javascript file
import {StyleSheet, Text, View} from 'react-native';
import React from 'react';
const App = () => {
 return (
   <View style={styles.container}>
     <Text>List of my workouts</Text>
       </View>
 );
};

export default App;

const styles = StyleSheet.create({
 container: {
   flex: 1,
   backgroundColor: '#fff',
   alignItems: 'center',
   justifyContent: 'center',
 },
});

```

我们的模拟器/仿真器将如下所示:

![Connected React Native Backend App](img/5b46eeb5fcbcc9f7839656fd312d0966.png)

React Native 不允许`localhost`与不在互联网上托管的后端进行连接和网络请求。因为我们仍然在开发中运行我们的后端服务器，如果我们使用`[https://localhost:3000/workouts](https://localhost:3000/workouts)`向任何后端端点发出请求，它将总是返回一个错误，说`Network Error/ Axios Network Error`。

要解决这个问题，右键单击您用于构建的 **Wi-Fi** 或 **LAN** 图标。然后，检查网络属性以获取您所连接的 IP 地址。

### 在 macOS 上

点击**苹果图标**–**系统偏好**–**网络**。从那里，您应该看到您的 IP 地址:

![React Native Backend Application Connected](img/86622f1207cfe06ecf0cd88bb3667b22.png)

### 在 Windows 上

右键点击 **Wi-Fi/LAN 图标**–**打开网络和互联网设置**–**状态**–**Wi-Fi**–**属性**–**IP 地址**

[Axios](https://blog.logrocket.com/axios-vs-fetch-best-http-requests/) 将用于向后端服务发出请求，后端服务可以使用`npm i axios`命令安装。

## 结论

我们已经成功地学会了如何选择和连接，以对 Supabase 和 Firebase 等自托管解决方案做出本机反应。我们还学习了如何使用 Node.js 和 MongoDB 创建定制的 React 本地后端服务。选择哪个 React 本地后端服务取决于偏好和用例。

在某些情况下，您可能希望构建一些简单快捷的东西。在这种情况下，Supabase 可能是首选。然而，当您想要完全控制您的后端服务时，创建您自己的定制后端服务可能是最好的选择。你可以在这里查看整个 GitHub 库。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)