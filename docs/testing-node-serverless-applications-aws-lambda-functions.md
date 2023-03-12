# 测试节点无服务器应用程序— AWS Lambda 函数 LogRocket 博客

> 原文：<https://blog.logrocket.com/testing-node-serverless-applications-aws-lambda-functions/>

我们都知道测试对于编写可维护的、高质量的代码是至关重要的。实现它们从来都不容易，但是它是开发过程中的一个重要部分。

无服务器架构的兴起带来了新的挑战。我们现在拥有了在我们无法控制的环境中运行的功能。有很多方法可以模拟云环境，但是这足够可靠吗？

在本文中，我们将讨论几种简化测试无服务器应用程序的方法。我们将重点关注 AWS，它是最受欢迎的云提供商之一，我们将用 Node.js 编写代码，因为它是无服务器应用程序最常用的语言之一。话虽如此，我们在这里讨论的一切都可以应用于其他云提供商和编程语言。

## 什么是单元测试、集成测试和端到端测试？

一般来说，有三种类型的测试:

1.  **单元** —测试单个、孤立的逻辑块
2.  **集成** —测试两个或多个单元之间的合同
3.  **端到端**——运行涵盖所有内容的完整测试

这三种测试之间有许多不同之处，包括学习曲线、所需资源和减少 bug 的有效性。例如，单元测试是最便宜的选择，但是你通常会得到你所付出的。另一方面，端到端测试虽然更昂贵，但通常是最有效的方法。

理论上，您应该有许多许多的单元测试、几个集成测试和几个端到端测试——至少，这是标准应用程序公认的最佳实践。然而，对于无服务器应用，我们倾向于编写更多的端到端测试，避免单元和集成测试，因为执行环境不在我们的控制范围内。

在我看来，有了正确的代码结构和设计，就有可能实现稳定的代码质量和高度的信心，同时保持适当比例的测试类型。为了演示，我将使用一个小而方便的 Lambda 函数作为例子。

现在让我们深入研究代码！

## 测试无服务器应用程序

假设我们有一个实现 Lambda 函数的任务，它将:

*   接收某些参数，比如来自 SQS 队列(Amazon 的简单队列服务)
*   根据这些参数从 S3 存储桶(亚马逊的文件存储服务)中获取一个图像
*   缩小图像的大小，如果需要的话，将其更改为不同的格式
*   将生成的图像上传到同一个 S3 桶

这是 Lambda 函数的一个相当常见的用例。记住，要编写好的测试，你必须首先编写可测试的代码和函数。因此，我将向您展示实现和测试。

编写无服务器函数的诀窍是检测函数与外界通信的所有地方，并将其抽象出来，这样就可以用一些廉价的单元测试来孤立地测试这些地方。我们将这些抽象称为适配器。

让我们回顾一些基础知识，以帮助确定我们的项目需要哪些适配器:

*   该函数以函数参数的形式接收数据/事件——我们称之为`EventParser`
*   该函数需要获取文件并上传到 S3——我们称该适配器为`FileService`

在某种程度上，适配器是用于 I/O 的。现在，除了向外部世界发送和从外部世界接收数据之外，我们还有一些逻辑要在我们的功能中实现。核心逻辑——图像的缩小和重新格式化——将在`image-reducer.js`内部。

适配器和`image-reducer.js`是逻辑隔离的，因此适合单元测试。完成后，我们需要根据我们的业务需求将它们连接起来。我们将在`main.js`文件中这样做，这适合于集成测试(稍后我们将演示)。

文件夹结构如下所示:

```
image-reducer-service/
  adapters/          - abstractions for sockets/file system etc. 
    event-parser.js
    file-service.js
  utils/             - regular utils functions based on our needs 
  tests/             - all of the tests
  image-reducer.js   - core lambda logic
  main.js            - connects adapters and core logic, good for integration test
  index.js           - entry file for serverless app
  serverless.yml
  package.json

```

`main.js`文件将导出一个包装函数，该函数将通过依赖注入来接收所需的每个适配器和核心逻辑函数。这样，集成测试很容易实现。

这是开始时的样子:

