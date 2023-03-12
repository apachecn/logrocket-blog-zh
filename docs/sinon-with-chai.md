# 如何在 Chai 上最好地使用 Sinon

> 原文：<https://blog.logrocket.com/sinon-with-chai/>

如果您正在用 JavaScript 构建出于任何原因发出`http`请求的软件，您将拥有依赖于这些请求的响应的代码。发出这些请求的代码具有外部依赖性，这使得单元测试更难编写。

如果您使用`mocha`作为测试运行器，这就是`sinon`的用武之地。这是一个全功能的存根库，用于 JavaScript 中的单元测试。它通过允许你改变被测函数的行为来帮助你用外部依赖来单元测试代码。

如果你用的是`jest`，它自带存根功能。这里的最佳实践将有 sinon 的具体例子，但原则适用于任何存根引擎。

本指南假设您已经了解了`chai`和`sinon`的基础知识。这将提供如何一起使用这两个模块的战术建议，同时避免一些**常见的陷阱。**

## api 客户端

Sinon 对于避免在单元测试中依赖外部依赖是非常有用的。因此，假设我们想要测试这个简单的 api 客户端的`get`方法。对于我们想要测试的已知响应状态，它有一些特殊的错误处理:

```
module.exports = class ApiClient {  
  constructor(httpClient, apiKey) { 
    this.httpClient = httpClient; 
    this.apiKey = apiKey;
    this.isApiKeyValid = true;
  }

  get(endpoint, callback) {

    this.httpClient.get(endpoint, {apiKey: this.apiKey}, (err, response) => {
      if (err) { return callback(err); }
      if (response.status >= 500) {
        return callback(new Error('INTERNAL_SERVER_ERROR'))
      }
      if (response.status == 403) {
        this.isApiKeyValid = false;
        return callback(new Error('AUTH_ERROR'))
      }
      return callback(null, response);
    })
  }
}
```

## 把真的东西切掉

如果您尝试存根的方法不存在，Sinon 将抛出一个非常有用的错误。最佳实践是将您希望在代码中使用的同一类型的对象上使用的方法存根化。如果代码使用不存在的方法，这将避免编写通过的单元测试:

```
const request = require('request');
const sinon = require("sinon");

it('issues the request', function() {

  sinon.stub(request, 'gettt')

  sinon.stub(request, 'get')
  ...
})
```

**常见的陷阱:**使用不带参数的`sinon.stub()`创建完全虚构的对象的测试会让测试传递带有难以捕捉的错别字的代码，从而导致错误。

## 兴农期望

将`sinon-chai`模块添加到 mix 中，以便使用带有`sinon`存根的期望语法。没有了`sinon-chai`,这种期望可以被笨拙地断言，如下所示:

```
it('issues the request', function(done) {
  sinon.stub(request, 'get').yields(null, {});
  apiClient = new ApiClient(request, 'api-key');
  apiClient.get('/endpoint', (err, response) => {
    expect(request.get.calledOnce).to.be.true
    done(err);
  })
})
```

在失败时，`chai`会告诉我们它“期望假为真”，这并没有提供太多的上下文。

常见陷阱:对于没有编写原始代码或测试的人来说，这使得测试更难维护。

有了`sinon-chai`，人们可以使用相同的期望链接，这使得期望语法易于阅读，并且是更好的失败报告:

```
const request = require('request');
const sinon = require("sinon");
const chai = require("chai");
const sinon = require("sinon");
const sinonChai = require("sinon-chai");
chai.use(sinonChai);
const expect = chai.expect;

it('issues the request', function(done) {
  sinon.stub(request, 'get').yields(null, {});
  apiClient = new ApiClient(request, 'api-key');
  apiClient.get('/endpoint', (err, response) => {
    expect(request.get).to.have.been.calledOnce
    done(err);
  })
})
```

如果失败，`sinon-chai`将告诉我们它“期望 request.get 被调用一次”，这是对测试失败原因的更准确的解释。

## 总是在沙盒里玩

始终使用沙箱来存储您的存根和间谍，以便于清理。不必记住恢复每个单独的存根，整个沙箱可以一次恢复。这将确保一个测试中的更改不会影响到下游的单元测试:

```
describe('TestModule', function() {
  beforeEach('setup sandbox', function() {
    this.sandbox = sinon.sandbox.create();
    this.sandbox.stub(request, 'get');
  });
  ...
  afterEach('restore sandbox' function() {
    this.sandbox.restore();
  });
})
```

这种策略将避免**常见的陷阱**，存根和间谍仍然有效，并改变不相关测试的行为。

如果你有任何全局测试设置助手/基础设施，如果`this.sandbox`被设置为避免难以调试的测试失败，考虑将沙箱恢复添加到全局`afterEach`中。如果测试后没有清理存根，就会发生这种情况:

```
 afterEach('restore sandbox', function() {
  if(this.sandbox) { this.sandbox.restore(); }
}
```

## 将`yields`用于异步接口

在许多情况下，外部依赖将使用异步接口。为了测试许多不同的结果，在`beforeEach`中创建一次存根，并在您的特定测试中使用`yields`方法，以将它限定在特定的情况下:

```
const ApiClient = require('./ApiClient');
const request = require('request');
const sinon = require('sinon');
const chai = require('chai');
const sinonChai = require('sinon-chai');

chai.use(sinonChai);
const expect = chai.expect;

describe('ApiClient#get', function() {
  beforeEach('create ApiClient instance', function() {
    this.sandbox = sinon.sandbox.create();
    this.sandbox.stub(request, 'get')
    this.apiClient = new ApiClient(request, 'api-key');
  });
  afterEach('restore stub', function() {
    this.sandbox.restore();
  }
  it('yields the request error if the request fails', function(done) {     
    let requestError = {some: 'error'}

    request.get.yields(requestError); 

    this.apiClient.get('/posts', (err, response) => {

      expect(request.get).to.have.been.calledWith('/posts', {apiKey: 'api-key'});

      expect(err).to.equal(requestError);
      return done();
  });
  it('yields INTERNAL_SERVER_ERROR when the response status is 500', function(done) { 

    request.get.yields(null, {status: 500}); 

    this.apiClient.get('/posts', (err, response) => {

      expect(request.get).to.have.been.calledWith('/posts', {apiKey: 'api-key'});

      expect(err).to.equal('INTERNAL_SERVER_ERROR');
      return done();
  });
  it('yields an AUTH_ERROR when the response status is 403', function(done) {     
    request.get.yields(null, {status: 403}); 

    this.apiClient.get('/posts', (err, response) => {

      expect(request.get).to.have.been.calledWith('/posts', {apiKey: 'api-key'});

      expect(err).to.have.property('message', 'AUTH_ERROR')

      expect(this.apiClient.isApiKeyValid).to.equal(false);
      return done();
  });
});
```

使用`yields`避免了**中常见的陷阱**，即创建额外的存根来充当已经被存根化的方法的回调。

## 结论

有了上面的提示，你和你的团队可以更好地利用`sinon`为具有外部依赖的代码编写单元测试，同时避免上面列出的**常见陷阱**！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)

* * *