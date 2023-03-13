# 使用 GitHub 动作抖动 CI/CD

> 原文：<https://blog.logrocket.com/flutter-ci-cd-using-github-actions/>

我们处于这样一种状态，公司在几分钟内发布软件和解决方案，并且他们通过遵循持续集成(CI)和持续交付(CD)的操作原则来这样做。

CI/CD 管道使得软件的自动交付更加频繁、可靠和安全。它关注更高的代码质量，这就是为什么它对移动开发者或团队至关重要。

在本教程中，您将学习如何使用 GitHub Actions 作为工具，遵循 CI/CD 原则部署您的 Flutter 应用程序。

本教程需要一个 Google 服务帐户，它将在 GitHub Actions 中用于将 Android 版本发布到 Play Store，因此要在 GCP 创建一个项目，[创建一个服务帐户](https://console.cloud.google.com/iam-admin/serviceaccounts/project)并选择您创建的项目。

注意，本教程假设你对颤振有一定的了解。如果你是 Flutter 新手，请浏览官方文档了解一下。

## 什么是 GitHub Actions？

GitHub Actions 是一个 CI/CD 工具，它可以帮助您直接从您的存储库构建、测试和部署生产中的变更。您可以使用它来设置特定事件的应用程序发布，如在存储库的特定分支中提交标签。

此外，人们不必为项目间的共同事务创建工作流，因为 GitHub 有一个市场，您可以从其中使用其他人开发的现有工作流。

GitHub Actions 工作流使用`.yml`(“YAML 不是标记语言”)文件，这些文件将存储在项目根目录下的`.github`目录中。

此外，GitHub Actions 支持不同的环境和容器，如 Linux、macOS、Windows，甚至虚拟机。

## 入门指南

按照以下步骤进行初始设置:

1.  使用您最喜欢的 IDE 或使用 Flutter 命令行工具建立一个新的 Flutter 项目
2.  在您的机器上的新项目中初始化 Git，并创建一个与您的 GitHub 帐户相关联的新存储库
3.  在 flutter 项目的根目录下创建 config 目录`.github`和一个名为`workflows`的新目录。此处的工作流将包含您所有的 CI/CD 工作流作为`.yml`文件

## 使用一个基本的颤动动作来构建一个 Android 版本

现在，您将创建一个基本的 Android 工作流来帮助您理解 GitHub Actions 如何构建您的 Flutter 应用程序。

用下面的代码在`workflows`中创建一个`.yml`文件`android-release.yml`:

```
name: Android Release

# 1
on:
  # 2
  push:
    branches: [ "master" ]
  pull_request:
    branches: [ "master" ]

  # 3
  workflow_dispatch:

# 4
jobs:
  # 5
  build:
    # 6
    runs-on: ubuntu-latest

    # 7
    steps:
      # 8
      - uses: actions/[email protected]
      # 9
      - uses: actions/[email protected]
        with:
          distribution: 'zulu'
          java-version: "12.x"
      # 10   
      - uses: subosito/[email protected]
        with:
          # 11
          flutter-version: "3.0.0"
          channel: 'stable'
      # 12
      - name: Get dependencies
        run: flutter pub get

      # Runs a set of commands using the runners shell
      - name: Start release build
        run: flutter build appbundle

```

上面的工作流程:

1.  控制工作流运行的时间
2.  针对`"master"`分支的推或拉请求事件触发工作流；你可以根据你的要求改变它
3.  允许您从 GitHub repo 的“操作”选项卡中手动运行此工作流(工作流运行由一个或多个可以顺序或并行运行的作业组成)l
4.  包含一个名为`build`的作业
5.  包含将运行作业的运行程序的类型
6.  使用步骤来表示将作为作业的一部分执行的一系列任务
7.  在`$GITHUB_WORKSPACE`下准备好您的存储库，以便您的作业可以访问它
8.  设置 Java，以便您的工作可以使用它来构建 Flutter 应用程序
9.  使用[子位置颤振工作流程](https://github.com/subosito/flutter-action)设置颤振
10.  调整到您正在使用的颤振版本
11.  使用运行程序的 shell 运行单个命令

### 这个基本动作的问题

这个基本工作流的问题是，每当你在`master`分支中推送变更，这个工作流每次都会触发并开始设置 Java SDK 和 Flutter SDK。所以最终，这将导致构建应用程序的延迟，因为您每次都必须设置服务。

### 如何让你的工作流程更快？

您可以通过缓存 Java 和 Flutter SDKs 使您的 Flutter 工作流更快，这样在下一次运行时，它不会在检查 SDK 的存在之前直接获取 SDK。

在您的`main.yml`文件中，进行以下更改:

```
      - uses: actions/[email protected]
        with:
          distribution: 'zulu'
          java-version: "12.x"
          cache: 'gradle' // 1

      - uses: subosito/[email protected]
        with:
          flutter-version: "3.0.0"
          channel: 'stable'
          cache: true // 2
```

您已经通过为 Java SDK ( `1`)提供要缓存的 Gradle 并为 Flutter SDK ( `2`)启用缓存更新了 SDK 设置。

下次运行工作帖子时，保存上述更改并观察花费的时间；你会看到一个基本流程的时差。

## 为 Play Store 发布做准备

现在，您将扩展您的工作流程，创建一个 Android Play 商店版本。

### 生成版本号

对于任何新的发布，您都应该有一个新的发布版本号，因此在构建之前，您需要使用下面的作业创建一个版本号:

```
# 1
version:
    name: Create version number
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      - uses: actions/[email protected]
      # 2
      - name: Install GitVersion
        uses: gittools/actions/gitversion/[email protected]
        with:
          versionSpec: "5.x"
      - name: Use GitVersion
        id: gitversion
        uses: gittools/actions/gitversion/[email protected]
      # 3
      - name: Create version.txt with nuGetVersion
        run: echo ${{ steps.gitversion.outputs.nuGetVersion  }} > version.txt
      # 4
      - name: Upload version.txt
        uses: actions/[email protected]
        with:
          name: gitversion
          path: version.txt
```

在上面的代码中，我们执行了以下操作:

1.  创建了将在构建作业之前执行的新作业版本
2.  安装了 [GitVersion](https://github.com/GitTools/GitVersion) ，一个通过查看你的 Git 历史来进行版本控制的工具
3.  使用 GitVersion 发布，将版本放在一个`version.text`文件中
4.  上传了名为`gitversion`的`version.text`文件，作为 actions 系统的工件，稍后将在构建作业中使用

### 签署应用程序

要将应用程序发布到 Play Store，您需要使用密钥库为您的应用程序提供数字签名。根据你的机器，按照官方的颤振文档来做:

```
keytool -genkey -v -keystore %userprofile%\upload-keystore.jks -storetype JKS -keyalg RSA -keysize 2048 -validity 10000 -alias upload
```

这将在您的主目录或您提供的任何路径中存储一个扩展名为`.jks`的文件。

*注意，确保将商店密码、密钥密码和密钥别名添加到您的 GitHub 存储库秘密中(来自 GitHub 存储库>秘密>动作)*

![Repository Secrets](img/1f757b3ae5e605f8cadd8d5ca73cb226.png)

#### 运行 keytool 时遇到问题？

如果您面临“‘keytool’不被识别为内部或外部命令”的问题，那么添加 JDK bin 的路径以使用环境变量，否则[安装 JDK](https://www.oracle.com/java/technologies/javase-jdk15-downloads.html) 并重复添加到环境变量的路径。

接下来，在应用程序的 Android 目录下创建一个新文件`key.properties`,并提供对之前生成的密钥库的引用:

```
storePassword=<password from previous step>
keyPassword=<password from previous step>
keyAlias=upload
storeFile=<location of the key store file, such as /Users/<user name>/your-keystore-file.jks>
```

要在发布模式下构建您的应用程序时使用此密钥，请按如下方式更新您的 Android 级`build.gradle`文件:

1.  定义`keyProperties` 变量，从`filesystem` :

    ```
       def keystoreProperties = new Properties()    def keystorePropertiesFile = rootProject.file('key.properties')    if (keystorePropertiesFile.exists()) {        keystoreProperties.load(new FileInputStream(keystorePropertiesFile))    }
    ```

    中引用`key.properties`文件
2.  更新`buildTypes`并增加`signingConfigs`如下:

    ```
       signingConfigs {        release {            keyAlias keystoreProperties['keyAlias']            keyPassword keystoreProperties['keyPassword']            storeFile keystoreProperties['storeFile'] ? file(keystoreProperties['storeFile']) : null            storePassword keystoreProperties['storePassword']        }    }    buildTypes {        release {            signingConfig signingConfigs.release        }    }
    ```

此后，您的新构建将使用键在发布模式下创建。

*注意，不要提交`keystore`键和`key.properties`文件，让它们成为私有的。*

所以你可能想知道，我们的作业如何知道这个键是否存在于文件系统中，并在`key.properties`文件中使用它作为引用？

要解决此问题，请执行以下操作:

1.  `Base64`使用 Git Bash 或 Bash 在您的机器中编码您的密钥库文件:

    ```
    base64 <your-keystore-file.jks>
    ```

2.  在你的 GitHub 库中创建一个新的秘密`ANDROID_KEYSTORE_BASE64`
3.  复制输出并将其作为`ANDROID_KEYSTORE_BASE64`粘贴到您的 GitHub 存储库中；那里会很安全

现在更新您的`android-release.yml`文件中的构建作业:

```
build:
    name: Create Android Build
    # 1
    needs: version
    runs-on: ubuntu-latest
    steps:
      - uses: actions/[email protected]
      # 2   
      - name: Get version.txt
        uses: actions/[email protected]
        with:
          name: gitversion
      # 3
      - name: Create new file without newline char from version.txt
        run: tr -d '\n' < version.txt > version1.txt
      # 4
      - name: Read version
        id: version
        uses: juliangruber/[email protected]
        with:
          path: version1.txt
      # 5
      - name: Update version in YAML
        run: sed -i 's/99.99.99+99/${{ steps.version.outputs.content }}+${{ github.run_number }}/g' pubspec.yaml
      # 6
      - name: Download Android keystore
        id: android_keystore
        uses: timheuer/[email protected]
        with:
          fileName: upload-keystore.jks
          encodedString: ${{ secrets.KEYSTORE_BASE64 }}
      # 7
      - name: Create key.properties
        run: |
          echo "storeFile=${{ steps.android_keystore.outputs.filePath }}" > android/key.properties
          echo "storePassword=${{ secrets.STORE_PASSWORD }}" >> android/key.properties
          echo "keyPassword=${{ secrets.KEY_PASSWORD }}" >> android/key.properties
          echo "keyAlias=${{ secrets.KEY_ALIAS }}" >> android/key.properties
      - uses: actions/[email protected]
        with:
          distribution: 'zulu'
          java-version: "12.x"
          cache: gradle
      - uses: subosito/[email protected]
        with:
          flutter-version: "3.0.0"
          channel: 'stable'
          cache: true

      - name: Get dependencies
        run: flutter pub get

      - name: Start Android Release Build
        run: flutter build appbundle
      # 8
      - name: Upload Android Release
        uses: actions/[email protected]
        with:
          name: android-release
          path: build/app/outputs/bundle/release/app-release.aab
```

在上面的代码中，您执行了以下操作:

1.  添加了对版本作业的依赖性，以便按顺序运行此作业
2.  使用名称`gitversion`下载第一个作业中上传的版本文件
3.  从`version.txt`创建了一个没有`newline char`的新文件
4.  从`version1.txt`文件中读取更新版本
5.  用包含版本的版本 ID 更新了`pubspec.yml`文件
6.  解码作为 ID `android_keystore`的秘密保存的`base64`编码密钥库值
7.  使用秘密和`android_keystore`创建了`key.properties`
8.  上传了 Android 发布包，作为将在下一个任务中使用的工件

### 部署应用程序

现在，您需要使用捆绑包并将其发送到 Play Store。在此之前，是时候使用您在本教程开始时创建的服务帐户了。如果创建了服务帐户，复制该帐户的密钥，并将其存储在 secrets 中作为`PLAYSTORE_ACCOUNT_KEY`。

接下来，在你的 [Google Play 控制台](https://play.google.com/console) > **用户&权限**，邀请用户并在此添加服务账号用户邮箱。

如果您在**应用权限**中没有看到您的应用，请确保您的项目启用了 GCP 的 Google Play 开发者 API。

接下来，更新用户的权限，使其拥有像管理员角色一样发布应用程序的权限。

现在，在您的`android-release`流程中添加一个新的工作部署:

```
deploy:
    name: Deploy Android Build
    # 1
    needs: build
    runs-on: ubuntu-latest

    steps:
    - uses: actions/[email protected]
      # 2
    - name: Get Android Build from artifacts
      uses: actions/[email protected]
      with:
        name: android-release
      # 3
    - name: Release Build to internal track
      uses: r0adkll/[email protected]
      with:
        serviceAccountJsonPlainText: ${{ secrets.PLAYSTORE_ACCOUNT_KEY }}
        packageName: <YOUR_PACKAGE_NAME>
        releaseFiles: app-release.aab
        track: alpha
        status: completed
```

在这里，您执行了以下操作:

1.  添加了按顺序运行此作业的依赖项
2.  使用名称`android-release`从 artificats 下载 Android 版本
3.  使用了 [【受电子邮件保护】](https://github.com/r0adkll/upload-google-play) 工作流和`PLAYSTORE_ACCOUNT_KEY`秘密、您的应用程序包名称、您要上传版本的轨道及其状态

之后，将您的更改推送到 GitHub，并查看工作流将您的应用程序部署到 Play Store。

以下是完整的工作流程:

```
name: Android Release

on:
  push:
    branches: [ "master" ]
  pull_request:
    branches: [ "master" ]

  workflow_dispatch:

jobs:
  version:
    name: Create version number
    runs-on: ubuntu-latest
    steps:
      - uses: actions/[email protected]
      - name: Install GitVersion
        uses: gittools/actions/gitversion/[email protected]
        with:
          versionSpec: "5.x"
      - name: Use GitVersion
        id: gitversion
        uses: gittools/actions/gitversion/[email protected]
      - name: Create version.txt with nuGetVersion
        run: echo ${{ steps.gitversion.outputs.nuGetVersion  }} > version.txt
      - name: Upload version.txt
        uses: actions/[email protected]
        with:
          name: gitversion
          path: version.txt

  build:
    name: Create Android Build
    needs: version
    runs-on: ubuntu-latest
    steps:
      - uses: actions/[email protected]
      - name: Get version.txt
        uses: actions/[email protected]
        with:
          name: gitversion
      - name: Create new file without newline char from version.txt
        run: tr -d '\n' < version.txt > version1.txt
      - name: Read version
        id: version
        uses: juliangruber/[email protected]
        with:
          path: version1.txt
      - name: Update version in YAML
        run: sed -i 's/99.99.99+99/${{ steps.version.outputs.content }}+${{ github.run_number }}/g' pubspec.yaml
      - name: Download Android keystore
        id: android_keystore
        uses: timheuer/[email protected]
        with:
          fileName: upload-keystore.jks
          encodedString: ${{ secrets.KEYSTORE_BASE64 }}
      - name: Create key.properties
        run: |
          echo "storeFile=${{ steps.android_keystore.outputs.filePath }}" > android/key.properties
          echo "storePassword=${{ secrets.STORE_PASSWORD }}" >> android/key.properties
          echo "keyPassword=${{ secrets.KEY_PASSWORD }}" >> android/key.properties
          echo "keyAlias=${{ secrets.KEY_ALIAS }}" >> android/key.properties
      - uses: actions/[email protected]
        with:
          distribution: 'zulu'
          java-version: "12.x"
          cache: gradle
      - uses: subosito/[email protected]
        with:
          flutter-version: "3.0.0"
          channel: 'stable'
          cache: true

      - name: Get dependencies
        run: flutter pub get

      - name: Start Android Release Build
        run: flutter build appbundle

      - name: Upload Android Release
        uses: actions/[email protected]
        with:
          name: android-release
          path: build/app/outputs/bundle/release/app-release.aab

  deploy:
    name: Deploy Android Build
    needs: build
    runs-on: ubuntu-latest
    steps:
    - uses: actions/[email protected]
    - name: Get Android Build from artifacts
      uses: actions/[email protected]
      with:
        name: android-release
    - name: Release Build to internal track
      uses: r0adkll/[email protected]
      with:
        serviceAccountJsonPlainText: ${{ secrets.PLAYSTORE_ACCOUNT_KEY }}
        packageName: <YOUR_PACKAGE_NAME>
        releaseFiles: app-release.aab
        track: alpha
        status: completed

```

*注:*

*   *您可以将所有这些作业合并到一个单独的作业中，这样在作业之间共享文件就不需要发布工件，这会消耗一个免费使用限制*
*   *有一个已知的问题，有时应用程序在首次运行时不会发布。因此，上传一个从这个管道构建的 APKor appbundle，并向内部用户推出。之后，该工作流将能够毫无问题地发布应用程序*
*   *如果您在部署期间仍然遇到问题，请确保所有配置和权限都是正确的，或者检查此[问题页面](https://github.com/r0adkll/upload-google-play/issues/55)*

## 将 web 发布到 GitHub 页面

现在创建一个新的`web-release.yml`工作流并粘贴以下代码:

```
name: Web Release

on:
  push:
    branches:  [ "master" ]

  pull_request:
    branches: [ "master" ]

  workflow_dispatch:

jobs:
  build:
    name: Create Web Build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/[email protected]
      - uses: actions/[email protected]
        with:
          distribution: 'zulu'
          java-version: "12.x"
          cache: gradle
      - uses: subosito/[email protected]
        with:
          flutter-version: "3.0.0"
          channel: 'stable'
          cache: true

      - name: Get dependencies
        run: flutter pub get

      - name: Start Web Release Build
        run: flutter build web --release

      - name: Upload Web Build Files
        uses: actions/[email protected]
        with:
          name: web-release
          path: ./build/web

  deploy:
    name: Deploy Web Build
    needs: build
    runs-on: ubuntu-latest

    steps:
    - name: Download Web Release
      uses: actions/[email protected]
      with:
        name: web-release

    - name: Deploy to gh-pages
      uses: peaceiris/[email protected]
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./
```

上面的工作流与 Android 工作流非常相似，但这里您使用的是 Flutter web `build`命令，然后使用 [peaceiris/ 【电子邮件保护】](https://github.com/peaceiris/actions-gh-pages) 工作流将 web 构建部署到 GitHub 页面。

*注:*

*   *`GITHUB_TOKEN`不是个人访问令牌。它会自动创建并在您的工作流程中进行认证*
*   *确保**设置**中* GitHub 页面*部分的*分支*设置为`gh-pages`*

## 结论

在本教程中，您了解了如何设置 GitHub Actions 工作流来跨 Web 和 Android 部署您的 Flutter 应用程序。下一步，您可以复制并修改工作流，以直接将应用程序发布到应用程序商店，或者了解 GitHub 操作的其他替代方案，如 [CircleCI](https://blog.logrocket.com/ci-cd-and-react-create-a-pipeline-using-heroku-and-circleci/) 、GitLab CI、Jenkins 等。

我们希望你喜欢这个教程。如果您有任何疑问，请随时联系我们。谢谢大家！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)