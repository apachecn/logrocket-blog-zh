# React 本机联系人:如何访问设备的联系人列表

> 原文：<https://blog.logrocket.com/react-native-contacts-how-to-access-a-devices-contact-list/>

从打电话到在社交媒体平台上寻找朋友，联系人在数字世界中占有重要地位。昔日庞大的电话号码簿现在变成了智能手机上的压缩列表。每个人都有自己独特的联系方式，包括电话号码、电子邮件等。

在本指南中，我们将探索 React 本机联系人，这是一个强大的联系人组件，可以使用 React 本机应用程序获取、更新和添加新的联系人。我们还将学习 Expo 的类似模块。

为了跟上进度，你应该熟悉 React Native 和 [Expo](https://docs.expo.dev/) 的[基础知识，包括](https://blog.logrocket.com/how-to-build-ios-apps-using-react-native/) [JSX](https://blog.logrocket.com/diving-into-the-new-jsx-transform/) 、[组件](https://blog.logrocket.com/a-complete-guide-to-default-props-in-react-984ea8e6972d/)(类别和功能)以及造型。

您可以简单地复制并粘贴本指南中的代码块，但是我建议您通读整个教程以获得完整的理解。本指南假设您已经完成了应用程序的基本设置。

[React Native Contacts](https://github.com/morenoh149/react-native-contacts) 是一个模块，提供在 iOS 和 Android 的 React Native 应用程序中创建、获取、更新和删除联系人的功能。它使用各种承诺来完成上面提到的所有任务。这有助于它完成所有需要的任务，而不会给应用程序的 UI 线程带来太大的压力。

这个模块不需要太多的设置，因为自动链接处理大部分工作。但是对于联系人所需的权限，我们需要在链接后做一些小小的添加。

首先，我们需要将模块添加到 React 本地项目中。

```
$ yarn add react-native-contacts

```

在 Android 的情况下，自动链接处理所有的工作。我们可能需要检查的一件事是为`AndroidManifest.xml`中的联系人添加了`<uses-permission`标记:

```
<uses-permission android:name="android.permission.WRITE_CONTACTS" />

```

对于 iOS，首先我们需要在`iOS/`目录中安装 pods:

```
$ pod install

```

下一步是给`info.plist`添加一个键:

```
Privacy - Contacts Usage Description

```

您可以添加一个字符串，这是一条应该在 iOS 的权限弹出窗口中显示的消息。

![You can add the key in xcode](img/29b9d93cbb9fa6dbb55ddf6cc4ec2667.png)

要访问联系人，首先我们需要导入联系人模块。

```
import Contacts from 'react-native-contacts';

```

要从手机访问联系人，我们将使用模块的`getAll` promise。

```
Contacts.getAll().then(contacts => {
      // setContacts(contacts);
});

```

我们将在 JSON 数组的`for`中接收数据，每个对象的结构如下:

```
{
      "birthday":{
         "day":20,
         "month":1,
         "year":1978
      },
      "company":"Creative Consulting",
      "emailAddresses":[
         [
            "Object"
         ]
      ],
      "familyName":"Bell",
      "givenName":"Kate",
      "hasThumbnail":false,
      "imAddresses":[

      ],
      "jobTitle":"Producer",
      "middleName":"",
      "phoneNumbers":[
         [
            "Object"
         ],
         [
            "Object"
         ]
      ],
      "postalAddresses":[
         [
            "Object"
         ]
      ],
      "recordID":"177C371E-701D-42F8-A03B-C61CA31627F6",
      "thumbnailPath":"",
      "urlAddresses":[
         [
            "Object"
         ]
      ]
   },

```

上面是一个从`getAll`承诺收到的联系示例。

为了在列表中显示数据，我们将使用 React Native 中的`FlatList`组件。请随意使用您喜欢的任何列表组件。

下面是列表的示例代码。

**`ContactsList.js`** :

```
import React, {useEffect, useState} from 'react';
import {FlatList, View, Text, StyleSheet} from 'react-native';
import Contacts from 'react-native-contacts';
import {Contact} from '.';
const ContactsList = () => {
  const [contacts, setContacts] = useState([]);
  useEffect(() => {
    Contacts.getAll().then(contacts => {
      setContacts(contacts);
    });
  }, []);
  const keyExtractor = (item, idx) => {
    return item?.recordID?.toString() || idx.toString();
  };
  const renderItem = ({item, index}) => {
    return <Contact contact={item} />;
  };
  return (
    <FlatList
      data={contacts}
      renderItem={renderItem}
      keyExtractor={keyExtractor}
      style={styles.list}
    />
  );
};
const styles = StyleSheet.create({
  list: {
    flex: 1,
  },
});
export default ContactsList;

```

**`Contact.js`** :

```
import React from 'react';
import {View, Text, StyleSheet} from 'react-native';
const Contact = ({contact}) => {
  return (
    <View style={styles.contactCon}>
      <View style={styles.imgCon}>
        <View style={styles.placeholder}>
          <Text style={styles.txt}>{contact?.givenName[0]}</Text>
        </View>
      </View>
      <View style={styles.contactDat}>
        <Text style={styles.name}>
          {contact?.givenName} {contact?.middleName && contact.middleName + ' '}
          {contact?.familyName}
        </Text>
        <Text style={styles.phoneNumber}>
          {contact?.phoneNumbers[0]?.number}
        </Text>
      </View>
    </View>
  );
};
const styles = StyleSheet.create({
  contactCon: {
    flex: 1,
    flexDirection: 'row',
    padding: 5,
    borderBottomWidth: 0.5,
    borderBottomColor: '#d9d9d9',
  },
  imgCon: {},
  placeholder: {
    width: 55,
    height: 55,
    borderRadius: 30,
    overflow: 'hidden',
    backgroundColor: '#d9d9d9',
    alignItems: 'center',
    justifyContent: 'center',
  },
  contactDat: {
    flex: 1,
    justifyContent: 'center',
    paddingLeft: 5,
  },
  txt: {
    fontSize: 18,
  },
  name: {
    fontSize: 16,
  },
  phoneNumber: {
    color: '#888',
  },
});
export default Contact;

```

**`index.js`** :

```
import ContactsList from './contactsList';
import Contact from './contact';
export default ContactsList;
export {Contact};

```

输出应该如下所示:

![Dummy contacts from the simulator](img/37e611696270a02e7c016b034da40d3e.png)

Expo 中获取联系人的概念与上面的相同，只是在接收的数据结构上略有变化。

同样，首先我们需要导入模块:

```
import * as Contacts from "expo-contacts";

```

要从手机访问联系人，我们将使用该模块的`getAll` promise。

```
const { data } = await Contacts.getContactsAsync({
          fields: [Contacts.PHONE_NUMBERS],
});

```

我们将在 JSON 数组的`for`中接收数据，每个对象的结构如下:

```
{
  "company": "Creative Consulting",
  "contactType": "person",
  "firstName": "Kate",
  "id": "177C371E-701D-42F8-A03B-C61CA31627F6",
  "imageAvailable": false,
  "jobTitle": "Producer",
  "lastName": "Bell",
  "name": "Kate Bell",
  "phoneNumbers": Array [
    Object {
      "countryCode": "us",
      "digits": "5555648583",
      "id": "EF48385D-28C2-48DE-AAB3-A81BC5F16981",
      "label": "mobile",
      "number": "(555) 564-8583",
    },
    Object {
      "countryCode": "us",
      "digits": "4155553695",
      "id": "3CD5F927-B150-4104-918B-C26DD6AC811B",
      "label": "main",
      "number": "(415) 555-3695",
    },
  ],
}

```

以上是从`getAll`承诺处收到的联系示例。

为了在列表中显示数据，我们将使用`FlatList`组件。同样，您可以使用任何清单组件。

虚拟组件的文件和结构与裸露的 React 本地示例保持一致。

以下是列表的示例代码:

**`ContactsList.js`** :

```
import React, { useEffect, useState } from "react";
import { FlatList, View, Text, StyleSheet } from "react-native";
import Contact from "./contact";
import * as Contacts from "expo-contacts";
const ContactsList = () => {
  const [contacts, setContacts] = useState([]);
  useEffect(() => {
    (async () => {
      const { status } = await Contacts.requestPermissionsAsync();
      if (status === "granted") {
        const { data } = await Contacts.getContactsAsync({
          fields: [Contacts.PHONE_NUMBERS],
        });
        if (data.length > 0) {
          setContacts(data);
          console.log(data[0]);
        }
      }
    })();
  }, []);
  const keyExtractor = (item, idx) => {
    return item?.id?.toString() || idx.toString();
  };
  const renderItem = ({ item, index }) => {
    return <Contact contact={item} />;
  };
  return (
    <FlatList
      data={contacts}
      renderItem={renderItem}
      keyExtractor={keyExtractor}
      style={styles.list}
    />
  );
};
const styles = StyleSheet.create({
  list: {
    flex: 1,
  },
});
export default ContactsList;

```

**`Contact.js`** :

```
import React from "react";
import { View, Text, StyleSheet } from "react-native";
const Contact = ({ contact }) => {
  return (
    <View style={styles.contactCon}>
      <View style={styles.imgCon}>
        <View style={styles.placeholder}>
          <Text style={styles.txt}>{contact?.name[0]}</Text>
        </View>
      </View>
      <View style={styles.contactDat}>
        <Text style={styles.name}>{contact?.name}</Text>
        <Text style={styles.phoneNumber}>
          {contact?.phoneNumbers[0]?.number}
        </Text>
      </View>
    </View>
  );
};
const styles = StyleSheet.create({
  contactCon: {
    flex: 1,
    flexDirection: "row",
    padding: 5,
    borderBottomWidth: 0.5,
    borderBottomColor: "#d9d9d9",
  },
  imgCon: {},
  placeholder: {
    width: 55,
    height: 55,
    borderRadius: 30,
    overflow: "hidden",
    backgroundColor: "#d9d9d9",
    alignItems: "center",
    justifyContent: "center",
  },
  contactDat: {
    flex: 1,
    justifyContent: "center",
    paddingLeft: 5,
  },
  txt: {
    fontSize: 18,
  },
  name: {
    fontSize: 16,
  },
  phoneNumber: {
    color: "#888",
  },
});
export default Contact;

```

`**index.js**`:

```
import ContactsList from './contactsList';
import Contact from './contact';
export default ContactsList;
export {Contact};

```

下面是上面代码的输出:

![Contacts from simulator (expo app)](img/0db9f03241e34efd2d53c219d35d888f.png)

## 结论

React Native Contacts 和 [Expo Contacts](https://docs.expo.dev/versions/latest/sdk/contacts/) 都是在用户设备上获取和操作联系人的优秀工具。

在本教程中，我们讨论了如何在 bare 和 Expo React 本地应用程序中获取联系人。如果你想探索更多的选项，我建议你试试 [Expo modules](https://docs.expo.dev/bare/installing-expo-modules/) 的 [expo-contacts](https://docs.expo.dev/versions/latest/sdk/contacts/) 模块，在你裸露的 React 原生应用中玩联系人游戏。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)