# 探索迅猛龙，Deno 的 npm 风格的脚本 runner 

> 原文：<https://blog.logrocket.com/exploring-velociraptor-deno-npm-style-script-runner/>

Node.js 的弟弟 Deno 一直在进化。自从它在 2020 年 5 月正式发布以来，发生了很多变化。炒作已经平息，运行时已经改进，社区以及整个模块生态系统已经开始形成。

然而，即使有这些变化， [Deno 仍然不是一些人可能认为的“Node.js 杀手”](https://blog.logrocket.com/what-is-deno/)。当然，它有很好的特性，比如一流的类型脚本支持、安全的基于权限的系统和基于 URL 的模块加载，但是它在思想共享和一些基本方面落后于 Node。

这种特性的一个例子是脚本运行程序。在 Node 中有`package.json`，在这里你可以指定你的`scripts`。

```
"scripts": {
    "dev": "vite",
    "build": "vite build",
    "serve": "vite preview"
  }
}

```

问题是，在 Deno 中没有内置的`package.json`或任何替代物。任何时候你想使用它，你都必须打出完整的命令。大量的配置标志并没有让事情变得更好:

```
deno run --allow-read --allow-write --allow-net --reload example.ts

```

解决方法是使用第三方脚本运行程序，比如 Velociraptor。让我们看看它有什么特性，它是如何工作的，以及它如何使 Deno 工作得更好！

## 迅猛龙简介

[迅猛龙](https://github.com/jurassiscripts/velociraptor)可以说是 Deno 最受欢迎的脚本运行者，在本文发表时有大约 500 名 GitHub 明星。它受到了`package.json`的`scripts`的启发，提供了与 Node 类似的开箱即用体验，但也增加了额外的 Deno 特定功能。

### 装置

你可以从[deno.land/x](https://deno.land/x/velociraptor)安装迅猛龙，就像任何其他模块一样:

```
deno install -qAn vr https://deno.land/x/[email protected]/cli.ts

```

在给可执行文件命名时，建议[使用`vr`。使用不同的名称可能会导致 Git 挂钩的一些已知问题。](https://velociraptor.run/docs/known-limitations/#custom-executable-names)

### 配置

接下来，创建您的配置文件——`package.json`替换文件。它可以是具有以下扩展名之一的`scripts`或`velociraptor`文件:

以下是不同配置文件的示例:

**scripts.yaml**

```
scripts:
  start: deno run --allow-net server.ts
  test: deno test --allow-net server_test.ts

```

**scripts.json**

```
{
  "scripts": {
    "start": "deno run --allow-net server.ts",
    "test": "deno test --allow-net server_test.ts"
  }
}

```

**script.ts**

```
export default {
  scripts: {
    start: "deno run --allow-net server.ts",
    test: "deno test --allow-net server_test.ts",
  },
};

```

在这篇文章的剩余部分，我们将坚持使用`yaml`。

除了基本的、紧凑的形式，脚本也可以被定义为允许传递附加选项的对象，比如用于描述脚本目的的`desc`或者用于在文件改变时重新加载脚本的`watch`。

```
scripts:
  start:
    desc: Runs the server
    cmd: deno run --allow-net server.ts
    watch: true

```

在对象格式中，在`cmd`属性下指定实际的命令。

### 迅猛龙 CLI

除了配置文件，Velociraptor 的 CLI 是其最重要的功能。这是你在 Deno 世界的`yarn`或`npm run`等价物。

在你的终端中运行普通的`vr`将会输出所有定义好的脚本，而`vr run <script>`或者`vr <script>`将会运行选中的脚本。

```
vr # list available scripts
vr run start # run "start" script
vr start # more concise way of running "start" script
vr start --prod # run "start" script with "prod" argument

```

除了运行脚本，Velociraptor 还提供了一个`export`命令，用于将一个或多个脚本作为独立的可执行文件输出，以便在没有安装 Velociraptor 的环境中使用。

```
vr export start # export "start" script
./bin/start --prod # run exported script with "prod" argument

vr export start -o output # change output directory from default "bin"

```

## 配置特征

虽然在大多数情况下，定义脚本并从 CLI 运行它们就足够了，但是 Velociraptor 远远不止于此。有了它的额外特性，在 Deno 中使用脚本运行器会成为一种更好的体验。

### 传递 Deno CLI 选项

Velociraptor 允许您使用专用的配置属性，以清晰、结构化的方式指定一些 Deno CLI 选项。

#### 许可

您可以在最上面的`allow`字段为所有脚本指定数组或对象中的 Deno 权限，或者逐个脚本地指定。

```
allow:
  - read
  - write

scripts:
  start:
    cmd: server.ts
    allow:
      net: 127.0.0.1

```

#### 配置文件

您还可以在单独的配置条目下提供文件路径，如`tsconfig.json`、`importmap.json`、锁文件和 SSL 的 PEM 证书。

```
scripts:
  start:
    cmd: server.ts
    cert: certificate.pem
    lock: lock.json
    imap: importmap.json
    tsconfig: tsconfig.json

```

除了上述选项之外，您还可以指定许多其他选项。其中包括:

*   `inspect`用于连接调试器
*   `reload`用于重新编译 TypeScript(全部或仅指定模块)
*   `log`用于指定日志级别
*   `v8Flags`用于将选项传递给 V8 发动机
*   更多的布尔标志，比如`unstable`、`cachedOnly`等等

查看[官方迅猛龙文档](https://velociraptor.run/docs/declarative-deno-options/)了解更多细节。

### 传递环境变量

除了 Deno CLI 选项之外，还有用于环境变量的专用字段。这包括:

*   `env`用于直接在配置文件中列出变量
*   `envFile`用于指定加载变量的 dotenv 文件(或文件列表)

可以在顶层和脚本层指定这些选项，并将它们组合起来，以便脚本可以接收所有需要的变量。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
# for all scripts
envFile:
  - .env
env:
  PORT: 8081

scripts:
  start:
    cmd: deno run --allow-net server.ts
    # script-specific
    envFile: .start_env
    env:
      PORT: 8082

```

### 复合脚本

Velociraptor 的另一个伟大特性是允许你同时运行多个脚本。使用复合脚本，用`&&`和其他操作符填充的复杂命令可以被替换为一个干净的脚本列表。

```
scripts:
  start:
    - deno run one.ts
    - deno run two.ts
  test: # With script objects
    - deno test test_one.ts
    - cmd: deno test test_two.ts
      tsconfig: tsconfig.json

```

上面的例子按顺序连续运行脚本。要并行运行它们，使用`pll`选项。

```
scripts:
  start:
    pll: # In parallel
      - deno run one.ts
      - deno run two.ts

```

`pll`可以与串行脚本列表结合，形成更复杂的执行流程。

```
scripts:
  start:
    - pll:
        - deno run one.ts
        - deno run two.ts
    - deno run three.ts

```

这提供了一组新的可能性，直接从配置文件中清晰地定义和执行复杂的脚本流。

## 在迅猛龙上使用 Git 钩子

Velociraptor 内置了对 Git 挂钩的支持。它允许您毫不费力地将您的脚本与 Git 挂钩集成，并与从事相同 repo 的其他人共享它们。

要将一个脚本链接到一个 Git 挂钩，向它添加`gitHook`属性。

```
scripts:
  format:
    cmd: deno fmt
    gitHook: pre-commit

```

要将多个脚本附加到 Git 挂钩上，可以在复合脚本旁边使用`gitHook`。

设置好钩子后，您可以通过运行`vr`命令来安装它们。如果您想要防止这种行为(例如，对于 CI 配置)，请将`VR\_HOOKS`环境变量设置为`false`。

## 工具集成

对于更高级的工作流程，Velociraptor 集成了 GitHub 动作，这要归功于一个 [`setup-velociraptor`动作](https://github.com/jurassiscripts/setup-velociraptor)。当与 [`setup-deno`](https://github.com/denoland/setup-deno) 结合使用时，您可以直接使用`vr`命令进行操作。

```
steps:
  - uses: denoland/[email protected]
  - uses: jurassiscripts/[email protected]
  - run: vr ...

```

### 编辑器支持

你可以在 VS 代码中为迅猛龙安装[专用扩展](https://marketplace.visualstudio.com/items?itemName=umbo.vscode-velociraptor)进行自动补全和其他代码辅助功能。这将自动完成`yaml`和`json`文件中的配置选项。

通过为配置文件使用 TypeScript 以及适当的类型注释，您仍然可以在其他编辑器和 ide 中获得自动完成功能。

```
import { ScriptsConfiguration } from "https://deno.land/x/[email protected]/mod.ts";

const config: ScriptsConfiguration = {
  scripts: {
    start: "deno run --allow-net server.ts",
    test: "deno test --allow-net server_test.ts",
  },
};

export default config;

```

## 结果

有了 Velociraptor 这样优秀的、功能丰富的脚本运行程序，Deno 的使用变得更加愉快。你所有的剧本就在`vr <script>`远处；Git 挂钩集成再简单不过了；由于复合脚本、结构化选项和自动完成，构建复杂的脚本流很容易。

如果您还没有，请在 GitHub 上查看 Velociraptor 并尝试一下！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)