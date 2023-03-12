# 比较 GKE、EKS 和 Azure - LogRocket 博客上的 Kubernetes

> 原文：<https://blog.logrocket.com/comparing-kubernetes-cloud-providers-gke-eks-azure/>

> 编者按:这篇文章最后一次更新是在 2021 年 12 月 2 日，包括了 GKE、EKS 和微软 Azure 的最新更新信息。

假设您已经决定运行 Kubernetes，也称为 K8s，您可能对在哪里运行它有一些顾虑和问题。

支持 Kubernetes 的云提供商其实有不少[家，但我们将重点关注三家主要的，谷歌的 Kubernetes 引擎(GKE)、微软的 Azure Kubernetes 服务(AKS)和亚马逊的 Elastic Kubernetes 服务(EKS)。](https://blog.logrocket.com/kubevela-intro-better-way-ship-applications/)

在本文中，我将讨论三个主要云提供商的主要特性和功能，提出我认为选择目标平台的一些非常明确的标准:您自己的本地数据中心和虚拟服务提供商。

### 目录

## 你应该在云上运行你的系统吗？

作为一个模块化、灵活且可扩展的平台，您可以在第三方数据中心、任何流行的云提供商甚至跨多个云提供商部署 K8s。当然，正确的做法取决于你独特的项目。让我们考虑以下场景。

首先，假设您在本地或第三方数据中心运行您的系统。您在定制基础架构上投入了大量时间、金钱和培训。随着时间的推移，自行构建基础架构的挑战变得越来越沉重。

或者，您在一个云提供商上运行您的非 Kubernetes 系统，但是您想从 K8s 的优势中获益。

您会注意到，在这两个场景中，我都没有提到容器。如果你已经集装箱化，对你有好处！如果没有，就当是入场费吧。

### 本地或第三方数据中心

您可能有很好的理由在您严密控制的环境中运行，例如，安全性、法规、遵从性、性能或成本。在这种情况下，云提供商可能会失败。但是，你仍然可以通过自己运行它来获得 K8s 的好处。因为您已经在做了，所以您拥有管理底层基础架构的专业知识、技能和经验。

但是，如果您选择投资您的内部基础架构，或者您跨多个虚拟服务提供商部署，仅仅因为您在云成为企业的可靠解决方案之前就开始了，那就另当别论了。

你有机会在一个复杂的镜头中升级一切。您可以切换到云中的托管基础设施，将您的系统打包到容器中，并使用 K8s 对它们进行编排。

### 你已经在云端了

在第二个场景中，选择运行由您的云提供商管理的 K8s 可能是显而易见的，因为您已经在云中运行了。K8s 让您有机会用一种流畅的体验来取代您必须构建、集成和维护的许多管理层、监控层和安全性层，这种体验在 Kubernetes 每次发布另一个版本时都会得到改进。

现在我们已经回顾了 Kubernetes 的一些基础知识，让我们来看看我们之前列出的三个云提供商。

## 谷歌 Kubernetes 引擎(GKE)

### GKE 一体化

K8s 来自谷歌，使 [GKE 成为谷歌管理 Kubernetes](https://cloud.google.com/kubernetes-engine) 的官方方法。Google SREs 管理 K8s 控制面板并提供自动升级。因为谷歌影响了 K8s，并从第一天起就将其作为谷歌云平台的容器编排解决方案，所以 GKE 提供最佳集成是有道理的。

同样，你可以相信 GKE 是最新的供应商。与其他云提供商相比，GKE 对 K8s 的新特性和功能进行了更多的测试。

### GKE 成本

在 GKE 上，您不必为 K8s 控制平面付费，但是，您需要为工作节点付费。您还可以访问 Google Container Registry (GCR)，以及 Stackdriver 日志记录和 Stackdriver 监控，它们提供了集成的中央日志记录和监控。最后，为了保护和优化您的部署管道，您可以利用 K8s 中包含的本机 CI/CD 工具。

### GKE 特色

与其他云提供商相比，谷歌网络被认为是顶级的。在撰写本文时，GKE 对多实例[GPU 的支持正在预览中](https://cloud.google.com/blog/products/containers-kubernetes/gke-now-supports-multi-instance-gpus)。

GKE 还有一些其他巧妙的技巧，例如，它利用通用 K8s 概念，如服务和入口，对负载平衡进行细粒度控制。如果您的 K8s 服务是类型`LoadBalancer`，GKE 将通过一个普通的 L4 TCP 负载均衡器向世界公开它。然而，如果你在你的服务前面创建一个`Ingress`对象，GKE 将创建一个 L7 负载平衡器，它能够完成 SSL 终止。如果注释正确，L7 负载平衡器甚至能够允许 gRPC 流量。

GKE 还提供两种不同的操作模式，标准和自动驾驶。标准体验从一开始就提供了，而 [autopilot](https://cloud.google.com/kubernetes-engine/docs/concepts/autopilot-overview) 是你的集群的自动化管理器，它只对你运行 pod 进行收费，以优化计费。

GKE 甚至包括预建的 K8s 模板，您可以将其用于您的部署。这些都可以在[谷歌云市场](https://console.cloud.google.com/marketplace/browse?filter=solution-type:k8s&_ga=2.217175377.69959840.1636458606-1489105345.1636458606&pli=1)上找到。

K8s 本身是平台不可知的。理论上，你可以轻松地从任何一个云平台切换到另一个平台，也可以在你自己的基础设施上运行。实际上，当您选择平台提供商时，您通常希望利用并受益于需要额外工作才能迁移到不同提供商或本地的特定服务。

[GKE 本地](https://cloud.google.com/gke-on-prem/)提供工具、集成和访问来帮助统一体验，并将本地集群视为在云中运行。它不是完全透明的，也不应该是，但它有所帮助。

## Microsoft azure kubrintes service(AK)

微软 Azure 最初提供了一个名为 ACS 的解决方案，支持 Apache Mesos、K8s 和 Docker Swarm。2017 年 10 月，它推出了 Azure Kubernetes 服务 AKS，作为专门的 Kubernetes 托管服务，导致其他选择失败。

### AKS 成本

[AKS 是微软提供的 K8s 解决方案](https://azure.microsoft.com/en-us/services/kubernetes-service/#solution-architectures)。AKS 和 GKE 很像，比如它也免费为你管理一个 K8s 集群。它被 CNCF 认证为符合 K8s，这意味着没有定制黑客。

### AKS 集成

微软在 K8s，尤其是 AKS 上投入了大量资源。AKS 包括与`ActiveDirectory`的强大集成，用于身份验证和授权、集成监控和日志记录以及 Azure 存储。您还可以获得内置的容器注册表、网络和支持 GPU 的节点。

AKS 最有趣的特性之一是使用 virtual-kublet 项目来集成[Azure Container Instances(ACI)](https://docs.microsoft.com/en-us/samples/azure-samples/virtual-kubelet-aci-burst/bursting-from-aks-to-aci-with-the-virtual-kubelet/)。ACI 消除了为集群配置节点的需要，如果您要处理高度可变的负载，这将是一个巨大的负担。

### 对 AKS 的批评

与本质上是黄金标准的 GKE 相比，开发者对 AKS 有一些相同的批评。在 AKS 的最初实现中，在 AKS 上设置集群需要很长时间，平均 20 分钟。此外，启动时间波动很大，有时需要一个多小时，导致开发人员体验不佳。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

最初，你还需要一些 web UI 的组合，比如 Azure Portal Manager、PowerShell 和一个普通的 CLI 来提供和设置一切。然而，随着最近的升级，体验得到了改善，与 Visual Studio 、 [Azure DevOps](https://azure.microsoft.com/en-us/services/devops/) 进行了集成，用于管道管理，与 [Azure Monitor](https://azure.microsoft.com/en-us/services/monitor/) 进行了集成，用于记录和监控您的基础设施。

### AKS 功能

Azure Monitor 包含优化功能，有助于提升应用的整体性能。例如，它告诉您每个节点上正在运行什么，以及平均 CPU 和内存利用率。它还提供关于哪些容器驻留在控制器或 pod 中的信息，帮助您监视整体性能。

Azure Monitor 审查主机上运行的工作负载的资源利用率，这些工作负载与支持 pod 的标准进程无关。它了解集群在平均负载和最重负载下的行为，帮助您确定容量需求并确定集群可以承受的最大负载。

如今，Visual Studio 和 Azure DevOps 都被开发人员广泛使用。对这两种服务的支持和集成极大地改善了 AKS 开发人员的体验。

## 亚马逊弹性库柏服务(EKS)

亚马逊对 K8s 的关注有点晚。它总是有自己的弹性容器服务(ECS)容器编排平台。然而，客户对 K8s 的需求是势不可挡的。许多组织使用[Kubernetes Operations](https://github.com/kubernetes/kops)(kOps)或类似的工具在 EC2 上运行他们的 K8s 集群。

### 例如整合

AWS 开始为官方集成提供适当的支持，今天，EKS 与 IAM 集成，用于身份管理、AWS 负载平衡器、网络和其他各种存储选项。

AWS 的一个有趣的转折是[承诺与 Fargate](https://aws.amazon.com/fargate/) 的集成，这类似于 AKS 与 ACI 的集成。这种集成将消除提供工作节点的需要。此外，它可能允许 Kubernetes 通过其水平 Pod 自动缩放(HPA)和垂直 Pod 自动缩放(VPA)功能自动缩放，提供真正的弹性体验。

### 前情提要

在 EKS，你必须支付管理控制平面，这可能是一个限制因素，如果你只是想与 k8 或你有很多小集群的实验。

### EKS 表演

就性能而言，EKS 介于两者之间。启动集群大约需要 10 到 15 分钟。正如您所预料的，复杂分布式系统的性能是非常微妙的，不能用一个单一的指标来衡量。也就是说，EKS 本身仍相对较新，可能需要一段时间才能充分利用 AWS 的强大基础。

EKS 也有针对几种不同用例的产品。首先，您可以利用完全基于云的基础设施来利用 EKS，包括 Fargate 或 EC2s。亚马逊 EKS Anywhere 是一个预打包的解决方案，可以在本地或混合环境中运行。最后，亚马逊 EKS 发行版允许你用自己的工具部署 EKS。

## k3s:K8s 的新选择

除了我们上面提到的 K8s 的实现，还有一个更新的轻量级替代方案叫做 K3s。K3s 的目标是边缘或基于物联网的应用程序，作为一个单一的打包二进制文件，它比传统的 K8s 占用的空间更小。

您可以通过主要的云提供商来设置和安装 K3s，但是您必须自己做大量的跑腿工作，在您正在使用的云实例上安装二进制文件。

然而，这为更分布式的设备打开了集群市场，对于对 K8s 的好处感兴趣而没有传统基础架构要求的客户来说，这可能是一个很好的选择。

## 结论

我们知道 [Kubernetes 是容器编排](https://blog.logrocket.com/kubernetes-toolbox/)的绝佳选择，但是对你来说最大的问题是你应该在哪里运行它。通常，答案很简单。如果您已经在一个云提供商上运行，只需将您的系统迁移到那个云平台上的 K8s。

如果您有特殊的需求并在自己的硬件上运行，您可以运行自己的 K8s 集群，或者您可以抓住这样一个大型基础架构迁移项目的机会，迁移到云。

如果您有更特殊的需求，但想要 K8s 传统上提供的一些好处，您也可以使用 K3s。无论您的用例是什么，您的应用程序都有很多选项。