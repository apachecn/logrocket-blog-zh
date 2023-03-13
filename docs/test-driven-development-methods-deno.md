# 探索 Deno 中测试驱动的开发方法

> 原文：<https://blog.logrocket.com/test-driven-development-methods-deno/>

您可能在某个地方听说过“测试驱动开发”这个术语，或者您可能有兴趣知道如何通过编写测试来使您的代码更加可靠。

本文将解释什么是测试驱动开发(TDD ),什么时候可以使用它，以及我们如何使用 Deno 的内置测试 API 来编写测试，这可以帮助我们使我们的代码更加可靠。我们还将看到如何将使用 Chai 或 Mocha 等库编写的现有测试移植到 Deno 中。

特别是，在本文中，我们将看到:

请注意，您需要熟练阅读和编写基本的 JavaScript 代码。这包括变量和函数声明、基本的作用域属性以及导入/导出内容。[对 Deno 有一点了解有助于](https://blog.logrocket.com/getting-started-deno-fresh/)阅读本指南，但不是必需的。

如果你已经熟悉了测试驱动开发的概念，你可以[直接跳到我们关于 Deno 测试的章节](#brief-introduction-deno)。否则，让我们从介绍测试驱动开发、测试类型以及何时使用 TDD 开始。

## 什么是测试驱动开发？

想象你对下一个十亿美元的创业公司有一个好主意。为了让它成为现实，你必须写一些代码，并让它工作——简单！你开始写一些代码，运行它，看看它能做什么，修复一些错误，然后再写一些代码，等等。

一开始，这种方法很棒。你正在生产一些工作代码，离十亿美元又近了一步，一切都很顺利。但是随着代码规模的增长和更多特性的增加，您开始遇到一些问题。

有时，当添加一个新的特性时，您会不小心破坏现有的东西。由于您的产品现在变得越来越复杂，很难确保您检查了每一个可能的场景，以确保没有任何问题。

您可能会保留一个需要注意的事项的列表，一个依赖关系的列表，这样您就知道哪些更改可能会潜在地影响代码的哪个部分。但是同样，手动操作会变得单调乏味，随着功能的增加，保持更新并确保每次都做好准备也变得有点麻烦。

这正是测试的用武之地。

您可以编写测试来确保每一个可能的场景都在运行，并且它们都如您的代码所预期的那样工作。使用一个简单的命令，您可以运行这些测试，所用的时间只是手动完成所有测试所需时间的一小部分。

因为您可以编写一次测试，然后每次都运行它，所以您可以确保列表中没有项目被意外跳过。

测试驱动开发(TDD)更进一步。思考 TDD 如何工作的一种方式是这样的:

您通过编写测试来开始开发过程，甚至在实现一个特性之前。测试应该检查新特性在多个输入中是否如预期的那样工作。自然，由于实现尚不存在，测试将会失败。然后，您应该编写足够的代码来通过测试。

所以，当你想在你的项目中添加一个新的特性时——比如说，一个用户注册功能——你首先会想到这个特性可能会遇到什么样的输入。然后，您将编写测试来检查这些输入是否给出了预期的输出，例如:

*   如果名称输入为空，则出现错误
*   另一个错误是，如果输入的名称超过了您可以接受的限制
*   确保正确处理非拉丁 Unicode 字符的测试
*   再进行一次测试，看看当所有输入都正确时，是否会创建一个新用户

当然，注册功能本身还不存在，所以这些测试现在不会做任何事情。但是在编写这些测试的过程中，您已经确定了代码需要处理的各种情况，现在您有了测试来确保代码在编写后处理这些情况。

下一步是编写足够通过这些测试的代码。这样，您就不会编写多余的代码，这些代码可能会被不必要地保存、遗忘、从未使用过。

因为有了这些测试，每当您向一个新特性添加一些更改时，您可以确保它不会意外地破坏您现有的代码。此外，因为您总是在编写代码之前编写测试，所以您可以确保总是有测试来检查添加的代码是否按预期工作！

此外，您可以在您的 CI 中添加各种步骤，以确保这些测试在每个 pull 请求上运行，并且只有当测试通过时，请求和更改才会被接受。这样，您就可以确保项目的主要分支在已知的输入和条件下始终正常工作。

## 探索各种类型的测试

考虑到代码的多样性，测试代码的方法也是多种多样的。每种测试方法都有助于确保代码以不同的方式工作。例如，考虑[单元和集成测试](https://blog.logrocket.com/unit-and-integration-testing-for-node-js-apps/)的不同用例。

专注于组成整个应用程序的代码的小部分的测试被归类为单元测试。其他测试也可以将应用程序作为一个整体来考虑，并确保这些部分能够正确地协同工作。这些被归类为集成测试。

### 单元测试

顾名思义，[单元测试关注于](https://blog.logrocket.com/unit-testing-node-js-applications-using-mocha-chai-and-sinon/)组成完整应用程序的单个小代码单元。这些测试检查对于给定的输入，单个单元产生正确的输出。

例如，单元测试可以检查在 DB 中创建和存储用户的注册函数是否在传递空字符串作为名称时出错。您可以更进一步，编写单元测试来确保在调用`create`函数后，用户确实以正确的值存储在数据库中。

您可以有多个测试，每个测试检查单独的、小的代码单元——甚至对相同的单元进行多个测试，测试不同的边缘条件。使用 TDD，编写这些可以帮助您指出哪些单独的元素是需要的，以及它们对于不同的输入应该如何工作。

单元测试将确保代码的每个单独部分都按预期工作。然而，系统作为一个整体仍然可能产生不正确的结果。

考虑我们的注册函数示例。您可能已经检查了注册函数会根据需要抛出错误，并且 DB 会正确保存用户，但是应用程序作为一个整体可能无法创建新用户。这可能是因为您忘记了在应用程序启动时正确设置数据库。

您对每个单独单元的测试将抽象出其余的参数。例如，当测试 save 函数时，您将为每个测试设置 DB，然后再将其拆除。虽然测试没有给出任何失败，但是注册在应用程序本身中无法工作，因为您没有在实际的应用程序中正确地设置 DB。

为了确保应用程序作为一个整体按预期工作，您将需要集成测试。

### 集成测试

集成测试将您的应用程序视为一个整体，查看完整的系统，以确保各个较小的单元正确地组合在一起。

这些测试并不关注单个单元，而是将它们视为黑盒。他们只关心确保对于给定的输入，系统作为一个整体给出适当的输出。

例如，考虑之前的注册动作。一个集成测试将不会关心是否有一个单一的函数来验证输入和创建用户，或者多个函数一起工作来完成这个任务。

相反，它将专注于确保在给出正确的注册输入后，用户可以稍后登录，而对于不正确的输入，尝试登录会给出适当的错误——例如`User does not exist`。

这些测试对于确保较小代码单元中的任何更改不会在整个应用程序的体验中产生意想不到的后果非常重要。

此外，还有其他类型的测试，如端到端测试，这涉及到在一个受控的环境中运行应用程序的完整副本，包括前端、后端、数据库和其他任何东西，以确保系统的所有移动部分都按预期工作。

您可能已经注意到，不同类型的测试之间的界限有点模糊。根据代码的组织方式，一个应用程序可能考虑的集成测试可能是其他应用程序的单元测试，一个应用程序可能考虑端到端测试，另一个应用程序可能在他们的集成测试套件中运行。

这里要注意的重要一点是，为了确保您的应用程序按预期工作，您需要在不同的粒度级别上测试它的每个单独部分以及所有部分的组合。

当然，像许多事情一样，测试应用程序说起来容易做起来难，TDD 可能并不总是最好的主意。让我们看看什么时候使用 TDD 有用，什么时候可能没用。

## 何时使用 TDD

像任何其他技术一样，TDD 只是开发人员工具箱中的另一个工具。开发人员必须判断用它来解决问题是否是件好事，并且尽量不要用锤子锯木头。

我们现在将会看到一些关于 TDD 何时何地有用的指导方针，以及在哪些情况下它可能没有用。

如前所述，在 TDD 中，我们从我们期望代码做什么开始，编写测试来确保代码做它期望做的事情，然后编写代码来做这些事情。自然地，如果我们对代码有好的、固定的规范和清晰的需求，编写测试就更容易了。

在这种情况下，TDD 是一个非常好的选择，因为它将帮助您确保代码不仅按预期工作，而且在引入任何更改时也能按预期工作。

另一方面，如果来自代码的需求还不清楚，或者它们一直在变化，那么使用 TDD 可能不是一个好主意。

### 不断变化的需求:为什么使用 TDD 可能没有用

考虑之前的注册功能。开始时，您只希望表单接受用户的电子邮件和密码，验证它们，报告任何错误，并创建用户的帐户。

然后，您意识到您还应该添加一个用户名字段，而不是随机生成一个，因此您进行了适当的更改并更新了测试。

但是，您意识到每个用户名必须是唯一的，所以您编写了另一个测试来验证不允许重复用户名的规则，如果输入与现有用户名匹配，就会给出一个错误。

后来要求变了；我们决定，多个用户可以输入相同的用户名，您只需在重复的用户名后面附加一个随机散列，使它们在您这边是唯一的。所以，你又更新了测试。

但是后来出现了支持外部认证(比如 OAuth)的需求，所以现在必须编写代码，并且必须再次更新测试。

正如您可能已经注意到的那样，为以上任何一项编写测试都不是挑战。这里的问题是需求的不断变化，同时架构还没有固定下来。如果从一开始就确定了需求，您可能会在编写和更新测试上花费相当少的时间。

这也增加了另一个考虑；即何时应该引入测试。

如果我们试图在最开始的时候添加它们，当需求不固定时，它们将需要大量的工作来保持更新。然而，如果交付代码的需求超过了运行测试的需求，它们可能会被忽略，永远不会运行。

另一方面，如果测试引入得太晚，项目的规模可能已经很大了，这意味着会有很多测试要添加。由于项目规模很大，团队可能会错过一些边缘案例，并且测试将需要大量的时间和资源来添加，导致他们被忽略，以利于编写实际的工作应用程序代码。

保持测试的平衡是一件棘手的事情，既要足够早地添加测试，使项目不至于太大，又要足够晚地添加测试，使项目的规范在很大程度上是清晰和固定的。

在另一种情况下，项目可能非常小，允许您舒适而彻底地手动跟踪所有执行路径。在这种情况下，编写测试可能会带来不必要的额外开销。

对于这种简单的项目，最好在开始时跳过编写测试，而是随着项目的增长引入测试，以确保一切按预期运行。

## Deno 简介

Deno 是一个 Javascript 运行时，与 Node.js 有点类似。事实上，Node 的创建者甚至帮助创建了 Deno。将 Deno 与 Node 区分开来的一些功能包括:

*   相同的 JS，不同的运行时
*   更严格的安全措施
*   顶级等待
*   现成的打字稿
*   整合模块

让我们更详细地回顾一下这些特性。

Deno 就像 Node 一样，是一个 JavaScript 运行时。任何不需要节点 API 的有效 JS 代码都可以在 Deno 上工作，就像在 Node 中一样。

默认情况下，使用 Deno 运行的程序以类似沙箱的环境中所需的最低权限运行。这意味着，在默认情况下，如果没有单独的权限，它不能发出任何 Internet 请求、访问文件系统或运行任何后台进程，在运行 Deno 程序时，必须通过标志显式授予这些权限。

与 Node 不同，在 Deno 中，您可以直接在顶层等待，而在顶层，您需要使用承诺或立即执行函数来编写异步代码。

Deno 可以运行 TypeScript 代码，不需要任何像 Babel 这样的中间 transpiler 先把你的 TypeScript 编译成 JS。

终于可以在各个单项项目中告别`node_modules`了！Deno 将所需的模块缓存在一个地方，并在项目间重用它们。

在下一节中，我们将深入探讨 Deno 如何支持原生测试；换句话说，您不必使用外部库来编写和运行测试。让我们从使用 Deno 内置的测试 API 编写简单的测试开始。

## 使用内置 API 的基本 Deno 测试

首先，我们将为一个需要将用户数据存储在数据库中的应用程序编写一些简单的测试。这意味着将有添加、删除和更新用户的功能。出于演示的目的，我们将使用内存中的 JS 对象来模拟 DB，而不是实际的 DB。

我们需要的第一个函数是`fetchUsers`，它应该根据用户与给定用户名的相似性来获取用户。出于我们的目的，我们只想获取用户名包含给定字符串的所有用户。

根据 TDD，我们需要首先考虑的情况是我们的函数应该返回:

*   给定有效字符串时的适当用户
*   如果给定一个空字符串，则不返回任何内容(否则将返回数据库中的所有用户)
*   如果没有找到匹配的用户，则为空列表

我们将从在测试文件中导入所需的模块开始:

```
import { assertEqual } from 'https://deno.land/std/testing/asserts.ts';
import { fetchUsers } from './stubs.ts';

```

Deno 的内置函数是用于断言的吗，有点像柴提供的。包含了我们所有的模拟 API 函数。

在此之后，我们可以简单地使用`test` API 来定义我们的测试，如下所示:

```
Deno.test('Testing user fetching',()=>{
    const name = 'testUser1';
    const users = fetchUsers(name);
    assertEquals(users.length,1);
})

```

我们从调用`Deno.test`开始，将测试注册到 Deno 的运行时。我们给它起了一个描述性的名字`Testing User fetching`,以便在测试结果中更容易识别，也便于快速浏览时理解它的用途。

然后，我们给我们的测试一个 arrow 函数，它将实际调用要测试的函数——在本例中是`fetchUsers`——并验证它的输出是否符合预期。在函数中，我们用`testUser1`的`name`调用了`fetchUsers`。我们希望它返回一个包含单个元素的数组，因为只有一个模拟用户的用户名包含该字符串。

最后，我们用实际值(用一个`-`符号表示)和期望值(用一个`+`符号表示)来调用`assertEquals`。如果它们不相等，该函数将抛出一个`assertionError`，测试将失败。

测试通过后，输出应该如下所示:

```
running 1 test from ./simple_test.ts
testing user fetching ... ok (7ms)

ok | 1 passed | 0 failed (30ms)

```

这显示了哪些测试是从哪个文件中运行的，每个测试花费了多少时间，多少测试通过，多少测试失败。

如果测试失败了——比方说，因为`fetchUsers`没有正确实现或者模拟数据库没有正确填充——而是返回了一个空数组，我们将得到类似下面的输出:

```
running 1 test from ./simple_test.ts
testing user fetching ... FAILED (9ms)

 ERRORS 

testing user fetching => ./simple_test.ts:6:6
error: AssertionError: Values are not equal:

    [Diff] Actual / Expected

-   0
+   1

  throw new AssertionError(message);
        ^
    at assertEquals (https://deno.land/[email protected]/testing/asserts.ts:184:9)
    at LogRocket-Blog-Code/tdd-in-deno/simple_test.ts:9:5

 FAILURES 

testing user fetching => ./simple_test.ts:6:6

FAILED | 0 passed | 1 failed (32ms)

error: Test failed

```

上面的输出显示来自文件`./simple_test.ts`的名为`test user fetching`的测试已经失败，失败是由于`assertEquals`抛出`assertionError`；它在预期值`1`处得到了实际值`0`。因此，我们可以看到到底有什么不同，我们可以尝试推理出哪里出错了。

类似地，我们可以定义多个测试。如前所述，我们需要测试三个条件来测试我们的`fetchUsers`函数。我们可以为每个案例编写一个测试:

```
Deno.test('return empty array on empty string',()=>{
    const users = fetchUsers('');
    assertEquals(users.length,0);
});
Deno.test('return all users matching given name',()=>{
    const users = fetchUsers('test');
    assertEquals(users.length,3);
    assertEquals(users[0].name,'testUser1');
    assertEquals(users[1].name,'testUser2');
    assertEquals(users[2].name,'testUser3');
});
Deno.test('return empty array on not matching name',()=>{
    const users = fetchUsers('abc');
    assertEquals(users.length,0);
})

```

给测试起一个描述性的名字可以帮助你快速理解它们的目的和预期的输出。当三个测试都通过时，您应该会看到类似如下的输出:

```
running 3 tests from ./simple_test.ts
return empty array on empty string ... ok (5ms)
return all users matching given name ... ok (4ms)
return empty array on not matching name ... ok (4ms)

ok | 3 passed | 0 failed (36ms)

```

这种方法允许我们编写简单的测试，不必为了运行而执行多个复杂的步骤。接下来，我们将看到如何在测试中使用 Deno 的 step 特性来编写这些测试。

## Deno 中的分步测试

当您必须在一个测试中执行多个复杂的操作并验证每个操作的结果时，将一个测试分成几个步骤会很有用。

在这种情况下，使用与以前类似的简单方法编写测试，测试期间的一个操作失败将导致整个测试失败。这会让我们手忙脚乱地使用堆栈跟踪来找出测试失败的地方。

相反，如果我们将每个操作及其断言编写为测试中它们自己的步骤，我们可以快速检查哪个特定的步骤失败了，以及该失败如何影响其他步骤。

这里我们将考虑这样一种情况，我们希望以一种特定的方式更新用户:首先获取具有给定用户名的用户，然后更新一些数据并将其作为新用户插入到 DB 中，最后从 DB 中删除旧的用户数据。

请注意，这不是用户更新的最佳实践，但对于我们的分步测试演示来说已经足够了。

为了实现这个更新过程，我们必须首先获取一个用户，并断言我们已经得到了预期的结果。然后，我们必须更新数据并将其插入到 DB 中，断言存储了一个新用户。最后，我们必须删除旧的用户对象，并检查旧的用户对象是否不存在。

作为测试函数给`Deno.test`的每个函数都有一个测试上下文参数。使用这些参数，我们可以通过调用`t.step`指定测试中的步骤，并将步骤名称和实际步骤作为一个函数:

```
import { assertEquals } from 'https://deno.land/std/testing/asserts.ts';
import { fetchUsers,insertUser,deleteUser, User } from './stubs.ts';

Deno.test('update User Name',async (t)=>{
    const name = 'testUser1';
    const newName = 'testUserUpdated';
    let user:User;

    await t.step('fetch user',()=>{
        const temp = fetchUsers(name);
        assertEquals(temp.length,1);
        assertEquals(temp[0].name,name);
        user = temp[0];
    });
    await t.step('update and store',()=>{
        const newUser:User = {
            ...user,
            name:newName,
        }
        insertUser(newUser);

        let temp = fetchUsers(newName);
        assertEquals(temp.length,1);
        assertEquals(temp[0].name,newName);
        assertEquals(temp[0].id,user.id);
        assertEquals(temp[0].password,user.password);
        temp = fetchUsers(name);
        assertEquals(temp.length,1);
        assertEquals(temp[0].name,name);
    });
    await t.step('delete old user data',()=>{
        deleteUser(user.name,user.id);
        let temp = fetchUsers(name);
        assertEquals(temp.length,0);
        temp = fetchUsers(newName);
        assertEquals(temp.length,1);
    })
})

```

注意，`t.step`是一个异步函数，在下一步调用它之前必须先调用`await`。上面将产生类似于下面的结果:

```
running 1 test from ./stepped_test.ts
update User Name ...
  fetch user ... ok (3ms)
  update and store ... ok (3ms)
  delete old user data ... ok (3ms)
update User Name ... ok (15ms)

ok | 1 passed (3 steps) | 0 failed (34ms)

```

在这里，我们可以看到，对于测试文件`stepped_test.ts`，运行了一个测试，它有三个步骤，每个步骤都通过了。

## Deno 中的行为驱动测试

Deno 还有一个 BDD 模块，它允许你以类似于 Chai 或 Mocha 等库的方式编写测试，并提供类似于`beforeEach`和`afterEach`的钩子。

要使用 BDD API，首先要导入它:

```
import {describe,it,afterEach,beforeEach} from "https://deno.land/[email protected]/testing/bdd.ts";
import { assertEquals } from 'https://deno.land/std/testing/asserts.ts';

```

然后我们可以编写类似于这些库的测试:

```
import { fetchUsers,insertUser,deleteUser, User } from './stubs.ts';

describe('User DB operations testing',()=>{
    it('fetches correct user based on username',()=>{
        const user = fetchUsers('testUser');
        assertEquals(user.length,3);
    });
    it('inserts user correctly',()=>{
        const newUser:User ={
            name:'newTestUser',
            id:'newtestuser',
            password:'newtestuser'
        };
        insertUser(newUser);
        const user = fetchUsers('new');
        assertEquals(user.length,1);
        assertEquals(user[0].name, 'newTestUser');
    });
    it('deletes user correctly',()=>{
        deleteUser('newTestUser','newtestuser');
        const user = fetchUsers('test');
        assertEquals(user.length,3);
    })
});

```

上面的代码定义了一个名为`User DB operations testing`的测试套件，并在该套件中执行三个测试:获取用户、插入用户和删除用户。

我们还可以使用`beforeEach`和`afterEach`定义一些在每个测试用例之前和之后运行的函数，以及使用`beforeAll`和`afterAll`定义在所有测试之前和之后运行的函数。这对于在每次测试前后设置和重置状态非常有用。

例如，我们可能希望在每次测试后将数据库重置为空白，并在每次测试前用已知值填充它，这样我们就可以控制测试看到的数据。另一个例子是，如果测试需要一些资源，比如一个目录或某些文件，您可以使用`beforeAll`创建它们，并使用`afterAll`清理它们。

在这里，我们将看到在模拟设置中设置和重置值的有用性。我们引入了一个`InventoryObject`，它存储了库存中某个商品的信息，以及它属于哪个用户。

与之前的测试不同，在之前的测试中，我们直接在用户数据库上操作，在这个场景中，我们希望首先使用`getDefaultInventory`创建库存数据库的副本，然后使用它运行测试。

为此，我们使用`beforeEach`来获取状态并为其设置一个内部变量，然后在`afterEach`中将该变量设置为一个空数组:

```
describe("Inventory operations", () => {
  let inv:Array<InventoryObject>;
  beforeEach(()=>{
    inv = getDefaultInventory();
  });
  afterEach(()=>{
    inv = [];
  });
  it('inserts object into inventory',()=>{
    insertIntoInventory(inv,{
      ojbType:'book',
      name:'just Another book',
      id:4,
      owner:'testUser3'
    });
    assertEquals(inv.length,4);
    assertEquals(inv[3],{
      ojbType:'book',
      name:'just Another book',
      id:4,
      owner:'testUser3'
    });
  });

  it('deletes object from inventory',()=>{
    inv = deleteFromInventory(inv,3);
    assertEquals(inv.length,2);
    const defaultInv = getDefaultInventory();
    assertEquals(inv[0],defaultInv[0]);
    assertEquals(inv[1],defaultInv[1]);
  })

});

```

这涵盖了使用 Deno 内置 API 编写测试的两种主要方式。在了解如何使用外部库(如 Chai 或 Mocha)之前，我们将首先了解如何直接从 Deno 中获取代码覆盖信息。

## 从 Deno 获取覆盖信息

Deno 提供了一种内置的方法，通过运行测试来生成代码覆盖率信息。为了生成这些信息，我们首先必须编写我们的测试，理想情况下应该覆盖代码中所有可能的路径。

之后，我们可以简单地运行`deno test`并给它一个额外的标志:

```
--coverage=dir-name-to-store-info

```

Deno 将运行测试，并使用给定的目录来存储覆盖信息。然后我们可以运行`deno coverage dir-name`，它打印出每个源文件的代码覆盖信息，如下所示:

```
cover LogRocket-Blog-Code/tdd-in-deno/stubs.ts ... 100.000% (65/65)

```

上面的打印结果显示，对于`stubs.ts`文件，测试已经运行了每个可能的代码行至少一次；因此，覆盖率是 100%。

在一些行从未在任何测试中运行的情况下，Deno 也会在覆盖率信息中打印这些行。例如，如果我们用空字符串注释 fetch 用户测试，它将生成以下输出:

```
cover LogRocket-Blog-Code/tdd-in-deno/stubs.ts ... 95.385% (62/65)
  54 |   if (name.length === 0) {
  55 |     return [];
  56 |   } else {

```

上面的打印结果告诉我们哪些代码行没有在测试中执行。

注意，我们必须在再次生成覆盖数据之前删除`coverage dir`，因为 Deno 本身不会删除这个信息。如果不删除的话，先前运行的测试的覆盖率信息可能会与当前运行的混淆，所以给出的输出不一定是正确的。

## 使用 Chai 和 Mocha 等外部库进行测试

到目前为止，我们已经了解了如何使用 Deno 的内置 API 编写测试。但是，也许您习惯于使用外部库编写测试，或者您正在将一个节点项目转移到 Deno，其中已经有用 Chai 和 Mocha 编写的[测试，您希望在移植代码时使用这些测试作为验证。](https://blog.logrocket.com/testing-node-js-mocha-chai/)

在这种情况下，Deno 还允许使用其他库进行测试，而不需要我们做很多修改。

要在 Deno 中使用外部库，我们只需导入它们，就像 Deno 内置的测试模块一样。请注意，如下所示的用于导入 Chai 的 URL 不同于用于 Mocha 的 URL:

```
import chai from "https://cdn.skypack.dev/[email protected]?dts";
import { describe, it } from "https://deno.land/x/deno_mocha/mod.ts";
const assert = chai.assert;
const expect = chai.expect;

```

有了导入库的能力，我们可以轻松地编写和运行使用 Chai 和 Mocha 编写的测试。请参见下面的示例:

```
describe("User DB operations testing using chai/mocha", () => {
  it("fetches correct user based on username", () => {
    const user = fetchUsers("testUser");
    assert(user.length === 3);
  });

  it("inserts user correctly", () => {
    const newUser: User = {
      name: "newTestUser",
      id: "newtestuser",
      password: "newtestuser",
    };
    insertUser(newUser);
    const user = fetchUsers("new");
    expect(user).to.have.lengthOf(1);
    expect(user[0].name).to.be.a("string");
    expect(user[0].name).to.equal("newTestUser");
  });

  it("deletes user correctly", () => {
    deleteUser("newTestUser", "newtestuser");
    const user = fetchUsers("test");
    expect(user).to.have.lengthOf(3);
  });
});

```

这些测试的运行方式与其他测试相同:通过调用`deno test`。

## 结论

在本文中，我们从高层次上了解了什么是测试驱动开发，以及它如何对项目有用。我们已经探索了什么时候使用 TDD 是有益的，什么时候可能不是。

关于 Deno 测试方法，我们已经看到了如何使用 Deno 的内置测试 API 编写和运行测试，以及如何从 Deno 获得代码覆盖信息。我们还看到了如何使用外部库(如 Chai 或 Mocha)来运行测试。

这方面的代码可以在 Github repo 中找到。感谢您的阅读！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)