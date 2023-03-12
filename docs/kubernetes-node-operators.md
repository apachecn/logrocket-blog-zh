# 节点操作者:Kubernetes 节点管理变得简单

> 原文：<https://blog.logrocket.com/kubernetes-node-operators/>

## 介绍

Kubernetes 是为自动化而设计的。它带有许多内置功能，有助于部署和运行工作负载，可以在控制器的帮助下进行定制。节点操作符是 Kubernetes API 的客户机，充当定制资源的控制器。

本教程分解了 Kubernetes 节点操作符的概念。它回顾了它们是什么，为什么和什么时候需要它们，以及使用它们的优点。它还涵盖了构建操作符的最佳实践，更重要的是，它提供了创建节点操作符的分步指南。

但是，在我们继续之前，让我们快速探索一下在阅读本文时可能会遇到的一些重要的 Kubernetes 组件。我的希望是，最终，这将成为构建 Kubernetes 节点操作符的一站式指南。

### Kubernetes 核心组件说明

*   [控制器](https://kubernetes.io/docs/concepts/architecture/controller/#:~:text=In%20Kubernetes%2C%20controllers%20are%20control,closer%20to%20the%20desired%20state.)是来自 Kubernetes 核心的循环，它通过 API 服务器不断监视集群的状态。这就允许集群拥有扩展的行为，而无需对 Kubernetes 代码本身进行修改
*   [定制资源](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)是为个别用例构建的 Kubernetes API 的扩展
*   Kubernetes API 公开了 HTTP API，允许终端用户和 Kubernetes 集群的其他组件相互通信
*   [Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/) 是由 kubernetes 创建和管理的最小可部署计算单元。它是 Kubernetes 中一个应用程序的单个实例，可能由一个或多个容器组成。
*   Kubernetes 中的节点是包含运行 pod 所需服务的机器(物理或虚拟)
*   部署是描述应用程序状态的声明性配置文件
*   [工作负载](https://kubernetes.io/docs/concepts/workloads/)是为 pod 设置部署规则的 Kubernetes 对象
*   [名称空间](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)是 Kubernetes 集群的逻辑隔离

### 先决条件

因为本教程不是为 Kubernetes 初学者设计的，所以我们至少应该有以下基本知识:

*   Go 编程语言
*   运行码头集装箱
*   运行库组件
*   通过 kube CTL(Kubernetes 命令行工具)与 Kubernetes 交互

出于测试的目的，我们可以使用 Minikube，这是一个使本地运行 Kubernetes 变得容易的工具。运行和安装 Minikube 的步骤见[此处](https://kubernetes.io/docs/setup/learning-environment/minikube/)，安装 kubectl 见[此处](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。也可以在这里看到下载围棋[的说明。最后，按照这些](https://golang.org/doc/install#install)[步骤](https://docs.docker.com/get-started/)来学习 Docker 及其安装。

## 透视 Kubernetes 节点运算符

节点操作员是利用 Kubernetes 的能力来提供云服务的自动化优势的应用程序。他们可以自始至终打包、部署和管理应用程序。这些应用程序不仅可以部署在平台上，还可以在 Kubernetes 可以运行的其他云服务器上运行，例如 EKS、GKE 等。

本质上，节点操作者通过 Kubernetes 提供了特定于应用程序的自动化。最简单的形式是，操作员向 Kubernetes API 服务器添加一个端点，称为定制资源(CR)。

这附带了一个控制平面组件，用于在创建自定义资源时对其进行监控和维护。然后，这些操作员可以根据资源的状态采取行动。

### 运营商是为了谁？

*   基础设施工程师和开发人员，他们经常希望扩展 Kubernetes，以提供特定于他们的应用程序的特性
*   集群管理员，因为操作员可以用更少的管理开销更容易地管理软件，如数据库
*   应用程序开发人员，他们可能希望使用运营商来管理他们交付的应用程序，从而简化 Kubernetes 集群上的部署管道和管理体验

## Kubernetes 节点运算符模式

这些是 Kubernetes 的原则，每个运营商都是建立在这些原则之上的。它们包括:

### 自定义资源

CRs 是为个人使用而构建的 Kubernetes API 的扩展。与其他内置资源不同，它们在默认的 Kubernetes 安装中并不总是可用的。[根据文件](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/):

> “它们代表了特定 Kubernetes 安装的定制…使 Kubernetes 更加模块化。”

CRs 是动态的，可以独立于集群本身进行更新。一旦安装了 CR，用户就可以使用 kubectl 创建和访问它的对象，就像我们对内置资源(如 pods、部署等)所做的一样。

> **注意** : CRs 是使用`CustomResourceDefinition` API 定义的。

### 定制控制器

当我们将自定义资源与自定义控制器相结合时，它提供了一个真正的声明式 API。这允许我们声明或指定资源的期望状态，并使 Kubernetes 对象的当前状态与期望状态保持同步。

## Kubernetes 节点运算符的优势

*   它是一个声明性系统，因为它管理从期望状态到最终状态的资源
*   它是基于 Kubernetes API 构建的
*   敏捷、灵活、操作方便，因为它们使得在 Kubernetes 应用程序上安装和构建变得容易
*   他们打包内部应用程序，并使它们易于访问
*   当我们想要构建更好的自动化体验，而不是手工做重复性的任务或操作时，节点操作符就派上了用场

## 何时使用 Kubernetes 节点操作符

操作员可以代表基础设施工程师/开发人员执行自动化任务。因此，有许多可以使用节点操作符的场景。

例如，在定义 Spark、Cassandra、Airflow、Zookeeper 等定制应用程序时，节点操作符就派上了用场。这些应用可能需要大量的微服务来管理它们的生命周期，而我们可以使用运营商来部署这些应用的实例，使它们更易于管理

它们对于数据库之类的有状态应用程序也很有用。其中一些有状态的应用程序具有预配置和后配置步骤，这些步骤很容易导致错误，可以通过操作员的自动化来减少错误。

其他使用案例可能包括:

*   实施安全策略—例如，在创建 pod 之前扫描图像中的漏洞可以使用操作员轻松实现。
*   创建可用于自动化目的的模板
*   管理复杂的管理任务，如授予访问权限。您可以创建操作符来实施 Kubernetes 级别的集群策略，例如，`do not allow`一些 pod

## 构建节点操作符

如果生态系统中没有实现应用程序所需行为的操作者，我们可以通过各种方法编写自己的代码。然而，本节将详细讨论[操作符 SDK](https://github.com/operator-framework/operator-sdk) 。

运营商 SDK 最初是 CoreOS 写的，现在由红帽维护。这是构建一个操作符最简单、最直接的方法之一，不需要非常了解 Kubernetes API 的复杂性。

其他方法包括 ClientGo，这是一个与 Kubernetes API 连接的 Go 客户端。然而，使用这个客户机构建一个操作符需要 Go 编程语言的工作知识。

Kube Builder 是另一个选择。这是 Kubernetes 特别兴趣小组(SIGs)的一部分，负责构建在 Kubernetes 内运行的应用程序。它也是用 Go 编写的，并使用控制器运行时——因此，它允许与 Kubernetes API 通信。

## 使用 Operator SDK 构建节点运算符

### 1.安装 Operator SDK

安装 Operator SDK 有多种方式，这里我们将重点介绍其中的两种。首先是通过操作符二进制直接安装。我们可以通过运行以下命令从 Operator 框架中获取最新版本的 Operator SDK 来实现这一点:

```
$ wget https://github.com/operator-framework/operator-sdk/releases/download/v0.15.2/operator-sdk-v0.15.2-x86_64-linux-gnu
```

下一步是通过运行以下命令将下载的操作符移动到可执行路径:

```
$ sudo mv operator-sdk-v0.15.2-x86_64-linux-gnu /usr/local/bin/operator-sdk
```

然后，我们可以通过运行以下命令使其可执行:

```
$ sudo chmod +x /usr/local/bin/operator-sdk
```

另一种方法是从托管 SDK 的 GitHub 存储库中克隆 SDK，并从那里进行安装。为此，我们可以在 Go 路径(Go 的安装路径)上为操作符框架创建一个目录:

```
$ mkdir -p $GOPATH/src/github.com/operator-framework
```

然后，我们通过运行以下命令导航到该路径:

```
$ cd $GOPATH/src/github.com/operator-framework
```

现在，我们可以通过运行以下命令集，将 Operator framework 存储库克隆到我们刚刚创建的目录中:

```
$ git clone https://github.com/operator-framework/operator-sdk
$ cd operator-sdk
$ git checkout v0.4.0
$ make dep
$ make install
```

`operator-sdk`命令引导操作员。下面显示了一个示例:

```
$ operator-sdk new sample-operator
$ cd sample-operator
```

运行上述命令生成的项目结构如下所示:

```
├── Gopkg.lock
├── Gopkg.toml
├── build
│   └── Dockerfile
├── cmd
│   └── manager
│       └── main.go
├── deploy
│   ├── operator.yaml
│   ├── role.yaml
│   ├── role_binding.yaml
│   └── service_account.yaml
├── pkg
│   ├── apis
│   │   └── apis.go
│   └── controller
│       └── controller.go
└── version
    └── version.go
```

### 2.自定义资源定义

接下来是生成一些代表项目 CR 定义的代码，即自定义资源(API)和自定义控制器。为此，我们可以运行以下命令:

```
$ operator-sdk add api --api-version=sample-operator.example.com/v1alpha1 --kind=App
$ operator-sdk add controller --api-version=sample-operator.example.com/v1alpha1 --kind=App
```

该命令指定 CRD 将被调用`App`。这为我们创建了`pkg/apis/app/v1alpha1/app_types.go`文件。可以修改该文件以添加额外的参数。

> **注意**:我们也可以运行以下命令来生成 CRD:
> 
> ```
>       $ operator-sdk generate crds
>       $ operator-sdk generate k8s
> 
> ```

这将生成一组新的 YAML 文件和 Go 代码，附加到上面的树中。

注意，`deploy/crds/sample-operator_v1alpha1_app_crd.yaml`文件包含定制资源定义，而`deploy/crds/sample-operator_v1alpha1_app_cr.yaml`文件包含定制资源。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

> **注意**:我们可以通过运行以下命令在 Kubernetes 集群上安装 CRD:
> 
> ```
> kubectl apply -f deploy/crds/sample-operator_v1alpha1_app_crd.yaml
> ```

### 3.控制器

此时，操作员运行所谓的“协调循环”这只是调用一个协调函数，确保每次从我们上面定义的 CR 定义中创建 CR 时都触发一段代码。

`pkg/controller/app/app_controller.go`控制器文件包含控制器逻辑和协调功能。它还包含创建 pod 的示例代码，我们可以根据需要进行调整。

在协调过程中，控制器获取当前名称空间中的应用资源，并将它的副本字段的值(即，要运行的 pod 的期望数量)与运行的 pod 的实际数量进行比较。

这将比较并确保所需的单元数量与活动单元的可用数量相匹配。修改控制器逻辑的一个例子是通过添加存储副本数量的字段，即在`pkg/apis/sample-operator/v1alpha1/app_types.go`文件中，来改变`appSpec` Go 结构。

```
Type appSpec struct {
  Replicas int32  `json:"replicas"`
}
```

> **注意**:对该文件的修改次数没有限制，因为它是高度可定制的。

记住，在对控制器结构进行更改后，总是要运行一个`operator-sdk generate k8s`命令，因为这将更新 API 包文件，即`pkg/apis/app/v1alpha1/zz_generated.deepcopy.go`。

## 测试操作员

在部署操作符之前，我们可以在集群之外的本地机器上测试它。为此，首先，我们通过运行以下命令来启动集群:

```
$ operator-sdk run local
```

接下来，我们可以通过运行以下命令来测试我们的示例应用程序:

```
$ kubectl apply -f <(echo "
apiVersion: sample-operator.example.com/v1alpha1
kind: app
metadata:
         name: test-app
spec:
         replicas: 3
")
```

> **注意**:这将旋转三个吊舱，如控制器逻辑中所定义的。
> 
> ```
>  
>       $ kubectl get pods -l app=test-app
>       NAME                                       READY            STATUS             RESTARTS           AGE
>       test-app-podc2ckn                   1/1                     Running                   0          103s
>       test-app-podhg56f                   1/1                     Running                   0          103s
>       test-app-pod12efd                   1/1                     Running                   0          103s
> 
> ```

一旦我们确信操作员按预期工作，并且其他 kubectl 命令(`create`、`describe`、`edit`)可以针对我们的 CR 成功运行，我们的下一步就是部署集群。

## 部署操作员

为了发布操作符，我们需要一个 Docker 容器映像，Kubernetes 集群可以轻松地访问它。我们将图像推送到任何容器注册表。请注意，在本教程中，我们将使用 [Quay.io](https://quay.io/) 。

下一步是通过运行以下命令构建并发布到注册表:

```
$ operator-sdk build quay.io/<username>/sample-operator
$ docker push quay.io/<username>/sample-operator
```

现在更新`deploy/operator.yml`文件，指向注册表中新的 Docker 映像。我们通过运行以下命令来实现这一点:

```
$ sed -i 's|REPLACE_IMAGE|quay.io/<username>/sample-operator|g' deploy/operator.yaml
```

### 运营商最佳实践

*   针对控制器代码运行足够的测试。这确保了如果操作符突然停止，您的应用程序仍将按预期运行
*   运营商应利用内置资源，如 pod、部署等。这使得运营商可以建立在先前测试和验证的 Kubernetes 资源上
*   为每个应用开发一个操作员。与让一个运营商部署多个应用程序相比，这种方式更容易维护。例如，一个数据库运营商部署 MySQL 和 Redis 并不理想
*   持续监控构建的操作程序
*   使用声明式 API，因为 Kubernetes 本身支持声明式配置。这使得用户更容易表达他们想要的集群状态
*   始终使用 SDK。这使得构建操作符变得更容易和更好，而不用担心 Kubernetes 库是如何实现的底层细节
*   确保控制器尽可能精简，不依赖于外部资源，这样仅`kubectl install`就足以部署操作员

## 结论

节点操作符旨在简化扩展 Kubernetes 的过程，正如我们所见，它们很容易集成和构建。

在众多好处中，它们简化了自动化，使我们可以在任何地方轻松部署云原生应用程序(小型、独立、松散耦合的服务的集合),并完全按照我们的意愿管理它们。

同样，希望这有助于快速开始构建自己的 Kubernetes 操作器。想找或者分享运营商？查看 [OperatorHub.io](https://operatorhub.io/) 了解更多详细信息。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)