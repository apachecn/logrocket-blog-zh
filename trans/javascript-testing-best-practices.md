# JavaScript 测试:需要学习的 9 个最佳实践

> 原文：<https://blog.logrocket.com/javascript-testing-best-practices/>

许多开发人员不喜欢测试，但它是软件工程的一个重要方面，直接影响代码质量。古怪的测试不会帮助你在编写代码时发现错误，这违背了测试的全部目的。

最重要的是，测试充当了其他开发人员的一份文档。通过阅读您创建的测试，他们应该能够很好地理解您开发的代码的目的。

本文聚焦于 JavaScript 测试的九个最佳实践，它们可以帮助您编写更好的测试，并帮助您的团队更好地理解您创建的测试。我们将重点关注三个具体要素:

1.  测试解剖和测试描述
2.  单元测试反模式
3.  测试准备

我们开始吧！

## 1.测试解剖和测试描述

这一部分探讨了如何改进您的测试结构和测试描述。目标是提高测试文件的可读性，这样开发人员可以快速浏览它们，找到他们想要的信息。

例如，他们更新了一个函数，并想了解哪些测试需要更改。你可以通过将结构应用到你的测试中并写下有意识的测试描述来真正帮助他们。

### 1.1–AAA 模式的结构测试

起初，AAA 模式可能什么都不会告诉你——所以让我们澄清一下！AAA 模式代表 **A** rrange， **A** ct， **A** ssert。您希望将测试内部的逻辑分成三个部分，以使它们更容易理解。

“安排”部分包括模拟测试场景所需的所有设置代码和测试数据。其次，顾名思义，“act”部分执行单元测试。通常，测试执行只包含一两行代码。最后，“断言”部分对所有断言进行分组，将收到的输出与预期的输出进行比较。

这里有一个例子来说明这一点:

```
it('should resolve with "true" when block is forged by correct delegate', async () => {
    // Arrange
    const block = {
        height: 302,
        timestamp: 23450,
        generatorPublicKey: '6fb2e0882cd9d895e1e441b9f9be7f98e877aa0a16ae230ee5caceb7a1b896ae',
    };

    // Act
    const result = await dpos.verifyBlockForger(block);

    // Assert
    expect(result).toBeTrue();
});
```

如果您将上面的测试结构与下面的例子进行比较，很明显哪个更具可读性。你必须花更多的时间阅读下面的测试来弄清楚它做了什么，而上面的方法在视觉上清楚地表明了测试是如何构造的。

```
it('should resolve with "true" when block is forged by correct delegate', async () => {
    const block = {
        height: 302,
        timestamp: 23450,
        generatorPublicKey: '6fb2e0882cd9d895e1e441b9f9be7f98e877aa0a16ae230ee5caceb7a1b896ae',
    };
    const result = await dpos.verifyBlockForger(block);
    expect(result).toBeTrue();
});
```

### 1.2–使用三层系统编写详细的测试描述

编写详细的测试描述听起来很容易，但是有一个系统可以让你的测试描述更容易理解。我建议使用三层系统来构建测试:

*   第 1 层:您想要测试的单元，或者测试需求
*   第 2 层:您想要测试的特定操作或场景
*   第 3 层:描述预期结果

这是一个用于编写测试描述的三层系统的例子。在这个例子中，我们将测试一个处理订单的服务。

这里，我们想要验证向购物篮添加新商品的功能是否如预期的那样工作。因此，我们写下两个“第 3 层”测试用例，在那里我们描述了期望的结果。这是一个简单的系统，可以提高测试的可扫描性。

```
describe('OrderServcie', () => {
    describe('Add a new item', () => {
        it('When item is already in shopping basket, expect item count to increase', async () => {
            // ...
        });

        it('When item does not exist in shopping basket, expect item count to equal one', async () => {
            // ...
        });
    });
});
```

## 2.单元测试反模式

单元测试对于验证您的业务逻辑至关重要——它们旨在捕捉代码中的逻辑错误。这是最基本的测试形式，因为在通过 E2E 测试开始测试组件或应用程序之前，您希望您的逻辑是正确的。

