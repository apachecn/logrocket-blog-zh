# 使用“自动动画”用一行动画反应组件

> 原文：<https://blog.logrocket.com/animate-react-components-single-line-using-autoanimate/>

动画为现代 web 应用程序提供了引人注目的用户体验，让它们栩栩如生，避免了可怕的单调外观。但是，当然，添加动画可能是一个困难和耗时的任务，如果你的应用程序有多个组件，它会变得更糟！

在本文中，我们将了解如何使用 AutoAnimate 库，通过一行代码添加动画来反应组件。

让我们直接进入它。

*向前跳转:*

## 什么是 AutoAnimate？

AutoAnimate 是一个零配置的开源动画实用程序库，它为 React 组件添加了平滑过渡，同时也非常[轻量级](https://bundlephobia.com/package/@formkit/auto-animate@1.0.0-beta.3) (2.3KB)。

## 为什么不用其他的动画库？

大多数动画库需要更多的配置，有些需要更改现有的组件结构来应用动画。

然而，AutoAnimate 只需要一行代码来激活组件，并且不需要改变任何现有的组件。它还提供了直接与现有代码库集成的好处。

当 DOM 中的元素发生变化时，AutoAnimate 可以带来流畅的体验。我想将 AutoAnimate 与 [React 转换组](https://reactcommunity.org/react-transition-group/)进行比较，后者是一个用于组件进入和退出的简单转换库，但是有一些额外的配置。

让我们用“反应过渡组”和“自动动画”创建一个警告消息，这样您就可以自己看到库之间的差异。

## 使用 React 转换组的警报消息

以下组件显示了如何使用 React 过渡组添加动画[。](https://blog.logrocket.com/react-transition-group/)

```
App.jsx
import React, { useState, useRef } from 'react';
import { createRoot } from 'react-dom/client';
import { Container, Button, Alert } from 'react-bootstrap';
import { CSSTransition } from 'react-transition-group';

import 'bootstrap/dist/css/bootstrap.min.css';
import './styles.css';

function Example() {
  const [showButton, setShowButton] = useState(true);
  const [showMessage, setShowMessage] = useState(false);
  const nodeRef = useRef(null);

  return (
    <Container style={{ paddingTop: '2rem' }}>
      {showButton && (
        <Button
          onClick={() => setShowMessage(true)}
          size="lg"
        >
          Show Message
        </Button>
      )}
      <CSSTransition
        in={showMessage}
        nodeRef={nodeRef}
        timeout={300}
        classNames="alert"
        unmountOnExit
        onExited={() => setShowButton(true)}
      >
        <Alert
          ref={nodeRef}
          variant="primary"
          dismissible
          onClose={() => setShowMessage(false)}
        >
          <Alert.Heading>
            Animated alert message
          </Alert.Heading>
          <p>
            This alert message is being transitioned in and
            out of the DOM.
          </p>
          <Button
            variant="primary"
            onClick={() => setShowMessage(false)}
          >
            Close
          </Button>
        </Alert>
      </CSSTransition>
    </Container>
  );
}
const container = document.getElementById('root');
const root = createRoot(container);
root.render(<Example />);

```

添加以下样式以添加过渡:

```
//styles.css
.alert-enter {
  opacity: 0;
  transform: scale(0.9);
}
.alert-enter-active {
  opacity: 1;
  transform: translateX(0);
  transition: opacity 300ms, transform 300ms;
}
.alert-exit {
  opacity: 1;
}
.alert-exit-active {
  opacity: 0;
  transform: scale(0.9);
  transition: opacity 300ms, transform 300ms;
}

```

上述代码将产生以下输出:

![Alert Message React Transition Group.](img/1e1a32863ea55cd3f4a2c71b3d65da27.png)

为了添加这个过渡，我们添加了几行 CSS 并向 CSS `transition`组件传递了一些道具。

现在，让我们使用零配置的 AutoAnimate 来再现相同的动画。

> **注意**，你可以在 [CodeSandbox](https://codesandbox.io/s/react-transition-group-th033h?file=/index.js) 找到上面的演示

## 使用自动动画的警告消息

下面的组件向您展示了如何使用 AutoAnimate 添加动画。

```
//App.jsx

import React, { useState, useRef } from 'react';
import { createRoot } from 'react-dom/client';
import { Container, Button, Alert } from 'react-bootstrap';
import { useAutoAnimate } from '@formkit/auto-animate/react';

import 'bootstrap/dist/css/bootstrap.min.css';

function Example() {
//Auto Animate
  const [parent] = useAutoAnimate(/* optional config */);

  const [showButton, setShowButton] = useState(true);
  const [showMessage, setShowMessage] = useState(false);
  const nodeRef = useRef(null);

  return (
    <Container style={{ paddingTop: '2rem' }}>
      {showButton && (
        <Button
          onClick={() => setShowMessage(true)}
          size="lg"
        >
          Show Message
        </Button>
      )}
      <div ref={parent}>
        {showMessage && (
          <Alert
            ref={nodeRef}
            variant="primary"
            dismissible
            onClose={() => setShowMessage(false)}
          >
            <Alert.Heading>
              Animated alert message
            </Alert.Heading>
            <p>
              This alert message is being transitioned in
              and out of the DOM.
            </p>
            <Button
              variant="primary"
              onClick={() => setShowMessage(false)}
            >
              Close
            </Button>
          </Alert>
        )}
      </div>
    </Container>
  );
}
const container = document.getElementById('root');
const root = createRoot(container);
root.render(<Example />);

```

这里，我们使用了与 React 转换组相同的代码。我们已经包含了 AutoAnimate 库，并添加了对`Alert` `parent`元素(`<div>`)的`useAutoAnimate`钩子引用。

这就是我们需要做的！我们没有必要添加 CSS 或过渡持续时间。让我们看看这里的输出:

![AutoAnimate Alert Message](img/d4aaebb9d72af9c706c861149448210d.png)

在这里我们可以看到，我们已经用零配置创建了相同的动画—这就是 AutoAnimate 与其他库的不同之处！

> **注意**，你可以在 [CodeSandbox](https://codesandbox.io/s/auto-animate-dffre9?file=/index.js) 找到上面的演示

## 它是如何工作的？

AutoAnimate 是一个单功能实用程序，它接受需要设置动画的组件的父元素。然后将动画应用于父元素的直接子元素。

发生以下事件时，自动动画会触发动画:

*   一个子元素被插入到 DOM 中
*   从 DOM 中移除一个子元素
*   DOM 中移动了一个子元素

## 快速设置和使用

现在，让我们设置 AutoAnimate 在您的项目中使用。

使用以下命令安装 AutoAnimate:

`yarn add @formkit/auto-animate`

将`useAutoAnimate`钩子导入到你想要制作动画的组件中，如下所示:

`import { useAutoAnimate } from '@formkit/auto-animate/react`

要制作一个组件的动画，我们需要将由`useAutoAnimate`钩子返回的引用添加到父元素中，如下所示:

```
//App.jsx

import { useState } from 'react'
import { useAutoAnimate } from '@formkit/auto-animate/react'

const App = function () {
  const [items, setItems] = useState([0, 1, 2])
  const [parent] = useAutoAnimate()
  const add = () => setItems([...items, items.length])
  return 
  <>
  <ul ref={parent}>
    {items.map(
      item => <li key={item}>{ item }</li>
    )}
  </ul>
  <button onClick={add}>Add number</button>
  </>
}

export default App

```

这里，我们将父元素`<ul>`的引用传递给了`useAutoAnimate`。当点击**添加数字**按钮时，新添加的列表将被动画显示。

接下来，我们将看一些更多的例子。

## 制作动态`Form`组件的动画

大部分 app 都有动态输入`Form`组件。现在，我们将创建一个动态组件，因此添加以下代码:

> **注意**，为了这个演练简单起见，我使用了蚂蚁设计`Form`

```
//DynamicForm.jsx

import { MinusCircleOutlined, PlusOutlined } from '@ant-design/icons';
import { Button, Form, Input, Space } from 'antd';
import React from 'react';

const DynamicForm = () => {
    return (
            <Form name="dynamic_form_nest_item" autoComplete="off" >
                <Form.List name="users">
                    {(fields, { add, remove }) => (
                        <div>
                            {fields.map(({ key, name, ...restField }) => (
                                <Space
                                    key={key}
                                    style={{
                                        display: 'flex',
                                        marginBottom: 8,
                                    }}
                                    align="baseline"
                                >
                                    <Form.Item
                                        {...restField}
                                        name={[name, 'first']}
                                        rules={[
                                            {
                                                required: true,
                                                message: 'Missing first name',
                                            },
                                        ]}
                                    >
                                        <Input placeholder="First Name" />
                                    </Form.Item>
                                    <Form.Item
                                        {...restField}
                                        name={[name, 'last']}
                                        rules={[
                                            {
                                                required: true,
                                                message: 'Missing last name',
                                            },
                                        ]}
                                    >
                                        <Input placeholder="Last Name" />
                                    </Form.Item>
                                    <MinusCircleOutlined 
                                      onClick={() => remove(name)} 
                                      />
                                </Space>
                            ))}
                            <Form.Item>
                                <Button 
                                    type="dashed" 
                                    onClick={() => add()} 
                                    block 
                                    icon={<PlusOutlined/>
                                    }>
                                    Add field
                                </Button>
                            </Form.Item>
                        </div>
                    )}
                </Form.List>
            </Form>
    );
};

export default DynamicForm;

```

现在，当我们运行组件时，我们将看到以下输出。当我们点击**添加字段时，**输入在几分之一秒内被添加；我感觉像是坏 UX！

![React Without AutoAnimate ](img/4cc5dd81264d405dec62fb47fcfd4161.png)

让我们用一行 AutoAnimate 动画显示表单。使用`DynamicForm`组件中的代码导入自动动画库:

`import { useAutoAnimate } from '@formkit/auto-animate/react'`

接下来，添加`useAutoAnimate`钩子，如下所示:

`const [parent] = useAutoAnimate(/* optional config */)`

然后，传递对父元素`<div>`的引用，如下所示:

`<div ref={parent}>`

现在，再次运行代码；您可以看到 AutoAnimate 的神奇之处！

![AutoAnimate Example](img/71d1fbcea05da2b252adaa77e0654557.png)

另一个用例是自动激活应用程序的`Comments`组件。

在这里，我们正在开发一个`Comments`组件，用于给帖子添加评论。如果添加了新的注释，它会显示在列表的顶部。

```
//Comments.jsx
import {Avatar, Button, Comment, Form, Input,} from 'antd';
import React, {useState} from 'react';
import {useAutoAnimate} from '@formkit/auto-animate/react'
const {TextArea} = Input;

const Editor = ({onChange, onSubmit, submitting, value}) => (
    <>
        <Form.Item>
            <TextArea rows={4} onChange={onChange} value={value}/>
        </Form.Item>
        <Form.Item>
            <Button 
                htmlType="submit" 
                loading={submitting} 
                onClick={onSubmit} 
                type="primary"
                >
            Add Comment
            </Button>
        </Form.Item>
    </>
);

const Comments = () => {
    const [comments, setComments] = useState([]);
    const [submitting, setSubmitting] = useState(false);
    const [value, setValue] = useState('');
    const [parent] = useAutoAnimate()
    const handleSubmit = () => {
        if (!value) return;
        setSubmitting(true);
        setTimeout(() => {
            setSubmitting(false);
            setValue('');
            setComments([
                ...comments,
                {
                    author: 'Han Solo',
                    avatar: 'https://joeschmoe.io/api/v1/random',
                    content: <p>{value}</p>,
                },
            ]);
        }, 500);
    };

    const handleChange = (e) => {
        setValue(e.target.value);
    };

    return (
        <>
            <ul ref={parent}>
                {comments.map((comment) => (
                        <Comment
                            key={comment.content}
                            author={comment.author}
                            avatar={
                                    <Avatar 
                                      src="https://joeschmoe.io/api/v1/random" 
                                      alt="Han Solo"
                                    />
                                    }
                            content={
                                <p>
                                    {comment.content}
                                </p>
                            }
                        />
                    )
                )}
            </ul>
            <Comment
                avatar={
                  <Avatar src="https://joeschmoe.io/api/v1/random" alt="Han Solo"/>
                }
                content={
                    <Editor
                        onChange={handleChange}
                        onSubmit={handleSubmit}
                        submitting={submitting}
                        value={value}
                    />
                }
            />
        </>
    );
};

export default Comments;

```

在上面的例子中，我们有一个注释输入。当用户键入评论并点击**添加评论** **、**时，输入的评论会以动画形式附加在顶部。为了给列表添加动画效果，我们添加了一个对`<ul>`元素的自动动画钩子引用。

现在，当我们运行组件时，我们将看到以下输出:

![User Comment Component.](img/da6ba8e5816486224e24043627a60e9e.png)

## 自定义动画持续时间

我们可以通过将`duration`道具传递给`useAutoAnimate`来定制过渡时间。让我们以动态卡为例来看看这一点。

在这个例子中，我们制作了 500 毫秒长的过渡，所以当用户点击**添加任务**时，一张新的卡片被插入，所有其他的卡片在 500 毫秒后被移动。

```
//DynamicComponents.jsx

import React, {useState} from "react";
import {Avatar, Button, Card, Col, Form, Input, Row} from 'antd';
import {useAutoAnimate} from "@formkit/auto-animate/react";

const {Meta} = Card;

export default function DynamicComponents() {

    const [comments, setComments] = useState([]);
    const [parent] = useAutoAnimate({duration: 500});

    const handleSubmit = (values) => {
        if (!values) return;
        setComments((prev) => [{content: values.content}, ...prev]);
    };

    return (
        <>
            <Form
                name="basic"
                onFinish={handleSubmit}
                autoComplete="off"
            >
                <Form.Item
                    name="content"
                >
                   <Input/>
                </Form.Item>
                <Form.Item>
                    <Button htmlType="submit" type="primary">
                        Add Task
                    </Button>
                </Form.Item>
            </Form>
            <Row gutter={[16, 24]} ref={parent}>
                {comments.map((comment) => (
                    <Col span={6} key={comment.content}>
                        <Card
                            style={{
                                width: 100,
                            }}
                            cover={
                                <img
                                    alt="example"
                                    src="https://gw.alipayobjects.com/zos/rmsportal/JiqGstEfoWAOHiTxclqi.png"
                                />
                            }
                        >
                            <Meta
                                avatar={
                                <Avatar src="https://joeschmoe.io/api/v1/random"/>
                                  }
                                description={comment.content}
                            />
                        </Card>
                    </Col>
                ))
                }
            </Row>
        </>
    );
}

```

现在，当我们运行组件时，我们将看到以下输出:

![Customize Animation Duration.](img/d1cdd4e5bc6c3e7fec1d56983eeb1c9c.png)

## 启用和禁用动画

有时，我们需要禁用一个动画，以后再使用它。为了处理这些情况，AutoAnimate 钩子`useAutoAnimate`返回**启用**和**禁用**函数，这些函数可用于启用和禁用动画。

我们可以在下面的代码块中看到这一点:

```
//DynamicCards.jsx

import React, {useState} from "react";
import {Avatar, Button, Card, Col, Form, Input, Row} from 'antd';
import {useAutoAnimate} from "@formkit/auto-animate/react";

const {Meta} = Card;

export default function DynamicCards() {
    const [comments, setComments] = useState([]);
    const [parent, enable] = useAutoAnimate({duration: 500});
    const [isEnabled, setIsEnabled] = useState(true)

    const handleSubmit = (values) => {
        if (!values) return;
        setComments((prev) => [{content: values.content}, ...prev]);
    };

    function toggle () {
        enable(!isEnabled)
        setIsEnabled(!isEnabled)
    }

    return (
        <>
            <Form
                name="basic"
                onFinish={handleSubmit}
                autoComplete="off"
            >
                <Form.Item
                    name="content"
                >
                    <Input/>
                </Form.Item>
                <Form.Item>
                    <Button htmlType="submit" type="primary">
                        Add Task
                    </Button>
                </Form.Item>
                <Form.Item>
                    <Button onClick={toggle} type="primary">
                        { isEnabled ? "🚫 Disable" : "✅ Enable" } animations
                    </Button>
                </Form.Item>
            </Form>
            <Row gutter={[16, 24]} ref={parent}>
                {comments.map((comment) => (
                    <Col span={6} key={comment.content}>
                        <Card
                            style={{
                                width: 100,
                            }}
                            cover={
                                <img
                                    alt="example"
                                    src="https://gw.alipayobjects.com/zos/rmsportal/JiqGstEfoWAOHiTxclqi.png"
                                />
                            }
                        >
                            <Meta
                                avatar={
                                  <Avatar 
                                  src="https://joeschmoe.io/api/v1/random"/>}
                                  description={comment.content}
                            />
                        </Card>
                    </Col>
                ))
                }
            </Row>
        </>
    );
}

```

这里，我们使用了前面的例子，为它添加了启用和禁用选项。动画由传递给`useAutoAnimate`钩子的`enable`布尔属性控制。

现在，当我们运行组件时，我们将看到以下输出:

![Enable And Disable Animations ](img/4ccdc5a19939594a7164e4fa50c6b312.png)

AutoAnimate 是一个零配置工具，它还提供了一个选项来自定义默认动画关键帧和使用自定义动画。然而，根据我的经验，AutoAnimate 的默认动画产品对于大多数组件来说已经足够了。

> **N.B.** ，注意:你可以在这个 [CodeSandbox](https://codesandbox.io/s/auto-animate-amimation-examples-react-tfk9d2) 中找到本教程的完整代码

## 结论

AutoAnimate 通过其零配置方法、易用性和快速实现使动画变得非常简单——它帮助开发人员在他们的项目中有效地提供流畅的用户体验。

除了我们今天看到的 React 示例，AutoAnimate 还支持 Vue、Angular 和 Svelte。你可以在官方[文档](https://auto-animate.formkit.com/#usage-vue)中找到其他 JavaScript 框架的例子。

请在下面的评论中告诉我你自己使用 AutoAnimate 的经历，感谢你的阅读！

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