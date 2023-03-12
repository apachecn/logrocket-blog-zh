# 使用 GitHub 操作对本机 CI/CD 做出反应- LogRocket 博客

> 原文：<https://blog.logrocket.com/react-native-ci-cd-using-github-actions/>

时间是我们人类最宝贵的财富。作为开发人员，我们花了太多的时间做两件事:盯着我们的代码想知道为什么它不工作，以及等待我们的应用程序构建。没有人喜欢等到构建完成，然后在回家之前发布它。为什么不自动化呢？CI/CD？是啊！React 本地应用也是如此。

今天，我们有很棒的工具可用于 React 原生应用的 CI/CD。GitHub Actions、GitLab CI/CD、CircleCI、Travis CI 和许多其他工具正在帮助开发人员节省大量时间和资源。今天，我们将对 React 本机应用程序的 CI/CD 使用 Github 操作。

这篇文章是我们之前讨论用 React 本地测试库测试 React 本地应用的文章的延续。虽然这不是必要的阅读，但是我们讨论了一些提交前的任务来保持代码的质量。基于同一个应用程序，我们将在这里完成 CI/CD 管道。

## 关于带有 GitHub 动作的 CI/CD

GitHub Actions 允许我们定义基于相关条件运行的工作流。每个存储库可以包含基于不同事件触发不同作业的多个工作流。每次触发 GitHub 都会选择`.github/workflows/`中的所有 YAML 文件，并执行所需的工作流。如果工作流突然结束，则该操作会被标记为失败。

