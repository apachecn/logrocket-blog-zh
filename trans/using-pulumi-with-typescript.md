# 将 Pulumi 与 TypeScript 一起使用

> 原文：<https://blog.logrocket.com/using-pulumi-with-typescript/>

Pulumi 是一个越来越受欢迎的基础设施代码(IaC)平台，利用多种编程语言与云资源进行交互。特别是，Pulumi 程序是基础设施的蓝图，描述了后者应该如何组成。

在本文中，我们将关注用 TypeScript 编写的程序。首先，我们先来看看基础设施即服务(IaaS)提供商和 IaC 的优势。然后，我们将深入探讨如何一起使用 Pulumi 和 TypeScript。我们将使用 Pulumi 和 TypeScript 建立一个小的示例项目，并使用 Amazon Web Services 作为云提供商进行测试。

*向前跳转:*

## IaaS 的利与弊

基础设施即服务旨在通过提供计算能力、内存、存储和相关软件作为云服务来取代本地数据中心和基础设施。简而言之，我们不是自己建立昂贵的数据中心，而是从另一家公司(所谓的 IaaS 或云提供商)租用这些数据中心。

流行的 IaaS 提供商的例子有 Google Compute Engine、AWS 和 Microsoft Azure。

迁移到基于 IaaS 的解决方案有几个优点:

*   **强大的基础设施**:云提供商提供的基础设施通常比我们在内部构建的基础设施更加强大和可靠
*   **强大的安全性**:云提供商对其基础设施的安全性进行了大量投资。因此，他们通常会发布最佳实践，如果遵循这些实践，也将增强我们基础架构的安全性
*   **访问资源指标**:大多数云提供商提供对几种不同资源指标(数据库使用、计算单元等)的访问。)，使我们更容易监控系统的运行情况，并主动应对潜在问题
*   **增强的可扩展性和灵活性**:我们可以设置基础设施，根据负载自动扩展和缩减，而不必手动(和物理)更新数据中心的机器

尽管有这些优势，IaaS 也带来了一些挑战。首先，对于 IaaS，我们非常依赖云提供商。此外，不同云提供商提供的资源没有标准。因此，从一个提供商迁移到另一个提供商可能非常复杂，这取决于我们基础设施的复杂程度。虽然 IaC 确实可以缓解这个问题，但是如果我们运行的是一个非常定制的基础设施，那么就不可能毫不费力地迁移它。

其次，IaaS 也会带来意想不到的成本。错误配置是非常容易的，每一个错误配置都可能导致意想不到的成本。例如，如果我们设置了一个自动缩放功能，如果我们的网站负载增加，我们可能会发现自己在 DoS 攻击的情况下成本会增加。最后，错误配置的另一个后果是设置了错误的安全策略。

一般来说，我们应该始终检查每个云提供商在服务级别协议、带宽和功能方面提供的服务，并仔细考虑这些服务是否符合我们的需求。

此外，在对特定的 IaaS 提供商做出全面承诺之前，我们应该确保我们拥有必要的能力。否则，我们可能会发现自己有意想不到的成本和安全漏洞。

## IaC 的利弊

如果我们将基础架构转移到云提供商，我们仍然需要手动创建和配置资源。这种操作成本极高，而且容易出错。

基础设施即代码旨在通过让我们使用机器可读的代码来配置我们的系统，而不是物理配置或交互式配置工具来解决这个问题。

尽管 IaC 和 IaaS 经常一起使用，但它们彼此完全独立。一方面，我们可以依赖 IaaS 提供商，手动管理我们的基础架构。另一方面，我们可以使用 IaC 来配置我们的内部环境。

IaC 有几个优点:

*   **自动化**:IaC 最大的优势就是自动化。如果我们用代码描述我们的整个基础设施，我们可以建立管道来部署对资源的更改。从理论上讲，如果出现问题，我们总是可以通过重新部署以前的管道来回滚它们。手动回滚资源将是一个非常复杂、容易出错的过程
*   可共享性:代码可以被版本化并在团队间共享，这使得重用变得更加容易
*   将最佳实践应用于我们的基础设施:通过 IaC，我们可以将一整套编码设计模式和最佳实践应用于我们的基础设施。例如，我们可以编写小的、模块化的组件，并用它们组成更复杂的资源和环境。使用小组件简化了故障排除
*   在我们的代码中包含构建标准(比如命名约定):通过在我们的代码中包含构建标准，我们不必在每次创建新资源时都记住它们
*   **简单的文档**:描述我们的基础设施的代码可以用于文档目的

然而，就像 IaaS 一样，IaC 也带来了一些挑战。首先，重新部署以前版本的基础设施并不总是可能的。根据我们重新部署的资源，以及我们选择的云提供商(如果有的话)，以完全自动化的方式恢复以前的状态可能是不可行的。

