# 使用 Rust - LogRocket 博客增强您的电子应用程序

> 原文：<https://blog.logrocket.com/supercharge-your-electron-apps-with-rust/>

电子已经成为创建跨平台桌面应用程序的首要技术。它被用于 Atom、VS Code、Spotify、Slack、Discord 等许多知名和受欢迎的平台。

JavaScript 是一种缓慢的语言，但在本教程中，我们将演示如何使用 Rust 来加速电子应用程序，以获得更好的用户体验。Rust 是一种低级语言，具有高级人机工程学。它运行速度极快，内存占用非常少。

## 建立一个电子项目

我们将使用`[electron-react-boilerplate](https://github.com/electron-react-boilerplate/electron-react-boilerplate)`为我们的演示应用程序创建一个样板文件。从克隆 GitHub repo 开始。

```
git clone --depth 1 --single-branch https://github.com/electron-react-boilerplate/electron-react-boilerplate.git md_notes

```

使用 npm 或 Yarn 安装所有依赖项。

```
npm run dev

```

我们将创建一个支持减价的笔记应用程序。markdown 解析器将用 Rust 编写。我们将使用各种方法来连接电子和铁锈。

## 将 UI 添加到您的电子项目中

我们将使用 [Material-UI](https://blog.logrocket.com/the-material-ui-grid-system/) 为我们的应用程序获取现成的组件。

```
import React, { useState, useEffect } from 'react';
import { makeStyles, createStyles, Theme } from '@material-ui/core/styles';
import Grid from '@material-ui/core/Grid';
import Paper from '@material-ui/core/Paper';
import { TextField, Typography, ListItemText, Button } from '@material-ui/core';
import List from '@material-ui/core/List';
import ListItem from '@material-ui/core/ListItem';
import { remote } from 'electron';
// import fs and path nodejs core module for wrting and reading files
const fs = remote.require('fs');
const path = remote.require('path');
const useStyles = makeStyles((theme: Theme) =>
  createStyles({
    root: {
      flexGrow: 1,
      width: '100vw',
    },
    paper: {
      width: '100%',
      minHeight: '100vh',
      padding: '5px',
    },
    control: {
      padding: theme.spacing(2),
    },
    textArea: {
      width: '100%',
      height: '100%',
    },
    heading: {
      textAlign: 'center',
    },
  })
);
const NOTES_FOLDER = 'notes';
const save = (name: string, data: any) => {
  fs.writeFileSync(
    path.join('.', NOTES_FOLDER, `${name}${Date.now()}.json`).toString(),
    JSON.stringify(data)
  );
};
export default function SpacingGrid() {
  const [editorState, setEditorState] = useState('');
  const [previewState, setPreview] = useState('');
  const [notes, setNotes] = useState([]);
  useEffect(() => {
    // we need to add code to parse md and set it to preview
  }, [setPreview, editorState]);
  useEffect(() => {
    const note = fs
      .readdirSync(path.join('.', NOTES_FOLDER))
      .map((file: string) => {
        return JSON.parse(
          String(fs.readFileSync(path.join('.', NOTES_FOLDER, file)))
        );
      });
    setNotes(note);
  }, []);
  const [title, setTitle] = useState('');
  const classes = useStyles();
  return (
    <Grid container className={classes.root} spacing={2}>
      <Grid item xs={12}>
        <Grid container justify="center" spacing={2}>
          <Grid item xs={9}>
            <Paper className={classes.paper}>
              <Grid container justify="space-between">
                <Grid item>
                  <Typography variant="h3" align="center">
                    Add Notes
                  </Typography>
                </Grid>
                <Grid item>
                  <Button
                    variant="outlined"
                    onClick={() => {
                      save(title, {
                        title,
                        createdAt: Date.now(),
                        body: editorState,
                      });
                    }}
                  >
                    Save
                  </Button>
                </Grid>
              </Grid>
              <Grid
                container
                direction="column"
                justify="space-between"
                spacing={2}
              >
                <Grid item>
                  <TextField
                    label="Title"
                    value={title}
                    onChange={(e) => setTitle(e.target.value)}
                    variant="outlined"
                    className={classes.textArea}
                  />
                </Grid>
                <Grid item>
                  <TextField
                    multiline
                    label="Content"
                    value={editorState}
                    onChange={(e) => setEditorState(e.target.value)}
                    variant="outlined"
                    className={classes.textArea}
                    rows={20}
                  />
                </Grid>
                <Grid item>
                  <div dangerouslySetInnerHTML={{ __html: previewState }} />
                </Grid>
              </Grid>
            </Paper>
          </Grid>
          <Grid item xs={3}>
            <Paper className={classes.paper}>
              <List>
                {notes.map((note, i) => (
                  <ListItem
                    key={`key-${i}`}
                    onClick={() => {
                      setEditorState(note.body);
                      setTitle(note.title);
                    }}
                  >
                    <ListItemText
                      primary={note.title}
                      secondary={new Date(note.createdAt).toLocaleString('in')}
                    />
                  </ListItem>
                ))}
              </List>
            </Paper>
          </Grid>
        </Grid>
      </Grid>
    </Grid>
  );
}

```

这是电子应用的样板代码。现在我们将实现一个解析函数来将 markdown 转换成 HTML。

我们将探索使用 Rust 解析 markdown 的各种方法，权衡每种方法的利弊。我们还将研究每种方法的用例。

![Electron Notes App](img/007e6ff365a86385c8c82abae58bc74d.png)

## 在电子中使用 WebAssembly

电子应用的核心就是 Chrome 浏览器和 Node.js。我们可以在 Chrome 和 Node 中利用 WebAssembly 支持。

WebAssembly 是一种二进制指令格式，在浏览器或基于堆栈的虚拟机中运行。我们将在 WebAssembly 中编译用 Rust 编写的 markdown 解析器。

### 在 WebAssembly 中创建 Rust markdown 分析器

Rust 对 WebAssembly 有一流的支持。它使用 LLVM 编译 WebAssembly 作为目标。 [`\wasm-pack\`](https://rustwasm.github.io/docs/wasm-pack/) 是 Rust 社区创建的一个工具，帮助开发者使用 WebAssembly 在 Rust 中编写节点模块。

### 使用 wasm 包

`wasm-pack`是一个创建、构建、发布用 WebAssembly 编写的节点模块的工具。

```
curl https://rustwasm.github.io/wasm-pack/installer/init.sh -sSf | sh

```

`wasm-pack`为 Linux 和 macOS 提供安装程序脚本。一旦在您的系统上安装了`wasm-pack`，您就可以使用`wasm-pack new`为 WebAssembly 模块创建一个样板文件。

这将用样板文件创建一个名为`md-parser`的新目录。

```
wasm-pack new md_webassembly

├── Cargo.toml
├── LICENSE_APACHE
├── LICENSE_MIT
├── README.md
├── src
│   ├── lib.rs
│   └── utils.rs
└── tests
    └── web.rs

```

`lib.rs`是 Wasm 库的主文件。仔细观察`lib.rs`,会发现它导出了 JavaScript 调用的`greet`函数。

```
// utils set a panic hook so it is console in js
mod utils;
use wasm_bindgen::prelude::*;
// When the `wee_alloc` feature is enabled, use `wee_alloc` as the global
// allocator.
#[cfg(feature = "wee_alloc")]
#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

// function defination for alert. It is dom api for generating a dialog
#[wasm_bindgen]
extern {
    fn alert(s: &str);
}

// wasm_bingen is a pro macro which transfor return type and arguments to the function
#[wasm_bindgen]
pub fn greet() {
// calling alert
    alert("Hello, md-webassembly!");
}

```

是 Rust 社区开发的一个板条箱，它使得创建 Wasm 模块变得更加容易。Wasm 不支持字符串或数组数据类型；它只支持整数和浮点数。所以它需要 JavaScript 堆上字符串的地址来使用 access string。将所有样板代码添加到我们的函数中。

对于降价解析，我们将使用`comrak` crate，它支持 GitHub 风格的降价。

```
[package]
name = "md-webassembly"
version = "0.1.0"
authors = ["anshul <[email protected]>"]
edition = "2018"

# libtype must be cdylib so it can be dynamically linked by electron
[lib]
crate-type = ["cdylib", "rlib"]

[features]
default = ["console_error_panic_hook"]

[dependencies]
wasm-bindgen = "0.2.63"
comrak = "0.8.2"
console_error_panic_hook = { version = "0.1.6", optional = true }
wee_alloc = { version = "0.4.5", optional = true }

[dev-dependencies]
wasm-bindgen-test = "0.3.13"

[profile.release]
# Tell `rustc` to optimize for small code size.
opt-level = "s"

# avoid wasm-opt error while build
[package.metadata.wasm-pack.profile.release]
wasm-opt = false

```

### 使用`comrak`箱

`comark`提供了`markdown_to_html`功能，将 markdown 解析为 HTML。我们将创建一个解析函数，它将 markdown 字符串作为参数，然后返回 HTML 字符串。

```
mod utils;
use comrak::{markdown_to_html, ComrakOptions};
use wasm_bindgen::prelude::*;
#[cfg(feature = "wee_alloc")]
#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

// take md string return html string
#[wasm_bindgen]
pub fn parse(md: String) -> String {
    markdown_to_html(md.as_str(), &ComrakOptions::default())
}

```

我们已经准备好使用 rust markdown 解析器了。用于编译使用`wasm-pack build`。`build`命令将用 typescript 类型创建 wasm 模块。

### 在电子中使用 WebAssembly

我们可以在电子版的浏览器端和 Node.js APIs 中使用 Rust markdown 解析器。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

Node 和 Chromium 都支持现成的 WebAssembly。而且，用来引导 Electron 的样板文件也默认支持导入`wasm`模块。我们可以直接导入 Wasm 代码，无需添加任何样板文件。

```
  useEffect(() => {
    import('../../md-webassembly/pkg/md_webassembly').then((module) =>
      setPreview(module.parse(editorState))
    );
  }, [setPreview, editorState]);

```

您应该使用动态导入，以便在 Wasm 模块被实例化时不会阻塞 UI 呈现。webpack 将负责休息。如果您想支持 web 应用程序，这种方法也很有用。

## NAPI 在电子学中的应用

皮娜是一个与 Node.js 通信的 C 头文件，这个方法实现起来有点困难。

我们将使用`[node-bindgen](https://github.com/infinyon/node-bindgen)`创建一个可以直接在 Node.js 中使用的 Rust 库。

用货物安装`nj-cli`。

```
cargo install nj-cli

```

我们现在可以为节点模块创建一个样板文件，然后将其导入到一个电子应用程序中。

让我们从创建一个简单的 Rust 项目开始。

```
cargo new md_napi --lib

```

现在我们需要配置`cargo`以在 Node.js 可以使用的动态库中编译它，当应用程序需要线程、SIMD 或特定于处理器的代码时，应该使用这个方法。

```
[package]
name = "md_napi"
version = "0.1.0"
authors = ["anshul <[email protected]>"]
edition = "2018"

[lib]
# compile to .so or .dl file based on OS
crate-type = ["cdylib"]

[dependencies]
# Node.js NAPI helper library
node-bindgen = { version = "2.1.1" }
# Parse MarkDown
comrak = "0.8.2"

[build-dependencies]
node-bindgen = { version = "2.1.1", features = ["build"] }

```

我们可以在`lib.rs`文件中导出一个函数。

```
use node_bindgen::derive::node_bindgen;
use comrak::{markdown_to_html, ComrakOptions};

#[node_bindgen]
fn parse(md: String) -> String {
  markdown_to_html(md.as_str(), &ComrakOptions::default())
}

```

现在我们可以使用`nj-cli build`命令构建这个库。它将用`index.node`创建一个`dist`目录。这个文件是直接需要电子版的。

```
import { remote } from 'electron';
const mdModule = remote.require('../md_napi/dist/index.node');

console.log((mdModule.parse('# Heading'))

```

在电子应用程序中，我们将简单地替换 WebAssembly 模块导入。

```
 useEffect(() => {
    setPreview(mdModule.parse(editorState));
  }, [setPreview, editorState]);

```

### 其他图书馆

还有一些额外的库可以用来创建附加组件。下面列出的库使用 V8 APIs 或 NAPIs。

## 比较

上述两种方法各有利弊。特定项目的正确选择取决于用例及需求。使用下表作为快速参考指南，帮助您选择最合适的方法。

| **功能** | **日/增** | **web assembly** |
| 线 | Libuv 线程 | 网络工作者 |
| SIMD | 全力支持 | 部分支持 |
| 网页浏览器 | 无支持的 | 支持 |
| CFI | 支持 | 无支持的 |
| 外部板条箱 | 全力支持 | 可能支持也可能不支持 |

## 结论

JavaScript 是一种缓慢的语言。所以电子 app 往往比较慢。诀窍是将 CPU 密集型任务转移给其他更快的语言。JavaScript 非常适合 UI 控制，而 Rust 更适合处理这些复杂的计算。如果你已经完成了本教程，现在你已经有了两个可靠的方法来使用 Rust 加速你的电子应用程序。

## [log rocket](https://lp.logrocket.com/blg/rust-signup):Rust 应用的 web 前端的全面可见性

调试 Rust 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监控和跟踪 Rust 应用程序的性能、自动显示错误、跟踪缓慢的网络请求和加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/rust-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/rust-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Rust 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

现代化调试 Rust 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/rust-signup)。