为了完成一些基本任务，比如检查我们的项目或缓存文件，GitHub 有一系列官方维护的动作。我们也可以利用[社区维护的几个开源动作](https://github.com/marketplace?type=actions&verification=verified)。

## 我们的目标

因此，我们的要求如下:

*   对于开发分支上的每个拉请求，执行所有测试**【CI】**
*   在开发分支的每次推送中，通过 Firebase 应用分发**【CD】**分发我们的 Android 应用
*   在 alpha 分支的每次推送中，通过 Google Play 控制台**【CD】**发布我们应用程序的 alpha 版本
*   在主分支的每次推送中，通过 Google Play 控制台**【CD】**发布我们应用程序的测试版

如上所述，我们的第一个需求属于 CI(持续集成)阶段。这允许我们建立一种自动化的方法来保持开发快速进行，而不破坏我们的应用程序。CD(连续交付)从 CI 结束的地方开始。CD 自动向选定的环境交付我们的应用程序。

## 为 React 本地应用添加 CI 工作流

概括地说，我们有两种不同的工作流:CI 和 CD。任何工作流都可以在不同的触发器上执行；在我们的例子中，CI 工作流将在受保护分支上的每个拉请求上执行，而 CD 工作流在受保护分支上的每个推请求上执行。

为了为我们的 repo 设置 CI 工作流，我们将在`.github/workflows/`中添加一个新文件`ci.yml`。

### 定义我们的 CI 工作流程

我们的 CI 工作流触发器是受保护分支上的拉请求。下面是[我们的工作流配置](https://github.com/zsajjad/testing-app/blob/main/.github/workflows/ci.yml#L1)应该是什么样子:

```
name: Continuous Integration

on:
  pull_request:
    branches:
      - develop
      - alpha 
      - main
```

正如所见，这将在定义的分支上的每个拉请求上执行。

### 项目检验

我们通过 GitHub Actions 定义的每个工作流都在一个单独的虚拟机中执行。为了在我们的代码库上执行任何操作，我们需要在分配的实例上签出 repo。如前所述，GitHub 有各种各样的实用操作来处理这些任务。这里[我们将使用结帐动作](https://github.com/zsajjad/testing-app/blob/main/.github/workflows/ci.yml#L15):

```
    - name: Checkout
      uses: actions/[email protected]
```

### 安装依赖项

一旦我们的结帐完成，我们将设置我们的环境和项目。要在 GitHub VM 中运行任何 Node.js 项目，我们可以使用[官方节点设置动作](https://github.com/actions/setup-node)。

因为我们使用 React 本地测试库执行测试，所以我们必须安装一些 npm 依赖项。在这一步，我们将[在 runner 上设置一个节点环境](https://github.com/zsajjad/testing-app/blob/main/.github/workflows/ci.yml#L17)并安装我们的 npm 依赖项。

```
    - uses: actions/[email protected]
    - uses: c-hive/[email protected]

    - name: Install node modules
      run: |
        yarn install
```

为了进行后续操作，我们可以缓存项目的 npm 依赖项。我们利用了 C-Hive 的单行纱线模块缓存动作。

### 运行我们的测试

既然节点环境和依赖项已经可用，让我们开始执行我们的测试套件。这就像[使用本地 CLI 运行我们的测试](https://github.com/zsajjad/testing-app/blob/main/.github/workflows/ci.yml#L24)一样简单。

```
    - name: Run test
      run: |
        yarn test-ci
```

如前所述，这个作业将在每个拉请求时执行；GitHub 将显示每个 PR 的管道执行结果。下面是它的截图:

![Successful Execution of the Pipeline in GitHub Actions](img/d823419a648be04a149b23cdc8927ebf.png)

这就结束了我们的 CI 工作流程。这个工作流将使我们的应用程序在代码质量方面更具可伸缩性，因为合并到我们 repo 中的任何代码都将通过我们的质量检查。

## 添加我们的构建作业

因为我们有一个完整的决定性 CI 阶段，所以合并到我们受保护的分支中的任何代码都通过了所有测试。因此，一旦合并了任何 PR，就可以安全地构建我们的应用程序了。在我们的 CD 管道中，我们将开始构建我们的应用程序，并根据我们的要求发布它们。

对于 Android 应用，我们构建工作的步骤如下:

### 设置梯度缓存

就像我们没有 npm 依赖一样，我们将缓存我们的梯度依赖和包装，以保持我们的构建更快。[我们将在这里使用 GitHub 的缓存操作](https://github.com/zsajjad/testing-app/blob/main/.github/workflows/main-cd.yml#L26):

```
    - name: Cache Gradle Wrapper
      uses: actions/[email protected]
      with:
        path: ~/.gradle/wrapper
        key: ${{ runner.os }}-gradle-wrapper-${{ hashFiles('gradle/wrapper/gradle-wrapper.properties') }}

    - name: Cache Gradle Dependencies
      uses: actions/[email protected]
      with:
        path: ~/.gradle/caches
        key: ${{ runner.os }}-gradle-caches-${{ hashFiles('gradle/wrapper/gradle-wrapper.properties') }}
        restore-keys: |
          ${{ runner.os }}-gradle-caches-
```

我们正在分别缓存我们的 Gradle 依赖项和包装器，以确保它们可用于后续的构建，并为我们节省一些宝贵的 CI/CD 时间。

### 生成 Android 发布版本

现在我们将[开始 Android 应用的构建过程](https://github.com/zsajjad/testing-app/blob/main/.github/workflows/main-cd.yml#L40):

```
    - name: Make Gradlew Executable
      run: cd android && chmod +x ./gradlew

    - name: Build Android App Bundle
      run: |
        cd android && ./gradlew bundleRelease --no-daemon
```

在某些 Linux 环境中，Gradle 默认是不可执行的。因此，为了安全起见，我们在开始繁重的构建过程之前确保`gradlew`是可执行的。

因为所有的动作都是作为单独的实例执行的，所以让 Gradle 守护进程运行是没有意义的；这会给记忆造成额外的负担。因此，我们在构建步骤中添加了一个`--no-daemon`标志。

### 签署 Android 版本

为了应用程序的安全性，建议我们继续在代码库之外签名属性。一旦我们的构建完成，我们将有一个未签名的应用捆绑包可用。为了对这个包进行签名，我们将使用一个开源签名动作，[r0 adkll/sign-Android-release](https://github.com/r0adkll/sign-android-release)。

[这是我们签署发布的步骤](https://github.com/zsajjad/testing-app/blob/main/.github/workflows/main-cd.yml#L47):

```
    - name: Sign App Bundle
      id: sign_app
      uses: r0adkll/[email protected]
      with:
        releaseDirectory: android/app/build/outputs/bundle/release
        signingKeyBase64: ${{ secrets.ANDROID_SIGNING_KEY }}
        alias: ${{ secrets.ANDROID_SIGNING_ALIAS }}
        keyStorePassword: ${{ secrets.ANDROID_SIGNING_STORE_PASSWORD }}
        keyPassword: ${{ secrets.ANDROID_SIGNING_KEY_PASSWORD }}
```

这项任务使用了我们项目中的秘密。我们可以使用以下命令生成`signingKeyBase64`:

```
openssl base64 < some_signing_key.jks | tr -d '\n' | tee some_signing_key.jks.base64.txt
```

输出的内容。txt 文件将是 Base64 签名密钥。

### 上传神器

GitHub Actions 允许我们保存任何作业的输出。这些文件可以在以后下载用于测试或备份。我们将[上传我们的签名应用程序作为这项工作的工件](https://github.com/zsajjad/testing-app/blob/main/.github/workflows/main-cd.yml#L57)。

```
    - name: Upload Artifact
      uses: actions/[email protected]
      with:
        name: Signed App Bundle
        path: ${{steps.sign_app.outputs.signedReleaseFile}}
```

这里的一个关键部分是我们签名的 APK 的路径；这是通过我们前面的步骤`sign_app`生成的，它输出了已签名的 APK 的路径。这里，我们将使用相同的路径。

工作的构建阶段到此结束！签名包或 APK 现在可供我们使用。

## 通过 Firebase 发布我们的应用

正如我们在上面的需求中提到的，在每次推送到`develop`分支时，我们都必须使用 Firebase 应用分发来分发我们的应用。为了使用 Firebase CLI，[我们将在实例上全局安装`firebase-tools`](https://github.com/zsajjad/testing-app/blob/main/.github/workflows/develop-cd.yml#L53)，并使其`PATH`可用。

```
    - name: Distribute app via Firebase App Distribution
      env:
          firebaseToken: ${{ secrets.FIREBASE_TOKEN }}
          firebaseGroups: ${{ secrets.FIREBASE_GROUPS }}
          firebaseAppId: ${{ secrets.FIREBASE_APP_ID }}
          notes: ${{ github.event.head_commit.message }}
      run: |
        yarn global add firebase-tools
        export PATH="$(yarn global bin):$PATH"
        firebase \
          appdistribution:distribute android/app/build/outputs/apk/release/app-release.apk \
          --app $firebaseAppId \
          --release-notes "$notes" \
          --groups "$firebaseGroups" \
          --token "$firebaseToken"
```

这里我们使用`firebaseToken`、`firebaseGroups`和`firebaseAppId`，都来自我们的秘密。我们在 env 中设置了这些值，然后在 CLI 命令中使用了 env 变量。这是获取秘密和其他变量的另一种方式。

此外，我们还访问了 GitHub 事件中 head commit 的消息。关于作业、提交等的广泛信息。在工作流上下文中[是否可用。](https://docs.github.com/en/free-pro-team@latest/actions/reference/context-and-expression-syntax-for-github-actions)

## 通过游戏控制台分发

让我们去打一个本垒打。在这一步中，我们将通过 Google Play 控制台分发我们的签名应用程序。这个过程需要[设置 Google Play 开发者 API](https://developers.google.com/android-publisher/getting_started) 并将其与 Play 控制台连接。完成这个设置后，我们将获得一个 JSON 文件，其中包含关于服务帐户的所有信息。我们会把它放在我们的项目秘密里。

[对于这一步](https://github.com/zsajjad/testing-app/blob/main/.github/workflows/main-cd.yml#L63)，我们将使用开源的[r0adkll/upload-Google-Play](https://github.com/r0adkll/upload-google-play)，它完成了所有与 Play API 相关的繁重工作。

```
    - name: Deploy to Play Store (BETA)
      uses: r0adkll/[email protected]
      with:
        serviceAccountJsonPlainText: ${{ secrets.ANDROID_SERVICE_ACCOUNT }}
        packageName: com.testedapp
        releaseFile: a${{steps.sign_app.outputs.signedReleaseFile}}
        track: beta
        inAppUpdatePriority: 3
        userFraction: 0.5
        whatsNewDirectory: android/release-notes/
```

兴奋来了——我们的测试应用现在交付给最终用户，无需任何手动干预！

## 下一步是什么？

正如开始时所讨论的，[CI/CD](https://blog.logrocket.com/from-front-end-developer-to-a-devops-an-intro-to-ci-cd-7a8a8713fb34/)的主要目标是节省开发人员的时间，并保持代码库和最终应用程序的质量不变。利用 GitHub 动作可以节省大量时间，而这些时间是我们手工构建和发布应用程序所花费的。

CI 还将允许开发人员只提取通过我们所有质量标准和测试的代码，这意味着更少的错误和更快的迭代。除了免费披萨，还有什么能让你更开心？

然而，我在使用 GitHub Actions 时错过的一个重要特性是在不同的作业之间共享步骤的能力。在我们的案例中，我们必须在所有的工作中克隆、安装依赖项并运行测试。

目前，所有脚本中都定义了步骤；如果我们在不同的工作之间共享步骤，这种冗余是可以避免的。让我们[继续关注这个讨论](https://github.community/t/reusing-sharing-inheriting-steps-between-jobs-declarations/16851/4)并希望它能尽快出现在 GitHub Actions 中。

您可以更深入地了解如何并行和相互依赖地执行多个任务。此外，您可以创建工作流模板，以便在不同的项目之间共享。分享你用 GitHub Actions 做的很酷的事情，并在下面的评论中做出本地反应！

## [LogRocket](https://lp.logrocket.com/blg/react-native-signup) :即时重现 React 原生应用中的问题。

[![](img/110055665562c1e02069b3698e6cc671.png)](https://lp.logrocket.com/blg/react-native-signup)

[LogRocket](https://lp.logrocket.com/blg/react-native-signup) 是一款 React 原生监控解决方案，可帮助您即时重现问题、确定 bug 的优先级并了解 React 原生应用的性能。

LogRocket 还可以向你展示用户是如何与你的应用程序互动的，从而帮助你提高转化率和产品使用率。LogRocket 的产品分析功能揭示了用户不完成特定流程或不采用新功能的原因。

开始主动监控您的 React 原生应用— [免费试用 LogRocket】。](https://lp.logrocket.com/blg/react-native-signup)