# 使用 Terraform 和 Docker - LogRocket 博客进行真实世界的 Azure 资源管理

> 原文：<https://blog.logrocket.com/real-world-azure-resource-management-with-terraform-and-docker/>

在我开始之前，我想感谢[伊恩·亨特](https://twitter.com/hunt3ri)给了我一些关于现实世界地形的宝贵建议。

如果您正在使用一个主要的云提供商来托管您的应用程序，并且您正在登录一个 web 门户并通过点击按钮来创建关键的基础架构，那么您正在犯一个代价非常高的错误。每一个基础设施项目都应该从一个可执行代码文件中创建，该文件经过一个拉请求过程，并被提交到一个版本化的源代码控制系统(如 git)中。Terraform 采用代码优先的方法来创建基础设施资源。

我读过的大多数关于 Terraform 的帖子都没有涉及我将如何在现实世界中使用它。很多帖子遗漏了一些必要的步骤，比如远程存储 Terraform 状态，也没有提到 Terraform 模块。我将欢迎任何进一步的建议，我错过了在文章末尾的评论部分。

## 为什么是 Terraform？

为什么你会使用地形而不是厨师，木偶，Ansible，盐堆，或云形成，等等。？Terraform 非常适合管理云资源。同时，像 Ansible 这样的工具更多的是用于供应软件和机器。我更喜欢 Terraform 的原因是，你是在用代码定义你的基础设施，而不是无尽的配置文件。你可以创建可重用的参数化模块，就像我在其他语言中习惯的那样。

## 不要在本地文件系统上存储地形状态

Terraform 必须存储有关托管基础设施和配置的状态。Terraform 使用这种状态将现实世界的资源映射到您的配置，跟踪元数据，并提高大型基础设施的性能。地形状态包括配置中所有资源的设置。默认情况下，地形状态存储在本地文件系统的一个名为`terraform.tfstate`的文件中。我读过的几乎每篇博文都没有提到保持 Terraform 状态的正确方法。地形状态应远程存储。

## 在 Azure Blob 存储中存储 Terraform 状态

你可以将状态存储在付费服务的 Terraform cloud 中，或者像 T2 的 AWS S3 T3 中。

在这个例子中，我将把状态持久化到 [Azure Blob 存储](https://azure.microsoft.com/en-gb/services/storage/blobs/?&ef_id=EAIaIQobChMIm7_94onT5wIVx7TtCh0n2A0nEAAYASAAEgLsCfD_BwE:G:s&OCID=AID2000125_SEM_BDl6KOkr&MarinID=BDl6KOkr_324524344032_azure%20blob%20storage_e_c__69094233971_kwd-296465854739&lnkd=Google_Azure_Brand&dclid=CKXC0uSJ0-cCFZfjGwod8zsOvw)。

我们的第一步是创建 Azure 资源来促进这一点。我需要在 Azure 中创建以下资源:

*   Azure 资源组——保存 Azure 解决方案相关资源的容器
*   Azure 存储帐户–包含您的所有 Azure 存储数据资源
*   Azure Blob 存储容器——组织一组 Blob，类似于文件系统中的目录
*   azure key vault store——我们将在其中存储所有我们不想硬编码到脚本中并签入源代码控制的秘密
*   [Azure 服务主体](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2Fazure%2Fazure-resource-manager%2Ftoc.json&view=azure-cli-latest)–为应用程序、托管服务和自动化工具访问 Azure 资源而创建的身份

我们将使用 [Azure CLI 工具](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)创建这些初始资源。我知道，我知道我们应该使用地形。稍后将详细介绍。

## Terraform 工作空间

在真实的场景中，工件是在特定的环境中创建的，比如开发、登台、生产等。Terraform 有工作空间的概念来帮助解决这个问题。默认情况下，Terraform 从默认工作区开始，但我们将在 dev 工作区下创建所有基础设施项目。

Terraform 将每个工作区的状态存储在远程存储的单独状态文件中:

```
env:/
    dev/
       state.tfs
```

## 创建存储帐户

以下脚本将创建一个资源组、一个存储帐户和一个存储容器。

```
#!/bin/bash
RESOURCE_GROUP_NAME=tstate
# $1 is the environment or terraform workspace, dev in this example
STORAGE_ACCOUNT_NAME="tstate$RANDOM$1"
CONTAINER_NAME="tstate$1"

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

这将向 STDOUT 回显类似这样的内容

```
storage_account_name: tstate666
container_name: tstate
access_key: wp9AZRTfXPgZ6aKkP94/hTqj/rh9Tsdj8gjlng9mtRSoKm/cpPDR8vNzZExoE/xCSko3yzhcwq+8hj1hsPhlRg==
```

生成一个允许访问存储器的`access_key`。如前所述，我们不希望在源代码控制中存储敏感机密，相反，我们将把它们存储在一个 [Azure key vault](https://azure.microsoft.com/en-gb/services/key-vault/) 中，它可以像`access_key`一样安全地存储和检索应用程序机密。

## 创建密钥保管库存储

微软的官方建议是为每个环境创建一个密钥保管库存储。
下面的脚本创建密钥保管库存储:

```
if [[ $# -eq 0 ]] ; then
    echo 'you must pass in an environment of dev,staging or production'
    exit 0
fi

vault_name="my-key-vault-$1"

az keyvault create --name $vault_name --resource-group "mystate" --location germanywestcentral
```

我们现在将在密钥保管库存储中存储`access_key`、存储帐户名称和存储容器名称:

```
az keyvault secret set --vault-name "my-key-vault-dev" --name "terraform-backend-key" --value "wp9AZRTfXPgZ6aKkP94/hTqj/rh9Tsdj8gjlng9mtRSoKm/cpPDR8vNzZExoE/xCSko3yzhcwq+8hj1hsPhlRg=="
az keyvault secret set --vault-name "my-key-vault-dev" --name "state-storage-account-name" --value "tstate6298"
az keyvault secret set --vault-name "my-key-vault-dev" --name "state-storage-container-name" --value "tstate"
```

我还将 Azure 订阅 ID 存储在密钥库存储中，以便于访问:

```
az keyvault secret set --vault-name "my-key-vault-dev" --name "my-subscription-id" --value "79c15383-4cfc-49my-a234-d1394814ce95"
```

## 创建服务主体

下一步是创建服务主体帐户，我们将在访问应用程序的基础设施时授予该帐户权限。

```
SUBSCRIPTIONID=$(az keyvault secret show --name my-subscription-id --vault-name my-key-vault --query value -o tsv)
az ad sp create-for-rbac --role contributor --scopes "/subscriptions/$SUBSCRIPTIONID" --name http://myterraform --sdk-auth
```

上面的脚本将输出如下内容:

```
{
  "clientId": "fd0e2604-c5a2-46e2-93d1-c0d77a8eca65",
  "clientSecret": "d997c921-5cde-40c8-99db-c71d4a380176",
  "subscriptionId": "79c15383-4cfc-49my-a234-d1394814ce95",
  "tenantId": "a567135e-3479-41fd-8acf-a606c8383061",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

这是你唯一一次可以看到`clientSecret`的机会，所以我们需要快速地将它放入 az 密钥库商店——聪明！再次访问`clientSecret`的唯一方法是重新生成它:

```
az keyvault secret set --vault-name "my-key-vault-dev" --name "sp-client-id" --value "e900db02-ab6a-4098-a274-5b91d5f510bb"
az keyvault secret set --vault-name "my-key-vault-dev" --name "sp-client-secret" --value "156c4cdf-23e7-44c0-ad2b-64a6f169b253"<
```

> 注意:更安全的方法是使用客户端证书。

## 通过 Docker 运行 Terraform

我们将通过 [Docker](https://www.docker.com/) 运行 Terraform。你应该问的第一个问题是为什么？

以下是您应该通过 Docker 运行 Terraform 的几个原因:

*   Terraform 脚本应被视为应用程序代码，并应具有类似可预测操作系统的东西
*   将所有需求封装在一个映像中
*   一次构建，随处运行
*   如果我们使用容器映像存储库，那么我们可以对映像进行版本控制
*   能够通过用运行时上下文相关的环境变量等参数化值来部署到不同的环境
*   当多个开发人员在同一个项目上工作时，一致的部署体验

## 地形码头文件

以下`Dockerfile`将安装 Terraform 和 Azure CLI 工具:

```
FROM ubuntu:19.04

ENV TERRAFORM_VERSION 0.12.19
ENV TERRAFORM_URL https://releases.hashicorp.com/terraform/$TERRAFORM_VERSION/terraform_${TERRAFORM_VERSION}_linux_amd64.zip
ENV AZURE_CLI_VERSION 2.0.77

RUN apt-get update && apt-get install -y \
    curl \
    python3-pip \
    zip

RUN echo 'alias python=python3' >> ~/.bashrc
RUN echo 'alias pip=pip3' >> ~/.bashrc
RUN pip3 install --upgrade pip

RUN curl -o /root/terraform.zip $TERRAFORM_URL && \
   unzip /root/terraform.zip -d /usr/local/bin/ && \
   rm /root/terraform.zip

RUN pip3 install azure-cli==${AZURE_CLI_VERSION}

WORKDIR /workspace

RUN chmod -R  +x .

ENTRYPOINT [ "./ops/help.sh", "-h" ]
CMD ["bash"]
```

上面的`Dockerfile`将安装特定版本的 Terraform 和 azure-cli。我也喜欢为我的 Docker 图像提供一个帮助菜单的入口点，解释 Docker 图像的功能。

`./ops/help.sh`文件如下所示:

```
#!/bin/bash

if [ "$1" == "-h" ] ; then
    cat << EndOfMessage
Usage:
./run.sh [environment] [init|destroy]
e.g.
./run.sh dev init
./run.sh dev destroy
EndOfMessage
    exit 0
fi
```

## 构建平台码头形象

下面的脚本将构建图像并为工作区适当地标记它:

```
#!/bin/bash

if [[ $# -eq 0 ]] ; then
    echo 'you must pass in an environment of dev,staging or production'
    exit 0
fi

version=$(cat ./terraform/version)
tag="my-azure:${version}-$1"

echo "Building images with default parameters"
docker image build \
  --rm \
  -f ./Dockerfile \
  -t $tag \
  --no-cache \
  .
```

运行`./build.sh`时，适当的工作空间参数作为参数传入:

```
./build.sh dev
```

## 运行 Terraform Docker 映像

在运行 Terraform 时使用 Docker 的部分原因是为了允许使用不同的环境变量从同一个 Docker 文件创建不同的环境或工作区。

下面的`run.sh`脚本将为此工作区选择正确的密钥保管库存储。这个脚本有两个参数，第一个是工作空间，第二个是命令`init`或`destroy`。

```
#!/bin/bash

if [[ $# -eq 0 ]] ; then
    echo 'you must pass in an environment of dev,staging or production and a command of init, destroy or -h'
    exit 0
fi

vault_name="c2-key-vault-$1"

version=$(cat ./terraform/version)
tag="${version}-$1"

working_directory="${PWD}/terraform"

vault_name="c2-key-vault-$1"
container_name="tf-azure-cli-$1"

case "$2" in
    ("init") command="./ops/init.sh" ;;
    ("destroy") command="./ops/teardown.sh" ;;
    (*) docker run \
          --rm \
          -v $working_directory:/workspace:z \
          --name $container_name \
          -it c2-azure:${tag}
        exit 0;;
esac

echo "about to run $command"

echo "setting environment variables for the $1 environment"

export subscription_id=$(az keyvault secret show --name c2-subscription-id --vault-name $vault_name --query value -o tsv)
export state_storage_account_name=$(az keyvault secret show --name state-storage-account-name --vault-name $vault_name --query value -o tsv)
export state_storage_container_name=$(az keyvault secret show --name state-storage-container-name --vault-name $vault_name --query value -o tsv)
export access_key=$(az keyvault secret show --name terraform-backend-key --vault-name $vault_name --query value -o tsv)
export client_id=$(az keyvault secret show --name sp-client-id --vault-name $vault_name --query value -o tsv)
export client_secret=$(az keyvault secret show --name sp-client-secret --vault-name $vault_name --query value -o tsv)
export tenant_id=$(az account show --query tenantId -o tsv)

docker run \
  --rm \
  -v $working_directory:/workspace:z \
  -e resource_group="c2state" \
  -e subscription_id="${subscription_id}"  \
  -e state_storage_account_name="${state_storage_account_name}" \
  -e state_storage_container_name="${state_storage_container_name}" \
  -e access_key="${access_key}" \
  -e client_id="${client_id}" \
  -e client_secret="${client_secret}" \
  -e tenant_id=${tenant_id} \
  -e workspace=$1 \
  --name $container_name \
  --entrypoint $command \
  -it c2-azure:${tag}
```

环境变量由 Azure key vault 存储中的值分配，随后在调用`docker run`时通过`-e`开关在 Docker 容器中可用。

主机卷还映射到我们的本地 Terraform 文件和脚本，因此容器可以立即获取更改，从而无需在每次更改后重建映像。

每个工作区执行一次`run.sh`脚本，`init`或`destroy`的第二个参数将最终委托给`terraform init`或`terraform destroy`。

```
# run.sh takes a workspace argument and a command
./run.sh dev init
```

结果是对`docker run`的调用。[–入口点开关](https://medium.com/@oprearocks/how-to-properly-override-the-entrypoint-using-docker-run-2e081e5feb9d)用于委托给`init.sh`脚本或`teardown.sh`脚本。下面是将创建 Azure 基础设施的`init.sh`脚本:

```
!/bin/bash

az login --service-principal -u $client_id -p $client_secret --tenant $tenant_id

export TF_VAR_client_id=$client_id
export TF_VAR_client_secret=$client_secret
export ARM_CLIENT_ID=$client_id
export ARM_CLIENT_SECRET=$client_secret
export ARM_ACCESS_KEY=$access_key
export ARM_SUBSCRIPTION_ID=$subscription_id
export ARM_TENANT_ID=$tenant_id
export TF_VAR_subscription_id=$subscription_id

terraform init \
    -backend-config="storage_account_name=${state_storage_account_name}" \
    -backend-config="container_name=${state_storage_container_name}" \
    -backend-config="access_key=${access_key}" \
    -backend-config="key=my.tfstate.$workspace"

terraform workspace select $workspace || terraform workspace new $workspace

terraform apply --auto-approve
```

在此脚本中，分配了 Terraform 脚本所需的环境变量。

使用`-backend-config`开关调用`terraform init`，指示 Terraform 将状态存储在本文开始时创建的 Azure Blob 存储容器中。

当前 Terraform 工作空间是在应用配置之前设置的。

[terra form apply–auto-approve](https://www.terraform.io/docs/commands/apply.html)执行创建资源的实际工作。

然后，Terraform 将执行`main.tf`文件并正常运行。

## 破坏

可以使用`destroy command`调用`run.sh`脚本:

```
./run.sh dev destroy
```

容器将执行这个`teardown.sh`脚本:

```
#!/bin/bash

echo "tearing the whole $workspace down"

az login --service-principal -u $client_id -p $client_secret --tenant $tenant_id

export TF_VAR_client_id=$client_id
export TF_VAR_client_secret=$client_secret
export ARM_CLIENT_ID=$client_id
export ARM_CLIENT_SECRET=$client_secret
export ARM_ACCESS_KEY=$access_key
export ARM_SUBSCRIPTION_ID=$subscription_id
export ARM_TENANT_ID=$tenant_id
export TF_VAR_subscription_id=$subscription_id  

terraform workspace select $workspace

terraform destroy --auto-approve
```

上升的，可以下降。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

## 地形模块

在我读过的大部分帖子中，我没有看到足够多的提到 [Terraform 模块](https://www.terraform.io/docs/modules/index.html)。

Terraform 模块既可以接受输入变量形式的参数，也可以返回被称为输出变量的其他 Terraform 模块使用的值。

下面的 Terraform 模块接受用于创建 Azure 资源组的两个输入变量`resource_group_name`和`resource_group_location`:

```
variable "resource_group_name" {
  type                      = string
}

variable "resource_group_location" {
  type                      = string
}

resource "azurerm_resource_group" "main" {
  name      = var.resource_group_name
  location  = var.resource_group_location
}

output "eu_resource_group_name" {
 value      = azurerm_resource_group.main.name
}

output "eu_resource_group_location" {
 value      = azurerm_resource_group.main.location
}
```

该模块还返回两个输出变量`eu_resource_group_name`和`eu_resource_group_location`，它们可以在其他 Terraform 脚本中使用。

上面的模块是这样调用的:

```
module "eu_resource_group" {
  source                        = "./modules/resource_groups"

  resource_group_name           = "${var.resource_group_name}-${terraform.workspace}"
  resource_group_location       = var.location
}
```

两个输入变量在`module`块中分配。字符串插值用于将当前 Terraform 工作空间名称添加到资源组名称中。所有 Azure 资源都将在该资源组下创建。

两个输出变量`eu_resource_group_name`和`eu_resource_group_location`可用于其他模块:

```
module "vault" {
  source                        = "./modules/vault"

  resource_group_name           = module.eu_resource_group.eu_resource_group_name
  resource_group_location       = module.eu_resource_group.eu_resource_group_location
}
```

## 收场白

当我读到很多 Terraform 的帖子时，我变得很沮丧，这些帖子太基础了，无法在真实的生产环境中使用。

即使是 Terraform 文档也没有详细说明除了脚本文件本身以外的其他方式存储密钥和秘密，这是一个很大的安全错误。如果您在真实场景中使用 Terraform，请不要使用本地 Terraform 状态。

具有输入和输出变量的 Terraform 模块比一个大型脚本好得多。

在 Docker 容器中执行 Terraform 是正确的做法，原因与我们将其他应用程序代码放入容器中完全相同。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)