```
// main.js
exports.imageReducerService = async (event, FileService, ImageReducer) => {
    const executionId = generateRandomId();
    try {
        console.log(`Started imageReducerService id: ${executionId}`);
        /*----------------
        Connect everything here
        -----------------*/
        console.log(`Finished imageReducerService id: ${executionId}`);
    }
    catch (error) {
        console.error(`Thrown imageReducerService id: ${executionId}`);
        throw error;
    }
};

```

这个 main 函数在`index.js`文件中是必需的，它包含将在 AWS 上运行的实际 Lambda 函数，并将所有内容注入到我们的 main 函数中。

```
// index.js
const { EventParser, FileService } = require('./adapters');
const ImageReducer = require('./image-reducer.js');
const ImageReducerService = require('./main.js');

exports.handler = (sqsMessage) =>
    ImageReducerService(EventParser.parse(sqsMessage), FileService, ImageReducer);

```

## 单元测试

让我们为第一个适配器`EventParser`编写代码和测试。这个适配器的目的是接收一个事件并对其进行净化，以便我们的主函数总是获得一组标准的属性。这在 AWS 上可能特别有趣，因为 Lambda 函数可以连接到许多源(SQS、SNS、S3 等)。)，并且每个源都有自己的事件模式。

`EventParser`可以用来处理其中的每一个并输出一个标准化的事件。目前，我们只通过 SQS 队列接收事件。它看起来是这样的:

```
// adapters/event-parser.js
const Joi = require('@hapi/joi');

const eventSchema = Joi.object({
    bucket: Joi.string().required(),
    key: Joi.string().required(),
    format: Joi.string().valid('png', 'webp', 'jpeg').default('png')
});
const extractEvent = (sqsMessage) => sqsMessage.Records[0].body;

exports.parse = (sqsMessage) => {
    const eventObject = extractEvent(sqsMessage);
    const { value: payload, error } = eventSchema.validate(eventObject);
    if (error) {
        throw Error(`Payload error => ${error}.`);
    }
    return payload;
};

```

该函数从 SQS 有效负载中提取一个嵌套事件，并通过`Joi`验证库确保该事件具有所有必需的属性。对于 SQS 来说，有效载荷(或者至少是外部结构)总是相同的；单元测试足以确保一切正常工作。

