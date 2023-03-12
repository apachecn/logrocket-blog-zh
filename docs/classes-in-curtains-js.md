# 在 curtains.js - LogRocket 博客中使用类

> 原文：<https://blog.logrocket.com/classes-in-curtains-js/>

curtains.js 是一个易于使用的 WebGL 库，可以将图像、视频和画布转换为 3D WebGL 图形。WebGL (Web Graphics Library)是一个 JavaScript API，用于渲染用户可以与之交互的 3D 和 2D 图形。curtains.js 是作为一个 WebGL JavaScript 库构建的。

如果您使用的是 7.0 版本之前的窗帘，那么您会注意到新旧版本之间的许多变化。在 7.0 版本中，为了更好的可读性和可维护性，窗帘被重写，新的库被分解成易于使用的不同类模块。

在本文中，我们将更详细地讨论这些课程模块，但首先，我们将回顾一些窗帘的基础知识。

## 回归基础:窗帘是做什么的？

为了更好地理解这个问题，我们需要首先看看窗帘想要解决的问题。根据其官方网站[的说法，kunds . js 将包含图像、视频和画布的 HTML 元素转换为 3D WebGL，这使得这些媒体可以通过使用着色器来进一步制作动画。这使得 curtains.js 3D 图形相对于网页的 DOM 很容易定位，这是其他 3D 库中不常见的奢侈。](https://www.curtainsjs.com/)

此外，因为 currents . js 是用 WebGL API 构建的，所以它省去了您直接处理对象大小和位置的压力。相反，窗帘处理这些发展本身的引擎盖下，使发展容易和直截了当。

最后，窗帘有明显的 SEO 好处，允许你为你的设计写干净的 HTML 代码，比其他 3D 库更有可能被搜索引擎高度排名。

## 7.0 版中的窗帘类别

如果您使用的是 7.0 版本之前的窗帘，那么您会注意到新旧版本之间的许多变化。在 7.0 版本中，为了更好的可读性和可维护性，窗帘被重写，新的库被分解成易于使用的不同类模块。我们将在下面更详细地讨论这些类模块。

### 核心类

在窗帘中创建基本的 WebGL 场景时，这些核心类是必不可少的。它们是在窗帘中创建任何 WebGL 图形所需的主干。核心课程包括窗帘、平面和纹理。

#### **`curtains`类**

要启动任何一个 curtains.js 项目，您需要创建一个`Curtains`对象来处理所提供的平面场景。该对象接受将包装画布的 HTML 元素的 id。

下面是一个示例`Curtains`对象实例:

```
// the id of the given html element in this case is canvas
const curtainsInstance = new Curtains({
    container: document.getElementById("canvas")
});

```

其他值得注意的参数设置包括:

*   **生产**:该参数接收一个布尔值，用于指定是否希望库处于生产模式
*   **容器**:该参数指定将包装画布的 HTML 元素。此参数接收字符串或 HTML 元素。请注意，该属性是可选的，如果没有指定，则 curtains.js 将不得不等待调用`setContainer()`来设置 WebGL 上下文
*   **watchScroll:** 这个参数决定了 currents . js 是否应该监听一个滚动事件。它接受一个布尔值作为它的值

除了不同的参数之外，窗帘对象实例还使用不同的方法，使用户能够更好地控制他们的项目。一些方法包括:

*   `clear()`:清除 WebGL 上下文的颜色和深度
*   `disableDrawing()`:防止已经绘制的场景被再次绘制。这使场景处于暂停状态，制服被禁用
*   `enableDrawing()`:需要重新启用已经暂停的场景。如果您计划在特定事件后开始一个场景，这将非常有用
*   `dispose()`:取消`requestAnimationFrame`循环，随后删除平面和 WebGL 上下文

最后，使用窗帘对象，我们可以用窗帘. js 库提供的类似于`onError()`和`onContextLost()`的函数来处理事件。

#### 平面**类**

curtains.js 提供的`plane`类基于提供的 HTML 元素创建一个 WebGL 平面。Plane 扩展自`baseplane`，一个用于设置 WebGL 的类。

要创建一个平面，您需要传入一个 HTML 元素和一个已经创建的`curtains`类，如下所示:

```
// the id of the given html element in this case is “canvas”
const curtainInstance = new Curtains({
    container: document.getElementById(“canvas”)
});

const planeElement = document.getElementById(“plane-element”);

const plane = new Plane(curtainInstance, planeElement)

```

就像在`curtains`类中一样，`plane`类接受第三个参数，在这里可以指定不同的选项，如下所示:

```
// the id of the given html element in this case is “canvas”
const curtainInstance = new Curtains({
    container: document.getElementById(“canvas”)
});

const planeElement = document.getElementById(“plane-element”)

const params = {
    vertexShaderID: "plane-element-vs", 
    fragmentShaderID: "plane-element-fs", /
    uniforms: {
         time: {
             name: "uTime", 
             type: "1f", 
             value: 0, 
         },
    },
};

const plane = new Plane(curtainInstance, planeElement , params)

```

`vertixShaderID`和`fragmentShaderID`用于设置着色器的顶点和片段，而`uniforms`允许你与你创建的平面进行交互。

#### 纹理类

顾名思义，`texture`类用于创建纹理。

您可以通过调用纹理的构造函数来轻松创建纹理，如下所示:

```
// the id of the given html element in this case is “canvas”
const curtainsInstance = new Curtains({
    container: document.getElementById("canvas")
});

const texture = new Texture( curtainInstance , {
    sampler: ‘texture’
})

```

或者，您可以在平面上调用 createTexture()方法:

```
// the id of the given html element in this case is “canvas”
const curtainInstance = new Curtains({
    container: document.getElementById(“canvas”)
});

const planeElement = document.getElementById(“plane-element”)

const plane = new Plane(curtainInstance, planeElement)

const myTexture = plane.createTexture({
    sampler: “uTexture”
})

```

现在我们已经讨论了核心类，我们将讨论更多的窗帘 v7.0 类，你可以在设计你的网页时使用。

### 框架 **b** 缓冲对象

这些是用来在飞机上添加特效的类。它们包括`renderTarget`和`ShaderPass`类。

在下面的代码中，我们将看到如何创建一个`ShaderPass`实例；你可以在这里了解更多关于帧缓冲对象[。](https://www.curtainsjs.com/documentation.html)

```
const curtainsInstance = new Curtains({
    container: document.getElementById("canvas")
});

const shaderPass = new ShaderPass(curtains)

```

### 装载机

加载器是用来实现基于 HTML 媒体元素的 WebGL 纹理的类。这方面的例子是`TextureLoader`。这个类使用图像、视频和画布作为其来源来创建纹理。您可以使用`TextureLoader`将网站的纹理预加载到 GPU，同时仍然显示加载器。

可以使用其构造函数创建新的纹理加载器，如下例所示:

```
// the id of the given html element in this case is “canvas”
const curtainsInstance = new Curtains({
    container: document.getElementById("canvas")
});

// create a new texture loader
const loader = new TextureLoader(curtainsInstance);

// load an image with the loader
const image = new Image();
image.crossOrigin = "anonymous";
image.src = "path/to/my-image.jpg";

loader.loadImage(image, {
    // options are set here
    sampler: "uTexture"
}, (texture) => {
    // successfully created a texture
}, (image, error) => {
    // error handler
});

```

每种媒体的纹理都是利用其各自的方法创建的。`TextureLoader`类使用不同的方法，包括`loadCanvas`和`loadVideo`。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

`loadCanvas`函数基于传递给它的画布创建纹理。它接受三个参数:给定的 canvas 元素、一个纹理选项和一个回调函数。这里可以找到[的一个例子](https://www.curtainsjs.com/examples/multiple-planes-canvas-text/index.html)。您还可以使用所提供的`loadCanvases()`方法来处理一组画布。

与`loadCanvas`类似，`loadVideo`函数基于传递给它的视频创建纹理，但也接受第四个参数进行错误处理。您可以使用`loadVideos()`方法处理多个视频。

其他方法，如`loadImage()`和`loadSource()`的工作方式与上面列出的两种方法类似。

### 数学

窗帘中的数学类基于 [three.js 数学实用类](https://threejs.org/docs/#api/en/math/MathUtils)。它们用于空间矢量操作。例如，`Vec2`、`Vec3`和`Mat4`分别用于 2D、3D 和 4D 矢量的计算和操作。

下面是一个使用构造函数的例子:

```
const nullVector = new Vec2();
const vector = new Vec2(1, 1)

```

注意，`Vec2`和`Vec3`实例使用 setter 和 getter，这允许在事件改变时执行动作。

这可以使用`onChange()`方法来完成:

```
// create a new Vec2 vector add an event listener
const vector = new Vec2().onChange(() => {
const normalizedVector = vector.normalize();
});

// the actions below triggers to different events
vector.x = 4;
vector.y = 6;

```

## 结论

curtains.js 因向包括图像、视频和画布在内的媒体添加 2D 和 3D 效果而赢得了最佳图书馆的声誉。随着 7.0 及更高版本的最新发展——包括用于更简化构建的类模块——开发变得更加容易。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。