### 2.1–避免测试私有方法

我见过很多测试私有方法实现细节的开发者。如果可以通过只测试公共方法来覆盖它们，为什么还要测试它们呢？如果实际上与您公开的方法无关的实现细节发生变化，您将会经历误报，并且您将不得不花费更多的时间来维护私有方法的测试。

这里有一个例子可以说明这一点。私有或内部函数返回一个对象，并且你也验证这个对象的格式。如果您现在更改私有函数的返回对象，即使实现是正确的，您的测试也会失败。没有要求允许用户计算增值税，只显示最终价格。然而，我们错误地坚持在这里测试类内部。

```
class ProductService {
  // Internal method - change the key name of the object and the test below will fail
  calculateVATAdd(priceWithoutVAT) {
    return { finalPrice: priceWithoutVAT * 1.2 };
  }

  //public method
  getPrice(productId) {
    const desiredProduct = DB.getProduct(productId);
    finalPrice = this.calculateVATAdd(desiredProduct.price).finalPrice;
    return finalPrice;
  }
}

it('When the internal methods get 0 vat, it return 0 response', async () => {
  expect(new ProductService().calculateVATAdd(0).finalPrice).to.equal(0);
});
```

### 2.2–避免在测试中发现错误

我经常看到开发人员使用`try...catch`语句来捕捉测试中的错误，以便在断言中使用它们。这不是一个好方法，因为它为假阳性敞开了大门。

如果您在您试图测试的函数的逻辑中犯了一个错误，当您期望它抛出一个错误时，该函数可能没有抛出一个错误。因此，测试跳过了`catch`块，测试通过了——即使业务逻辑不正确。

下面是这样一个例子，当您创建一个新产品而没有提供产品名称时，期望`addNewProduct`函数抛出一个错误。如果`addNewProduct`函数没有抛出错误，您的测试将会通过，因为在`try...catch`块之外只有一个断言来验证函数被调用的次数。

```
it('When no product price, it throws error', async () => {
    let expectedError = null;
    try {
        const result = await addNewProduct({ name: 'rollerblades' });
    } catch (error) {
        expect(error.msg).to.equal("No product name");
        errorWeExceptFor = error;
    }
    expect(errorWeExceptFor).toHaveBeenCalledTimes(1)
});
```

那么如何重写这个测试呢？例如，Jest 为开发人员提供了一个`toThrow`函数，在这个函数中，您希望函数调用抛出一个错误。如果函数没有抛出错误，断言就会失败。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
it('When no product price, it throws error', async () => {
    await expect(addNewProduct({ name: 'rollerblades' }))
        .toThrow(AppError)
        .with.property("msg", "No product name");
});
```

### 2.3–不要嘲笑一切

一些开发人员在单元测试中模仿所有的函数调用，所以他们最终测试了`if...else`语句。这种测试毫无价值，因为你可以相信一种编程语言能够正确地实现一个`if...else`语句。

您应该只模拟底层或最低级别的依赖项和 I/O 操作，比如数据库调用、API 调用或对其他服务的调用。这样，您可以测试私有方法的实现细节。

例如，下面的例子说明了一个调用内部方法`calculateVATAdd`的`getPrice`函数，该函数本身调用一个带有`getVATPercentage`的 API。不要嘲讽`calculateVATAdd`功能；我们想验证这个函数的实现细节。

因此，我们应该只模仿外部 API 调用`getVATPercentage`,因为我们对这个 API 返回的结果没有任何控制。

```
class ProductService {
    // Internal method
    calculateVATAdd(priceWithoutVAT) {
        const vatPercentage = getVATPercentage(); // external API call -> Mock
        const finalprice = priceWithoutVAT * vatPercentage;
        return finalprice;
    }

