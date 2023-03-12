# 创建一个自定义的 React 本地下拉式日志博客

> 原文：<https://blog.logrocket.com/creating-custom-react-native-dropdown/>

有许多第三方 React 本地库为您的应用程序提供下拉菜单，这些功能允许用户从项目列表中选择一个项目来填充表单字段。

但是大多数第三方库实际上是移动术语中所谓的“spinners”。在 Android 上，它们类似于下拉菜单，而在 iOS 上，它们可能看起来像屏幕底部的一个旋转的轮子，也可能有其他配置，这取决于组件中的道具和您使用的 iOS 版本。

在本文中，我们将构建您在 web 开发中可能习惯的下拉列表类型:一个基本的选择表单字段，在 Android 和 iOS 手机上看起来基本相同。

## 为 React Native 创建下拉字段

我使用 [create-react-native-app](https://github.com/expo/create-react-native-app) 创建了这个项目。你可以在这里找到 GitHub repo [和](https://github.com/eristoddle/log-rocket-react-native-dropdown-tutoria)[世博小吃](https://snack.expo.dev/@eristoddle/react-native-select-dropdown)。这是下拉组件的起点。

它有一个`TouchableOpacity`按钮，你可以触摸它使下拉菜单可见或不可见，但是现在，显示下拉菜单的功能只显示一些文本。我唯一使用的第三方库是 [react-native-elements](https://www.npmjs.com/package/react-native-elements) ，它使得向下拉菜单添加图标变得简单。

注意，只有一个状态变量用于切换可见性，并且`dropdown`元素的位置是绝对的，所以它在表单的其余部分打开。

```
import React, { FC, useState } from 'react';
import { StyleSheet, Text, TouchableOpacity } from 'react-native';
import { Icon } from 'react-native-elements';

interface Props {
  label: string;
}

const Dropdown: FC<Props> = ({ label }) => {
  const [visible, setVisible] = useState(false);

  const toggleDropdown = () => {
    setVisible(!visible);
  };

  const renderDropdown = () => {
    if (visible) {
      return (
          <Text style={styles.dropdown}>
            This is where the dropdown will be rendered.
          </Text>
      );
    }
  };

  return (
    <TouchableOpacity
      style={styles.button}
      onPress={toggleDropdown}
    >
      {renderDropdown()}
      <Text style={styles.buttonText}>{label}</Text>
      <Icon type='font-awesome' name='chevron-down'/>
    </TouchableOpacity>
  );
}

const styles = StyleSheet.create({
  button: {
    flexDirection: 'row',
    alignItems: 'center',
    backgroundColor: '#efefef',
    height: 50,
    width: '90%',
    paddingHorizontal: 10,
    zIndex: 1,
  },
  buttonText: {
    flex: 1,
    textAlign: 'center',
  },
  dropdown: {
    position: 'absolute',
    backgroundColor: '#fff',
    top: 50,
  },
});

export default Dropdown;

```

在这里，我修改了 create-react-native-app 生成的`App.tsx`文件，使其包含 dropdown 组件和一些打开时可以覆盖的文本，但文件的其余部分保持不变:

```
export default function App() {
  return (
    <View style={styles.container}>
      <Dropdown label={'Select Item'} />
      <Text>This is the rest of the form.</Text>
      <StatusBar style="auto" />
    </View>
  );
}

```

下面是它的实际应用:

![Dropdown Skeleton](img/8b25c3334d6a8ae6661a87386ebc9681.png)

## 在 React Native 中创建真正的下拉菜单

这就是有趣的地方。我们的组件还需要几个道具:一个用于填充下拉列表项的数据，以及一个在选择一项时触发的函数。

这是下拉菜单中道具的新界面:

```
interface Props {
  label: string;
  data: Array<{ label: string; value: string }>;
  onSelect: (item: { label: string; value: string }) => void;
}

```

## 为下拉菜单使用模态

为了创建下拉列表，我们将使用一个`[Flatist](https://blog.logrocket.com/deep-dive-react-native-flatlist/)`。它对于这类事情来说很方便，并且具有内置功能，这使得它比`ScrollView`更具性能。

我们可以单独使用`FlatList`，除了一个原因。当你点击下拉列表之外的时候，它不会自动关闭，会迫使用户选择一个项目来让它消失。

因此，为了获得我们想要的功能，我们必须将`renderDropdown`函数改为:

```
const renderDropdown = (): ReactElement<any, any> => {
    return (
      <Modal visible={visible} transparent animationType="none">
        <TouchableOpacity
          style={styles.overlay}
          onPress={() => setVisible(false)}
        >
          <View style={[styles.dropdown, { top: dropdownTop }]}>
            <FlatList
              data={data}
              renderItem={renderItem}
              keyExtractor={(item, index) => index.toString()}
            />
          </View>
        </TouchableOpacity>
      </Modal>
    );
  };

```

在`FlatList`上使用的道具数量很少:

*   `data`:用于建立列表的数据
*   `renderItem`:返回每个项目的组件
*   `keyExtractor`:设置物品上的按键，以便识别。大多数情况下，您可以复制并粘贴这个相同的函数

在代码块中，我们在`Modal`上设置了`transparent`道具。这意味着`Modal`将充满整个屏幕，但是任何没有内容的部分将被透明覆盖。

在`Modal`内部，我们添加了一个 100%宽和高的`TouchableOpacity`来覆盖整个覆盖图，这样当点击它时，我们可以将`visible`设置为`false`并隐藏`Modal`。

包装`FlatList`的`View`为模态的可见部分设置样式，并且拥有所有元素中最多的样式属性:

```
dropdown: {
    position: 'absolute',
    backgroundColor: '#fff',
    width: '100%',
    shadowColor: '#000000',
    shadowRadius: 4,
    shadowOffset: { height: 4, width: 0 },
    shadowOpacity: 0.5,
  },

```

## 修改可见性切换功能

您可能已经注意到，在我们刚刚检查的视图中，样式中的`top`值是用一个变量设置的。

因为模态覆盖了整个屏幕，下拉菜单在其中有一个绝对位置，我们必须测量按钮在屏幕上的位置，并将这个`top`值设置为按钮的高度加上它离屏幕顶部的距离。

所以，我们还需要几个常量:

```
const DropdownButton = useRef();
const [dropdownTop, setDropdownTop] = useState(0);

```

并将我们创建的引用添加到按钮:

```
<TouchableOpacity
  ref={DropdownButton}
  style={styles.button}
  onPress={toggleDropdown}
>

```

当我们将下拉菜单的可见性设置为`false`时，功能不会改变。但是当我们使下拉菜单可见时，我们从引用中获得按钮的当前实例，并使用元素的`measure`方法来找到它的高度和它在屏幕上的垂直位置。然后我们将`dropdownTop`状态变量设置为高度和垂直位置的和。

```
const toggleDropdown = (): void => {
  visible ? setVisible(false) : openDropdown();
};

const openDropdown = (): void => {
  DropdownButton.current.measure((_fx, _fy, _w, h, _px, py) => {
    setDropdownTop(py + h);
  });
  setVisible(true);
};

```

## 呈现项目

还记得`FlatList`的`renderItem`道具吗？下面是实现这一点的函数:

```
const renderItem = ({ item }): ReactElement<any, any> => (
  <TouchableOpacity style={styles.item} onPress={() => onItemPress(item)}>
    <Text>{item.label}</Text>
  </TouchableOpacity>
);

```

没什么大不了的。我们需要显示标签，并在项目被按下时做一些事情。

下面是`onItemPress`函数:

```
 const onItemPress = (item): void => {
    setSelected(item);
    onSelect(item);
    setVisible(false);
  };

```

第一行设置一个状态变量来跟踪选中的项目。

```
const [selected, setSelected] = useState(undefined);

```

然后，当按钮标签存在时，我们使用所选项目来替换它:

```
<Text style={styles.buttonText}>
  {(selected && selected.label) || label}
</Text>

```

函数中的下一行调用`onSelect`属性来设置项目。然后，我们隐藏下拉菜单。列表中的每一项都应用了最少量的样式来填充和分隔它们。

```
item: {
  paddingHorizontal: 10,
  paddingVertical: 10,
  borderBottomWidth: 1,
},

```

## 修改`App.tsx`文件

下面是新的`App.tsx`函数:

```
const App: FC = () => {
  const [selected, setSelected] = useState(undefined);
  const data = [
    { label: 'One', value: '1' },
    { label: 'Two', value: '2' },
    { label: 'Three', value: '3' },
    { label: 'Four', value: '4' },
    { label: 'Five', value: '5' },
  ];

  return (
    <View style={styles.container}>
      {!!selected && (
        <Text>
          Selected: label = {selected.label} and value = {selected.value}
        </Text>
      )}
      <Dropdown label="Select Item" data={data} onSelect={setSelected} />
      <Text>This is the rest of the form.</Text>
    </View>
  );
};

```

我们为下拉列表中选中的项目添加了一个状态变量、一些要使用的数据、一个显示选中的`Item`的`Text`元素以及下拉列表组件的新道具。结果如下:

![Final Dropdown](img/c8afd780a234d76423f131efe7da2b2d.png)

## 结论

我们创建的下拉组件通常在 Android 或 iOS 上看起来是一样的。它现在非常基础，但是随着主要功能的完成，你可以添加更好的风格和更多的特性。

它确实为下拉图标使用了一个外部库，如果您想要一个自包含的组件，可以很容易地用图像替换它。你可以在这个 [GitHub repo](https://github.com/eristoddle/log-rocket-react-native-dropdown-tutoria) 找到这个教程的代码。随意叉一下，添加功能！你也可以在这个[世博小吃](https://snack.expo.dev/@eristoddle/react-native-select-dropdown)中看到它的作用。

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)