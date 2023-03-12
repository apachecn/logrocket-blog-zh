# 肮脏的 Terraform hacks 

> 原文：<https://blog.logrocket.com/dirty-terraform-hacks/>

Terraform 是一个管理基础设施的神奇工具，它非常简单，只需几个小时就能掌握。然而，一旦你开始使用 Terraform，你会很快遇到看似简单却没有明显解决方案的任务。让我们来看看一些技巧和窍门，帮助您充分利用流行的基础设施即代码(IaC)解决方案。

## 使用`count`作为资源的开关

Terraform 的优势之一是能够将资源和数据块转化为可重用的模块。作为这个过程的一部分，您通常需要一种方法来禁止基于输入变量创建某些资源。目前，没有像`resource_enabled = false`这样的属性来禁止创建资源。幸运的是，您可以通过设置`count = 0`禁用资源创建或者设置`count = 1`启用资源创建来达到类似的效果。

`count`可用于创建资源数组，而不仅仅是单个资源，因此设置`count = 0`将创建长度为 0 的资源数组，有效地禁用资源。这种技术甚至在官方的地形模块中也很常见。例如，以下缩写代码片段来自官方 [`terraform-aws-autoscaling`](https://github.com/terraform-aws-modules/terraform-aws-autoscaling/blob/226f911f810b26929121ba96cccd8bdd10c35a9b/main.tf#L5) 模块源代码。

```
resource "aws_launch_configuration" "this" {
  count = var.create_lc ? 1 : 0

  image_id                    = var.image_id
  instance_type               = var.instance_type
  # ...
}

```

如果在使用模块时变量`create_lc`被设置为`true`，该代码创建一个 [AWS 自动缩放启动配置](https://www.terraform.io/docs/providers/aws/r/launch_configuration.html)。

设置`count`会导致资源变成一个数组，而不是一个单独的项目，所以如果您需要访问该资源的属性，您需要将它们作为一个数组来访问。例如，如果您需要从上面的`aws_launch_configuration`中访问`id`属性，您需要编写类似于`concat(aws_launch_configuration.this.*.id, [""])[0]`的代码来安全地从资源数组中取出`id`。

## 用`null_resource`运行本地命令

有时，Terraform 提供的内置功能是不够的。例如，您可能需要在运行 Terraform 的机器上本地执行一些命令。您可以使用神秘命名的`[null_resource](https://www.terraform.io/docs/providers/null/resource.html)`来实现这一点。这类似于 Terraform 资源图中的普通资源，但实际上不做任何事情。

这为什么有用？因为`null_resource`可以像任何普通资源一样运行供应器，包括在本地机器上运行命令的`local-exec`供应器。您可以通过传入一个`triggers`地图来控制该置备程序何时运行。

例如，如果 Kubernetes Terraform 提供程序没有您需要的所有功能，您可以使用`null_resource`手动运行`kubectl apply`命令，如下所示。

```
variable "config_path" {
  description = "path to a kubernetes config file"
}
variable "k8s_yaml" {
  description = "path to a kubernetes yaml file to apply"
}

resource "null_resource" "kubectl_apply" {
  triggers = {
    config_contents = filemd5(var.config_path)
    k8s_yaml_contents = filemd5(var.k8s_yaml)
  }

  provisioner "local-exec" {
    command = "kubectl apply --kubeconfig ${var.config_path} -f ${var.k8s_yaml}"
  }
}

```

在上面的例子中，对 Kubernetes 配置文件或 Kubernetes YAML 内容的任何更改都会导致命令重新运行。不幸的是，使用这种方法很难获得`local-exec`命令的输出并将其保存到 Terraform 状态。您还需要确保运行 Terraform 的机器安装了依赖项，以运行由`local-exec` provisioner 指定的实际命令。

## 将依赖的提供者分成阶段地形运行

如果您正在 Terraform 中构建一个大型基础设施，您可能需要在 Terraform 中创建一个服务，然后通过一个单独的 Terraform 提供者配置该服务。Terraform 非常擅长处理资源之间的依赖关系，但是它不能处理一个 Terraform 提供者依赖于另一个提供者的资源创建的情况。

例如，如果您需要使用 Terraform 创建一个 Kubernetes 集群，然后在创建之后使用 Terraform Kubernetes 提供程序配置同一个集群，那么您就会遇到麻烦。这是因为 Terraform 将尝试连接到所有定义的 provisioners，并在规划期间读取所有定义的资源的状态，但它不能连接到 Kubernetes provider，因为集群尚不存在。

如果 Terraform 可以像这样处理提供者之间的依赖关系，那就太好了，但是您可以通过将您的 Terraform 项目分成可以在一个链中运行的较小的项目来解决这个先有鸡还是先有蛋的难题。

假设您正在为 Terraform 使用远程状态，您可以使用`[terraform_remote_state](https://www.terraform.io/docs/providers/terraform/d/remote_state.html)`数据源从以前的运行中导入 Terraform 状态。这使得先前 terraform 运行的输出可以作为下一次 terraform 运行的输入。

假设 Terraform 运行创建了一个 Kubernetes 集群，并输出该集群的连接信息。下一次 Terraform 运行可以导入第一次运行的状态，并将集群连接信息读入 Terraform Kubernetes 提供程序。

下面演示了该技术。第一阶段可能看起来像这样:

```
# stage1/main.tf

provider "aws" {
  region  = "us-east-1"
}

terraform {
  backend "s3" {
    bucket = "my-terraform-state-bucket"
    key    = "stage1.tfstate"
    region = "us-east-1"
  }
}

resource "aws_eks_cluster" "k8s" {
  name = "sample-kubernetes-cluster"
  # ...
}

# Output connection info for the kubernetes cluster into the Terraform state
output "k8s_endpoint" {
  value = aws_eks_cluster.k8s.endpoint
}

output "k8s_ca_data" {
  value = aws_eks_cluster.k8s.certificate_authority.0.data
}

```

Terraform 配置的第二阶段将如下所示。

```
# stage2/main.tf

provider "aws" {
  region  = "us-east-1"
}

terraform {
  backend "s3" {
    bucket = "my-terraform-state-bucket"
    key    = "stage2.tfstate"
    region = "us-east-1"
  }
}

# Import the state from stage 1 and read the outputs
data "terraform_remote_state" "stage1" {
  backend = "s3"

  config = {
    bucket = "my-terraform-state-bucket"
    key    = "stage1.tfstate"
    region = "us-east-1"
  }
}

provider "kubernetes" {
  cluster_ca_certificate = base64decode(data.terraform_remote_state.stage1.outputs.k8s_ca_data)
  host = data.terraform_remote_state.stage1.outputs.endpoint
  # ...
}

resource "kubernetes_deployment" "example" {
  # ... continue configuring cluster
}
```

在上面的例子中，我们运行了第一个阶段来创建一个 Kubernetes 集群，并将集群的连接信息输出到 Terraform 状态。然后，第二阶段将第一阶段的 Terraform 状态作为`data`导入，并读取连接信息以配置集群。

## 用`templatefile()`处理资源间的文件依赖关系

Terraform 使得从一个资源获取输出并将其作为另一个资源的输入变得容易。然而，当一个资源在本地文件系统上写入一个文件，而另一个资源需要读取该文件作为输入时，它就会陷入困境。

理想情况下，资源永远不会这样做，但在现实中，提供者有时会将输出写入本地文件，而不是将输出作为字符串返回。当资源之间的依赖关系是以读写本地文件的形式出现时，Terraform 无法判断出这种依赖关系。

幸运的是，您可以通过使用 [`templatefile()`](https://www.terraform.io/docs/configuration/functions/templatefile.html) 函数来欺骗 Terraform 实现这种依赖性。这个函数从文件系统中读取一个文件，并在它读取文件时将您传递给该函数的任何变量替换到文件中。但是，如果这些变量来自另一个资源的输出，那么 Terraform 必须在读取文件之前等待该资源的应用。

下面使用来自阿里云平台的 [`alicloud_cs_managed_kubernetes`](https://www.terraform.io/docs/providers/alicloud/r/cs_managed_kubernetes.html) 资源演示了这一点。该资源创建一个 Kubernetes 集群，并将集群配置写到本地磁盘上的一个文件中。然后，我们使用`templatefile()`读取该文件，并将其内容写入输出。

```
resource "alicloud_cs_managed_kubernetes" "k8s" {
  name_prefix               = "sample kubernetes cluster"
  kube_config               = "${path.module}/kube.config"
  # ...
}

output "k8s_cluster_config_contents" {
  value = templatefile("${path.module}/kube.config", {
    # This variable creates a dependency on the cluster before reading the file
    cluster_id = alicloud_cs_managed_kubernetes.k8s.id
  })
}

```

在上面的例子中，`kube.config`文件是通过带有变量的`templatefile()`函数读取的，该变量取决于集群资源的输出。实际上并没有使用`cluster_id`变量；它只是强制 Terraform 在尝试读取`kube.config`内容之前等待集群被创建。如果您使用这种技术，您将需要在第一次运行之前在您的本地文件系统上手动创建该文件，因为 Terraform 希望该文件在它开始运行之前就已经存在。

## 结论

希望这些技术能在你的陆地旅行中派上用场。如果你发现了任何有用的地形技巧，请在下面的评论中分享。

快乐的地球化！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)