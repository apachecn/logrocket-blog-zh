# 使用 react-three-fiber - LogRocket 博客配置 3D 模型

> 原文：<https://blog.logrocket.com/configure-3d-models-react-three-fiber/>

将 3D 模型加载到 WebGL 场景中是浏览器中 3D 渲染最复杂的方面之一。虽然像 Three.js 和 Babylon.js 这样的库提供了强大的 API 来帮助缓解使用 WebGL 的压力，但它们也不能避免自己繁琐的过程，例如从头开始创建着色器，以及在向场景添加对象时重用`scene.add()`。

react-three-fiber 是 Three.js 的 react 渲染器，通过处理必要的 Three.js 功能并通过钩子提供对原始 Three.js 对象的访问，简化了在 web 上处理 3D 模型的工作。

R3F 既不提高也不降低 Three.js 的基本性能；它保持不变，因为 R3F 是构建在 Three 之上的库。除了 React 的组件模型之外，它们具有相同的特征，R3F 使用组件模型来平衡复杂性和性能。

与 Three.js 相比，在 R3F 上扩展性能要容易得多，因为它提供了一种全面而简单的方法来减轻 WebGL 对硬件资源的昂贵使用。例如，在 R3F 中实现按需渲染性能优化就像给`Canvas`组件添加一个`frameloop`道具和一个`demand`值一样简单:

```
<canvas frameloop="demand"></canvas>
```

其他性能优化包括:实例化、重用几何体和材质、减少材质细节层次、运动回归等。

在本文中，我们将介绍如何在 React 项目中使用 react-three-fiber 渲染和配置在 3D 软件程序(如 Blender 或 Maya)中创建的 3D 资产。到本文结束时，您将能够在自己的网站上渲染 3D 模型。

### 先决条件

