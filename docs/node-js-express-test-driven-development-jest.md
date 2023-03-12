# Node.js 使用 Jest 表达测试驱动开发

> 原文：<https://blog.logrocket.com/node-js-express-test-driven-development-jest/>

测试驱动开发，或 TDD，是工作场所中相当常见的编程风格，并且在许多情况下，对于某些项目是强制性的。幸运的是，当您理解过程背后的基本原理时，TDD 非常简单。

JavaScript 中也有很多用于后端测试的库和框架。其中最受欢迎的两个是 [Jest](https://jestjs.io/) 和 [Mocha](https://mochajs.org/) ，另外还有[茉莉](https://jasmine.github.io/)、[艾娃](https://github.com/avajs/ava)、[磁带](https://github.com/substack/tape)和 [QUnit](https://qunitjs.com/) 。

Node.js 现在有自己的内置测试运行器，自 18 版以来一直稳定。然而，它仍处于实验模式，因此很可能会随着时间的推移而改变。

在本文中，我们将讨论如何使用新的 Node.js 测试运行器进行一些基本测试，以及使用 Jest 测试不同的端点。

## 后端测试

首先，让我们开始使用 Node.js 进行后端测试。

## 使用 Node.js 测试运行器测试后端代码

如果您想要使用内置的 Node.js 测试运行器，您需要导入下面的库，并按照 [Node.js 文档运行测试](https://nodejs.org/api/test.html#test-runner):

```
// JavaScript ESM Modules syntax
import test from 'node:test';

// JavaScript CommonJS syntax
const test = require('node:test');

```

当然，你还需要确定你用的是 v18 或者更高版本。您可以使用`node -v`在命令行中检查您当前运行的版本。

首先，让我们看看使用内置的 Node.js 测试运行器进行测试是什么样的。

### 项目设置

打开您的 BASH 应用程序并`cd`到您选择的目录。运行以下命令来搭建您的项目:

```
mkdir node-backend-testing
cd node-backend-testing
npm init -y
touch index.js index.test.js

```

接下来，在代码编辑器中打开新创建的项目，并将代码添加到它们相关的文件中。

下面是文件`index.js`的代码:

```
const calcAge = (dob) => {
    const digits = {
        year: 'numeric',
    };

    const year = new Date().toLocaleDateString('en-US', digits);

    console.log(year);

    return year - dob;
};

const createBox = (x, y) => {
    return x * y;
};

const canDrive = () => {
    const age = 18;

    if (age >= 18) {
        return 'Full Driving Licence';
    } else {
        return 'Provisional License';
    }
};

const powerLevel = () => {
    const power = 9001;

    if (power > 9000) {
        return true;
    } else {
        return false;
    }
};

const workSchedule = (employeeOne, employeeTwo) => {
    return employeeOne + employeeTwo;
};

module.exports = {
    calcAge,

    createBox,

    canDrive,

    powerLevel,

    workSchedule,
};

```

接下来，我们有了`index.test.js`的代码:

```
const test = require('node:test');

const assert = require('assert/strict');

const {
  calcAge,
  createBox,
  canDrive,
  powerLevel,
  workSchedule,
} = require('./index');

// Calculates how old someone is and depending on the year this test could pass or fail

test('calculates age', () => {
  return assert.equal(calcAge(2000), 22);
});

// Creates a box with an equal height and width

test('creates a box', async (t) => {
  await t.test('creates a small box', () => {
    assert.equal(createBox(10, 10), 100);
  });

  await t.test('creates a large box', () => {
    assert.equal(createBox(50, 50), 2500);
  });
});

// Checks to see whether or not the person has a full driving licence

test('checks license', () => {
  return assert.match(`${canDrive()}`, /Full Driving Licence/);
});

// Confirms that the person has a power level that is over 9000!

test('confirms power level', () => {
  return assert.ok(powerLevel());
});

// Checks to see if the employees have the same amount of shift work days in a week

test('employees have an equal number of work days', () => {
  const employeeOne = ['Monday', 'Tuesday', 'Wednesday,', 'Thursday'];

  const employeeTwo = ['Friday', 'Saturday', 'Sunday,', 'Monday'];

  return assert.equal(workSchedule(employeeOne.length, employeeTwo.length), 8);
});

```

最后，为文件`package.json`添加以下运行脚本:

```
"scripts": {

"test": "node index.test.js"

},

```

测试脚本运行节点测试运行程序，然后运行`index.test.js`中的测试。

### 运行节点测试

您只需要从根文件夹中运行一个命令。同样，您需要使用 Node v18 或更高版本才能工作。

运行下面的命令，您应该看到控制台中通过了五个测试:

```
npm run test

```

您可以试验一下`index.js`和`index.test.js`文件中的代码，看看测试是通过还是失败。如果您查看控制台中的测试错误，您就会知道失败的原因。

下面我举几个例子。

#### 计算年龄测试

要计算用户的年龄，请使用当前年份减去其出生年份。参见下面`index.test.js`文件中的例子:

```
test('calculates age', () => {
  return assert.equal(calcAge(2000), 21);
});

```

要查看测试失败，请输入不正确的年龄，如`21`。在本例中，该函数预期返回值为`22`，因此数字`21`会导致测试失败。

#### 创建一个箱式测试

该测试期望方程`10 x 10`和`50 x 50`分别为`100`和`2500`的答案。输入不符合正确输出的值，以查看测试是否失败。

```
test('creates a box', async (t) => {
  await t.test('creates a small box', () => {
    assert.equal(createBox(10, 30), 100);
  });

  await t.test('creates a large box', () => {
    assert.equal(createBox(50, 20), 2500);
  });
});

```

#### 检查许可证测试

这项测试检查一个人是否有有效的驾驶执照。将`index.js`文件的`canDrive`函数中的年龄更改为小于`18`的值。测试将会失败。

```
const canDrive = () => {
  const age = 17;

  if (age >= 18) {
    return 'Full Driving Licence';
  } else {
    return 'Provisional License';
  }
};

```

#### 确认功率水平测试

这个测试检验一个人是否有超过 9000 的异能水平(你抓到龙珠 Z 参考了吗？😁)

将`index.js`文件的`powerLevel`功能内的功率水平更改为小于`9000`。测试将会失败。

```
const powerLevel = () => {
  const power = 5000;

  if (power > 9000) {
    return true;
  } else {
    return false;
  }
};

```

#### 相同工作日数测试

此测试检查员工的工作天数是否相等。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

两名员工目前每人工作四天，这样我们总共工作八天。要看到测试失败，只需在数组中输入或删除几天，这样它们就不再是相同的长度。

```
test('employees have an equal number of work days', () => {
  const employeeOne = ['Monday', 'Tuesday', 'Wednesday,', 'Thursday'];

  const employeeTwo = ['Friday', 'Saturday'];

  return assert.equal(workSchedule(employeeOne.length, employeeTwo.length), 8);
});

```

## 使用 Jest 测试后端代码

现在，让我们使用 Jest 测试库做一些后端测试。

### 项目设置

打开您的 BASH 应用程序并`cd`到您喜欢的目录。运行以下命令来搭建您的项目:

```
mkdir jest-backend-testing
cd jest-backend-testing
npm init -y
npm i express http-errors jest nodemon supertest
mkdir routes
touch routes/products.js
touch app.js app.test.js server.js

```

现在，在代码编辑器中打开项目，并将下面的代码添加到相应的文件中。

下面是文件`routes/product.js`的代码:

```
const express = require('express');

const router = express.Router();

const createError = require('http-errors');

// Products Array

const products = [{ id: '1', name: 'Playstation 5', inStock: false }];

// GET / => array of items

router.get('/', (req, res) => {
  res.json(products);
});

// GET / => items by ID

router.get('/:id', (req, res, next) => {
  const product = products.find(
    (product) => product.id === String(req.params.id)
  );

  // GET /id => 404 if item not found

  if (!product) {
    return next(createError(404, 'Not Found'));
  }

  res.json(product);
});

router.post('/', (req, res, next) => {
  const { body } = req;

  if (typeof body.name !== 'string') {
    return next(createError(400, 'Validation Error'));
  }

  const newProduct = {
    id: '1',

    name: body.name,

    inStock: false,
  };

  products.push(newProduct);

  res.status(201).json(newProduct);
});

module.exports = router;

```

接下来，文件`app.js`的代码:

```
const express = require('express');

const productsRoute = require('./routes/products');

const app = express();

app.use(express.urlencoded({ extended: false }));

app.use(express.json());

app.use('/', productsRoute);

module.exports = app;

```

下面是文件`app.test.js`的代码:

```
const request = require('supertest');

const app = require('./app');

describe('GET /', () => {
  it('GET / => array of items', () => {
    return request(app)
      .get('/')

      .expect('Content-Type', /json/)

      .expect(200)

      .then((response) => {
        expect(response.body).toEqual(
          expect.arrayContaining([
            expect.objectContaining({
              id: expect.any(String),

              name: expect.any(String),

              inStock: expect.any(Boolean),
            }),
          ])
        );
      });
  });

  it('GET / => items by ID', () => {
    return request(app)
      .get('/1')

      .expect('Content-Type', /json/)

      .expect(200)

      .then((response) => {
        expect(response.body).toEqual(
          expect.objectContaining({
            id: expect.any(String),

            name: expect.any(String),

            inStock: expect.any(Boolean),
          })
        );
      });
  });

  it('GET /id => 404 if item not found', () => {
    return request(app).get('/10000000000').expect(404);
  });

  it('POST / => create NEW item', () => {
    return (
      request(app)
        .post('/')

        // Item send code

        .send({
          name: 'Xbox Series X',
        })

        .expect('Content-Type', /json/)

        .expect(201)

        .then((response) => {
          expect(response.body).toEqual(
            expect.objectContaining({
              name: 'Xbox Series X',

              inStock: false,
            })
          );
        })
    );
  });

  it('POST / => item name correct data type check', () => {
    return request(app).post('/').send({ name: 123456789 }).expect(400);
  });
});

```

快到了！下面是`server.js`文件的代码:

```
const app = require('./app');

const port = process.env.PORT || 3000;

app.listen(port, () =>
  console.log(`Server running on port ${port}, http://localhost:${port}`)
);

```

最后，将这些运行脚本添加到您的`package.json`文件中。

```
"scripts": {

"start": "node server.js",

"dev": "nodemon server.js",

"test": "jest --watchAll"

},

```

`start`脚本使用 node 运行服务器文件。

`dev`脚本使用`nodemon`运行服务器文件，支持自动重载。

`test`脚本运行 test runner Jest，该 Jest 自动监视文件更改。

### 运行 Jest 测试

是时候启动应用程序并测试 runner 了。在不同的选项卡或窗口中运行下面的命令，这样一个脚本运行开发服务器，另一个运行 Jest 测试运行程序。

服务器运行在 [http://localhost:3000/](http://localhost:3000/) :

```
npm run dev

npm run test

```

您现在应该已经运行了`dev`服务器。Jest 测试运行程序也应该运行五个通过的测试。让我们看一下每个测试，你可以看到他们通过或失败。

测试与标题同名，所以在文件中应该很容易找到。

#### 项目阵列测试

这个测试检查是否返回了一个对象数组。要看到它失败，打开`products.js`文件，用下面的代码替换顶部的路线:

```
router.get('/', (req, res) => {
  res.send('products');
});

```

您应该得到错误`expected Content-Type" matching /json/, got "text/html; charset=utf-8`并且测试应该失败。

现在尝试将它改为下面的代码:

```
router.get('/', (req, res) => {
  res.json('products');
});

```

您应该会得到这个错误`Expected: ArrayContaining [ObjectContaining {"id": Any<String>, "inStock": Any<Boolean>, "name": Any<String>}]`。

#### 通过`id`测试的项目

该测试检查返回的项目是否带有正确的`id`。将`products.js`中文件顶部的`products`数组更改为一个数字(而不是字符串)的`id`来查看它是否失败:

```
const products = [{ id: 1, name: 'Playstation 5', inStock: false }];

```

您应该得到这个错误:`expected "Content-Type" matching /json/, got "text/html; charset=utf-8"`。

#### 未找到项目测试

该测试检查是否有项目找不到。要看到它失败，将`404 error`代码更改为`products.js`文件中的其他代码，如下例所示:

```
if (!product) {
  return next(createError(400, 'Not Found'));
}

```

您应该得到错误:`expected 404 "Not Found", got 400 "Bad Request"`。

#### 正确数据测试

该测试检查对象是否具有正确的数据和数据类型。要看到它失败，打开`app.test.js`文件，用下面的代码替换发送代码:

```
.send({

name: 'Nintendo Switch',

})

```

您应该会看到以下错误:

```
 - Expected  - 2
    + Received  + 3

    - ObjectContaining {
    + Object {
    +   "id": "1",
        "inStock": false,
    -   "name": "Xbox Series X",
    +   "name": "Nintendo Switch",

```

#### 正确数据类型测试

该测试检查 name 变量的数据类型是否正确。只有在检测到字符串时，它才会失败。要查看测试是否失败，打开`app.test.js`文件，滚动到底部，将数字改为如下字符串:

```
return request(app).post('/').send({ name: '123456789' }).expect(400);

```

您应该得到这个错误:`expected 400 "Bad Request", got 201 "Created"`。

## 笑话 28:新功能

Jest 28 于 2022 年 4 月发布，并带来了许多新功能。最受欢迎的功能之一叫做[s](https://jestjs.io/docs/cli#--shard)哈丁。本质上，分片允许您将测试套件分割成不同的分片来运行套件测试的一部分。

例如，要运行三分之一的测试，可以使用下面的命令:

```
jest --shard=1/3
jest --shard=2/3
jest --shard=3/3

```

## 结论

仅此而已！我们刚刚介绍了使用 Node 和 Jest 在后端进行测试。我们所涵盖的只是测试的基础，还有很多东西需要学习！你可以在这里查看关于 [Jest](https://jestjs.io/) 的官方文档。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.