这带来了更大的挑战，迫使我们手动解决这个问题。类似地，如果部署在过程中失败，可能很难从同一点重新启动，从头重新部署可能需要很长时间。

第二，描述基础设施的代码可能很快变得非常大。理解代码做什么并跟踪代码库中的所有依赖项可能很困难。

第三，我们的代码可能会依赖于我们的云提供商提供的一些库。因此，我们将不得不管理这种依赖关系的更新(可能会破坏更新)。此外，如果不小心管理基础设施，我们可能会有漂移。

当我们的资源的部署版本与我们的代码提供的描述不匹配时，漂移就会发生。这可能是因为有人手动执行了某些操作，但也可能是因为自动更新了部署的资源。

最后，由于代码可能在某个存储库中进行版本控制，我们应该限制对该存储库的访问，否则我们可能会遇到安全问题。

## 带 Pulumi 和 TypeScript 的 IaC

现在，让我们把重点放在使用 TypeScript 作为编程语言在 Pulumi 中编写基础设施代码上。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

对于我们的例子，我们将建立一个非常简单的基于类型脚本的 Pulumi 项目，在 AWS 上创建一个 S3 桶。然而，我们可以将 Pulumi 与其他云提供商(如 Azure 和 GCP)或编程语言(如 Java、Python 或 Go)一起使用。

### 一般概念

如前所述，Pulumi 程序描述了项目基础设施的蓝图。特别是，它们分配资源并设置它们的属性以匹配基础结构的期望状态。

资源也可以在整个程序中用来设置依赖关系。例如，我们可能希望在另一个资源 R [2] 之后创建一个资源 R [1] 。

Pulumi 程序驻留在项目中。这些项目是包含源文件(例如，类型脚本文件)以及配置部署的元数据(即，程序运行的方式)的目录。

Pulumi 程序的实例称为栈，代表不同的部署环境。例如，我们可能为开发、试运行和生产各准备了一个堆栈。

### 项目布局

最简单的形式是，基于 TypeScript 的 Pulumi 项目包含以下文件:

*   `index.ts`:我们的 Pulumi 程序的“主”文件，描述作为当前栈的一部分将要部署的资源
*   `package.json`和`package-lock.json`:描述我们项目依赖关系的文件
*   `Pulumi.<stack-name>.yaml`:一个或多个设置我们的堆栈配置参数的文件
*   `Pulumi.yaml`:一个设置我们项目的一些一般信息的文件，比如名称和描述
*   `tsconfig.json`:用于配置类型脚本的文件

### 添加依赖关系

下面的 JSON 片段显示了 Pulumi 使用 AWS 作为云提供者为 TypeScript 项目生成的默认`package.json`文件:

| `{
"name": "LogRocket",
"main": "index.ts",
"devDependencies": {
"@types/node": "^14"
},
"dependencies": {
"@pulumi/pulumi": "^3.0.0",
"@pulumi/aws": "^5.0.0",
"@pulumi/awsx": "^0.40.0"
}
}` |
|  |

这个文件定义了项目的名称、`LogRocket`、主文件、`main.ts`和几个依赖项。特别是，`pulumi/aws`和`pulumi/awsx`包含了描述基础设施资源的必要类。

`pulumi/awsx`遵循 AWS 架构良好的最佳实践，定义自以为是的组件，其默认值被认为可以简化和加快工作基础设施的部署。

### 项目配置

下面的 YAML 代码片段显示了默认的`Pulumi.yaml`文件:

```
name: LogRocket
description: A minimal AWS TypeScript Pulumi program
runtime: nodejs
```

正如我们所看到的，我们项目的基本配置相当简单。我们必须再次设定项目的`name`，并提供一个简要的`description`和目标`runtime`。Pulumi 将使用后一个属性来建立如何运行我们的程序。

由于我们使用的是 TypeScript，我们还应该提供一个`tsconfig.json`文件。

### 堆栈配置

我们要担心的最后一项配置是每个堆栈的配置。正如我们在上面看到的，Pulumi 中的栈只是我们程序的实例，对应于我们基础设施的不同部署环境。

例如，我们可能为试运行和生产部署了相同的一组资源，但是用于生产的资源可能比用于试运行的资源性能更高。

下面的 YAML 代码片段显示了一个名为`Pulumi.dev.yaml`的可能的配置文件:

```
config:
  aws:profile: ProfileName
  aws:region: us-west-2
  aws:assumeRole:
    roleArn: "arn:aws:iam::000000000000:role/AccessRole"
```