为了跟随本教程，你需要 React 和 [Three.js 架构的基础知识，包括照明、几何和画布](https://blog.logrocket.com/intro-to-three-js-for-game-developers/)。您还需要在您的机器上安装 Node.js。我们开始吧！

## 为 web 创建和准备 3D 模型

Three.js 支持多种 3D 模型格式。由于这些文件格式的复杂性和难度，建议尽可能使用 glTF (GL 传输格式)。

专注于运行时资产交付，glTF 资产可以快速加载，并可以压缩成紧凑的大小进行传输。glTF 资产以 JSON `.gltf`和二进制`.glb`两种格式提供。每种支持的文件格式在 Three.js 中都有各自的加载器。例如，您可以使用 glTF 加载器将 glTF 或 GLB 文件加载到 react-three-fiber 场景中。

要创建您的模型，您可以使用您选择的 3D 建模软件，但根据我的经验，大多数人更喜欢 Blender。如果你像我一样，没有任何创建 3D 模型的经验，你可以从 sketchfab.com 这样的网站外包公共领域的 glTF 文件。

这些网站上的大部分免费资源都受到[知识共享许可](https://help.sketchfab.com/hc/en-us/articles/115004276346-Licenses)的保护，这意味着只要你注明原创者，你就可以在你的项目中使用它们。在将资产包含到项目中之前，检查其使用权限是明智的。

如果您计划将动画或事件映射等动作添加到模型中，您应该以这样的方式设计资源，即每个部分或网格都被正确地分组和分离。不幸的是，网上很少有免费资源实现了这一点，所以你需要购买一个或者自己构建一个。

在本教程中，我们将使用[鞋子资产](https://codesandbox.io/s/shoe-configurator-qxjoj)作为示例项目，它是由[尤里·阿提乌克](https://twitter.com/akella)创建的。如果你发现很难建立或找到一个模型，你可以从 [Three.js GitHub repo](https://github.com/mrdoob/three.js/tree/master/examples/models/gltf) 得到一个。

## 将 glTF 转换为 GLB

glTF 基于 JSON，这意味着它的一些数据存储在外部，例如，几何体、着色器、纹理和动画数据。然而，GLB 将这些数据存储在内部，使其大小比 glTF 小得多。因此，`.glb`二进制格式最适合在您的项目中使用。现在，让我们把 glTF 文件转换成 GLB。

有几个工具可以用于这个操作，但是，[glTF-pipeline](https://github.com/CesiumGS/gltf-pipeline)是最好的选择之一。这是一个用于优化 glTF 资产的灵活工具，它有几个扩展，可以直接从命令行工具执行常见操作，如 GLB 到 glTF 的转换、glTF 压缩等等。

或者，您可以使用 VS 代码的官方 [glTF 工具扩展](https://marketplace.visualstudio.com/items?itemName=cesium.gltf-vscode),直接从编辑器中预览、调试和转换模型。您还可以使用这个扩展来编辑和调整 glTF 文件。例如，如果模型的网格命名不正确，您可以编辑文件并根据需要重命名。

要使用 glTF-pipeline 开始转换过程，首先，我们需要使用以下命令在我们的计算机上全局安装它:

```
npm install -g gltf-pipeline

```

接下来，将 glTF 文件放在一个空文件夹中，并打开命令行工具。`cd`放入文件夹，运行下面的命令:

```
gltf-pipeline -i <source file> -o <output file>

```

用源文件的名称替换上面命令中的第一个占位符。用您首选的输出文件名替换第二个占位符:

```
gltf-pipeline -i shoe.gltf -o shoe.glb

```

运行上面的命令后，您应该会在文件夹中看到一个新的 GLB 文件，与之前的 glTF 文件放在一起。这个过程也可以反过来将 GLB 转换成 glTF 格式。

## 压缩模型

为了避免由于 3D 资源的大小而降低我们网站的性能，我们将在将它加载到我们的场景之前对其进行压缩。建议您的文件大小不要超过 1 到 2 兆字节。我们将使用名为 [Draco](https://google.github.io/draco/) 的 glTF 管道压缩扩展:

```
gltf-pipeline -i <source file> -o <output file> --draco.compressionLevel=10

```

上面的命令类似于我们之前在转换过程中使用的命令，唯一的区别是压缩级别标志。Draco 压缩的最大值为 10，最小值为零。将`10`传递到压缩级别提供了最大可能的压缩。

现在，我们可以建立一个项目，创建一个场景，并加载我们的模型。

## 建立我们的反应三纤维项目

首先，让我们用 Create React App 创建一个新的 React 项目:

```
npx create-react-app react-three

```

然后，用下面的命令安装 react-three-fiber:

```
npm install three @react-three/fiber

```

完成后，继续运行下面的命令来安装项目所需的所有依赖项:

```
npm i @react-three/drei react-colorful valtio

```

上面的命令将安装以下依赖项:

*   [React-colorful](https://www.npmjs.com/package/react-colorful):React 的拾色器组件
*   drei :为 react-three-fiber 提供有用的附件，比如相机、飞机和控制
*   Valtio :一个轻量级的、基于代理的 React 状态管理工具

接下来，我们将在一个空组件中创建一个场景。首先，导入并创建一个空画布，如下所示:

```
import React from "react";
import { Canvas } from "react-three-fiber";
import "./styles.css";
export default function App() {
  return <Canvas style={{ background: "#171717" }}></Canvas>;
}

```

现在，我们的项目和场景已经设置好，可以开始加载我们的模型了！在我们可以将资产加载到场景中并对其进行配置之前，我们需要找到一种简单的方法来将模型转换为组件。

## 将模型转换成可重用的 React 组件

将 glTF 模型加载到 Three.js 场景中需要大量的工作。在我们可以配置或动画我们的模型的网格，我们需要迭代通过我们的模型网格的每个部分，并分别保存它们。

对我们来说幸运的是，react-three-fiber 有一个名为 [gltfjsx](https://github.com/pmndrs/gltfjsx) 的出色的实用程序包，它可以分解模型，并将其编译成一个声明性的、可重用的 jsx 组件。

首先，打开您的命令行工具，`cd`进入您的压缩 glTF 模型所在的文件夹，并运行下面的命令:

```
npx gltfjsx <glTF model source file>

```

在我们之前下载的鞋子模型上运行上面的命令。现在，上面的代码将如下所示:

```
npx gltfjsx shoe-draco.gltf

```

上面的命令将返回一个 JavaScript 文件，该文件以 React 功能组件的格式规划所有资产的内容。该文件的内容将类似于以下代码:

```
import React, { useRef } from 'react'
import { useGLTF } from '@react-three/drei/useGLTF'

function Shoe({ ...props }) {
 const group = useRef()
 const { nodes, materials } = useGLTF('/shoe-draco.glb')
 const snap = useSnapshot(state);
 return (
<group ref={group} {...props} dispose={null}>
  <group ref={group} {...props} dispose={null}>
    <mesh geometry={nodes.shoe.geometry} material={materials.laces}/>
    <mesh geometry={nodes.shoe_1.geometry} material={materials.mesh}/>
    <mesh geometry={nodes.shoe_2.geometry} material={materials.caps} />
    <mesh geometry={nodes.shoe_3.geometry} material={materials.inner} />
    <mesh geometry={nodes.shoe_4.geometry} material={materials.sole}/>
    <mesh geometry={nodes.shoe_5.geometry} material={materials.stripes}/>
    <mesh geometry={nodes.shoe_6.geometry} material={materials.band}/>
    <mesh geometry={nodes.shoe_7.geometry} material={materials.patch} />
   </group>
 </group>
)}

```

节点和材质的值被`useGLTF`钩子析构，而模型的路径作为参数传递给钩子。

析构节点是一个包含模型中所有信息的对象，包括动画、纹理和几何图形。在这种情况下，节点使用模型的重构几何体，并作为道具传递到网格组件中。

## 将模型组件添加到场景中

您可以将新创建的文件按原样放入项目的 src 文件夹中，也可以将文件中的代码复制并粘贴到项目的现有组件中。

若要将模型添加到场景中，请像导入任何其他 React 组件一样导入它:

```
import Shoe from './Shoe.js'

```

接下来，将之前压缩的 glTF 文件移动到`/public`文件夹中。最后，在画布中添加组件:

```
import React from "react";
import { Canvas } from "react-three-fiber";
import Shoe from './Shoe.js'
import "./styles.css";

export default function App() {
  return(
<Canvas style={{ background: "#171717" }}>
   <Shoe />
</Canvas>;
)};

```

您也可以将它添加到您的组件中，如下所示:

```
import React, { useRef } from "react"
import { Canvas } from "react-three-fiber"
import { useGLTF } from '@react-three/drei/useGLTF'
import "./styles.css";

function Shoe({ ...props }) {
 const group = useRef()
 const { nodes, materials } = useGLTF('/shoe-draco.glb')
 return (
<group ref={group} {...props} dispose={null}>
  <group ref={group} {...props} dispose={null}>
    <mesh geometry={nodes.shoe.geometry} material={materials.laces}/>
    <mesh geometry={nodes.shoe_1.geometry} material={materials.mesh}/>
    <mesh geometry={nodes.shoe_2.geometry} material={materials.caps} />
    <mesh geometry={nodes.shoe_3.geometry} material={materials.inner} />
    <mesh geometry={nodes.shoe_4.geometry} material={materials.sole}/>
    <mesh geometry={nodes.shoe_5.geometry} material={materials.stripes}/>
    <mesh geometry={nodes.shoe_6.geometry} material={materials.band}/>
    <mesh geometry={nodes.shoe_7.geometry} material={materials.patch} />
   </group>
 </group>
)}

export default function App() {
  return(
<Canvas style={{ background: "#171717" }}>
   <Shoe />
</Canvas>;
)};

```

此时，如果启动开发服务器，React 将抛出编译错误。模型组件是异步的，因此，我们必须将它嵌套在画布中的`<Suspense>`组件中，让我们能够控制中间加载回退和错误处理。

首先，我们需要从 React 导入模型组件，然后在画布中将它包裹在模型周围:

```
 import React, { Suspense, useRef } from "react";
import { Canvas } from "react-three-fiber";
import { useGLTF } from '@react-three/drei/useGLTF'
import "./styles.css";

function Shoe({ ...props }) {
 const group = useRef()
 const { nodes, materials } = useGLTF('/shoe-draco.glb')
 return (
<group ref={group} {...props} dispose={null}>
  <group ref={group} {...props} dispose={null}>
    <mesh geometry={nodes.shoe.geometry} material={materials.laces}/>
    <mesh geometry={nodes.shoe_1.geometry} material={materials.mesh}/>
    <mesh geometry={nodes.shoe_2.geometry} material={materials.caps} />
    <mesh geometry={nodes.shoe_3.geometry} material={materials.inner} />
    <mesh geometry={nodes.shoe_4.geometry} material={materials.sole}/>
    <mesh geometry={nodes.shoe_5.geometry} material={materials.stripes}/>
    <mesh geometry={nodes.shoe_6.geometry} material={materials.band}/>
    <mesh geometry={nodes.shoe_7.geometry} material={materials.patch} />
   </group>
 </group>
)}

export default function App() {
  return(
<Canvas style={{ background: "#171717" }}>
  <Suspense fallback={null}>
     <Shoe />
  </Suspense>
</Canvas>;
)};

```

上面的代码将清除错误，我们的模型将成功地显示在浏览器上。然而，无论我们加载到场景中的是什么，都只会显示为模型的轮廓。

![React Three Fiber Model Silhouette](img/ea0b4fe7ef549b8db048842a7d9ea425.png)

目前，我们的场景没有光源。一个常见的解决方案是简单地在画布中的`<Suspense>`之前添加`ambientLight`和`spotLight`组件:

```
<Canvas style={{ background: "#171717" }}>
<ambientLight intensity={1} />
<spotLight intensity={0.5} angle={0.1} penumbra={1} position={[10, 15, 10]} castShadow />
  <Suspense fallback={null}>
      <Shoe />
   </Suspense>
</Canvas>;

```

之后，模型将正确显示:

![React Three Fiber Scene Lighting Source](img/142e4d786df342583e66fa0b61553faf.png)

继续摆弄灯光的强度、角度和位置。如果你对结果不满意，你也可以通过添加一个定位道具来定位灯光。

## 配置模型

既然我们的模型是声明性的，我们就可以完全访问它的网格，这意味着我们可以添加、删除和更改模型的某些部分。

例如，如果代码中的第一个网格组件表示鞋子的鞋带，我们删除或注释掉该网格，鞋子的鞋带将在场景中消失。您可以决定制作动画、添加事件，甚至在模型上执行条件。

要改变模型，只需改变道具。如果我们要将第一个网格的颜色改为红色，添加`material-color="Red"`作为道具。在场景中，模型的该部分将变为红色:

```
<mesh material={materials.White} geometry={nodes['buffer-0-mesh-0'].geometry} material-color="Red"/>

```

我们可以用 React 的`useState`钩子，以及任何支持悬念的状态管理库动态地做同样的事情。在这种情况下，我们将使用之前安装的 Valtio。

## 用 Valtio 配置我们的模型

继续从 Valtio 导入`proxy`和`useSnapshot`:

```
import {proxy, useSnapshot} from 'valtio'

```

接下来，复制下面的代码并将其粘贴到您的模型组件之前:

```
const state = proxy({
  current: null,
  items: {
    laces: "#ff3",
    mesh: "#3f3",
    caps: "#3f3",
    inner: "#3f3",
    sole: "#3f3",
    stripes: "#3f3",
    band: "#3f3",
    patch: "#3f3",
  },
})

```

我们用两个键创建了一个对象，`items`和`current`。`current`有一个值`null`，而`items`有一个对象值和一个十六进制颜色值，对象值的关键点代表我们模型的每个部分。然后用代理包装该对象。

为了使用组件内部的状态，我们将在模型组件内部创建一个常量变量`snap`，并将其赋值为`useSnapshot`。之后，我们将状态作为参数传递到快照中:

```
import React, { useRef, Suspense} from 'react'
import { Canvas } from "react-three-fiber";
import "./styles.css";
import { useGLTF } from '@react-three/drei/useGLTF'
import {proxy, useSnapshot} from 'valtio';

const state = proxy({
  current: null,
  items: {
    laces: "#ff3",
    mesh: "#3f3",
    caps: "#3f3",
    inner: "#3f3",
    sole: "#3f3",
    stripes: "#3f3",
    band: "#3f3",
    patch: "#3f3",
  },
})

function Shoe({ ...props }) {
 const group = useRef()
 const { nodes, materials } = useGLTF('/shoe-draco.glb')
 const snap = useSnapshot(state);
 return (
<group ref={group} {...props} dispose={null}>
  <group ref={group} {...props} dispose={null}>
    <mesh geometry={nodes.shoe.geometry} material={materials.laces}/>
    <mesh geometry={nodes.shoe_1.geometry} material={materials.mesh}/>
    <mesh geometry={nodes.shoe_2.geometry} material={materials.caps} />
    <mesh geometry={nodes.shoe_3.geometry} material={materials.inner} />
    &lt;mesh geometry={nodes.shoe_4.geometry} material={materials.sole}/>
    <mesh geometry={nodes.shoe_5.geometry} material={materials.stripes}/>
    <mesh geometry={nodes.shoe_6.geometry} material={materials.band}/>
    <mesh geometry={nodes.shoe_7.geometry} material={materials.patch} />
   </group>
 </group>
)}

export default function App() {
  return <Canvas style={{ background: "#171717" }}>
<ambientLight intensity={1} />
<spotLight intensity={0.5} angle={0.1} penumbra={1} position={[10, 15, 10]} castShadow />
  <Suspense fallback={null}>
      <Shoe />
  </Suspense>
</Canvas>;
}

```

在组件中使用状态快照之前，我们必须将每个网格的材质颜色设置为状态变量 color。为此，我们将像之前一样给每个网格添加`material-color`道具。这一次，我们将把我们州的快照传递给它:

```
const state = proxy({
  current: null,
  items: {
    laces: "#ff3",
    mesh: "#3f3",
    caps: "#3f3",
    inner: "#3f3",
    sole: "#3f3",
    stripes: "#3f3",
    band: "#3f3",
    patch: "#3f3",
  },
})

function Shoe({ ...props }) {
 const group = useRef()
 const { nodes, materials } = useGLTF('/shoe-draco.glb')
 const snap = useSnapshot(state);
 return (
<group ref={group} {...props} dispose={null}>
  <group ref={group} {...props} dispose={null}>
    <mesh geometry={nodes.shoe.geometry} material={materials.laces} material-color={snap.items.laces}/>
    <mesh geometry={nodes.shoe_1.geometry} material={materials.mesh} material-color={snap.items.mesh}/>
    <mesh geometry={nodes.shoe_2.geometry} material={materials.caps} material-color={snap.items.caps}/>
    <mesh geometry={nodes.shoe_3.geometry} material={materials.inner} material-color={snap.items.inners}/>
    <mesh geometry={nodes.shoe_4.geometry} material={materials.sole} material-color={snap.items.sole}/>
    <mesh geometry={nodes.shoe_5.geometry} material={materials.stripes} material-color={snap.items.stripes}/>
    <mesh geometry={nodes.shoe_6.geometry} material={materials.band} material-color={snap.items.band}/>
    <mesh geometry={nodes.shoe_7.geometry} material={materials.patch} material-color={snap.items.patch}/>
   </group>
 </group>
  )
  }

```

现在，如果我们改变状态项中的任何颜色，模型的该部分将更新其在场景中的颜色。

## 添加事件

我们可以向模型中添加事件，而不是手动更改状态，这样用户就可以选择鞋子的每个部分，并用颜色选择器更改颜色。

首先，我们将向模型的`group`组件添加`onPointerDown`和`onPointerMissed`事件。我们将创建 DOM 事件函数，该函数为被单击的模型部分设置状态，并在未选择任何内容时设置`null`:

```
<group ref={group} {...props} dispose={null}
 onPointerDown={(e) => {e.stopPropagation(); state.current = e.object.material.name }}
onPointerMissed={(e) =>{state.current = null} }
>

```

现在，我们将从 react-colorful 导入`HexColorPicker`。我们将创建一个新的组件，它将包含`HexColorPicker`组件和一个 header 标签，该标签显示被点击的模型部分的名称:

```
import {HexColorPicker} from 'react-colorful'

 function ColorPicker(){
 const snap = useSnapshot(state);
  return(
   <div>
     <HexColorPicker color={snap.items[snap.current]} onChange={(color)=>(state.items[state.current] = color)}/>
     <h1>{snap.current}</h1>
   </div>
    )
  }

```

在我们保存代码后，浏览器上会立即出现一个颜色选择器 GUI，如下图所示:

![React Three Fiber Color Picker GUI](img/d4f32a5966914baa4c5f0c0752b712f5.png)

现在，要更改模型网格的颜色，我们可以单击它并用颜色选择器选择一种颜色。你也可以添加`classNames`到颜色选择器和一个`<H1>`标签。

![React Three Fiber Custom Styling Color GUI](img/d7d567a45abf90dac7671654a8f1c5d1.png)

## 制作模型动画

现在，这个模型感觉不像一个 3D 物体。具体来说，它缺乏像静态图像那样的深度和运动。我们可以通过向画布添加一个`orbitControls`组件来解决这个问题:

```
 export default function App() {
      return (
<Canvas style={{ background: "#171717" }}>
 <spotLight intensity={0.5} angle={0.1} penumbra={1} position={[10, 15, 10]} castShadow />
 &lt;ambientLight intensity={1} />
 <Suspense fallback={null}>
          <Shoe />
 </Suspense>
       <orbitControls />
</Canvas>;
    )}

```

在 react-three-fiber 中，模型是用`useFrame`钩子来制作动画的，这类似于 JavaScript `requestAnimationFrame()`方法。它每秒返回 60 次或更多的回调，这取决于显示刷新率。

如果我们将模型在 y 轴上的旋转值设置为`useFrame`回调函数中的`"5.09"`，模型将每秒钟沿着 y 轴上下平移 60 次，产生一个浮动对象的错觉。

为了获得更真实的动画，您可以改变`useFrame`钩子中 z 轴和 x 轴的旋转值。为了节省时间，我们将使用下面的代码:

```
useFrame( (state) => {
 const t = state.clock.getElapsedTime()
 ref.current.rotation.z = -0.2 - (1 + Math.sin(t / 1.5)) / 20
 ref.current.rotation.x = Math.cos(t / 4) / 8
 ref.current.rotation.y = Math.sin(t / 4) / 8
 ref.current.position.y = (1 + Math.sin(t / 1.5)) / 10
  })

```

记得从 react-three-fiber 导入`useFrame`。

我们项目的完整代码如下:

```
import {HexColorPicker} from 'react-colorful'
import React, { useRef, Suspense} from 'react'
import { Canvas, useFrame } from "react-three-fiber";
import "./styles.css";
import { useGLTF } from '@react-three/drei/useGLTF'
import {proxy, useSnapshot} from 'valtio';

const state = proxy({
  current: null,
  items: {
    laces: "#ff3",
    mesh: "#3f3",
    caps: "#3f3",
    inner: "#3f3",
    sole: "#3f3",
    stripes: "#3f3",
    band: "#3f3",
    patch: "#3f3",
  },
})

function Shoe({ ...props }) {
 const group = useRef()

useFrame( (state) => {
 const t = state.clock.getElapsedTime()
 ref.current.rotation.z = -0.2 - (1 + Math.sin(t / 1.5)) / 20
 ref.current.rotation.x = Math.cos(t / 4) / 8
 ref.current.rotation.y = Math.sin(t / 4) / 8
 ref.current.position.y = (1 + Math.sin(t / 1.5)) / 10
  })

 const { nodes, materials } = useGLTF('/shoe-draco.glb')
 const snap = useSnapshot(state);
 return (
<group 
ref={group} 
{...props} 
dispose={null}
onPointerDown={(e) => {e.stopPropagation(); state.current = e.object.material.name }}
onPointerMissed={(e) =>{state.current = null} }
>
  <group ref={group} {...props} dispose={null}>
    <mesh geometry={nodes.shoe.geometry} material={materials.laces}/>
    <mesh geometry={nodes.shoe_1.geometry} material={materials.mesh}/>
    <mesh geometry={nodes.shoe_2.geometry} material={materials.caps} />
    <mesh geometry={nodes.shoe_3.geometry} material={materials.inner} />
    <mesh geometry={nodes.shoe_4.geometry} material={materials.sole}/>
    <mesh geometry={nodes.shoe_5.geometry} material={materials.stripes}/>
    <mesh geometry={nodes.shoe_6.geometry} material={materials.band}/>
    <mesh geometry={nodes.shoe_7.geometry} material={materials.patch} />
   </group>
 </group>
)}

function ColorPicker(){
 const snap = useSnapshot(state);
  return(
   <div>
     <HexColorPicker color={snap.items[snap.current]} onChange={(color)=>(state.items[state.current] = color)}/>
     <h1>{snap.current}</h1>
   </div>
    )
  }

export default function App() {
  return (
  <>
<Canvas style={{ background: "#171717" }}>
<ambientLight intensity={1} />
<spotLight intensity={0.5} angle={0.1} penumbra={1} position={[10, 15, 10]} castShadow />
  <Suspense fallback={null}>
      <Shoe />
  </Suspense>
</Canvas>;
<ColorPicker />
</>
)}

```

## 其他使用案例

向 Three.js 场景添加模型是在 web 上构建 3D 体验的第一步；这个过程需要更多的努力，R3F 可以无缝地管理场景，无论是在电子商务网站上添加 3D 产品浏览器，VR/AR 体验，甚至是游戏。

R3F 的局限性在于一个人的想象力。以下是一些展示 react-three-fiber 可能实现的功能的示例项目:

![R3F Particles](img/a9621212defb07cd3f3d69ea0255d8ad.png)

[https://codesandbox.io/s/jflps](https://codesandbox.io/s/jflps)

![R3F Dots](img/b8b7b60948b6a5d907b43da8238f9736.png)

[https://codesandbox.io/s/nor86](https://codesandbox.io/s/nor86)

![R3F Magnify Text](img/3cef0dd52103da0f3f300d7e7a9320fe.png)

[https://codesandbox.io/s/nor86](https://codesandbox.io/s/nor86)

## 结论

在本文中，我们介绍了如何为 web 配置 3D 模型，以及如何使用 Valtio 状态管理将事件映射到模型。我们澄清了 glTF 和 GLB 文件之间的区别，决定了在我们的项目中哪一个是最好的。

我们向模型中添加了事件，允许我们创建定制工具，如颜色选择器和光源。你可以挑战自己，在你的模型中加入更多的元素，比如一个影子投射者或者一个悬停事件。

希望这篇文章能成为将 3D 模型整合到你的网站的有用资源。编码快乐！

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。