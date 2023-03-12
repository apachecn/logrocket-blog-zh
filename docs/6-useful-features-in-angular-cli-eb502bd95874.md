# Angular CLI - LogRocket 博客中的 6 个有用特性

> 原文：<https://blog.logrocket.com/6-useful-features-in-angular-cli-eb502bd95874/>

随着 Google 的 web 框架 Angular 的第二次迭代和 AngularJS 的完全重写，Angular 社区在 Angular 命令行界面(CLI)的开发效率方面有了显著的提升。

CLI 使新老开发人员能够轻松创建只需一个命令就能工作的 Angular 应用程序。CLI 还管理项目配置、文件生成和构建过程，使开发工作流在 Angular 应用程序中更加简化和一致。

在本帖中，我们将看看 Angular CLI 提供的一些最有用的功能。

安装 CLI

### 使用 CLI 提供的任何功能的第一步是安装 CLI。您可以使用以下命令轻松做到这一点:

`npm install -g @angular/cli`

如有必要，您可以随时参考[官方网站](https://cli.angular.io/)上 CLI 的安装步骤。

在这里，您可以使用以下 CLI 命令创建新的 Angular 项目:

`ng new my-angular-app`

一旦完成，您就可以 cd 到您的新项目并运行您的应用程序:

`cd my-angular-app`

`ng serve`

与 AngularJS 时代相比，Angular CLI 减少了我们在设置项目时需要做的大量工作，这使我们可以将更多的时间放在为应用程序创建新功能上。

层代和别名

### 生成应用程序后，首先要做的事情之一就是开始为应用程序生成新的原理图，比如组件、服务和模块。

您可以通过利用 CLI 的 generate 命令来实现这一点:

`ng generate component my-component`

CLI 中的许多命令还包括别名，以减少运行这些命令所需的键入量。使用上面的例子，我们可以通过对生成命令使用别名`g`和对元件原理图使用别名`c`来缩短它:

`ng g c my-component`

您可以在 CLI 的 [wiki](https://github.com/angular/angular-cli/wiki/generate#available-schematics) 中查看使用 generate 命令创建的可用原理图的完整列表。

按指定路线发送

### 如果您使用 CLI 生成一个新项目，您将很快启动并运行，但默认设置非常基本。这些默认设置的一个副作用是，没有为具有独立视图路由的应用程序提供适当的结构，而这是现代 web 应用程序的常见要求。

在创建应用程序时，您可以通过添加一个简单的标志来手动将路由添加到您的项目中，CLI 可以为您完成所有这些工作。

创建新项目时，只需添加`--`路由标志，CLI 将在`src/app/app-routing.module.ts`中为您的项目生成一个路由模块:

`ng new my-project --routing`

稍后，当您为您的应用程序开发模块时，您还可以生成单独的路由模块，这在您希望避免混淆根应用程序路由模块时非常有用。同样，您在生成模块时使用相同的`--`路由标志。

`ng generate module my-module --routing`

造型预处理程序

### 生成角度项目时，您可以选择对样式文件使用 CSS 预处理器，这样您就可以使用 Sass、Less 或手写笔编写 CSS 样式。

更多来自 LogRocket 的精彩文章:

* * *

### 幸运的是，CLI 通过在 ng new 命令中添加`--`样式标志，使这一点变得非常容易。

* * *

`ng new my-project --style=scss`

如果您想使用不同的预处理器，只需用`less`或`styl`替换`scss`。

试运行

### 在使用 CLI 提供的任何命令时，您可能会发现自己希望可以对该命令进行一次测试，看看 CLI 将为您生成和更新什么，而不必在发生意外情况时实际创建文件并删除它们。

CLI 再次提供了一个标志来帮助您，该标志允许您检查命令的输出，而无需实际执行命令并修改您的项目。这个标志是—预演标志。

`ng g c my-test-component --dry-run`

如果你喜欢别名，你也可以使用这个旗帜的别名`-d`。

`ng g c my-test-component -d`

跳过测试

### 到目前为止，您可能已经注意到，这些生成命令中的许多会在您的应用程序中自动生成文件扩展名为`.spec.ts`的测试文件。这是一个很好的默认设置，显示了 Angular 如何将您推向最佳实践的方向，但是有时您希望覆盖默认设置并减少应用程序的额外膨胀。

当使用生成命令生成原理图时，可以使用`--`规格标志:

`ng g c my-testless-component --spec=false`

在使用新命令创建应用程序时，也可以这样做:

`ng new my-testless-application --skip-tests`

`ng new my-testless-application -S`

证明文件

### 当您在进行角度应用时，不可避免地会遇到需要参考[角度文档](https://angular.io/docs)获取更多信息的情况。

您可能已经在浏览器中将文档加入了书签，但是您总是可以使用 CLI 提供的 doc 命令来保存一些点击。

只需使用 doc 命令添加您的搜索词，CLI 将自动打开一个新的浏览器窗口，其中包含您在文档中指定的搜索词。

`ng doc viewchild`

结论

### 现在，您已经了解了 Angular CLI 为开发人员提供的附加命令和选项，我希望您在处理项目时能够找到将它们合并到开发工作流中的方法。如果您发现自己需要手动创建文件或进行项目配置，那么有必要检查一下 CLI 中是否有一个功能可以让这个过程变得更加简单。

如果您对 CLI 有任何疑问，并需要更多信息，请务必查看官方的 [CLI 命令参考](https://angular.io/cli)或 GitHub 上 CLI 的 [wiki 页面](https://github.com/angular/angular-cli/wiki)。

像用户一样体验 Angular 应用程序

## 调试 Angular 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪生产中所有用户的角度状态和动作感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/angular-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你网站上发生的一切，包括网络请求、JavaScript 错误等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

[![LogRocket Dashboard Free Trial Banner](img/2794ac39244976f37c4941d9a910be23.png)](https://lp.logrocket.com/blg/angular-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/angular-signup)

LogRocket NgRx 插件将角度状态和动作记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化调试 Angular 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/angular-signup)。

Modernize how you debug your Angular apps - [Start monitoring for free](https://lp.logrocket.com/blg/angular-signup).

* * *