上面的代码简单地配置了 AWS 提供者，告诉 Pulumi 使用哪个 AWS 概要文件来创建基础设施。在这种情况下，我们要求 Pulumi 承担一个给定的角色，而不是硬编码 AWS 凭证。

我们可以使用一些命令添加任何其他配置值。例如，`pulumi config set bucketName my-bucket`将添加一个新的设置`bucket-name`，其值为`my-bucket`:

```
config:
  LogRocket:bucketName: my-bucket
```

配置值是命名空间。在上面的例子中，Pulumi 使用名称空间的默认值，即项目名称。如果我们想要一个不同的值，我们需要将其指定为键名的一部分:`pulumi config set namespace:key value`。

如果将`--secret`传递给命令，那么 Pulumi 将加密该值，而不是在`.yaml`文件中以纯文本形式显示它。秘密加密依赖于堆栈。因此，如果在不同的堆栈中设置，相同的秘密将导致不同的加密值。

### 打字稿代码

在设置了所有必需的配置值之后，我们终于可以关注创建 S3 存储桶的代码了:

```
import * as pulumi from "@pulumi/pulumi";
import * as aws from "@pulumi/aws";

// Access the configuration and read the name of the bucket
const config = new pulumi.Config();
const bucketName = config.require("bucketName");

// Create a new resource
const exampleBucket = new aws.s3.BucketV2("bucket", {
    bucket: bucketName
});

new aws.s3.BucketVersioningV2("bucket-versioning", {
    bucket: exampleBucket.bucket,
    versioningConfiguration: {
                status: "Enabled"
    }
});

// Export the ARN of the newly-created bucket
export const bucketArn = bucket.arn;
```

作为第一步，我们访问配置来检索我们想要创建的 bucket 的名称。这是通过创建一个`pulumi.Config()`的实例来完成的。默认情况下，`config`对象将访问`LogRocket`名称空间中的值，其中`LogRocket`是项目的名称。

要从不同的名称空间获取值，我们只需在`pulumi.Config()`的构造函数中传递名称空间名称。然后，我们使用`config.require()`检索存储桶的名称。如果在我们当前部署的栈的`.yaml` 文件中没有找到名为`bucketName`的键，这将抛出一个异常。

我们现在可以创建一个版本化的存储桶。首先，我们实例化一个类型为`aws.s3.BucketV2`的新资源。构造函数中的第一个参数是部署的本地 ID。因此，如果我们在不同的堆栈中部署相同的资源，我们可以重用相同的 ID。

第二个参数是属性列表。在这种情况下，我们只设置资源的名称，使用所有其他属性的默认值。

然后我们创建另一个类型为`aws.s3.BucketVersioningV2,`的资源，告诉 AWS 创建一个版本化的 bucket。在这种情况下，我们在`exampleBucket`上设置了一个隐式依赖关系。

事实上，我们将`exampleBucket.bucket`设置为`aws.s3.BucketVersioningV2\.` 的 bucket 属性值，这确保了 Pulumi 将在`aws.s3.BucketV2`资源之后创建`aws.s3.BucketVersioningV2`资源。

最后，我们在 AWS 中导出新创建的名为 ARN 的 bucket 的标识符。堆栈输出在更新期间显示，可以从命令行访问。我们通常导出堆栈中重要资源的标识符，以便其他堆栈可以访问它们。

### 部署

我们现在可以通过运行`pulumi up`来请求 Pulumi 部署我们的堆栈。如果我们只想看到我们的程序应用的更改，我们可以使用`pulumi preview`:

```
$ pulumi preview

Previewing update (dev)

View Live: https://app.pulumi.com/…

Type Name Plan

+ pulumi:pulumi:Stack LogRocket-dev create

+ ├─ aws:s3:BucketV2 bucket create

+ └─ aws:s3:BucketVersioningV2 bucket-versioning create

Outputs:

bucketArn: output<string>

Resources:

+ 3 to create
```

`pulumi preview` 命令的输出向我们展示了一些关于我们的部署的有用信息。

首先，它显示当前堆栈的名称`dev`。

其次，它显示了资源列表。它显示了每个资源的 ID，并告诉我们是创建、更新还是删除资源。

最后，它显示了一个输出列表，并最终总结了将要创建、删除或更新的资源数量。

### 组件创建

Pulumi 中的组件是资源的逻辑分组。我们可以使用它们来实例化一组相关的资源，以创建一个更大的抽象。在我们的例子中，我们可能想要为一个`VersionedBucket`创建一个组件资源。

要创建一个组件，我们所要做的就是子类 Pulumi 的`ComponentResource`类，使用构造函数来分配子资源:

```
export interface VersionedBucketArgs {
    bucketName: string
}

export class VersionedBucket extends pulumi.ComponentResource {

    public readonly bucket: aws.s3.BucketV2

    constructor(
                name: string,
                args: VersionedBucketArgs,
                opts?: pulumi.ComponentResourceOptions
    ) {
                super("LogRocket:example:VersionedBucket", name, {}, opts);

                this.bucket = new aws.s3.BucketV2(`${name}-bucket`, {
                bucket: args.bucketName
                }, { parent: this });

                new aws.s3.BucketVersioningV2(`${name}-bucket-versioning`, {
                    bucket: this.bucket.bucket,
                    versioningConfiguration: {
                    status: "Enabled"
                    }
                }, { parent: this });

                this.registerOutputs({
                    bucketArn: this.bucket.arn
                });
    }
}
```

在上面的例子中，我们首先定义了一个接口`VersionedBucketArgs`，来描述我们组件的参数。在这种情况下，我们只对存储桶的名称感兴趣。

然后，`VersionedBucket`扩展`ComponentResource`来创建一个新的组件。

首先，我们调用父构造函数。这将在 Pulumi 引擎中注册组件资源实例，以便我们可以看到不同部署之间的差异。

其次，组件资源还必须注册一个唯一的类型。一般来说应该是`package:module:type`的形式。这种情况下，我们选择了`LogRocket:example:VersionedBucket`。我们将在`pulumi preview`命令输出中看到这种类型。

第三，我们可以像以前一样简单地创建子资源。然而，在本例中，我们显式地设置了`parent`，这样 Pulumi 就知道我们正在创建一个子资源。此外，从父母的名字派生出孩子的名字也是一种好的做法。因此，在这个例子中，我们使用了`name`参数作为子资源名称的前缀。

最后，我们可以注册一些输出来告诉 Pulumi 我们已经完成了子资源的创建。作为一个最佳实践，我们应该总是调用`registerOutput`，即使我们的组件不输出任何东西，让 Pulumi 知道我们的组件可以被认为是完全构造的。还要注意我们的组件如何公开子桶，将其提升为类字段。

我们现在可以重写`index.ts`文件来使用`VersionedBucket`:

```
import * as pulumi from "@pulumi/pulumi";
import { VersionedBucket } from "./components/VersionedBucket"

// Access the configuration and read the name of the bucket
const config = new pulumi.Config();
const bucketName = config.require("bucketName");

const versionedBucket = new VersionedBucket("versioned-bucket", {
    bucketName: bucketName
});

// Export the ARN of the newly-created bucket
export const bucketArn = versionedBucket.bucket.arn;
```

代码现在清晰多了。我们只需将新组件导入到范围中，并使用它来创建一个版本化的 bucket。然后，我们通过访问底层的`BucketV2`对象来注册一个堆栈输出。

`pulumi preview`输出现在会略有不同:

```
$ pulumi preview

Previewing update (dev)

View Live: https://app.pulumi.com/…

Type Name Plan

+ pulumi:pulumi:Stack LogRocket-dev create

+ └─ LogRocket:example:VersionedBucket versioned-bucket create

+ ├─ aws:s3:BucketV2 versioned-bucket-bucket create

+ └─ aws:s3:BucketVersioningV2 versioned-bucket-bucket-versioning create

Outputs:

bucketArn: output<string>

Resources:

+ 4 to create
```

## 结论

在本文中，我们研究了基于 IaaS 和 IaC 的解决方案的优缺点。我们还演示了如何利用 Pulumi 在 AWS 中使用 TypeScript 创建一个简单的 S3 桶。

根据我的经验，作为代码的基础设施值得一试。手动设置复杂的基础设施肯定更容易出错。IaC 让我们能够精确地描述我们的代码，可能根据需要自动部署。

然而，编写基础设施代码与编写应用程序代码也有很大不同。例如，在许多情况下，复制基础设施代码就可以了。同时，复制可应用的代码通常意味着我们的设计中有不好的地方。

此外，测试 IaC 代码要复杂得多，有时甚至完全不可能。因此，程序员应该总是从保持简单开始，避免复杂的架构或基础设施代码的一般化。

在撰写本文时，Pulumi 无疑是为基于 AWS 的基础设施编写代码的最佳工具之一。我们可以用 TypeScript 编写的代码比基于 Terraform 或 CloudFormation 的替代方法可读性更好，在这两种方法中，我们必须分别使用专有语言或编写 JSON/YAML 部署文件。

在 AWS 生态系统中，与 Pulumi 最接近的竞争对手是 [AWS 云开发套件(CDK)](https://aws.amazon.com/cdk/) ，它依赖于引擎盖下的云形成。在 CDK，部署比 Pulumi 慢得多，也更脆弱，Pulumi 依赖于 AWS SDK。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.