    //public method
    getPrice(productId) {
        const desiredProduct = DB.getProduct(productId);
        finalPrice = this.calculateVATAdd(desiredProduct.price); // Don't mock this method, we want to verify implementation details
        return finalPrice;
    }
}
```

### 2.4–使用真实的数据

不是每个开发人员都喜欢创建测试数据。但是测试数据应该尽可能地真实，以覆盖尽可能多的应用程序路径来检测缺陷。因此，存在许多数据生成策略来转换和屏蔽生产数据，以便在您的测试中使用它。另一个策略是开发生成随机输入的函数。

简而言之，不要使用典型的`foo`输入字符串来测试你的代码。

```
// Faker class to generate product-specific random data
const name = faker.commerce.productName();
const product = faker.commerce.product();
const number = faker.random.number());
```

### 2.5–避免每个测试用例有太多的断言

不要害怕分解场景或者写下更具体的测试描述。包含五个以上断言的测试用例是潜在的危险信号；说明你试图一次验证太多东西。

换句话说，你的测试描述不够具体。最重要的是，通过编写更具体的测试用例，开发人员可以更容易地识别出在进行代码更新时需要更改的测试。

> **提示**:使用像 [faker.js](https://www.npmjs.com/package/faker) 这样的库来帮助你生成真实的测试数据。

## 3.测试准备

最后一部分描述了测试准备的最佳实践。

### 3.1–避免过多的助手库

通常，使用助手库来抽象大量复杂的设置需求是一件好事。然而，过多的抽象会变得非常混乱，尤其是对于那些不熟悉测试套件的开发人员。

您可能有一个边缘案例，您需要不同的设置来完成一个测试场景。现在创建你的 edge case 设置变得非常困难和混乱。最重要的是，抽象太多的细节可能会让开发人员感到困惑，因为他们不知道幕后发生了什么。

根据经验，您希望测试简单有趣。假设您必须花 15 分钟以上的时间来弄清楚在`beforeEach`或`beforeAll`挂钩的设置过程中到底发生了什么。在这种情况下，您的测试设置过于复杂了。这可能表明您删除了太多的依赖项。或者相反:什么都不做，创建一个非常复杂的测试设置。请注意这一点！

> 提示:你可以通过让一个新的开发人员找出你的测试套件来衡量这一点。如果花费的时间超过 15 分钟，这表明您的测试设置可能太复杂了。记住，测试应该很容易！

### 3.2–不要过度使用备考挂钩

引入过多备考挂钩— `beforeAll`、`beforeEach`、`afterAll`、`afterEach`等。—而将它们嵌套在`describe`块中则变得难以理解和调试。Jest 文档中的一个例子说明了这种复杂性:

```
beforeAll(() => console.log('1 - beforeAll'));
afterAll(() => console.log('1 - afterAll'));
beforeEach(() => console.log('1 - beforeEach'));
afterEach(() => console.log('1 - afterEach'));

test('', () => console.log('1 - test'));

describe('Scoped / Nested block', () => {
  beforeAll(() => console.log('2 - beforeAll'));
  afterAll(() => console.log('2 - afterAll'));
  beforeEach(() => console.log('2 - beforeEach'));
  afterEach(() => console.log('2 - afterEach'));
  test('', () => console.log('2 - test'));
});

// 1 - beforeAll
// 1 - beforeEach
// 1 - test
// 1 - afterEach
// 2 - beforeAll
// 1 - beforeEach
// 2 - beforeEach
// 2 - test
// 2 - afterEach
// 1 - afterEach
// 2 - afterAll
// 1 - afterAll
```

注意使用备考挂钩。只有当你想为你所有的测试用例引入行为时，才使用钩子。最常见的是，钩子用于启动或关闭流程来运行测试场景。

## 结论

测试一开始可能看起来很简单，但是有很多事情你可以改进，让测试对你和你的同事来说更有趣。你的目标是让你的测试易于阅读，易于扫描，易于维护。避免复杂的设置或过多的抽象层，这会增加测试的复杂性。

通过引入三层系统和 AAA 模式，您可以显著地影响测试的质量和可读性。这是一个小小的努力，却能为你的团队带来巨大的价值。不要忘记考虑这篇博文中描述的其他最佳实践。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.