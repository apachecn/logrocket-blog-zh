# 用 Ionic React 编写 Android 应用程序

> 原文：<https://blog.logrocket.com/writing-an-android-app-with-ionic-react/>

如果您知道如何创建 React web 应用程序，那么您可以使用这些技能来开发使用 Ionic 框架的移动应用程序。在本文中，我们将看看如何使用 Ionic 框架创建一个 Android 食谱应用程序，并做出反应。

## 入门指南

让我们从安装一些东西开始。首先，通过运行以下命令全局安装 Ionic CLI:

```
npm install -g @ionic/cli native-run cordova-res
```

接下来，让我们创建我们的 Ionic 应用程序项目:

```
ionic start react-ionic-recipes-app blank  --type=react --capacitor
```

`blank`意味着我们正在创建一个空白应用。一个空白的应用程序添加了路由——这是我们唯一需要的东西。`type`被设置为`react`，这意味着我们将创建一个 React 项目。最后，`--capacitor`增加了[电容](https://capacitorjs.com/)，这是一个来自 Ionic 的跨平台原生运行时，允许我们从我们的项目文件运行和构建一个原生应用。

然后，我们将通过在`ionic-app`项目文件夹中运行以下命令来为我们的项目安装 React 挂钩:

```
npm install @ionic/react-hooks @ionic/pwa-elements
```

最后，让我们在浏览器中运行应用程序:

```
ionic serve
```

我们应该能在`[http://localhost:8100](http://localhost:8100)`上看到我们的应用。

## 用 Ionic React 创建我们的配方应用程序

现在我们应该有一个项目，可以在我们的 Android 移动设备或类似于 [Genymotion](https://www.genymotion.com/) 的模拟器上运行。

`App.tsx`是项目的入口点组件；这也是路由代码所在的位置。让我们通过编写以下内容来添加更多路线:

```
// src/App.tsx

import React from 'react';
import { Redirect, Route } from 'react-router-dom';
import { IonApp, IonRouterOutlet } from '@ionic/react';
import { IonReactRouter } from '@ionic/react-router';
import Home from './pages/Home';

/* Core CSS required for Ionic components to work properly */
import '@ionic/react/css/core.css';

/* Basic CSS for apps built with Ionic */
import '@ionic/react/css/normalize.css';
import '@ionic/react/css/structure.css';
import '@ionic/react/css/typography.css';

/* Optional CSS utils that can be commented out */
import '@ionic/react/css/padding.css';
import '@ionic/react/css/float-elements.css';
import '@ionic/react/css/text-alignment.css';
import '@ionic/react/css/text-transformation.css';
import '@ionic/react/css/flex-utils.css';
import '@ionic/react/css/display.css';

/* Theme variables */
import './theme/variables.css';
import RecipeForm from './pages/RecipeForm';

const App: React.FC = () => (
  <IonApp>
    <IonReactRouter>
      <IonRouterOutlet>
        <Route path="/home" component={Home} exact={true} />
        <Route exact path="/" render={() => <Redirect to="/home" />} />
        <Route exact path="/recipe-form/:id" component={RecipeForm} />
        <Route exact path="/recipe-form" component={RecipeForm} />
      </IonRouterOutlet>
    </IonReactRouter>
  </IonApp>
);

export default App;
```

Ionic 自带路由器，我们可以随心所欲的使用。为了添加我们的路线，我们将`recipe-form`路线添加到了`IonRouterOutlet`中。`exact`道具意味着离子路由器在路由上进行精确匹配。`path`当然定义了路由的路径，`:id`是一个路由参数占位符。`component`定义当我们导航到给定路径时，我们想要安装哪个组件。

### 构建食谱页面

我们希望在应用程序的主页上显示食谱列表。为此，我们将`Home.tsx`文件添加到`pages`文件夹中。然后，我们写道:

```
// src/pages/Home.tsx

import { IonButton, IonCol, IonContent, IonHeader, IonItem, IonLabel, IonList, IonPage, IonRow, IonTitle, IonToolbar } from '@ionic/react';
import React, { useEffect, useState } from 'react';
import './Home.css';
import { Plugins } from '@capacitor/core';
import { RouteComponentProps } from 'react-router';
import { Recipe } from '../interfaces/recipe';
const { Storage } = Plugins;

const Home: React.FC<RouteComponentProps> = ({ match }) => {
  const [recipes, setRecipes] = useState<Recipe[]>([])

  const getRecipes = async () => {
    const ret = await Storage.get({ key: 'recipes' });
    const recipes: Recipe[] = JSON.parse(ret.value as string) || [];
    setRecipes(recipes)
  }

  useEffect(() => {
    getRecipes()
  }, [match])

  const saveRecipes = async (data: Recipe[]) => {
    await Storage.set({
      key: 'recipes',
      value: JSON.stringify(data)
    });
  }

  const deleteRecipe = async (id: string) => {
    const ret = await Storage.get({ key: 'recipes' });
    const recipes: Recipe[] = JSON.parse(ret.value as string) || [];
    await saveRecipes(recipes.filter(r => r.id !== id))
    await getRecipes()
  }

  return (
    <IonPage>
      <IonHeader>
        <IonToolbar>
          <IonTitle>Recipe App</IonTitle>
        </IonToolbar>
      </IonHeader>
      <IonContent fullscreen>
        <IonRow>
          <IonCol size="12">
            <IonButton routerLink="/recipe-form">Add Recipe</IonButton>
          </IonCol>
        </IonRow>
        <IonRow>
          <IonCol>
            <IonList>
              {recipes.map(({ id, title }) => (
                <IonItem key={id}>
                  <IonLabel>{title}</IonLabel>
                  <IonButton routerLink={`/recipe-form/${id}`}>Edit</IonButton>
                  <IonButton onClick={deleteRecipe.bind(undefined, id)}>Delete</IonButton>
                </IonItem>
              ))}
            </IonList>
          </IonCol>
        </IonRow>
      </IonContent>
    </IonPage>
  );
};

export default Home;
```

在这个文件中，我们使用电容存储库来异步操作存储。

在我们的例子中，这是比本地存储更好的选择，因为本地存储只能存储少量的临时数据。本地存储还有一个数据回收策略，如果数据占用存储设备 50%以上的空间，该策略会释放空间。Capacitor 的存储 API 也是异步的，所以它不会阻碍 UI。

在`Home.tsx`中，我们通过`getRecipes`函数获取带有关键字`recipes`的数据。然后，我们调用`setRecipes`来设置`recipes`状态。

每当`match`属性改变时，我们运行`getRecipes`函数。`match`道具包含导航数据，如 URL 和 URL 参数；因此，只要导航完成，我们就能获得最新的数据。

当我们调用`deleteRecipe`时，就会调用`saveRecipes`函数。我们用关键字`recipes`获取值，解析它，然后调用`saveRecipes`，而不调用带有给定`id`的项目。这让我们删除带有给定`id`的条目。

由于这个应用程序非常小，我们可以获得所有条目，而不用担心性能损失——但是，如果我们的应用程序存储大量数据，我们应该考虑其他存储解决方案。

当我们点击**删除**按钮时，我们调用`deleteRecipe`，当我们点击**编辑**时，我们转到带有菜谱 ID 的`recipe-form` URL，这样我们就可以编辑带有给定 ID 的条目。我们还有**添加配方**按钮，可以转到`recipe-form`。`IonLabel`有`title`，我们在列表中显示。

### 添加和编辑配方

现在我们需要创建页面来添加或编辑食谱。为此，我们在`src/pages`文件夹中创建一个`RecipeForm.tsx`文件，并写入:

```
// src/pages/RecipeForm.tsx

import {
  IonBackButton,
  IonButton,
  IonButtons,
  IonContent,
  IonHeader,
  IonImg,
  IonInput,
  IonItem,
  IonLabel,
  IonList,
  IonPage,
  IonTextarea,
  IonTitle,
  IonToolbar
} from '@ionic/react';
import React, { useCallback, useEffect, useState } from 'react';
import { useCamera } from '@ionic/react-hooks/camera';
import { CameraResultType, CameraSource } from "@capacitor/core";
import { RouteComponentProps } from 'react-router';
import { Plugins } from '@capacitor/core';
import { v4 as uuidv4 } from 'uuid';
import { base64FromPath } from "@ionic/react-hooks/filesystem"
import { Recipe } from '../interfaces/recipe';
const { Storage } = Plugins;

function usePhotoGallery() {
  const { getPhoto } = useCamera();
  const [photo, setPhoto] = useState<string>('');

  const takePhoto = async () => {
    const cameraPhoto = await getPhoto({
      resultType: CameraResultType.Uri,
      source: CameraSource.Camera,
      quality: 100
    });

    const base64Data = await base64FromPath(cameraPhoto.webPath!);
    setPhoto(base64Data)
  };

  return {
    photo,
    takePhoto
  };
}

interface RecipeFormProps extends RouteComponentProps<{
  id?: string;
}> { }

const RecipeForm: React.FC<RecipeFormProps> = ({ match, history }) => {
  const { photo, takePhoto } = usePhotoGallery();
  const [title, setTitle] = useState<string>('')
  const [steps, setSteps] = useState<string>('')
  const [existingPhoto, setExistingPhoto] = useState<string>('')

  const saveRecipes = async (data: Recipe[]) => {
    await Storage.set({
      key: 'recipes',
      value: JSON.stringify(data)
    });
  }

  const save = async () => {
    if (!title) {
      alert('Title is required')
      return
    }

    if (!steps) {
      alert('Steps is required')
      return
    }
    const recipe: Recipe = {
      photo,
      title,
      steps,
      id: uuidv4()
    }

    const ret = await Storage.get({ key: 'recipes' });
    const recipes: Recipe[] = JSON.parse(ret.value as string) || [];
    if (!match.params.id) {
      await saveRecipes([...recipes, recipe])
      setExistingPhoto('')
      setTitle('')
      setSteps('')
    }
    else {
      const recipeIndex = recipes.findIndex(r => r.id === match.params.id)
      recipes[recipeIndex] = {
        photo,
        title,
        steps,
        id:match.params.id
      }
      await saveRecipes(recipes)
    }
    history.push('/');
  }

  const getRecipe = useCallback(async () => {
    if (match.params.id) {
      const ret = await Storage.get({ key: 'recipes' });
      const recipes: Recipe[] = JSON.parse(ret.value as string) || [];
      const recipe = recipes.find(r => r.id === match.params.id)
      if (match.params.id && recipe) {
        const { photo, title, steps } = recipe;
        setExistingPhoto(photo)
        setTitle(title)
        setSteps(steps)
      }
    }
  }, [match.params.id])

  useEffect(() => {
    getRecipe()
  }, [getRecipe, match.params.id])

  useEffect(() => {
    setExistingPhoto(photo)
  }, [photo])

  return (
    <IonPage>
      <IonHeader>
        <IonToolbar>
          <IonButtons slot="start">
            <IonBackButton defaultHref="/" />
          </IonButtons>
          <IonTitle>{match.params.id ? 'Edit' : 'Add'} Recipe Form</IonTitle>
        </IonToolbar>
      </IonHeader>
      <IonContent fullscreen>
        <IonHeader collapse="condense">
          <IonToolbar>
            <IonTitle size="large">Recipe Form</IonTitle>
          </IonToolbar>
        </IonHeader>
        <IonList>
          <IonItem>
            <IonButton onClick={takePhoto}>Take Recipe Photo</IonButton>
          </IonItem>
          <IonItem>
            <IonLabel>Recipe Photo</IonLabel>
            {existingPhoto && <IonImg src={existingPhoto} />}
          </IonItem>
          <IonItem lines="none">
            <IonInput value={title} placeholder="Title" onIonChange={e => setTitle(e.detail.value!)}></IonInput>
          </IonItem>
          <IonItem lines="none">
            <IonTextarea rows={10} value={steps} placeholder="Steps" onIonChange={e => setSteps(e.detail.value!)}></IonTextarea>
          </IonItem>
          <IonItem lines="none">
            <IonButton onClick={save}>Save</IonButton>
          </IonItem>
        </IonList>
      </IonContent>
    </IonPage >
  );
};

export default RecipeForm;
```

在这个组件中，我们创建了一个`usePhotoGallery`钩子来让我们拍照并返回照片的 Base64 字符串版本。

在钩子中，我们调用`useCamera`钩子来获得`getPhoto`函数，我们调用这个函数来拍照。`resultType`设置为`CameraResultType.Uri`返回照片的网址。我们用`CameraSource.Camera`将照片`source`设置到手机摄像头。`quality`设置为数字；100 是高质量。

它返回一个带有相机照片数据的承诺。`base64FromPath`让我们获得照片的 Base64 字符串版本。我们调用`setPhoto`来获取照片，并返回`photo`和`takePhoto`对象，这样我们就可以在`RecipeForm`组件中使用它们。

在`RecipeForm`组件中，我们有`match`属性来获取食谱条目的 ID。`history`有浏览器历史对象，它让我们以编程方式导航。

如果不明显的话，`saveRecipes`函数保存食谱数据；它用在`save`功能中。在其中，我们检查了`title`和`steps`状态。如果它们被设置，那么我们继续保存条目。

`title`是配方标题，`steps`是配方步骤的内容。然后我们创建带有 ID 的`recipe`对象。这用于新条目。我们调用`Storage.get`来获取带有关键字`recipes`的所有条目，然后用`JSON.parse`解析它。

如果`match.params.id`是 falsy，这意味着我们正在添加一个条目，我们将该条目添加到食谱数组的末尾，并用它调用`saveRecipes`。之后我们清除所有的州。否则，我们用`findIndex`方法找到`recipes`数组的索引，然后用我们的状态更新条目。

然后我们调用`saveRecipes`保存数据，然后调用`history.push`返回主页。如果`match.params.id`存在，`getRecipe`功能用于获取现有条目的配方数据。

我们从检索到的条目中设置`photo`、`title`和`steps`状态。我们观察`match.params.id`，所以如果我们得到一个新的值，我们总是调用`getRecipe`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

然后我们使用`useEffect`钩子来观察`match.params.id`，当它们改变时我们调用`getRecipe`。`match.params.id`有配方的 ID，所以当我们点击**编辑**时，我们用它来获取配方。

当我们更新了`photo`状态时，我们还设置了`existingPhoto`状态，这样我们就可以显示它了——只显示`existingPhoto`比在不同条件下同时显示更容易。`photo`和`existingPhoto`都是菜谱照片的 Base64 字符串。

在 JSX 中，我们有带按钮的`IonList`来调用`takePhoto`来拍照，我们有标题和步骤的输入。我们还添加了一个**保存**按钮，通过调用`save`来保存条目。

最后，我们将为组件构建一个接口。创建文件`src/interfaces/recipe.ts`并添加:

```
// src/interfaces/recipe.ts

export interface Recipe {
  photo: string;
  title: string;
  steps: string;
  id: string;
}
```

### 使用 Genymotion 运行应用程序

要使用 Genymotion 运行我们的应用程序，我们还需要做一些事情。首先，我们通过运行以下命令创建一个资产文件夹:

```
ionic build
```

我们将需要它来运行和构建我们的 Android 应用程序。接下来，通过运行以下命令添加 Android 依赖项:

```
npx cap add android
npx cap sync
```

现在我们需要安装 Android Studio 和 Genymotion，然后我们为 Android Studio 安装 Genymotion 插件。完成后，我们使用以下命令运行我们的 Ionic React 项目:

```
ionic capacitor run android --livereload --external --address=0.0.0.0
```

当被问到时，我们应该选择“VirtualBox 虚拟适配器”。这样，我们可以从 Genymotion 使用互联网。然后，在 Android Studio 中，按 Alt+Shift+F10，我们应该会看到我们的应用程序在 Genymotion 中运行。

## 结论

有了 Ionic，我们可以利用 React 的知识创建一个可以访问硬件和存储设备的 Android 移动应用程序。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)