# Top React 图像裁剪库

> 原文：<https://blog.logrocket.com/top-react-image-cropping-libraries/>

构建应用程序时，最佳实践要求降低图像的环境噪声，从而将用户的注意力引向图像的特定部分。图像裁剪是一种处理图像以删除任何不想要的元素的方法。通过改变纵横比或方向，我们可以将观众的目光吸引到照片的主体上，并改善整体构图。这适用于个人资料图片或上传特定尺寸的图片。

![React Image Cropping Demo](img/c9a7cf6a8574fb2ebd8169db296d32fa.png)

在本文中，我们将比较四个顶级的 React 图像裁剪库，React Avatar Editor、react-cropper、react-image-crop 和 react-easy-crop，从性能、受欢迎程度和开发人员体验方面评估每个库。

首先，我们将在 React 中构建一个图像上传应用程序，我们将在每个库中使用它。本教程结束时，您应该能够选择适合您的项目。要跟随本教程，您需要:

*   React 和 JavaScript 的基础知识
*   Node.js 和 npm 安装在您的计算机上

以下是我们将要介绍的内容:

我们开始吧！

> 您可以通过访问[项目的代码](https://github.com/iamfortune/react-cropper-libraries)来跟进。

## 用 Create React app 初始化一个新的 React App

我们将首先初始化一个新的 React 应用程序，并用 Create React App 引导它；在您的终端中运行下面的命令，创建一个新的 React 应用程序:

```
npx create-react-app image uploader

```

接下来，我们将导航到我们的项目目录并运行以下命令来启动我们的开发服务器:

```
cd image uploader && yarn start 

```

上面的命令将在我们的浏览器中打开一个选项卡，并显示应用程序的默认样板文件。接下来，我们将安装依赖项，它们将包含在我们的三个裁剪器库中。

### 属国

我们将在应用程序中安装以下依赖项:

接下来，我们将为我们的组件构建组件。在我们的应用程序中，我们需要一个`button`组件来上传图像，还需要一个`modals`组件来上传、保存和删除上传者上传的图像。最后，对于我们的每个 croppers，我们需要一个`modalWrapper`。

## `Button`组件

`Button`组件是我们应用程序中所有按钮的框架组件。

首先，我们将使用样式组件为我们的按钮创建一个样式指南。让我们在应用程序的`src`目录中创建一个`components`文件夹。在您的`components`文件夹中，创建一个名为`Button`的新目录；在这个文件夹中，创建一个`Button. jsx`文件，并添加下面的代码:

```
import styled from "styled-components";
const Button = ({ children, className, ...props }) => {
    return (
        <StyledButton
            style={{
                background: "skyblue",
                color: "#000",
            }}
            className={`${className} block text-black px-6 rounded-md font-semibold hover:opacity-75 transition-opacity duration-500 ease-in`}
            type="button"
            {...props}
        >
            {children}
        </StyledButton>
    );
};

```

在上面的代码块中，我们创建了一个`Button`组件，并传递了`children`、`className`和`props`。现在，我们将[用样式组件](https://blog.logrocket.com/benefits-using-styled-components-react/)给我们的按钮添加样式:

```
const StyledButton = styled.button`
    background-color: #2eff7b;
    border: none;
    outline: none;
    height: 45px;
    &:focus {
        border: none;
        outline: none;
    }
    &:disabled {
        opacity: 1;
        cursor: not-allowed;
    }
`;
export default Button;

```

## `Checkbox`组件

现在，我们将构建一个链接每个图像裁剪器的`Checkbox`组件。首先，在我们的组件目录中创建一个名为`Checkbox`的新文件夹。在里面，我们将创建一个名为`Checkbox.jsx`的新文件，并添加以下代码:

```
import styled from "styled-components";
const Checkbox = ({ label, onChange, id, isChecked }) => {
    return (
        <Wrapper>
            <label htmlFor={id}>{label}</label>
            <input
                id={id}
                type="checkbox"
                name={label}
                onChange={() => {
                    onChange(!isChecked ? label : null);
                }}
                value={isChecked ? label : ""}
                checked={isChecked}
            />
            <span className="rounded-full" />
        </Wrapper>
    );
};

```

在上面的代码块中，我们创建了一个`Checkbox`组件，里面有一个标签和一个 ID，我们将使用它来选择我们的裁剪库。接下来，我们将构建一个用于打开和关闭特定裁剪库的`modal`组件。

## 创建我们的`modal`组件

我们的模态组件将使用类似于`onModalClose`、`showModal`和`onSaveHandler`的道具。我们还将添加一个按钮来上传和保存我们的裁剪图像。

在我们的`components`目录中，让我们创建一个名为`Modal`的新文件夹。在里面，创建一个名为`Modal.jsx`的新文件，并添加下面的代码块:

```
import { createPortal } from "react-dom";
import styled from "styled-components";
import Button from "../Button/Button";
const Modal = ({ children, onModalClose, showModal, onSaveHandler }) => {
    return createPortal(
        <Wrapper
            style={{
                opacity: showModal ? 1 : 0,
                pointerEvents: showModal ? "all" : "none",
            }}
        >
            <div
                onClick={onModalClose}
                role="button"
                className="iu-modal-backdrop"
                style={{
                    display: showModal ? "flex" : "none",
                }}
            />
            <div className="iu-modal-content">
                {children}
                <footer className="px-12 md:sticky absolute bottom-0 bg-white w-full left-0 py-4 border-t border-black flex items-center justify-between">
                    <Button onClick={onModalClose}>Dismiss</Button>
                    <Button
                        onClick={() => {
                            onSaveHandler();
                            onModalClose();
                        }}
                    >
                        Save
                    </Button>
                </footer>
            </div>
        </Wrapper>,
        document.getElementById("modal")
    );
};

```

在上面的代码块中，我们初始化了一个`modal`组件并创建了一个`Wrapper`组件，其中我们添加了一个上传和保存图像的函数。

## 反应-头像-编辑器

[r](https://github.com/mosch/react-avatar-editor)[eact](https://github.com/mosch/react-avatar-editor)[-avatar-e](https://github.com/mosch/react-avatar-editor)[ditor](https://github.com/mosch/react-avatar-editor)是 React 应用的一个头像和图像裁剪器。通过直观的用户界面，react-avatar-editor 可以轻松地裁剪、调整和旋转图像。

凭借 npm 上的 85k [周下载量和 GitHub](https://www.npmjs.com/package/react-avatar-editor) 上的 1.8k [明星，react-avatar-editor 是 react 应用程序中最受欢迎的裁剪库之一。要查看 react-avatar-editor 的运行情况，首先，我们需要使用下面的命令将其安装到我们的应用程序中:](https://github.com/mosch/react-avatar-editor)

```
//Yarn 

yarn add react-avatar-editor

//npm

npm install --save react-avatar-editor

```

接下来，创建一个名为`ReactAvatarEditor`的新文件夹。在里面，创建一个名为`ReactAvatarEditor.jsx`的新文件，并添加下面的代码:

```
import { useRef } from "react";
import AvatarEditor from "react-avatar-editor";
import styled from "styled-components";
import Modal from "../../Modal/Modal";

const ReactAvatarEditor = ({
    showModal,
    onModalClose,
    imgURL,
    onSaveHandler,
}) => {
    const EditorRef = useRef(null);
    const showCroppedImage = async () =&gt; {
        if (EditorRef.current) {
            const img = EditorRef.current.getImage().toDataURL();
            return img;
        }
    };

    return (
        <Modal
            showModal={showModal}
            onSaveHandler={async () => onSaveHandler(await showCroppedImage())}
            onModalClose={onModalClose}
        >
            <Wrapper className="w-full h-full flex flex-col items-center justify-center">
                <AvatarEditor
                    ref={EditorRef}
                    image={imgURL}
                    width={250}
                    height={250}
                    border={0}
                    scale={1.2}
                    color={[255, 255, 255, 0.6]}
                />
            </Wrapper>
        </Modal>
    );
};

```

在上面的代码中，我们导入了`useRef`钩子，将我们的 cropper packager 初始化为`AvatarEditor`，并导入 styled-components 来为我们的应用程序添加样式。

然后，我们用`AvatarEditor`初始化一个功能组件，并从我们的`modal`组件传递一些方法。最后，我们将`ref`分配给我们的`AvatarEditor`来获取我们的图像，将其转换为 URL，并解析它以进行裁剪。

为了呈现我们裁剪的图像，我们创建了一个包装器组件并添加了类似于`image`的道具，这是我们想要裁剪的图像的 URL。注意`width`、`height`、`border`、`color`是指编辑器的属性。

## 反应-图像-裁剪

react-image-crop 是一个开源库，允许我们裁剪图像。需要知道的一点是，它没有依赖性，这意味着它是轻量级的。首先，让我们安装`react-image-crop`:

```
//Using yarn
yarn add react-image-crop

//using npm
npm i react-image-crop

```

接下来，在我们的组件中，让我们从`react-image-crop`导入`ReactCrop`组件:

```
import { useState } from "react";
import ReactCrop from "react-image-crop";
import "react-image-crop/dist/ReactCrop.css";

```

然后我们将使用`useState`钩子创建变量:

```
 const [image, setImage] = useState(null);
    const [crop, setCrop] = useState({ unit: "%", width: 30, aspect: 16 / 9 });
    const [croppedImageUrl, setCroppedImageUrl] = useState("");

```

`crop`包含我们的尺寸，`image`包含我们的图像文件，`croppedImageUrl`包含最终裁剪的图像。

我们现在要添加两个函数(`makeClientCrop`和`getCroppedImg`)来帮助我们返回带有`setCroppedImageUrl()`的裁剪图像:

```
const Cropper = ({ imgURL }) => { 
const makeClientCrop = async (crop) => {
        if ((image, crop.width && crop.height)) {
            const croppedImg = await getCroppedImg(image, crop, "newFile.jpeg");
            setCroppedImageUrl(croppedImg);
        }
    };

    const getCroppedImg = (sourceImage, crop, fileName) => {
    const canvas = document.createElement("canvas");
  const scaleX = sourceImage.naturalWidth / sourceImage.width;
  const scaleY = sourceImage.naturalHeight / sourceImage.height;
  canvas.width = crop.width;
  canvas.height = crop.height;
  const ctx = canvas.getContext("2d");
  ctx.drawImage(
    sourceImage,
    crop.x * scaleX,
    crop.y * scaleY,
    crop.width * scaleX,
    crop.height * scaleY,
    0,
    0,
    crop.width,
    crop.height
  );
    try {
      return new Promise((resolve) => {
        canvas.toBlob((file) => {
          resolve(URL.createObjectURL(file));
        }, "image/jpeg");
      });
    } catch (error) {
      console.log(error);
      return null;
    }
  };
}

```

让我们将`ReactCrop`添加到组件中:

```
            <ReactCrop
                src={imgURL}
                crop={crop}
                ruleOfThirds
                onImageLoaded={ (img) => {
                    console.log(img);
                    setImage(img);
                }}
                onComplete={(crop) => image? makeClientCrop(crop): console.log("wait")}
                onChange={(cropData) => setCrop(cropData)}
            />

```

在初始装载时，我们用我们的`imgUrl`给`src`供料。一旦`ReactCrop`加载了图像，它就使用`setImage`将它分配给我们的变量。当有任何尺寸变化时，它使用`setCrop`分配数据。

同样，`onComplete`检查`image`是否存在。如果是这样的话，它将触发我们的`makeClientCrop`功能和裁剪后的图像:`cropData`。

## 反应收割机

[r](https://github.com/react-cropper/react-cropper) [eact-cropper](https://github.com/react-cropper/react-cropper) 是一个开源的 React 包装器组件，用于 [C](https://github.com/fengyuanchen/cropperjs) [ropper.js，](https://github.com/fengyuanchen/cropperjs)一个 JavaScript 库，包括一个照片编辑器和一个图像裁剪器。react-cropper 在 npm 上有超过 63k [的周下载量，在 GitHub](https://www.npmjs.com/package/react-cropper) 上有 1.6k 的星下载量。要在您的应用程序中使用 react-cropper，首先，用下面的命令安装它:

```
// Using yarn 
yarn add react-cropper 

// Using npm
npm install --save react-cropper 

```

接下来，使用我们的图像上传应用程序添加以下代码块来裁剪图像:

```
import { useRef, useState } from "react";
import Cropper from "react-cropper";
import "cropperjs/dist/cropper.css";
import Modal from "../../Modal/Modal";

const ReactCropper = ({ showModal, onModalClose, imgURL, onSaveHandler }) => {
    const cropperRef = useRef(null);
    const [croppedImg, setCroppedImg] = useState("");
    const onCrop = () => {
        const imageElement = cropperRef?.current;
        const cropper = imageElement?.cropper;
        setCroppedImg(cropper.getCroppedCanvas().toDataURL());
    };

    return (
        <Modal
            showModal={showModal}
            onSaveHandler={() => onSaveHandler(croppedImg)}
            onModalClose={onModalClose}
        >
            <Cropper
                src={imgURL}
                style={{ height: 500, width: "732px" }}
                initialAspectRatio={16 / 9}
                guides={false}
                crop={onCrop}
                ref={cropperRef}
                viewMode={1}
                // guides={true}
                minCropBoxHeight={10}
                minCropBoxWidth={10}
                // background={false}
                responsive={true}
                autoCropArea={1}
                aspectRatio={4 / 3}
                checkOrientation={false}
            />
        </Modal>
    );
};
export default ReactCropper;

```

类似于 react-avatar-editor，我们从 react 导入了`useRef`和`useState`钩子。接下来，我们初始化了一个功能组件`ReactCropper`，它接受了许多像图片 URL 和`modal`这样的属性。

为了裁剪我们的图像，我们连接到图像裁剪器属性，比如图像 URL，然后解析它。使用 React 的`useState`钩子，我们为裁剪后的图像创建了一个状态。

为了呈现我们的图像，我们初始化了一个`Cropper`组件，并从`Cropper.js`传递了一些道具，包括用于解析图像 URL 的`src`。最后，我们将样式传递给`style`道具。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 易反应作物

react-easy-crop 是一个开源的 react 组件，它有一个干净的用户界面来裁剪图像和视频。react-easy-crop 是移动友好的，提供了以像素和百分比为单位的作物尺寸，以及拖动和缩放的交互。

在 npm 上，react-easy-crop 目前每周下载超过 [125k 次](https://www.npmjs.com/package/react-easy-crop)。在 [GitHub](https://github.com/ricardo-ch/react-easy-crop) 上，react-easy-crop 有 1.4k 颗星。您可以通过使用包管理器安装库来开始使用 react-easy-crop，如下面的代码块所示:

```
//Yarn 
yarn add react-easy-crop

// npm
npm install react-easy-crop --save

```

要使用 react-easy-crop，您需要将其包装在一个`Cropper`组件标签中。让我们使用下面的库来构建一个裁剪器组件:

```
import { useCallback, useState } from "react";
import Cropper from "react-easy-crop";
import Modal from "../../Modal/Modal";

const ReactEasyCrop = ({ showModal, onModalClose, imgURL, onSaveHandler }) => {
    const [crop, setCrop] = useState({ x: 2, y: 2 });
    const [zoom, setZoom] = useState(1);
    const [croppedArea, setCroppedArea] = useState("");

    const onCropComplete = useCallback((croppedArea, croppedAreaPixels) => {
        setCroppedArea(croppedAreaPixels);
    }, []);
    const showCroppedImage = useCallback(async () => {
        try {
            const croppedImage = await getCroppedImg(imgURL, croppedArea, 0);
            return croppedImage;
        } catch (error) {
            console.error(error);
        }
    }, [croppedArea, imgURL]);

```

在上面的代码块中，我们创建了一个名为`ReactEasyCrop`的组件，其中包含类似`showModal`、`onModalClose`、`imgURL`、`onSaveHandler`和`showModal`的道具。

像其他图像裁剪器一样，`showModal`和`onModalClose`道具用于上传图像，而`imgUrl`提供我们正在上传的图像的 URL。

`onCropComplete`允许我们保存图像的裁剪区域，并指定裁剪图像的像素或百分比大小。接下来，我们将使用`Modal`包装器组件呈现我们的应用程序，如下所示:

```
return (
                <Modal
                        showModal={showModal}
                        onSaveHandler={async () => onSaveHandler(await showCroppedImage())}
                        onModalClose={onModalClose}
                >
                        <div className="relative w-full">
                                <Cropper
                                        image={imgURL}
                                        crop={crop}
                                        zoom={zoom}
                                        aspect={4 / 3}
                                        onCropChange={setCrop}
                                        onCropComplete={onCropComplete}
                                        onZoomChange={setZoom}
                                />
                        </div>
                </Modal>
        );
};

export default ReactEazyCrop;

```

在上面的代码块中，我们将整个应用程序包装在一个`Modal`组件中，允许我们上传图像。接下来，我们初始化了`Cropper`组件，在那里我们传递了裁剪图像的 URL。

我们还添加了`crop`道具，用于确定要裁剪的图像的位置。`zoom`用于放大图像，默认设置为`1`。

`onCropChange`用于更新我们的应用程序的裁剪状态。当用户停止缩放图像时，调用`onCropComplete`。

## 结论

无论您正在处理的项目类型如何，裁剪库都可以通过从照片中移除不需要的区域来帮助您改善整体 UI。本教程中介绍的库具有完整的内置特性，并且易于定制。

现在，您应该能够为您的应用程序选择正确的裁剪库了。我希望你喜欢这个教程！

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