# 用 Node.js 和 Socket 构建实时定位 app。超正析象管(Image Orthicon)

> 原文：<https://blog.logrocket.com/building-real-time-location-app-node-js-socket-io/>

插座。IO 提供 web 客户端和 Node.js 服务器之间的实时通信。对于今天的许多用例来说，开发人员需要不断地实时更新他们应用程序的信息，这就需要使用一个双向通信工具来保持数据即时更新。

在本文中，我们将了解如何使用 Socket。使用 Node.js 为您的应用程序提供实时数据通信，就像这样的用例。

*向前跳转:*

## REST API 与 WebSockets

当我们想要检索资源并且不需要持续更新时，传统的 REST APIs 最有用。

如果你看一个加密交易，例如，当我们出价购买一枚硬币时，出价不太可能经常改变，所以我们可以使用传统的 REST API 精确地建模这种行为。

然而，硬币本身的实际价格是非常不稳定的，因为它像任何资产一样对市场趋势做出反应。为了获得最新的价格，我们需要发起一个请求，并且当我们的响应到达时，它很有可能再次改变！

在这样的场景中，我们需要在资产价格变化时得到通知，这正是 WebSockets 的亮点。

用于构建传统 REST APIs 的资源是高度可缓存的，因为它们很少更新。与此同时，WebSockets 并没有从缓存中获得太多好处，因为这可能会对性能产生负面影响。

有大量的指南强调了传统 REST APIs 和 WebSockets 的不同用例。

在本指南中，我们将使用 Node.js 和 Socket 构建一个实时的位置共享应用程序。IO 是我们的使用案例。

以下是我们将使用的技术:

*   **我们应用服务器的 Node.js/Express:**
*   **插座。IO:** 在幕后实现 WebSockets
*   **Postgres:** 存储用户位置数据的首选数据库
*   Postgis: 这个扩展使得处理数据库中的位置成为可能，并提供了额外的功能，比如计算位置周围的距离

## 设置 Express.js 服务器

我们将从设置一个 Express 应用程序开始。

为此，我修改了我们将使用的样板文件。让我们按照这些说明开始吧:

1.  克隆这个 GitHub 库
2.  `cd socket-location/`
3.  `npm install`
4.  最后，在根目录下创建一个`.env`文件，并复制`env.`示例文件的内容。为了获得这些值，我们实际上需要建立一个本地数据库，或者使用在线 Postgres 数据库平台，如 [Elephant](https://www.elephantsql.com/) 或 Heroku。访问平台并免费创建一个数据库，获取 URL，填写凭证，并启动应用程序

## 使用套接字设置 WebSockets。超正析象管(Image Orthicon)

来设置套接字。对于我们现有的 starter 文件，使用 JavaScript 提升对我们来说很重要，这使我们能够跨不同的文件使用 socket 实例。

我们将从安装 Socket 开始。超正析象管(Image Orthicon)

```
npm install socket.io

```

我们需要做的下一件事是将它与我们的 express 应用程序集成。在项目目录的根目录下打开`app.js`文件，按如下方式编辑:

```
const express = require("express");
const { createServer } = require("http");
const { Server } = require("socket.io");
const { initializeRoutes } = require("./routes");

let app = express();
const port = 3000;
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

app = initializeRoutes(app);
app.get("/", (req, res) => {
  res.status(200).send({
    success: true,
    message: "welcome to the beginning of greatness",
  });
});

const httpServer = createServer(app);
const io = new Server(httpServer, {
  cors: {
    origin: "*",
    methods: ["GET", "POST"],
  },
});

io.on("connection", (socket) => {
  console.log("We are live and connected");
  console.log(socket.id);
});

httpServer.listen(port, () => {
  console.log(`Example app listening on port ${port}`);
});

```

我们已经成功地将我们的服务器与套接字连接起来；现在我们需要连接我们的客户端来响应这个连接，这样就可以有来回的通信。

我们的客户端可以是一个 web 应用程序、一个移动应用程序或任何可以配置为使用 WebSockets 的设备。

为了简洁起见，我们将在这个应用程序中用 POSTMAN 模拟客户端行为。

为此，请执行以下操作:

1.  打开 Postman，点击左上角的“新建”按钮
2.  然后单击弹出窗口上的“Websocket 请求”按钮
3.  应该有文本显示“raw”——使用下拉菜单，将其更改为“Socket”。木卫一"
4.  将您的连接字符串粘贴到选项卡上—在本例中是 localhost:3000 —并单击 connect 按钮

您应该看到控制台打印出一个唯一的 ID，并且 Postman 选项卡应该显示“已连接”。

这里有一段视频展示了我们的现状，并简要回顾了我们迄今为止所做的工作。

> 没有描述

更多来自 LogRocket 的精彩文章:

* * *

### 认证和授权

* * *

## 现在我们已经成功地连接到我们的插座。例如，我们需要提供某种形式的身份验证和授权来阻止不需要的用户。当您考虑到我们允许来自任何客户端的连接时，情况尤其如此，正如连接时 CORS 选项中的`*`所指定的。

为此，我们将使用这个中间件:

首先，我们检查请求头中是否提供了令牌。在允许用户连接之前，我们还会检查它是否是有效的令牌，以及用户是否存在于我们的数据库中—这确保了只有经过身份验证的用户才能访问。

```
io.use((socket, next) => {
  if (socket.handshake.headers.auth) {
    const { auth } = socket.handshake.headers;
    const token = auth.split(" ")[1];
    jwt.verify(token, process.env.JWT_SECRET_KEY, async (err, decodedToken) => {
      if (err) {
        throw new Error("Authentication error, Invalid Token supplied");
      }
      const theUser = await db.User.findByPk(decodedToken.id);
      if (!theUser)
        throw new Error(
          "Invalid Email or Password, Kindly contact the admin if this is an anomaly"
        );
      socket.theUser = theUser;
      return next();
    });
  } else {
    throw new Error("Authentication error, Please provide a token");
  }
});

```

用户之间共享位置

## 为了在用户之间共享位置，我们将使用套接字连接。我们还需要准备数据库来接受几何对象，为此我们将在数据库上安装 PostGIS 扩展。在客户端，我们将使用 JavaScript 地理定位 API。

首先，我们将在数据库上安装 PostGIS 扩展。

在项目的根目录下运行以下命令:

然后，打开生成的迁移文件并粘贴以下内容:

```
 npx sequelize-cli migration:generate --name install-postgis

```

当我们运行迁移时，上面的代码片段将在我们的数据库实例上安装`postgis`扩展。

```
"use strict";
/** @type {import('sequelize-cli').Migration} */
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.sequelize.query("CREATE EXTENSION postgis;");
  },
  down: (queryInterface, Sequelize) => {
    return queryInterface.sequelize.query("DROP EXTENSION postgis CASCADE;");
  },
};

```

(注意:在撰写本文时，由于权限问题，我无法成功运行迁移来在 ElephantSQL 提供的 Postgres 实例上安装 PostGIS 扩展，但是我能够在 Heroku 实例上成功运行相同的操作)

```
npm run migrate

```

接下来，我们需要准备数据库来存储用户位置信息，为此，我们将创建另一个迁移文件:

删除内容并粘贴以下内容:

```
npx sequelize-cli model:generate --name Geolocation --attributes socketID:string,location:string

```

…然后我们再次运行迁移:

```
"use strict";

/** @type {import('sequelize-cli').Migration} */

module.exports = {
  async up(queryInterface, Sequelize) {
    await queryInterface.createTable("Geolocations", {
      id: {
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
        type: Sequelize.INTEGER,
        references: {
          model: "Users",
          key: "id",
          as: "id",
        },
      },
      socketID: {
        type: Sequelize.STRING,
        unique: true,
      },
      location: {
        type: Sequelize.GEOMETRY,
      },
      online: {
        type: Sequelize.BOOLEAN,
      },
      trackerID: {
        type: Sequelize.INTEGER,
        references: {
          model: "Users",
          key: "id",
          as: "id",
        },
      },
      createdAt: {
        allowNull: false,
        type: Sequelize.DATE,
      },
      updatedAt: {
        allowNull: false,
        type: Sequelize.DATE,
      },
    });
  },
  async down(queryInterface, Sequelize) {
    await queryInterface.dropTable("Geolocations");
  },
};

```

我们还想在两个模型之间创建一个关系，所以在 models/user.js 中，添加以下代码。这里，我们在用户模型和地理位置模型之间创建了一对一的关系。

```
npm run migrate

```

然后，在 models/geolocation.js 中添加:

```
...
static associate(models) {
      // define association here
      this.hasOne(models.Geolocation, { foreignKey: "id" });
    }
...

```

接下来，我们需要构建实际的路由来促进连接。为此，进入`routes`并创建`/track/track.js`。然后，添加以下内容:

```
.... 
static associate(models) {
      // define association here
      this.belongsTo(models.User, { foreignKey: "id" });
    }
....

```

此外，我们还想确保我们每次注册用户时，都为他们创建一个地理位置对象。

```
const { Router } = require("express");
const db = require("../../models");
const { handleJwt } = require("../../utils/handleJwt");
const trackerRouter = Router();
trackerRouter.post(
  "/book-ride",
  handleJwt.verifyToken,
  async (req, res, next) => {
    // search for user that is offline
    // assign the booker id to the
    const {
      user,
      body: { location },
    } = req;
    //returns the first user that meets the criteria
    const user2 = await db.User.findOne({
      where: { role: "driver" },
    });
    db.Geolocation.update(
      {
        trackerID: user2.id,
        online: true,
      },
      { where: { id: user.id }, returning: true }
    );
    db.Geolocation.update(
      {
        trackerID: user.id,
        location: {
          type: "Point",
          coordinates: [location.longitude, location.latitude],
        },
        online: true,
      },
      { where: { id: user2.id }, returning: true }
    );
    if (!user2)
      return res.status(404).send({
        success: false,
        message,
      });
    return res.status(200).send({
      success: true,
      message: "You have successfully been assigned a driver",
    });
  }
);
module.exports = { route: trackerRouter, name: "track" };

```

在 routes/user/user.js 文件中创建用户时，在发送响应之前添加以下内容:

如果您现在打开 POSTMAN 并发送请求，您应该得到响应，您已经成功地被分配了一个驱动程序。

```
....
await db.Geolocation.create({ id: user.id });
const token = handleJwt.signToken(user.dataValues);
.....

```

发出和接收事件

## 我们将发送和接收表示用户地理位置的对象，这将通过地理位置 API 在 web 上完成。

它使用起来非常简单，对于绝大多数现代消费设备(如智能手机和笔记本电脑)来说非常准确，因为它们内置了 GPS。

当我们注销捕获此信息的对象时，我们有:

如果你用手机访问 [w3schools](https://www.w3schools.com/js/js_api_geolocation.asp) ，边走边看经纬度信息，你会发现你的位置在不断变化，实时反映你所处的位置。

```
position: {
          coords: {
                       accuracy: 7299.612787273156
                       altitude: null
                       altitudeAccuracy: null
                       heading: null
                       latitude: 6.5568768
                       longitude: 3.3488896
                       speed: null
           },
        timestamp: 1665474267691
}

```

这是因为当你移动的时候，你的新位置信息会被触发！在位置对象中还捕获了其他信息，包括速度和高度。

现在，让我们创建套接字处理程序，它将实际发出并监听用户和驱动程序之间的这些事件。

在我们的 app.js 中，编辑下面一行，如下所示:

然后，我们将在应用程序的根目录下创建一个套接字目录，并添加以下文件:

```
const { onConnection } = require("./socket");

....
io.on("connection", onConnection(io));
....

```

`driversocket.js`

### 上面的代码处理用驱动程序位置更新数据库，并将其广播给监听用户。

```
const { updateDbWithNewLocation } = require("./helpers");
const db = require("../models");
const hoistedIODriver = (io, socket) => {
  return async function driverLocation(payload) {
    console.log(`driver-move event has been received with ${payload} 🐥🥶`);
    const isOnline = await db.Geolocation.findByPk(payload.id);
    if (isOnline.dataValues.online) {
      const recipient = await updateDbWithNewLocation(payload, isOnline);
      if (recipient.trackerID) {
        const deliverTo = await db.Geolocation.findOne({
          where: { trackerID: recipient.trackerID },
        });
        const { socketID } = deliverTo.dataValues;
        io.to(socketID).emit("driver:move", {
          location: recipient.location,
        });
      }
    }
  };
};
module.exports = { hoistedIODriver };

```

`usersocket.js`

### 上面的代码处理用用户位置更新数据库，并将其广播给监听驱动程序。

```
const { updateDbWithNewLocation } = require("./helpers");
const db = require("../models");
const hoistedIOUser = (io, socket) => {
  return async function driverLocation(payload) {
    console.log(
      `user-move event has been received with ${JSON.stringify(payload)} 🍅🍋`
    );
    const isOnline = await db.Geolocation.findByPk(payload.id);
    if (isOnline.dataValues.online) {
      const recipient = await updateDbWithNewLocation(payload, isOnline);
      if (recipient.trackerID) {
        const deliverTo = await db.Geolocation.findOne({
          where: { trackerID: recipient.trackerID },
        });
        const { socketID } = deliverTo.dataValues;
        io.to(socketID).emit("user:move", {
          location: recipient.location,
        });
      }
    }
  };
};
module.exports = { hoistedIOUser };

```

`index.js`

### …最后:

```
const { hoistedIODriver } = require("./driversocket");
const { hoistedIOUser } = require("./usersocket");
const configureSockets = (io, socket) => {
  return {
    driverLocation: hoistedIODriver(io),
    userLocation: hoistedIOUser(io),
  };
};
const onConnection = (io) => (socket) => {
  const { userLocation, driverLocation } = configureSockets(io, socket);
  socket.on("user-move", userLocation);
  socket.on("driver-move", driverLocation);
};
module.exports = { onConnection };

```

`helpers.js`

### 要获得工作代码示例，请访问 [GitHub 库](https://github.com/gbols/socket-location.git)并在分支之间克隆和导航。

```
const db = require("../models");
const updateDbWithNewLocation = async (payload, oldGeoLocationInfo) => {
  const { id, socketID } = payload;
  const [, [newLocation]] = await db.Geolocation.update(
    {
      online: oldGeoLocationInfo.online,
      socketID,
      trackerID: oldGeoLocationInfo.trackerID,
      location: {
        type: "Point",
        coordinates: [payload.coords.longitude, payload.coords.latitude],
      },
    },
    { where: { id }, returning: true }
  );
  return newLocation;
};
module.exports = { updateDbWithNewLocation };

```

好吧，让我们兜一圈，看看这是怎么回事！

没有描述

> No Description

结论

## 感谢您跟随本教程学习 Node.js 和 Socket 的使用。IO 打造实时定位 app。

我们能够演示如何使用基本但常见的工具，开始实现带有 WebSockets 的位置跟踪应用程序的功能。

200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

## 部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

.

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

.

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.