# 使用 SVGLoader - LogRocket 博客将 SVG 引入 Three.js

> 原文：<https://blog.logrocket.com/bringing-svgs-three-js-svgloader/>

Three.js 是最受欢迎的 3D WebGL 库，支持无数的 3D 体验，如登录页面、VR 房间、游戏，甚至整个 3D 编辑器！如果您对开发感兴趣，比如说，用于建模或 3D 打印的 3D 编辑器，或者程序几何生成器，您可能会考虑将 SVG 带到聚会上。

在本教程中，我将向您展示如何使用其 [SVGLoader](https://threejs.org/docs/#examples/en/loaders/SVGLoader) 将矢量图形导入 Three.js，以及如何在 3D 中挤压和预览它们！

## 安装

让我们从基础开始。我们将安装所需的依赖项，配置 [Vite 构建工具](https://blog.logrocket.com/getting-started-with-vite/)，并设置 Three.js 场景。

### 装置

首先，从 Vite 的“vanilla”模板开始一个新项目，并安装 Three.js:

```
# npm 6.x
npm init @vitejs/app svg-threejs --template vanilla

# npm 7+, extra double-dash is needed:
npm init @vitejs/app svg-threejs -- --template vanilla

cd svg-threejs
npm install three
npm run dev

```

有了这几行代码，开发环境就设置好了。

### HTML 和 CSS 文件

接下来，我们将对默认的 HTML 和 CSS 文件进行一些更改:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="favicon.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Three.js SVG extruder</title>
  </head>
  <body>
    <div id="app"></div>
    <div class="controls">
      <input type="range" min="1" max="50" id="input" />
    </div>
    <script type="module" src="/main.js"></script>
  </body>
</html>

```

在 HTML 中，添加一个`type=range`输入字段来控制 SVG 拉伸的级别。然后，在 CSS 中，根据您的需要对其进行定位和样式化。在下面的例子中，我放置滑块并调整顶部元素的大小，以便 Three.js 画布覆盖整个窗口。

```
html,
body,
#app {
  height: 100%;
  margin: 0;
  overflow: hidden;
}
.controls {
  position: fixed;
  bottom: 1rem;
  right: 1rem;
}

```

完成这些后，您转到 JavaScript 开始构建 Three.js 场景。

### 构建 Three.js 场景

从 Vite 创建的`main.js`文件开始，我们访问 DOM 元素，监听`input`事件以进行未来的挤压更改处理，并将创建 Three.js 场景的任务委托给另一个模块— `scene.js`。

```
import "./style.css";
import { setupScene } from "./scene";

const defaultExtrusion = 1;
const container = document.querySelector("#app");
const extrusionInput = document.querySelector("#input");
const scene = setupScene(container);

extrusionInput.addEventListener("input", () => {
  // Handle extrusion change
});
extrusionInput.value = defaultExtrusion;

```

`scene.js`文件中的重点是创建一个 Three.js 场景:

```
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";

const setupScene = (container) => {
  const scene = new THREE.Scene();
  const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
  const camera = new THREE.PerspectiveCamera(
    50,
    window.innerWidth / window.innerHeight,
    0.01,
    1e7
  );
  const ambientLight = new THREE.AmbientLight("#888888");
  const pointLight = new THREE.PointLight("#ffffff", 2, 800);
  const controls = new OrbitControls(camera, renderer.domElement);
  const animate = () => {
    renderer.render(scene, camera);
    controls.update();

    requestAnimationFrame(animate);
  };

  renderer.setSize(window.innerWidth, window.innerHeight);
  scene.add(ambientLight, pointLight);
  camera.position.z = 50;
  camera.position.x = 50;
  camera.position.y = 50;
  controls.enablePan = false;

  container.append(renderer.domElement);
  window.addEventListener("resize", () => {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
  });
  animate();

  return scene;
};

export { setupScene };

```

### 快速回顾

现在，我假设你已经对 Three.js 有所了解。如果没有，网上有一些很好的指南，包括[这个博客](https://blog.logrocket.com/intro-to-three-js-for-game-developers/)。也就是说，这里有一个正在发生的事情的概述。

首先，创建每个 Three.js 场景的基本部分:`scene`、`renderer`和`camera`。注意`THREE.WebGLRenderer`的选项——打开抗锯齿和背景透明——这对让应用看起来更好很重要。

然后，还有灯和`THREE.OrbitControls`。这对于正确地照亮我们将要使用的材质和方便地控制 3D 视图是必要的。

最后，还有`render`循环，额外的设置，如相机位置、渲染器视口大小和窗口大小调整处理程序。

该函数返回`THREE.Scene`实例，以便于从主模块访问。

## 使用 SVGLoader

场景设置好了，是时候加载一些 SVG 文件了！为此，我们将转到另一个模块:`svg.js`。

```
import * as THREE from "three";
import { SVGLoader } from "three/examples/jsm/loaders/SVGLoader";

const fillMaterial = new THREE.MeshBasicMaterial({ color: "#F3FBFB" });
const stokeMaterial = new THREE.LineBasicMaterial({
  color: "#00A5E6",
});
const renderSVG = (extrusion, svg) => {
  const loader = new SVGLoader();
  const svgData = loader.parse(svg);

  // ...
};

export { renderSVG };

```

在这里，您可以看到将用于挤出几何体的材质，以便填充和描边可以更好地在 3D 空间中可视化源 SVG 形状及其挤出。

接下来，这是我们的焦点——`renderSVG()`函数，它将使用`SVGLoader`来加载 SVG 形状，并在稍后将其挤出。

但在此之前，让我们快速看一下 SVGLoader API。

## SVGLoader API

SVGLoader 是 Three.js `Loader`类的一个实例，继承并扩展了它的方法和属性，最著名的是`load()`、`loadAsync()`和`parse()`。

这三个方法负责 SVGLoader 的大部分功能。所有这些都会产生一组`ShapePath`实例，只是方式不同。

```
// ...
const loader = new SVGLoader();
const svgUrl = "..."; //SVG URL
const svg = "..."; // SVG data

loader.load(svgUrl, (data) => {
  const shapePaths = data.paths;
  // ...
});
// or
loader.loadAsync(svgUrl).then((data) => {
  const shapePaths = data.paths;
  // ...
});
// or
const data = loader.parse(svg);
const shapePaths = data.paths;

```

要点是，在使用 SVGLoader 时，根据您希望访问 SVG 数据的方式，您将至少使用这些方法中的一种。更多详细信息，可以参考[官方文件](https://threejs.org/docs/#examples/en/loaders/SVGLoader)。

一旦你有了`ShapePath` s，你需要把它们转换成一个`Shape` s 的数组。为此，你应该使用`SVGLoader.createShapes()`静态方法，就像这样:

```
shapePaths.forEach((path) => {
  const shapes = SVGLoader.createShapes(path);
  // ...
});

```

从这里开始，剩下的就是从可用的形状中生成`ExtrudeGeometry`。

## 挤出几何图形

为了挤出源自 SVG 的`Shape`,我们需要更新`renderSVG()`函数。

```
// ...
const renderSVG = (extrusion, svg) => {
  const loader = new SVGLoader();
  const svgData = loader.parse(svg);
  const svgGroup = new THREE.Group();
  const updateMap = [];

  svgGroup.scale.y *= -1;
  svgData.paths.forEach((path) => {
    const shapes = SVGLoader.createShapes(path);

    shapes.forEach((shape) => {
      const meshGeometry = new THREE.ExtrudeBufferGeometry(shape, {
        depth: extrusion,
        bevelEnabled: false,
      });
      const linesGeometry = new THREE.EdgesGeometry(meshGeometry);
      const mesh = new THREE.Mesh(meshGeometry, fillMaterial);
      const lines = new THREE.LineSegments(linesGeometry, stokeMaterial);

      updateMap.push({ shape, mesh, lines });
      svgGroup.add(mesh, lines);
    });
  });

  const box = new THREE.Box3().setFromObject(svgGroup);
  const size = box.getSize(new THREE.Vector3());
  const yOffset = size.y / -2;
  const xOffset = size.x / -2;

  // Offset all of group's elements, to center them
  svgGroup.children.forEach((item) => {
    item.position.x = xOffset;
    item.position.y = yOffset;
  });
  svgGroup.rotateX(-Math.PI / 2);

  return {
    object: svgGroup,
    update(extrusion) {
      updateMap.forEach((updateDetails) => {
        const meshGeometry = new THREE.ExtrudeBufferGeometry(
          updateDetails.shape,
          {
            depth: extrusion,
            bevelEnabled: false,
          }
        );
        const linesGeometry = new THREE.EdgesGeometry(meshGeometry);

        updateDetails.mesh.geometry.dispose();
        updateDetails.lines.geometry.dispose();
        updateDetails.mesh.geometry = meshGeometry;
        updateDetails.lines.geometry = linesGeometry;
      });
    },
  };
};

```

让我们来分析一下这里发生了什么。

首先，您会注意到，在 SVG 加载位之间，我们创建了一个`THREE.Group`来保存所有挤出的形状。然后，它在 Y 轴上翻转，稍后，我们适当地偏移它，旋转它到适当的位置，并在我们的场景中适当地居中它。这确保了使用`OrbitControls`时的最佳用户体验，因此没有平移，控件主要围绕对象的底部旋转。

还有一些重要的代码在`shapes`循环中，我们从形状中生成`THREE.ExtrudeBufferGeometry`。因为我们不需要以任何复杂的方式与这些几何图形交互，所以选择缓冲几何图形可以在不增加成本的情况下提高性能。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

我们也使用`THREE.EdgesGeometry`和`THREE.LineSegments`来突出边缘。

网格被添加到组中，所需的细节被保存到我们的`updateMap`。这在返回的`update()`方法中使用，以根据选择的挤出正确更新几何图形。为此，我们创建新的几何图形，并丢弃旧的几何图形以清理内存。

## 把所有的放在一起

现在`renderSVG()`函数已经准备好了，我们现在可以回到`main.js`模块并好好利用它。

```
// ...
import { renderSVG } from "./svg";
import { svg } from "./example";

// ...
const { object, update } = renderSVG(defaultExtrusion, svg);

scene.add(object);

extrusionInput.addEventListener("input", () => {
  update(Number(extrusionInput.value));
});
// ...

```

从`example.js`开始，我将导出一个 SVG 字符串进行测试。在这里，它被导入并与默认挤出一起传递给`renderSVG()`。生成的对象被析构，场景中添加了`THREE.Group`，使用`update()`方法处理挤压变化。

至此，我们已经准备好了基本的 SVG 挤出机！

参见 [CodePen](https://codepen.io) 上 Arek 纳沃( [@areknawo](https://codepen.io/areknawo) )
的 Pen [Three.js SVG 挤出机](https://codepen.io/areknawo/pen/abWzjGO)。

改进的余地

## 当然，上面的应用程序是相当基本的，它可以受益于额外的功能。首先想到的是一个“聚焦”功能，它可以在改变挤压时调整`OrbitControls`和`camera`。我们来看看吧！

添加对焦功能并使相机适合物体

### 我们将把这个函数放在`scene.js`模块中，因为它是密切相关的。

步骤如下:

```
// ...
// Inspired by https://discourse.threejs.org/t/camera-zoom-to-fit-object/936/3
const fitCameraToObject = (camera, object, controls) => {
  const boundingBox = new THREE.Box3().setFromObject(object);
  const center = boundingBox.getCenter(new THREE.Vector3());
  const size = boundingBox.getSize(new THREE.Vector3());
  const offset = 1.25;
  const maxDim = Math.max(size.x, size.y, size.z);
  const fov = camera.fov * (Math.PI / 180);
  const cameraZ = Math.abs((maxDim / 4) * Math.tan(fov * 2)) * offset;
  const minZ = boundingBox.min.z;
  const cameraToFarEdge = minZ < 0 ? -minZ + cameraZ : cameraZ - minZ;

  controls.target = center;
  controls.maxDistance = cameraToFarEdge * 2;
  controls.minDistance = cameraToFarEdge * 0.5;
  controls.saveState();
  camera.position.z = cameraZ;
  camera.far = cameraToFarEdge * 3;
  camera.updateProjectionMatrix();
};

export { fitCameraToObject, setupScene };

```

获取对象的边界框，并计算其最大尺寸来调整相机

1.  应用选定的偏移(1.25)，使对象不会填满整个屏幕
2.  设置`OrbitControls`目标，使摄像机围绕对象旋转，并通过调整`maxDistance`和`minDistance`属性来防止其过分放大和缩小
3.  `setupScene()`功能也需要调整，以便轻松访问摄像机和控件实例。

然后只需在 HTML 中的`.controls`容器中添加一个`#focus`按钮，并编辑`main.js`来集成所有的更改。

```
// ...
const setupScene = (container) => {
  // ...
  return { scene, camera, controls };
};
// ...

```

这就是我们如何将聚焦功能添加到我们的 3D 应用程序中！

```
// ...
import { fitCameraToObject, setupScene } from "./scene";
// ...

const focusButton = document.querySelector("#focus");
const { scene, camera, controls } = setupScene(app);

// ...
focusButton.addEventListener("click", () => {
  fitCameraToObject(camera, object, controls);
});
// ...

```

参见 [CodePen](https://codepen.io) 上 Arek 纳沃( [@areknawo](https://codepen.io/areknawo) )
的笔 [Three.js SVG 挤出机带焦点](https://codepen.io/areknawo/pen/qBmEJwz)。

结果

如你所见，Three.js 是一个非常强大的库。它的 SVGLoader，以及无数其他 API，使它非常通用。

## 有了一个想法、一些学习和时间，您可以使用 Three.js 为 web 带来前所未有的原生 3D 体验。天空是无限的！

您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

## LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) works perfectly with any app, regardless of framework, and has plugins to log additional context from Redux, Vuex, and @ngrx/store. Instead of guessing why problems happen, you can aggregate and report on what state your application was in when an issue occurred. LogRocket also monitors your app’s performance, reporting metrics like client CPU load, client memory usage, and more.

Build confidently — [Start monitoring for free](https://lp.logrocket.com/blg/javascript-signup).