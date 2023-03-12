# 使用 asdf 管理 Node.js 版本

> 原文：<https://blog.logrocket.com/manage-node-js-versions-using-asdf/>

当一次处理几个项目时，您可能需要使用不同版本的编程语言。这导致卸载和重新安装每种语言的正确版本，浪费了大量时间。

幸运的是，使用节点版本管理器、Ruby 的 rbenv 或 Python 的 pyenv 等版本管理器，可以很容易地在不同版本之间进行切换。

这些都是很大的帮助，但是现在您需要为您的多语言项目管理和安装多个工具，这可能很麻烦。相反，您可以使用 [asdf](https://github.com/asdf-vm/asdf) 来管理多个运行时版本，通过一个 CLI 工具管理您正在使用的所有语言版本。

Asdf 是一个可扩展的版本管理器，它同时支持多种语言，比如 Ruby、Node.js、Elixir、Erlang、Python 等等。您可以在全局和每个目录的语言版本之间切换。

在本文中，我们将学习使用 asdf 和 Node 所需的一切，包括如何在本地机器上设置和更新 asdf，以及安装、更新和删除 Node 插件。

## 入门指南

要开始使用 asdf，我们需要安装一些依赖项，比如`coreutils`、`curl`和`git`。Asdf 使用这些依赖项来安装不同的语言。

执行以下命令安装 asdf:

```

brew install coreutils curl git  # on macOS with Homebrew

sudo apt install curl git        # on Linux

```

Asdf 文档建议从 git repo 克隆，如下所示:

```
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.8.1

```

要安装 asdf，请将其添加到您的 shell 中。您可以在[文档中查看其他安装方法。](https://asdf-vm.com/guide/getting-started.html#_3-install-asdf)我将使用“痛击&家酿(macOS)”的方法。

将`asdf.sh`添加到您的`~/.bash_profile`中，如下所示:

```
echo -e "\\n. $(brew --prefix asdf)/libexec/asdf.sh" >> ~/.bash_profile

```

然后，设置 bash 完成:

```
echo -e "\\n. $(brew --prefix asdf)/etc/bash_completion.d/asdf.bash" >> ~/.bash_profile

```

你可以这样看你的`~/.bash_profile`:

```
cat ~/.bash_profile

```

Asdf 也支持`Zsh`和`Fish`外壳。

现在，关闭终端，并启动一个新的终端。让我们通过在命令行中键入`asdf`来确保 asdf 已安装。

## 安装 Node.js 插件

在 asdf 中，每种语言都由一个[插件](https://github.com/asdf-vm/asdf-plugins)支持。他们有上百个插件，但是如果你没有看到你需要的，你也可以自己创建。

插件不仅仅包括语言；asdf 也支持数据库。

使用以下命令安装插件:

```
asdf plugin add <name> 

```

在安装节点插件之前，我们首先要安装 [GnuPG](https://www.gnupg.org/) 来验证包的真实性。每个插件都有不同的要求，你可以在每个插件的 GitHub repo 上找到[。](https://github.com/asdf-vm/asdf-plugins#:~:text=have%20previously%20set.-,Plugin%20List,-Language)

对于 Mac，我们可以通过自制程序安装 GnuPG:

```
brew install gpg

```

安装 GnuPG 后，通过运行以下命令安装节点插件:

```
asdf plugin add nodejs https://github.com/asdf-vm/asdf-nodejs.git

```

安装后，您可以看到已安装插件的列表，如下所示:

```
asdf plugin list

```

## 安装编程语言版本

Asdf 列出了每种编程语言的所有可用版本；在撰写本文时，我们总共可以安装 604 个版本的 Node。

您可以使用以下内容查看节点版本列表:

```
asdf list all nodejs | wc -l   # prints 604

```

要安装特定版本的节点，请运行:

```
asdf install nodejs 14.17.6

```

如果在安装某个版本的 Node 时遇到一些错误，请尝试以下命令:

```
asdf reshim nodejs

```

你可能会在这里得到一个关于`reshim`的错误。如果不运行`reshim`命令，npm 包将不会出现在您的路径中:

```
Unknown command: `asdf reshim nodejs 14.17.6`
/usr/local/Cellar/asdf/0.8.1_1/libexec/bin/asdf: line 82: /Users/<your-username>/.asdf/lib/commands/command-help.bash: No such file or directory

```

您可以通过运行以下命令来查看您安装了哪些版本:

```
asdf list all nodejs

```

要安装最新版本的 Node，请运行:

```
asdf install nodejs latest

```

我们还可以安装以给定字符串开头的最新节点版本:

```
# asdf install <name> latest:<version>
asdf install nodejs latest:16

```

## 设置版本

可以在三个级别上设置编程语言版本:全局、在当前 shell 中或本地。

### 全球的

您可以设置 Node 的默认全局版本，这样它将在任何新的 shell 中保持不变。这将在`Home`目录下的`.tool_versions`文件中添加或更新一行。

要设置节点的全局或默认版本，请运行:

```
# asdf global <name> <version> [<version>...]
asdf global nodejs 14.17.6
asdf global nodejs latest

```

现在，您可以用`asdf current`命令验证 asdf 的当前版本。在我的例子中，它显示的是 16.9.0 的最新节点版本:

```
(base) ➜  ~ asdf current
nodejs          16.9.0          /Users/<username>/.tool-versions

```

如果没有为工具执行列出版本，这将返回一个错误。使用这个命令，我们可以从当前目录中看到工具和版本解析，或者看不到。

全局默认值在`$HOME/.tool-versions`中管理。您可以通过运行以下命令来查看其内容:

```
cat $HOME/.tool-versions

```

我已经将全局节点版本设置为最新，我可以通过运行以下命令来验证:

```
(base) ➜  ~ node -v
v16.9.0 

```

### 壳

如果希望仅在当前 shell 中使用节点版本，请运行以下命令:

```
# asdf shell <name> <version> [<version>...]
asdf shell nodejs 14.17.6
asdf shell nodejs latest

```

此版本的 Node 将仅在当前 shell 会话中运行，因此当您关闭会话时，此版本将不可用。

在幕后，`shell`将版本设置为一个名为`ASDF_${LANG}_VERSION`的环境变量，仅用于当前 shell 会话:

```
ASDF_NODEJS_VERSION=14.17.6 node --version

```

### 当地的

您也可以基于单个项目运行特定版本的 Node，方法是在本地设置版本:

```
asdf local <name> <version> [<version>...]
asdf local nodejs 14.17.6
asdf local nodejs latest

```

`local`将版本写入`$PWD/.tool-versions`。

您可以通过运行`node -v`来验证本地和 shell 环境的节点版本。

要基于单个项目设置一个本地版本，首先安装您想用`asdf install nodejs` 使用的版本并运行`asdf local nodejs` 。这将使用您选择的节点版本创建一个`.tool-versions`文件。

### 卸载一个版本

要卸载某个版本，请使用以下命令:

```
# asdf uninstall <name> <version>
asdf uninstall nodejs 14.17.6
asdf uninstall nodejs latest

```

## 传统支持

在团队中采用新的版本管理器需要时间，这就是 asdf 让您回退到遗留版本文件的原因。如果你愿意，你仍然可以使用你喜欢的版本管理器[，比如`nvm`](https://blog.logrocket.com/switching-between-node-versions-during-development/) 。

要启用遗留支持，只需在主目录(`$HOME/.asdfrc`)中添加一个`.asdfrc`文件，并添加以下配置设置:

```
legacy_version_file = yes  # .asdfrc file

```

通过将遗留版本文件设置为`yes`，asdf 将能够读取其他版本管理器文件，如`.nvmrc`、`.node-versions`或`.ruby-versions`。

## 更新插件

在某些时候，你可能需要更新插件。您可以通过运行以下命令来实现:

```
asdf plugin update --all

```

到目前为止，我们一直依赖 asdf 来管理多个开发环境。要跟上`asdf`版本，运行以下命令:

```
asdf update       # via git
brew upgrade asdf # via homebrew

```

如果你想卸载 asdf，你可以跟随[文档](https://asdf-vm.com/manage/core.html#uninstall)。

## 结论

Asdf 对于通晓多种语言的程序员来说是一个很好的工具。它实现了无忧安装和多语言版本之间的轻松切换，节省了真正重要的时间-不要忘记阅读 asdf 的[歌谣！](https://github.com/asdf-vm/asdf#:~:text=our%20helpful%20community-,Ballad%20of%20asdf,-Once%20upon%20a)

现在，您可以安装任意多的语言。除了使用许多不同的包管理器，您可以让 asdf 尝试去管理它们。

你呢？你用 asdf 吗？你的体验如何？

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.