在本文中，我将[使用`Jest`库](https://blog.logrocket.com/writing-end-to-end-tests-for-graphql-servers-using-jest/)编写测试。以下是对`EventParser`的测试:

```
const EventParser = require('../../adapters/event-parser.js');
const createStubbedSqsMessage = (payload) => ({ Records: [{ body: payload }] });

describe('EventParser.parse() ', () => {
    test('returns parsed params if event has required params', async () => {
        const payload = {
            bucket: 'bucket',
            key: 'key',
            format: 'jpeg'
        };
        const stubbedSqsMessage = createStubbedSqsMessage(payload);
        const result = EventParser.parse(stubbedSqsMessage);
        expect(result).toBeDefined();
        expect(result.bucket).toBe(payload.bucket);
        expect(result.key).toBe(payload.key);
        expect(result.format).toBe(payload.format);
    });
    test('throws when event object has missing required params', async () => {
        const payload = {
            bucket: 'bucket'
        };
        const stubbedSqsMessage = createStubbedSqsMessage(payload);
        expect(() => EventParser.parse(stubbedSqsMessage)).toThrow();
    });
    test('throws when event has required params with incorrect type', async () => {
        const payload = {
            bucket: ['bucket'],
            key: 'key'
        };
        const stubbedSqsMessage = createStubbedSqsMessage(payload);
        expect(() => EventParser.parse(stubbedSqsMessage)).toThrow();
    });
});

```

第二个适配器`FileService`，应该具有获取和上传图像的功能。让我们使用 Amazon 的 SDK 来实现流。

```
// adapters/file-service.js
const Assert = require('assert');
const { Writable } = require('stream');
const Aws = require('aws-sdk');

exports.S3 = new Aws.S3();
exports.fetchFileAsReadable = (bucket, key) => {
    Assert(bucket && key, '"bucket" and "key" parameters must be defined');
    return exports.S3.getObject({ Bucket: bucket, Key: key}).createReadStream();
}
exports.uploadFileAsWritable = (bucket, key, writable) => {
    Assert(bucket && key, '"bucket" and "key" parameters must be defined');
    Assert(
      writable instanceof Writable,
      '"writable" must be an instance of stream.Writable class'
    );
    return exports.S3.upload({
        Bucket: bucket, Key: key, Body: writable, ACL: 'private'
    }).promise();
}

```

测试`Aws.S3`库没有任何好处，因为它维护得很好。只有当 Lambda 无法访问互联网时，问题才会出现——我们将在端到端测试中讨论这个问题。在这里，我们将测试无效参数和/或函数参数向 SDK 的正确传递。

由于这种情况下的函数非常小，我们将只测试第一种情况。

```
const FileService = require('../../adapters/file-service.js');

describe('FileService', () => {
    describe('fetchFileAsReadable()', () => {
        test('throws if parameters is are undefined', async () => {
            expect(() => FileService.fetchFileAsReadable())
                .toThrow('"bucket" and "key" parameters must be defined');
        });
    });
    describe('uploadFileAsWritable()', () => {
        it('throws if last argument is not a writable stream', async () => {
            expect(() => FileService.uploadFileAsWritable('bucket', 'key', {}))
                .toThrow('"writable" must be an instance of stream.Writable class');
        });
    });
});

```

接下来要实现和测试的是核心的 Lambda 逻辑——即图像的缩减和重新格式化。我们将使用 Node.js 的`Sharp`库来保持简洁。

```
// image-reducer.js
const Sharp = require('sharp');
const WIDTH = 320;
const HEIGHT = 240;

exports.createTransformable = (format = 'png', width = WIDTH, height = HEIGHT) =>
    format === 'jpeg' ? Sharp().resize(width, height).jpeg() :
    format === 'webp' ? Sharp().resize(width, height).webp() :
    Sharp().resize(width, height).png()

```

该函数接受某些参数并创建一个转换流，该转换流可以接收可读的图像二进制数据流，并将其转换为不同格式的较小图像。使用 Node 的流魔法，我们可以通过创建可读和可写的流存根很容易地测试所有这些。

```
const Path = require('path');
const Fs = require('fs');
const Sharp = require('sharp');
const ImageReducer = require('../image-reducer.js');

const BIG_IMAGE_PATH = Path.join(__dirname, '/big-lambda.png');
const SMALL_IMAGE_PATH_PNG = Path.join(__dirname, '/small-lambda.png');
const SMALL_IMAGE_PATH_WEBP = Path.join(__dirname, '/small-lambda.webp');
const SMALL_IMAGE_PATH_JPEF = Path.join(__dirname, '/small-lambda.jpeg');

describe('ImageReducer.createTransformable()', () => {
    describe('reducing size and transforming image in .png format', () => {
        test('reducing image', async () => {
            const readable = Fs.createReadStream(BIG_IMAGE_PATH);
            const imageReductionTransformable = ImageReducer.createTransformable();
            const writable = Fs.createWriteStream(SMALL_IMAGE_PATH_PNG);

            readable.pipe(imageReductionTransformable).pipe(writable);
            await new Promise(resolve => writable.on('finish', resolve));

            const newImageMetadata = await Sharp(SMALL_IMAGE_PATH_PNG).metadata();
            expect(newImageMetadata.format).toBe('png');
            expect(newImageMetadata.width).toBe(320);
            expect(newImageMetadata.height).toBe(240);
        });
    });
    describe('reducing size and transforming image in .webp format', () => {
        test('reducing image', async () => {
            const readable = Fs.createReadStream(BIG_IMAGE_PATH);
            const imageReductionTransformable = ImageReducer
              .createTransformable('webp', 200, 100);
            const writable = Fs.createWriteStream(SMALL_IMAGE_PATH_WEBP);

            readable.pipe(imageReductionTransformable).pipe(writable);
            await new Promise(resolve => writable.on('finish', resolve));

            const newImageMetadata = await Sharp(SMALL_IMAGE_PATH_WEBP).metadata();
            expect(newImageMetadata.format).toBe('webp');
            expect(newImageMetadata.width).toBe(200);
            expect(newImageMetadata.height).toBe(100);
        });
    });
    describe('reducing size and transforming image in .jpeg format', () => {
        test('reducing image', async () => {
            const readable = Fs.createReadStream(BIG_IMAGE_PATH);
            const imageReductionTransformable = ImageReducer
              .createTransformable('jpeg', 200, 200);
            const writable = Fs.createWriteStream(SMALL_IMAGE_PATH_JPEF);

            readable.pipe(imageReductionTransformable).pipe(writable);
            await new Promise(resolve => writable.on('finish', resolve));

            const newImageMetadata = await Sharp(SMALL_IMAGE_PATH_JPEF).metadata();
            expect(newImageMetadata.format).toBe('jpeg');
            expect(newImageMetadata.width).toBe(200);
            expect(newImageMetadata.height).toBe(200);
        });
    });
});

```

## 集成测试

集成测试的目的是测试已经过单元测试的两个或更多代码组件之间的契约和集成。由于我们没有集成上面的所有代码，现在让我们这样做。

```
// main.js
const { promisify } = require('util');
const { PassThrough, pipeline } = require('stream');
const { generateRandomId, appendSuffix } = require('./utils');
const pipelineAsync = promisify(pipeline);

exports.imageReducerService = async (event, FileService, ImageReducer) => {
    const executionId = generateRandomId();
    try {
        console.log(`Started imageReducerService id: ${executionId}`);

        const { bucket, key, format } = event;
        const readable = FileService.fetchFileAsReadable(bucket, key);
        const imageReductionTransformable = ImageReducer.createTransformable(format);
        const writable = new PassThrough();

        const newKey = appendSuffix(key, format);
        const pipelineProcess = pipelineAsync(
          readable,
          imageReductionTransformable,
          writable
        );
        const uploadProcess = FileService
          .uploadFileAsWritable(bucket, newKey, writable);
        await Promise.all([pipelineProcess, uploadProcess]);

        console.log(`Finished imageReducerService id: ${executionId}`);
    }
    catch (error) {
        console.error(`Thrown imageReducerService id: ${executionId}`);
        throw error;
    }
}; 

```

这段代码在事件被我们的`EventParser`净化后获取解析的事件，并基于此，在第 13 行以可读流的形式从 S3 获取一个图像。然后，它在第 14 行创建一个图像缩小转换流，并在第 15 行创建一个可写流。然后在第 18 行的可读、转换和可写流之间创建一个管道链。接下来，可写流在第 23 行开始上传到 S3 桶。换句话说，这些代码所做的就是以流的形式获取、调整和上传图像。

因为这个 Lambda 函数的例子不是很大，所有的连接都在一个文件中完成，我们可以用一个测试来覆盖它。在其他情况下，可能需要将其分成几个测试。

这是我们的测试:

```
require('dotenv').config();
const { EventParser, FileService, ImageReducer } = require('../adapters');
const { imageReducerService } = require('../main.js');
const { appendSuffix } = require('../utils');
const createFakeSqsMessage = (payload) => ({ Records: [{ body: payload }] });

describe('ImageReducerService', () => {
    test('integration', async () => {
        const realBucket = process.env.BUCKET;
        const existingFileKey = process.env.KEY;
        const sqsMessage = createFakeSqsMessage({
            bucket: realBucket,
            key: existingFileKey
        });
        await imageReducerService(
          EventParser.parse(sqsMessage),
          FileService,
          ImageReducer
        );
        // check if the new reduced image exists on the S3 bucket
        const reducedImageMetadata = await FileService.S3
            .headObject({
              bucket: realBucket,
              key: appendSuffix(existingFileKey, 'png')
            })
            .promise();
        expect(reducedImageMetadata).toBeDefined();
   });
});

```

这个测试实际上是针对一个使用环境变量的真实 S3 桶。这种方法有优点也有缺点。一方面，它更现实，几乎像一个端到端的测试(如果我们不考虑有效负载实际上并不来自真实的 SQS 队列)。缺点是它很脆弱，因为连接总是会中断。

另一种方法是使用几个插件来模拟 Lambda 环境——事实上，是几乎所有的 AWS 服务——使用 docker 镜像。其中之一是[无服务器离线](https://github.com/dherault/serverless-offline)，它有一个庞大的扩展列表。这真的很有用，但是也有相反的代价:不太现实，不太有信心，但是更容易设置，执行起来更快。

对于这个 Lambda，我只选择第一条路径，因为它相当简单。对于更复杂的代码，我会重新考虑并选择第二个选项，因为我们将使用真实的云基础设施再次测试代码，作为端到端测试的一部分。

## 端到端测试

如果您还记得，我们写的所有内容都集成到了一行代码中——实际上是两行，但这仅仅是因为格式的原因。看起来是这样的:

```
const { EventParser, FileService } = require('./adapters');
const ImageReducer = require('./image-reducer.js');
const ImageReducerService = require('./main.js');

exports.handler = (sqsMessage) =>
    ImageReducerService(EventParser.parse(sqsMessage), FileService, ImageReducer); 

```

既然我们已经完成了所有需要进行的单元测试和集成测试，那么是时候使用真实的 AWS 基础设施在真实环境中测试我们的功能了。因为我们的 Lambda 函数从 SQS 队列接收事件，所以我们需要将一条消息插入到与该函数连接的队列中，并确定在该函数执行完毕后，给定的 S3 桶上是否存在新的图像。

```
require('dotenv').config();
const Aws = require('aws-sdk');
const { appendSuffix } = require('../utils');

Aws.config.update({region: 'us-east-1'});
const Sqs = new Aws.SQS({ apiVersion: '2012-11-05' });
const S3 = new Aws.S3();

describe('imageReducerService', () => {
    test('end-to-end functionality', async () => {
        const event = { bucket: process.env.BUCKET, key: process.env.KEY };
        const params = {
          MessageBody: JSON.strigify(event),
          QueueUrl: process.env.SQS_QUEUE
        };
        await Sqs.sendMessage(params).promise();

        const reducedImageMetadata = await S3
            .headObject({
              bucket: realBucket,
              key: appendSuffix(existingFileKey, 'png') 
            })
            .promise();
        expect(reducedImageMetadata).toBeDefined();
    });
});

```

这个测试包含了我们的 Lambda 将要使用的基础设施的每一部分，并且有助于确保一切都被正确连接。它创建了一个与实时场景完全一样的动作流。因此，它要求一切都已经在 AWS 上启动并运行。

我们可以首先在试运行/QA 环境中运行这个测试，然后在实际的生产环境中再次运行，以确保一切都是连接的。可选地，我们可以使用 [Lambda 别名](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-lambda-alias.html)来自动化流程。我们将首先部署函数的新版本，然后运行端到端测试，如果一切顺利，在当前活动函数和新版本之间切换别名。

## 结论

如果您想在一个地方查看所有内容，您可以在这个 [GitHub repo](https://github.com/DarkMannn/testing-node-serverless-applications) 中找到本文的完整代码。

为 Lambda 编写测试不是一项简单的任务。对于一个可测试的 Lambda 函数，我们必须从实现的最开始就注意，并相应地规划设计。

## 200 只![](img/61167b9d027ca73ed5aaf59a9ec31267.png)显示器出现故障，生产中网络请求缓慢

部署基于节点的 web 应用程序或网站是容易的部分。确保您的节点实例继续为您的应用程序提供资源是事情变得更加困难的地方。如果您对确保对后端或第三方服务的请求成功感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/node-signup)

.

[![LogRocket Network Request Monitoring](img/cae72fd2a54c5f02a6398c4867894844.png)](https://lp.logrocket.com/blg/node-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/node-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录下用户与你的应用程序交互时发生的一切。您可以汇总并报告有问题的网络请求，以快速了解根本原因，而不是猜测问题发生的原因。

LogRocket 检测您的应用程序以记录基线性能计时，如页面加载时间、到达第一个字节的时间、慢速网络请求，还记录 Redux、NgRx 和 Vuex 操作/状态。

[Start monitoring for free](https://lp.logrocket.com/blg/node-signup)

.