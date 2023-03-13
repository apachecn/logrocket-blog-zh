# 在 React Native 中创建简历生成器应用程序

> 原文：<https://blog.logrocket.com/creating-resume-builder-app-react-native/>

学习如何编码的最有效的方法是构建现实生活中的应用程序，并让自己经历实际的项目工作。你会更好地理解语法，学会更有效地研究，成为一个更好的问题解决者，磨练你的 UI/UX 设计技能，并理解仅仅看教程不能提供给你的细微差别。

记住这一点，让我们使用 React Native 构建一个简历生成器应用程序。在本教程中，您将学习一些重要的移动开发实用技能和技术。

向前跳:

## 简历生成器应用程序项目概述

简历，或 CV，是一个人用来向未来雇主展示他们的技能、工作经验和成就的文件。简历生成器应用程序通过提示用户在各种字段中填写适当的信息，然后用这些信息生成一份整洁、有风格的简历，使这一点变得更容易。

在这个项目中，我们将在 React Native 中创建一个简历生成器应用程序。该应用程序的用户界面将包括两个屏幕。

第一个屏幕将包含各种输入字段，其中包含提示用户提供与其简历相关的信息，包括他们的姓名、联系方式和过去的工作经验:

![Resume Builder App Form Screen With Input Fields Separated Into Sections Such As Personal Details And Contact Details. App Background Is Dark Blue. Section Title Text Is Yellow. All Other Text Is White. Teal Line Separates App Title From Form Fields](img/b667e95cea2aceddf629f657289eb97a.png)

视用户设备的大小而定，在任何给定时间视口中显示的字段数量可能会有所不同。一旦用户滚动到表单的末尾，他们应该会看到一个按钮来提交他们的信息:

![End Of Resume Builder App Form With Final Fields And Teal Button To Create Resume](img/70ecacb83ca9a5ce7d9979450cd67396.png)

用户提交表单后，我们将收集所有信息并在第二个屏幕上显示简历，如下图所示:

![Resume Builder App Showing Beginning Of Final Generated Resume](img/4ab9e7467d6bb088eafec65402062912.png)

