# 如何在 Node.js - LogRocket 博客中为 RBAC 和 ABAC 使用 AccessControl

> 原文：<https://blog.logrocket.com/using-accesscontrol-for-rbac-and-abac-in-node-js/>

当构建软件时，系统安全性是一个关键的考虑因素，并且有多种机制用于确保软件系统的安全性。常见的有基于角色的访问控制(RBAC)和基于属性的访问控制(ABAC)。

[AccessControl](https://www.npmjs.com/package/accesscontrol) ，Node.js 模块，可以用来实现这两种访问控制机制。在深入研究 AccessControl 如何工作之前，让我们简要解释一下这两种机制以及它们是如何工作的。

## 基于角色的访问控制(RBAC)

基于角色的访问控制，也称为基于角色的安全性，是一种限制用户使用其角色、特权和权限访问系统的机制。

在应用程序中，为各种用户类型(例如，作者或读者)创建角色。执行某些操作或访问应用程序资源的权限被分配给特定的角色。例如，在写作应用程序中，作者可以被授予创建、更新、阅读和删除帖子的权限，而读者只能阅读帖子。

使用 RBAC 时，有[三条指导原则](https://en.wikipedia.org/wiki/Role-based_access_control):

1.  角色分配:只有当主体选择或被分配了角色时，主体(即用户)才能行使权限。
2.  角色授权:必须为主体授权主体的活动角色。对于上面的规则 1，该规则确保用户只能担当他们被授权的角色。
3.  权限授权:只有当权限被授权给主体的活动角色时，主体才能行使权限。对于规则 1 和规则 2，该规则确保用户只能行使他们被授权的权限。

一个用户可以有多个角色，一个角色可以有多个权限。RBAC 还支持角色层次结构，其中高级角色继承其子角色的权限。可以应用附加约束来对来自另一个角色的权限的潜在继承设置限制性规则。一些约束的例子是:

*   角色不能继承自身
*   不允许交叉继承(如果编写器从读取器继承，则读取器不能从编写器继承)
*   角色不能预先扩展不存在的角色

## 基于属性的访问控制(ABAC)

[基于属性的访问控制](https://en.wikipedia.org/wiki/Attribute-based_access_control)，也称为 IAM 的基于策略的访问控制，定义了一种访问控制范式，通过使用将属性结合在一起的策略，将访问权限授予用户。策略可以使用任何类型的属性，例如用户属性、资源属性、对象和环境属性。

ABAC 可用于补充 RBAC，因为除了角色和权限之外，策略可用于定义允许或不允许的属性。

## 使用 AccessControl 的 Node.js RBAC-ABAC

Node.js 模块融合了 RBAC 和 ABAC 的精华。它实现了 RBAC 基础，也关注资源和动作属性。有关该模块功能的完整列表，请查看文档。

### 装置

与 [npm](https://www.npmjs.com/package/accesscontrol) : `npm i accesscontrol --save`。
带[纱](https://yarnpkg.com/package/accesscontrol) : `yarn add accesscontrol`

### 角色

角色充当权限的容器。根据用户的责任将它们分配给用户。您可以简单地通过在一个`AccessControl`实例上调用`.grant(<role>)`或`.deny(<role>)`方法来创建和定义角色。

```
import { AccessControl } from 'accesscontrol';

const ac = new AccessControl();
ac.grant('reader');

```

角色可以扩展其他角色。您可以通过在现有角色上调用`.extend`来扩展角色。

```
ac.grant('reader').extend('writer');

```

### 动作和动作属性

动作和动作属性表示角色可以对资源执行的操作。它们是基于经典 CRUD 的有限固定列表。有两个动作属性定义了角色对资源的占有:own 和 any。

例如，`editor`角色可以`create`、`read`、`update`或`delete` (CRUD)任何`post`资源。但是`writer`角色可能只读取或更新它自己的`post`资源。

您可以使用:`createOwn`、`readOwn`、`updateOwn`、`deleteOwn`、`createAny`、`readAny`、`updateAny`和`deleteAny`方法来定义对资源的操作和占有。

```
const ac = new AccessControl();
ac.grant('reader')
  .readAny('post')
  .grant('writer')
    .createOwn('post')             
    .deleteOwn('post')
    .readAny('post')
  .grant('editor')                   
    .extend('writer')                 
    .updateAny('post')  
    .deleteAny('post');

```

### 资源和资源属性

这些代表我们想要保护的系统元素，比如`post`。多个角色可以访问特定的资源，但可能对资源的所有属性没有同等的访问权限。您可以使用 Glob 符号来定义允许或拒绝的属性。

例如，我们有一个具有以下属性的`post`资源:`id`、`title`和`description`。任何`post`资源的所有属性都可以被`editor`角色读取:

```
ac.grant('editor').readAny('post', ['*']);

```

但是`id`属性不应该被`reader`角色读取。

```
ac.grant('reader').readAny('post', ['!id']);

```

### 检查权限和过滤属性

使用角色、操作和资源的组合来确定授予的权限。您可以在一个`AccessControl`实例上添加`.can(<role>).<action>(<resource>)`,以检查特定资源和动作的授权许可。

```
const permission = ac.can('reader').readAny('post');
permission.granted;
permission.attributes;
permission.filter(data);

```

### 一次定义所有授权

您可以将授权直接传递给`AccessControl`构造函数。它接受一个`Object`:

```
let grantObjects = {
reader: {
        post: {
            'read:any': ['*', '!id]
        }
    },
    writer: {
        post: {
            'create:own': ['*'],
            'read:any': ['*'],
            'update:own': ['*'],
            'delete:own': ['*']
        }
    },
    editor: {
        post: {
            'create:any': ['*'],
            'read:any': ['*'],
            'update:any': ['*'],
            'delete:any': ['*']
        }
    }
}

const ac = new AccessControl(grantsObject);
```

或者一个数组:

```
let grantArray = [
  { role: 'reader', resource: 'post', action: 'read:any', attributes: '*, !id' },
  { role: 'writer', resource: 'post', action: 'read:any', attributes: '*' },
  { role: 'writer', resource: 'post', action: 'create:own', attributes: '*' },
  { role: 'writer', resource: 'post', action: 'update:own', attributes: '*' },
  { role: 'writer', resource: 'post', action: 'delete:own', attributes: '*' },
  { role: 'editor', resource: 'post', action: 'read:any', attributes: '*' },
  { role: 'editor', resource: 'post', action: 'create:any', attributes: '*' },
  { role: 'editor', resource: 'post', action: 'update:any', attributes: '*' },
  { role: 'editor', resource: 'post', action: 'delete:any', attributes: '*' },
]

const ac = new AccessControl(grantArray);

```

### Express.js 示例

```
const ac = new AccessControl(grants);

router.get('/posts/:title', function (req, res, next) {
    const permission = ac.can(req.user.role).readAny('post');
    if (permission.granted) {
        Video.find(req.params.title, function (err, data) {
            if (err || !data) return res.status(404).end();
            res.json(permission.filter(data));
        });
    } else {
        res.status(403).end();
    }
});

```

## 结论

我们已经展示了如何在服务器端应用程序中使用`AccessControl`进行授权。我们还可以使用它来授权客户端应用程序中的路由和 UI 元素，这可以通过对服务器和客户端使用相同的 grant 对象来完成。`AccessControl`是 Node.js 中唯一实现访问控制的库，Node-casbin 和 CASL 也是实现访问控制的 Node.js 库。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.