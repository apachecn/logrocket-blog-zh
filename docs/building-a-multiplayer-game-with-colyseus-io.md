# 如何让多人游戏联网像小孩子的 play 一样

> 原文：<https://blog.logrocket.com/building-a-multiplayer-game-with-colyseus-io/>

电脑游戏太棒了！它们不仅玩起来有趣，而且制作起来也很有趣。事实上，每个程序员，在某个时候，都至少考虑过开发一个游戏。

也就是说，开发游戏并不容易，需要丰富的想象力才能创造出真正令人印象深刻的东西。如果你想建立一个多人游戏，你不仅要创建一个伟大的游戏，还要建立所有的网络，这本身就是一项艰巨的任务。

Colyseus 旨在减轻网络的负担，让您可以完全专注于您的游戏机制。为了展示它所提供的功能，我们将实现一个多人俄罗斯方块克隆版——我们称之为 Tetrolyseus。

## 入门:Colyseus 后端设置

Colyseus 提供了一个 npm-init 初始化器，可以自动创建新项目。

```
npm init colyseus-app ./my-colyseus-app

```

这个交互式初始化器负责我们的基本设置。虽然也可以将 Colyseus 与普通的旧 JavaScript 或 [Haxe](https://haxe.org/) 一起使用，但我们将坚持使用 TypeScript。

```
? Which language you'd like to use? …
❯ TypeScript (recommended)
  JavaScript
  Haxe

```

一旦完成，我们将在`my-colyseus-app`中生成以下文件。

```
.
├── MyRoom.ts
├── README.md
├── index.ts
├── loadtest
├── node_modules
├── package-lock.json
├── package.json
└── tsconfig.json

```

让我们深入了解一下科利塞斯:

### `index.ts`

新创建的`index.ts`文件是我们的主要入口点，它设置了我们的服务器。

```
const port = Number(process.env.PORT || 2567);
const app = express()

app.use(cors());
app.use(express.json())

const server = http.createServer(app);
const gameServer = new Server({
  server,
});

```

虽然不是必须的，默认的`colyseus-app`模板也使用 [express](https://www.npmjs.com/package/express "express js on npm") ，所以我们可以很容易地在后端注册额外的路线处理程序。如果我们不想提供额外的处理程序，我们的设置可以归结为:

```
const port = Number(process.env.PORT || 2567);

const gameServer = new Server();

```

我们的`index.ts`文件的第二部分是我们实际暴露游戏逻辑的地方。

```
// register your room handlers
gameServer.define('my_room', MyRoom);

// skipped for brevity

gameServer.listen(port);
console.log(`Listening on ws://localhost:${ port }`)

```

科利塞斯使用房间的概念来实现游戏逻辑。一个房间在我们的服务器上由它的唯一名称定义，客户端使用它来连接到它。一个房间处理客户端连接，也保存游戏的状态。这是我们游戏的核心部分。

### `MyRoom.ts`

```
import { Room, Client } from "colyseus";

export class MyRoom extends Room {
  onCreate (options: any) {
    this.onMessage("type", (client, message) => {
      // handle "type" message
    });
  }

  onJoin (client: Client, options: any) {
  }

  onLeave (client: Client, consented: boolean) {
  }

  onDispose() {
  }
}

```

如您所见，Colyseus 房间附带了一些生命周期事件:

*   `onCreate`是房间实例化时调用的第一个方法。我们将初始化我们的游戏状态并在`onCreate`中连接我们的消息监听器
*   一旦有新客户连接到我们的游戏室，就会被调用
*   `onLeave`与`onJoin`正好相反，因此每当客户端离开时，断开和重新连接逻辑都在这里处理
*   是在游戏室被处理掉之前调用的最后一个方法，在那里可能会执行诸如将游戏结果存储到数据库中之类的事情
*   虽然没有包含在默认的房间实现中，`onAuth`允许我们为加入的客户端实现定制的认证方法，如[认证 API 文档](https://docs.colyseus.io/server/authentication/)所示

现在我们已经完成了一个基本的 Colyseus 后端设置，让我们开始建模我们的游戏状态。

你可以在 [GitHub](https://github.com/s1h-org/tetrolyseus) 上的附带资源库中找到我们迄今为止编写的代码。对应的标签是`01-basic-setup`:

```
git checkout tags/01-basic-setup -b 01-basic-setup

```

## 管理游戏状态

以这样或那样的方式，每一个游戏都保持着状态。球员位置，当前比分，你说吧。国家是游戏的支柱。

当谈到在线多人游戏时，状态变成了一个更加复杂的话题。我们不仅要正确地建模，还要考虑如何在所有玩家之间同步我们的状态。

这就是科利塞斯真正开始发光的地方。它的主要目标是消除网络和状态同步的负担，这样我们就可以专注于重要的事情:游戏逻辑。

## 有状态游戏室

之前，我们了解到科利修斯的房间能够存储我们的游戏状态。每当一个新的房间被创建，我们初始化我们的状态。

```
import { Room, Client } from "colyseus";
import { MyGameState } from "./MyGameState";

export class MyRoom extends Room<MyGameState> {
  onCreate (options: any) {
    this.setState(new MyGameState());
    ...
  }

  ...
}

```

每次客户端连接到我们的房间时，它都会在初始同步中自动接收完整的房间状态。

因为房间状态是可变的，所以它必须被连续地同步。但是，在完全状态同步之后，Colyseus 将只发送应用于初始状态的增量更新。每个房间的状态同步间隔可通过其 [patchRate](https://docs.colyseus.io/server/room/#patchrate-number "Room patchRate docs") 进行配置，默认为 50 毫秒(20fps)。更短的间隔允许更快节奏的游戏。

事不宜迟，让我们为我们的状态建模。

### `Position`

二维 Tetrolyseus 板由几行和几列组成。`Position`状态对象通过左上角的行和列来存储我们的活动 Tetrolyso 块的位置。

```
import {Schema, type} from "@colyseus/schema";

export class Position extends Schema {
    @type("number")
    row: number;

    @type("number")
    col: number;

    constructor(row: number, col: number) {
        super();
        this.row = row;
        this.col = col;
    }
}

```

我们的 state 类必须满足某些属性才能进行同步:

*   它必须扩展 [`Schema`基类](https://docs.colyseus.io/state/schema/ "Schema docs")
*   选择同步的数据需要一个 [`type`](https://docs.colyseus.io/state/schema/#primitive-types "Type docs") 注释
*   必须通过 [`setState`](https://docs.colyseus.io/state/schema/#primitive-types "setState docs") 向游戏室提供状态实例

`Position`是一个简单的状态类，它同步两个`number`属性:`row`和`col`。它很好地演示了 Colyseus `Schema`类如何允许我们从原始类型组装状态，自动启用同步。

### `Board`

接下来是我们的游戏板状态。类似于`Position`，它存储两个`number`属性:我们的二维游戏板的`rows`和`cols`。此外，它的`values`属性保存一个数字数组，代表我们的董事会。

到目前为止，我们只处理了单个数据，那么我们将如何对保存数据集合的 state 类建模呢？使用 Colyseus，集合应该存储在一个用于一维数据的 [`ArraySchema`](https://docs.colyseus.io/state/schema/#arrayschema "ArraySchema docs") 、Colyseus 的可同步 [`Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array "MDN Array docs") 数据类型中。

```
import {ArraySchema, Schema, type} from "@colyseus/schema";

export class Board extends Schema {
    @type(["number"])
    values: number[];

    @type("number")
    rows: number;

    @type("number")
    cols: number;

    constructor(rows: number = 20, cols: number = 10) {
        super();
        this.rows = rows;
        this.cols = cols;
        this.values = new ArraySchema<number>(...(new Array<number>(rows * cols).fill(0)));
    }
}

```

### Tetrolyso

Tetrolyso 块基本上只是一个扩展版本的棋盘，增加了一个存储颜色的属性`number`。为了简洁起见，我们在这里将跳过它。要了解更多信息，你可以参考 GitHub 上的可用实现。

### `GameState`

更有趣的是我们整体的游戏状态。

```
import {Schema, type} from "@colyseus/schema";
import {getRandomBlock, Tetrolyso} from "./Tetrolyso";
import {Position} from "./Position";
import {Board} from "./Board";

export class GameState extends Schema {
    @type(Board)
    board: Board;

    @type(Tetrolyso)
    currentBlock: Tetrolyso;

    @type(Position)
    currentPosition: Position;

    @type(Tetrolyso)
    nextBlock: Tetrolyso;

    @type("number")
    clearedLines: number;

    @type("number")
    level: number;

    @type("number")
    totalPoints: number;

    constructor(rows: number = 20, cols: number = 10, initialLevel = 0) {
        super();
        this.board = new Board(rows, cols);
        this.currentBlock = getRandomBlock();
        this.currentPosition = new Position(0, 5);
        this.nextBlock = getRandomBlock();
        this.level = initialLevel;
        this.clearedLines = 0;
        this.totalPoints = 0;
    }
}

```

它由几个`number`属性组成。此外，它拥有几个[子模式属性](https://docs.colyseus.io/state/schema/#child-schema-properties "Child schema property docs")来组合整体状态。

使用这种嵌套的子状态类在建模状态时给了我们很大的灵活性。注释提供了一种简单且类型安全的方式来实现同步，嵌套的子模式允许我们分解状态，从而实现重用。

同样，如果你想继续，当前的标签是我们的[库](https://github.com/s1h-org/tetrolyseus "Tetrolyseus repo")中的`02-gamestate`。

```
git checkout tags/02-gamestate -b 02-gamestate

```

## 使用游戏状态:前端

现在，我们国家的第一份草案已经完成，让我们看看我们如何才能与它合作。我们将开始为我们的游戏构建一个前端，这将允许我们可视化我们的游戏状态。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

Colyseus 附带了一个 [JavaScript 客户端](https://docs.colyseus.io/getting-started/javascript-client/ "Colyseus JS client docs"):

```
npm i colyseus.js

```

我们不会使用任何前端框架，只使用普通的 HTML、CSS 和 TypeScript。我们将用来构建前端的另外两个东西是 [NES.css](https://nostalgic-css.github.io/NES.css/ "nes.css homepage") 和 [Parcel.js](https://parceljs.org "parcel.js homepage") 。

我们将通过 CDN 包含 NES，因此我们只需将包裹添加到我们的`devDependencies`。

```
npm i -D parcel

```

只够构建以下布局:

```
+----------------------------------------------------------+
|                                                          |
|  Title                                                   |
|                                                          |
+----------------------------------------------------------+
             +--------------------+ +------------+
             |                    | |            |
             |                    | | Score      |
             |                    | |            |
             |                    | +------------+
             |                    | +------------+
             |                    | |            |
             |                    | | Level      |
             |                    | |            |
             |      Playing       | +------------+
             |      Field         | +------------+
             |                    | |            |
             |                    | | Next Piece |
             |                    | |            |
             |                    | +------------+
             |                    |
             |                    |
             |                    |
             |                    |
             |                    |
             |                    |
             +--------------------+

```

我们布局的 HTML 表示如下:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Tetrolyseus</title>
    <link href="https://unpkg.com/[email protected]/css/nes.min.css" rel="stylesheet"/>
    <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">
    <link rel="stylesheet" type="text/css" href="index.css">
</head>
<body>
<div class="nes-container is-dark with-title">
    <p class="title">Tetrolyseus</p>
    <p>A cooperative approach to the famous blocks game.</p>
</div>
<div id="playingfield">
    <div id="board" class="nes-container is-rounded is-dark"></div>
    <div id="infobox">
        <div class="nes-container is-dark with-title">
            <p class="title">Score</p>
            <p id="score"></p>
        </div>
        <div class="nes-container is-dark with-title">
            <p class="title">Level</p>
            <p id="level"></p>
        </div>
        <div class="nes-container is-dark with-title">
            <p class="title">Next</p>
            <div id="preview"></div>
        </div>
    </div>
</div>
</body>
<script src="index.ts" type="application/javascript"></script>
</html>

```

### 连接到后端

让我们建立一个到后端的连接。

```
document.addEventListener('DOMContentLoaded', async () => {
    const client = new Client(process.env.TETROLYSEUS_SERVER || 'ws://localhost:2567');

    ...
});

```

一旦连接上，我们就可以加入或创建一个游戏室。

```
const room: Room<GameState> = await client.joinOrCreate<GameState>("tetrolyseus");

```

我们提供给 [`joinOrCreate`](https://docs.colyseus.io/client/client/#joinorcreate-roomname-string-options-any "joinOrCreate docs") 的名字必须是在后台定义的游戏房间之一。顾名思义，`joinOrCreate`要么加入一个现有的房间实例，要么创建一个新实例。除此之外，还可以显式地[创建](https://docs.colyseus.io/client/client/#create-roomname-string-options-any "create docs")或[加入](https://docs.colyseus.io/client/client/#join-roomname-string-options-any "join docs")一个房间。

作为回报，`joinOrCreate`提供了一个保存我们的`GameState`的[房间](https://docs.colyseus.io/client/room/#room-api-client-side "Room docs")实例，它让我们可以访问我们的`Board`、当前的`Tetrolyso`、当前的`Position`，等等——渲染我们的游戏所需的一切。

### 游戏渲染

现在我们已经可以访问当前的`GameState`，我们可以呈现我们的 UI 了。使用 [CSS 网格](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout "CSS Grid docs")和我们的`Board`状态，我们可以画出我们的游戏场。

```
const drawBoard = (board: Board): void => {
    const boardElement = queryBoardElement();
    const elementRect = boardElement.getBoundingClientRect();
    const blockHeight = Math.floor((elementRect.height - 32) / board.rows);
    boardElement.style.gridTemplateColumns = `repeat(${board.cols}, ${blockHeight}px)`;
    boardElement.style.gridTemplateRows = `repeat(${board.rows}, ${blockHeight}px)`;
    boardElement.style.height = "fit-content";
    boardElement.style.width = "fit-content";

    const boardPosition = queryByRowAndColumn(board);

    for (let row = 0; row < board.rows; ++row) {
        for (let col = 0; col < board.cols; ++col) {
            const cellDiv = document.createElement("div");
            cellDiv.id = `cell-r${row}-c${col}`
            cellDiv.style.background = `#${boardPosition(row, col).toString(16)}`;
            boardElement.append(cellDiv);
        }
    }
}

```

给定我们的二维网格，我们还可以显示当前的`Tetrolyso`。

```
const drawTetrolyso = (currentBlock: Tetrolyso, currentPosition: Position) => {
    const blockPosition = queryByRowAndColumn(currentBlock);

    for (let row = currentPosition.row; row < currentPosition.row + currentBlock.rows; ++row) {
        for (let col = currentPosition.col; col < currentPosition.col + currentBlock.cols; ++col) {
            if (blockPosition(row - currentPosition.row, col - currentPosition.col) !== 0) {
                const boardSquare = <HTMLDivElement>document.querySelector(`#cell-r${row}-c${col}`);
                boardSquare.style.background = `#${currentBlock.color.toString(16)}`;
                boardSquare.style.border = `1px solid black`;
            }
        }
    }
}

```

### 接收状态更新

到目前为止，我们能够在给定当前状态的情况下呈现我们的 UI。然而，为了让我们的游戏运行起来，我们必须在每次状态改变时重新渲染我们的 UI。

房间提供了某些我们可以附加回调的[事件](https://docs.colyseus.io/getting-started/javascript-client/#room-events "Room events docs")，因此我们可以将我们的渲染代码附加到`onStateChange`处理程序。

```
room.onStateChange((newState: GameState) => {
    clearBoard();
    clearPreview();
    drawBoard(newState.board);
    drawPreview(newState.nextBlock);
    drawTetrolyso(newState.currentBlock, newState.currentPosition);
    drawScore(newState.totalPoints);
    drawLevel(newState.level);
});

```

### 处理玩家输入

此时，你可能想知道我们什么时候实现一些游戏逻辑来移动我们的 Tetrolyso，检查碰撞，等等。长话短说，我们不会——至少在我们的前端不会。

我们的 UI 应该服务于一个目的:呈现我们的状态。状态操作应该发生在我们的后端。每当我们的一个玩家击出一个键，我们就向我们的后端发送一条信息，描述我们想要做什么，例如移动或旋转当前的方块。如果我们的游戏规则允许我们执行我们想要的动作，游戏状态将被更新，并且我们的前端将由于这种状态改变而重新呈现 UI。

```
document.addEventListener('keydown', (ev: KeyboardEvent) => {
    if (ev.code === "Space") {
        room.send("rotate", {});
    } else if (ev.code === "ArrowLeft") {
        room.send("move", LEFT);
    } else if (ev.code === "ArrowRight") {
        room.send("move", RIGHT);
    } else if (ev.code === "ArrowDown") {
        room.send("move", DOWN);
    }
});

```

[`room.send`](https://docs.colyseus.io/client/room/#send-type-message "Room#send docs") 允许我们将消息从我们的客户端传递到我们的服务器。我们的一个箭头键上的事件将指示我们的后端向左、向右或向下移动当前的 Tetrolyso。撞击空间会使其旋转。

### 前端总结

我们对游戏逻辑的声明性方法使我们的前端保持简单，并允许我们专注于我们想要实现的东西:呈现我们的游戏状态。

这里我们要添加的最后一件事是一个 npm 脚本来构建我们的前端。

```
"scripts": {
  "start:frontend": "parcel frontend/index.html"
},

```

当前前端状态可以在标签`03-frontend`中找到。

```
git checkout tags/03-frontend -b 03-frontend

```

## 处理游戏状态:后端

是时候开始我们的游戏后端了。但是在我们继续编写代码之前，让我们将现有的代码移动到一个名为`backend`的专用子文件夹中。

```
backend
├── TetrolyseusRoom.ts
└── index.ts

```

我们将通过`start:backend` npm 脚本启动我们的后端。

```
"scripts": {
  "start:backend": "ts-node backend/index.ts",
  "start:frontend": "parcel frontend/index.html"
},    

```

### 初始化状态

现在一切就绪，让我们进一步扩展我们的`TetrolyseusRoom`。因为这是一个有状态的房间，我们要做的第一件事就是初始化我们的状态。

```
import {Client, Room} from "colyseus";
import {GameState} from "../state/GameState";

export class TetrolyseusRoom extends Room<GameState> {
    onCreate(options: any) {
        this.setState(new GameState())
    }

    onJoin(client: Client, options: any) {
    }

    onLeave(client: Client, consented: boolean) {
    }

    onDispose() {
    }
}

```

到目前为止，我们没有太大的变化，但是如果我们同时启动后端和前端，我们应该会看到我们的游戏板。这将显示水平，分数，以及当前和下一个四重奏。一切都是基于我们的初始化状态呈现的。

### 得分

让我们按照[任天堂评分系统](https://tetris.fandom.com/wiki/Scoring "Nintendo scoring system")来计算我们的通关分数。

```
const baseScores: Map<number, number> = new Map<number, number>([
    [0, 0],
    [1, 40],
    [2, 100],
    [3, 300],
    [4, 1200]
]);

export const computeScoreForClearedLines = (clearedLines: number, level: number): number => {
    return baseScores.get(clearedLines) * (level + 1);
}

```

评分实施在`04-scoring`标记。

```
git checkout tags/04-scoring -b 04-scoring

```

### 检测碰撞

我们的块由一系列 0 和 1 以及行和列信息表示。可视化后，`Z`块如下所示。

```
+--------+
|110||001|
|011||011|
|000||010|
+--------+

```

由于它们的形状，一些块可能有空行或空列。当涉及到碰撞检测时，我们必须弥补这些空值。否则，我们将无法用完我们的板的所有空间。

实现这一点的简单方法是确定块超出电路板的偏移量，并检查是否有任何非零块元素位于该范围内。

```
   +-------------------------+
   |                         |
   |                         |
   |                         |
+-------+                    |
|00|1100|                    |
|00|1100|                    |
|00|1111|                    |
|00|1111|                    |
|00|1100|                    |
|00|1100|                    |
+-------+                    |
   |                         |

```

```
export const isLeftOutOfBounds = (board: Board, tetrolyso: Tetrolyso, position: Position): boolean => {
    if (position.col >= 0) {
        return false;
    }

    const blockElement = queryByRowAndColumn(tetrolyso);

    const offset = -position.col;
    for (let col = 0; col < offset; ++col) {
        for (let row = 0; row < tetrolyso.rows; ++row) {
            if (blockElement(row, col) !== 0) {
                return true;
            }
        }
    }
    return false;
}

```

相同的方案适用于电路板底部和右侧的碰撞检查。

检查当前块是否与棋盘上任何现有块冲突的过程非常相似。简单地检查棋盘和当前块之间重叠的非零元素来确定冲突。

```
export const collidesWithBoard = (board: Board, tetrolyso: Tetrolyso, position: Position): boolean => {
    const blockElement = queryByRowAndColumn(tetrolyso);
    const boardElement = queryByRowAndColumn(board);

    for (let boardRow = position.row; boardRow < position.row + tetrolyso.rows; ++boardRow) {
        for (let boardCol = position.col; boardCol < position.col + tetrolyso.cols; ++boardCol) {
            const blockRow = boardRow - position.row;
            const blockCol = boardCol - position.col;
            if (blockElement(blockRow, blockCol) !== 0 && boardElement(boardRow, boardCol) !== 0) {
                return true;
            }
        }
    }
    return false;
}

```

在`05-collision`标记完成的碰撞检测实现。

```
git checkout tags/05-collision -b 05-collision

```

### 游戏逻辑

直到现在，我们的游戏一直相当静态。我们看到的不是移动的积木，而是一个静止不动的积木。

在我们开始行动之前，我们必须定义一些游戏必须遵循的规则。换句话说，我们需要实现我们的游戏逻辑，包括以下步骤。

*   计算下落物体的下一个位置
*   检测碰撞并移动当前块或将其冻结在当前位置
*   确定已完成的行
*   更新分数
*   更新公告板(删除已完成的行，添加空行)
*   检查我们是否达到了下一个级别

我们房间中实现的游戏逻辑重用了来自`05-collision`的功能来更新状态。

```
detectCompletedLines() {
    let completedLines = [];
    for (let boardRow = this.state.board.rows - 1; boardRow >= 0; --boardRow) {
        if (isRowEmpty(this.state.board, boardRow)) {
            break;
        }

        if (isRowCompleted(this.state.board, boardRow)) {
            completedLines.push(boardRow);
        }
    }
    return completedLines;
}

updateBoard(completedLines: number[]) {
    for (let rowIdx = 0; rowIdx < completedLines.length; ++rowIdx) {
        deleteRowsFromBoard(this.state.board, completedLines[rowIdx] + rowIdx);
        addEmptyRowToBoard(this.state.board);
    }
}

dropNewTetrolyso() {
    this.state.currentPosition = new Position(
        0,
        5
    );
    this.state.currentBlock = this.state.nextBlock.clone();
    this.state.nextBlock = getRandomBlock();
}

moveOrFreezeTetrolyso(nextPosition: Position) {
    if (
        !isBottomOutOfBounds(this.state.board, this.state.currentBlock, nextPosition) &&
        !collidesWithBoard(this.state.board, this.state.currentBlock, nextPosition)
    ) {
        this.state.currentPosition = nextPosition;
    } else {
        freezeCurrentTetrolyso(this.state.board, this.state.currentBlock, this.state.currentPosition);
        this.dropNewTetrolyso();
        this.checkGameOver();
    }
}

```

在`06-game-logic`标记完整的游戏逻辑。

```
git checkout tags/06-game-logic -b 06-game-logic

```

### 游戏循环

现在我们已经设置好了游戏逻辑，让我们组装一个游戏循环来运行游戏！

我们的游戏循环执行我们在上一节中列出的所有步骤。

```
loopFunction = () => {
    const nextPosition = this.dropTetrolyso();
    this.moveOrFreezeTetrolyso(nextPosition);

    const completedLines = this.detectCompletedLines();
    this.updateClearedLines(completedLines);
    this.updateTotalPoints(completedLines);
    this.updateBoard(completedLines);
    this.checkNextLevel();
}

```

我们将为游戏时钟使用一个[延迟的](https://docs.colyseus.io/server/timing-events/#delayed "Delayed docs")实例。

```
gameLoop!: Delayed;

```

`onCreate`处理器将开始循环。

```
onCreate(options: any) {
    ...
    const loopInterval = 1000 / (this.state.level + 1);
    this.gameLoop = this.clock.setInterval(this.loopFunction, loopInterval);
    ...
}

```

这些方块最初会以每秒一行的速度下降，随着等级的提高速度会越来越快。如果我们到达下一级，我们重新开始我们的循环。

```
checkNextLevel() {
    const nextLevel = this.determineNextLevel();
    if (nextLevel > this.state.level) {
        this.state.level = nextLevel;
        this.gameLoop.clear();
        const loopInterval = 1000 / (this.state.level + 1);
        this.gameLoop = this.clock.setInterval(this.loopFunction, loopInterval);
    }
}

```

在`onCreate`中唯一缺少的是[消息处理器](https://docs.colyseus.io/server/room/#onmessage-type-callback "onMessage docs")。前端通过消息与后端通信。因此，如果我们希望能够旋转或移动我们的块，我们的后端必须相应地处理这些消息。

```
onCreate(options: any) {
    ...
    this.onMessage("rotate", (client, _) => {
        const rotatedBlock = this.state.currentBlock.rotate();
        const rotatedPosition = keepTetrolysoInsideBounds(this.state.board, rotatedBlock, this.state.currentPosition);
        if (!collidesWithBoard(this.state.board, rotatedBlock, rotatedPosition)) {
            this.state.currentBlock = rotatedBlock;
            this.state.currentPosition = rotatedPosition;
        }
    });
    this.onMessage("move", (client, message: Movement) => {
        const nextPosition = new Position(
            this.state.currentPosition.row + message.row,
            this.state.currentPosition.col + message.col
        );
        if (
            !isLeftOutOfBounds(this.state.board, this.state.currentBlock, nextPosition) &&
            !isRightOutOfBounds(this.state.board, this.state.currentBlock, nextPosition) &&
            !isBottomOutOfBounds(this.state.board, this.state.currentBlock, nextPosition) &&
            !collidesWithBoard(this.state.board, this.state.currentBlock, nextPosition)
        ) {
            this.state.currentPosition = nextPosition;
        }
    });
}

```

至此，我们应该可以玩一个 Tetrolyseus 的游戏了。如果我们多次打开前端，我们还可以从多个会话中移动和旋转块。

如果你想直接跳到这一点，你可以查看标签`07-game-loop`。

```
git checkout tags/07-game-loop -b 07-game-loop

```

## 让它成为多人游戏

随着我们的 Tetrolyseus 游戏开始运行，还有一个问题:什么是多人游戏？

Tetrolyesues 实现了一个多人模式，允许一个玩家只能移动一个方块，而另一个玩家只能旋转它。我们将保留当前玩家的列表，并为每个玩家分配一个玩家类型。

```
export enum PlayerType {
    MOVER,
    ROTATOR
}

export class Player {
    constructor(public readonly id: string, private _ready: boolean, private readonly _type: PlayerType) {
    }

    public get isReady(): boolean {
        return this._ready
    }
    public set isReady(isReady: boolean) {
        this._ready = isReady;
    }
    public isMover(): boolean {
        return this._type === PlayerType.MOVER;
    }
    public isRotator(): boolean {
        return this._type === PlayerType.ROTATOR;
    }
}

```

我们的房间里有一张玩家地图:

```
playerMap: Map<string, Player>;

```

这个映射在`onJoin`和`onLeave`处理程序中都使用。

```
onJoin(client: Client, options: any) {
    if (!this.playerMap.size) {
        const playerType = Math.random() >= 0.5 ? PlayerType.MOVER : PlayerType.ROTATOR;
        this.playerMap.set(client.id, new Player(client.id, false, playerType));
    } else {
        if (this.roomHasMover()) {
            this.playerMap.set(client.id, new Player(client.id, false, PlayerType.ROTATOR));
        } else {
            this.playerMap.set(client.id, new Player(client.id, false, PlayerType.MOVER));
        }
    }
}

onLeave(client: Client, consented: boolean) {
    this.playerMap.delete(client.id);
}

```

地图限制玩家在`onMessage`处理程序中的动作。

```
this.onMessage("move", (client, message: Movement) => {
    if (this.playerMap.has(client.id)) && this.playerMap.get(client.id).isMover()) {
        ...

```

```
this.onMessage("rotate", (client, _) => {
    if (this.playerMap.has(client.id) && this.playerMap.get(client.id).isRotator()) {
        ...

```

第一个加入的玩家被随机指定为`MOVER`或`ROTATOR`，下一个玩家被指定为另一个角色，依此类推。

## 准备好玩了吗？

至此，我们的游戏循环从创建我们的房间开始。这给第一个加入的玩家带来了一点问题，他只能移动或旋转一个方块。

为了解决这个问题，让我们给我们的`GameState`添加一个`running`标志。

```
@type("boolean")
running: boolean;

```

此外，我们将引入一种新的消息类型:`ReadyState`。

```
export interface ReadyState {
    isReady: boolean;
}

export const READY = {
    isReady: true
}

export const NOT_READY = {
    isReady: false
}

```

我们`ReadyState`的消息处理器将更新玩家的状态。一旦所有的角色都被分配，所有的玩家都准备好了，我们将开始游戏循环。

```
onCreate(options: any) {
    ...
    this.onMessage("ready", (client, message: ReadyState) => {
        if (this.playerMap.has(client.id)) {
            this.playerMap.get(client.id).isReady = message.isReady;
        }

        if (this.roomHasMover() && this.roomHasRotator() && this.allPlayersReady()) {
            this.state.running = true;
            this.startGameLoop();
        }
    });
}

```

前端将显示一个模态，提示玩家将自己设置为就绪。

```
<div class="nes-container is-dark with-title">
    <p class="title">Tetrolyseus</p>
    <p>A cooperative approach to the famous blocks game.</p>
</div>
<div id="ready-modal" class="nes-container is-rounded is-dark with-title">
    <p class="title">Ready to play?</p>
    <label>
        <input id="ready" type="radio" class="nes-radio is-dark" name="answer-dark" checked />
        <span>Yes</span>
    </label>

    <label>
        <input id="not-ready" type="radio" class="nes-radio is-dark" name="answer-dark" />
        <span>No</span>
    </label>
</div>
<div id="playingfield">
...

```

一个按钮点击发送相应的`ReadyState`消息到我们的后端。

```
document.addEventListener('DOMContentLoaded', async () => {
    ...

    const readyModal = queryReadyModal();
    const readyButton = queryReadyButton();
    const notReadyButton = queryNotReadyButton();

    readyButton.addEventListener("click", () => room.send("ready", READY));
    notReadyButton.addEventListener("click", () => room.send("ready", NOT_READY));

    room.onStateChange((newState: GameState) => {
        if (newState.running) {
            if (!(typeof document.onkeydown === "function")) {
                document.addEventListener('keydown', handleInput);
            }
            readyModal.style.display = "none";
            renderGame(newState);
        } else {
            document.removeEventListener('keydown', handleInput);
        }
    });
});

```

一旦游戏运行，模态将被隐藏，游戏开始！

如果你想马上查看游戏，使用标签`08-multiplayer`。

```
git checkout tags/08-multiplayer -b 08-multiplayer

```

## 准备发货了吗？

我们终于准备好推出我们的游戏了！让我们添加一些额外的脚本来创建一个应用程序包，以便于发布。

首先，我们将扩展我们的`package.json`。

```
"scripts": {
  ...
  "build:backend": "tsc -p tsconfig.json",
  "build:frontend": "parcel build frontend/index.html",
  "clean": "rimraf ./dist ./app",
  "bundle": "npm run clean && npm run build:backend && npm run build:frontend && ncp dist/ app/public"
  ...
  },

```

我们可以通过在`backend/index.ts`中添加以下配置来指示我们的后端 express 实例也服务于我们的前端。

```
const app = express()

const staticPath = join(__dirname, '../public');
console.log(`Using static path '${staticPath}'`);
app.use(express.static(staticPath));

app.use(cors());

```

运行`npm run bundle`会在`app`中创建一个应用包:

```
app
├── backend
├── messages
├── public
└── state

```

最后一个签出的标签是`09-app-bundle`。

```
git checkout tags/09-app-bundle -b 09-app-bundle

```

## 摘要

在本教程中，我们从头开始构建了一个全功能的多人游戏，而不用太担心网络。科利修斯真的让它远离我们的方式，让你完全专注于你的游戏。因为伟大的游戏性最终会让人们迷上游戏，所以这是构建在线多人游戏的一个非常好的解决方案。

### 你从这里去哪里？

除了我们在这里讨论的内容之外，科利塞斯还可以提供更多的内容。我们没有时间介绍的一些功能包括:

*   社交登录
*   有密码保护的房间
*   配置房间
*   处理退学/重新录取

合乎逻辑的下一步是添加一个高分列表。现在你有一个基本的多人游戏来建立和改进，天空的限制！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.