参见世博小吃上的这个项目的[完整源代码。](https://snack.expo.dev/@ubahthebuilder/295685)

## 创建用于收集用户数据的表单输入

此时，您应该已经准备好您的[世博会小吃项目来编辑](https://snack.expo.dev/)。如果是这样，在项目的根文件夹中创建一个名为`screen`的新文件夹。接下来，在`screen`文件夹中创建两个文件— `ResumeForm.js`和`ShowCV.js`。

在`ResumeForm.js`内部，首先导入 React、`[useState()](https://blog.logrocket.com/a-guide-to-usestate-in-react-ecb9952e406c/)`[钩子](https://blog.logrocket.com/a-guide-to-usestate-in-react-ecb9952e406c/)，以及构建 UI 的各种组件，然后定义一个准系统`ResumeForm`组件函数如下:

```
import * as React from 'react';
import { Text, View, StyleSheet, TextInput, Button, TouchableOpacity } from 'react-native';
import { useState } from 'react'

export default function ResumeForm({ navigation }) {
    return (
        // JSX goes here
    )
}

```

在我们开始指定要呈现的元素之前，让我们用`useState()`钩子定义组件状态。

在`return`语句之前添加以下代码:

```
  const [userDetails, setUserDetails] = useState({
    fullName: '',
    avatarUrl: '',
    profTitle: '',
    phoneNo: '',
    email: '',
    website: '',
    company: '',
    jobTitle: '',
    jobStartDate: '',
    jobEndDate: '',
    experience: '',
    profSummary: '',
    certificate: '',
    collegeName: '',
    colStartDate: '',
    colEndDate: '',
    skill: '',
    hobby: ''
  });

```

该对象包含用户需要通过表单输入提供的信息。此刻，一切都是空的。但是我们将使用 setter 方法——`setUserDetails`——在用户输入相应的表单输入时填充每个状态分支。

接下来，在`return`语句中，创建容器`<View>`元素和其中的标题文本:

```
<View style={styles.container}>      
   <View style={styles.header}>
      <Text style={styles.headerText}>Resume Builder</Text>        
   </View>

   // Input groups will go here
</View>

```

现在我们需要开始创建不同的输入组。

## 创建各种简历输入组

第一个输入组用于收集用户的个人信息——姓名、头像 URL 和职位。

我们正在使用 [React Native 的](https://blog.logrocket.com/complete-guide-textinput-react-native/) `[TextInput](https://blog.logrocket.com/complete-guide-textinput-react-native/)` [元素](https://blog.logrocket.com/complete-guide-textinput-react-native/)收集数据。下面是实现这一点的代码，您应该将它添加到容器`View`中:

```
<View style={styles.details}>
         <Text style={styles.titleText}>Personal Details</Text>        
          <TextInput 
            style={styles.textinput} 
            placeholder="Enter your full name" 
            value={userDetails.fullName}          
            onChangeText={(e) => {
              setUserDetails(userDetails => ({
                ...userDetails, ...{'fullName': e}
              }));
            }}
          />
          <TextInput 
            style={styles.textinput} 
            placeholder="Enter your avatar URL" 
            value={userDetails.avatarUrl}  
            onChangeText={(e) => {
              setUserDetails(userDetails => ({
                ...userDetails, ...{'avatarUrl': e}
              }));
            }}        
          />
          <TextInput style={styles.textinput} 
            placeholder="Enter your professional title" 
            value={userDetails.profTitle} 
            onChangeText={(e) => {
                setUserDetails(userDetails => ({
                  ...userDetails, ...{'profTitle': e}
                }));
              }}         
          />
</View>

```

每个`TextInput`都有一个`onChangeText`属性，当用户在表单输入中键入内容时，该属性会实时更新状态。每个`setUserDetails`调用只更新州中相应的分支。

让我们看看第三个表单输入组作为另一个例子。第三个表单组用于收集用户的工作经历信息—以前的工作、开始和结束日期以及该工作经历的描述:

```
<View style={styles.details}>
         <Text style={styles.titleText}>Previous Job</Text>                  
          <TextInput 
            style={styles.textinput} 
            placeholder="Enter company name" 
            value={userDetails.company}
            onChangeText={(e) => {
              setUserDetails(userDetails => ({
                ...userDetails, ...{'company': e}
              }));
            }}
          />                  
          <TextInput style={styles.textinput} placeholder="Enter job title" value={userDetails.jobTitle}               
          onChangeText={(e) => {
              setUserDetails(userDetails => ({
                ...userDetails, ...{'jobTitle': e}
              }));
            }}
          />                    
          <TextInput style={styles.textinput} placeholder="Enter start date (e.g. 11/11/2022)" value={userDetails.jobStartDate}              
          onChangeText={(e) => {
              setUserDetails(userDetails => ({
                ...userDetails, ...{'jobStartDate': e}
              }));
            }}
          />
          <TextInput style={styles.textinput} placeholder="Enter end date (e.g. 12/11/2022)" value={userDetails.jobEndDate}
          onChangeText={(e) => {
              setUserDetails(userDetails => ({
                ...userDetails, ...{'jobEndDate': e}
              }));
            }}
          />
          <TextInput style={styles.textinput} placeholder="Describe your experience" value={userDetails.experience}                   
          onChangeText={(e) => {
              setUserDetails(userDetails => ({
                ...userDetails, ...{'experience': e}
              }));
            }}
          />
 </View>

```

为了简单起见，我们使用`TextInput`表示日期。你可以像集成`[react-native-date-picker](https://www.npmjs.com/package/react-native-date-picker)`一样集成一个 [React 原生日期选择器库](https://blog.logrocket.com/best-date-pickers-react-native-app/)来使项目更有趣。

第二个和第四个表单组使用的格式与到目前为止讨论过的格式相同，所以为了简洁起见，我将它们排除在外。你可以[参考博览会小吃报告](https://snack.expo.dev/@ubahthebuilder/295685)来看看我们在这个简历生成器应用示例中还包括了什么。

## 创建按钮以提交简历信息

在表单输入组下面，让我们使用 React Native 的`Button`组件创建用于提交表单的按钮:

```
  <Button
        title="Create Resume"
         style={styles.button}
         onPress={() => navigation.navigate('ShowCV', userDetails)}
         >           
  </Button>

```

在这里，我们指定当用户按下按钮时，我们希望将他们导航到`ShowCV`屏幕，并将用户的简历信息作为参数传递。我们将在`ShowCV`屏幕上向用户呈现简历详情。

我们将在下一节创建`ShowCV.js`。但是首先，让我们[给我们的 React 原生](https://blog.logrocket.com/react-native-styling-tutorial-with-examples/)简历生成器应用添加一些样式。

在`Resume`函数下面，粘贴下面的样式表使表单看起来更好:

```
const styles = StyleSheet.create({
  cont: {
    flex: 1,    
    backgroundColor: '#36485f',
    paddingLeft: 40,
    paddingRight: 40,        
    paddingTop: 40
  },  
  header: {    
    marginBottom: 20,
    alignSelf: 'stretch'
  },
  details: {
    marginBottom: 15
  },
  headerText: {
    fontSize: 24,
    color: '#fff',    
    borderBottomColor: '#199187',
    paddingBottom: 10,
    borderBottomWidth: 1
  },  
  titleText: {
    fontWeight: 'bold',
    color: 'yellow',
    fontSize: 15,
    marginBottom: 10
  },
  textinput: {
    alignSelf: 'stretch',
    height: 40,
    color: '#d3d3d3',
    marginBottom: 20,
    borderBottomColor: '#f8f8f8',
    borderBottomWidth: 1
  },
  button: {
    alignSelf: 'stretch',
    alignItems: 'center',
    padding: 10,
    backgroundColor: '#59cbbd',
    marginTop: 5,
    marginBottom: 20,    
  }
});

```

您的应用程序现在应该如下所示:

![Resume Builder App Form Screen With Input Fields Separated Into Sections Such As Personal Details And Contact Details. App Background Is Dark Blue. Section Title Text Is Yellow. All Other Text Is White. Teal Line Separates App Title From Form Fields](img/b667e95cea2aceddf629f657289eb97a.png)

接下来，让我们创建`ShowCV.js`。

## 呈现简历

在这一节中，我们将创建`ShowCV`屏幕向用户展示他们的完整简历。进入您的`screens/ShowCV.js`文件并包含以下代码:

```
import * as React from 'react';
import { Text, View, StyleSheet, TextInput, Button, TouchableOpacity } from 'react-native';
import { useState } from 'react'

export default function ShowCV({ route }) {
    let dataObj = route.params

    return (
        <View style={styles.container}>      
           <View style={styles.header}>
              <Text style={styles.headerText}>Your Resume</Text>        
           </View>

           // Resume details go here
        </View>
    )
}

```

当从一个屏幕导航到另一个屏幕时，目标屏幕可以访问根对象。我们将在下一节中设置 React 导航。

在上面的`ShowCV`函数中，我们将从`ResumeForm`屏幕传来的`route.params`对象赋给了一个新变量。

接下来，在屏幕上呈现详细信息，从用户的个人信息开始——姓名、头像和职位:

```
<View style={styles.details}>
         <Text style={styles.titleText}>Personal Details</Text>        
         <Image
          source={{ uri: dataObj.avatarUrl }}
          style={{ width: 80, height: 80 }}
          /> 
          <Text style={styles.text}>
            <Text style={styles.key}>Name: </Text>
            <Text>{dataObj.fullName}</Text>
          </Text>    

          <Text style={styles.text}>
            <Text style={styles.key}>Professional Title: </Text>
            <Text>{dataObj.profTitle}</Text>
          </Text>  
</View>

```

我们从`avatarUrl`属性获取图像 URL，从`fullName`属性获取名称，从`profTitle`属性获取标题。

其余的表单组以类似的方式呈现。我们在主元素中嵌套了两个`Text`元素。一个包含属性名，比如`Professional Title`。另一种包含来自`dataObj`的实际值，如`Web` `Developer`。

再次，你可以[参考世博小吃项目](https://snack.expo.dev/@ubahthebuilder/295685)获取完整源代码。

当你完成了这个视图，下一步就是让它看起来更好。将以下样式表代码粘贴到`ShowCV`函数下:

```
const styles = StyleSheet.create({
  cont: {
    flex: 1,    
    backgroundColor: '#36485f',
    paddingLeft: 40,
    paddingRight: 40,        
    paddingTop: 40
  },  
  header: {    
    marginBottom: 20,
    alignSelf: 'stretch'
  },
  details: {
    marginBottom: 15
  },
  headerText: {
    fontSize: 24,
    color: '#fff',    
    borderBottomColor: '#199187',
    paddingBottom: 10,
    borderBottomWidth: 1
  },  
  titleText: {
    fontWeight: 'bold',
    color: 'yellow',
    fontSize: 15,
    marginBottom: 10
  },
  key: {
    fontWeight: 'bold'
  },
  text: {
     color: '#d3d3d3',
  }
});

```

两个屏幕现在都设置好了。下一步是在`App.js`中创建一个屏幕导航器，将两个屏幕连接在一起。

## 连接两个屏幕

在`App.js`中，首先导入以下依赖项:

```
import * as React from 'react';
import ResumeForm from './screens/ResumeForm'
import ShowCV from './screens/ShowCV'

import { NavigationContainer } from "@react-navigation/native"
import { createNativeStackNavigator } from "@react-navigation/native-stack"

```

在第二行和第三行，我们导入了刚刚创建的两个屏幕。然后，我们从`@react-navigation/native`导入`NavigationContainer`，从`@react-navigation/native-stack`导入`createNativeStackNavigator`，帮助我们连接屏幕。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们需要安装这两个库，然后才能使用它们。

如果您正在学习 Expo Snack 的教程，您会在屏幕底部看到一条消息，提示您安装这两个库。接受所有提示，包括安装`react-native-screens`和`react-native-safearea`的提示，这两个都是`react-navigation`工作所必需的。

接下来，调用`createNativeStackNavigator`来检索`Stack`:

```
const Stack = createNativeStackNavigator()

```

这个`Stack`允许你在你的应用程序中“堆叠”你想要切换的屏幕。
创建`App`组件函数，在`<Stack.Navigation>`中返回两个屏幕，如下图所示。确保将`Stack.Navigator`包裹在`<NavigationProvider>`中，否则无法工作；

```
export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen 
          name="Resume"
          component = {ResumeForm}
        />

        <Stack.Screen 
          name="ShowCV"
          component = {ShowCV}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

```

`ResumeForm`屏幕被放置在堆栈的顶部，这意味着应用程序将在完成加载后首先呈现`ResumeForm`组件。

现在一切都准备好了。您可以通过填写表格并点击`Create Resume`按钮来测试该应用程序。您应该会在`ShowCV`屏幕上看到如下所有信息:

![Resume Builder App Showing Beginning Of Final Generated Resume](img/4ab9e7467d6bb088eafec65402062912.png)

## 结论

现在你知道在 React Native 中构建一个简历生成器应用程序所需要的一切了。我们讨论了在 React 本地应用程序的屏幕之间导航的技术。

我建议你不要止步于此——在 React 原生教程的基础上定制和增强这个简历生成器应用程序。例如，您可以验证表单输入，以确保在提交表单之前已经填写了表单。

此外，您可以集成一个像`react-native-date-picker`这样的日期选择器库，来强制执行表单中开始和结束日期的自定义规则，或者确保日期有效。两者都是学习新事物的好机会。

感谢您的关注，祝您度过愉快的一周。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)