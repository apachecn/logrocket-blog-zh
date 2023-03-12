# 用 Jest 对 NestJS 应用程序进行单元测试

> 原文：<https://blog.logrocket.com/unit-testing-nestjs-applications-with-jest/>

测试应用程序可能看起来是一个相当复杂的概念，因此，许多程序员由于害怕失败而避免使用它——尤其是在 Node.js 世界中，测试应用程序不像 Java 那样无处不在，测试资源也很稀缺。

尽管彻底测试一个应用程序是一件复杂的事情，但是单元测试是最容易掌握的一种测试。顾名思义，单元测试专注于为尽可能小的单元编写测试。一个单元通常是一个单独的函数或方法。如果函数是纯的，这意味着它们没有任何副作用，那么为它们编写单元测试是非常容易的，因为我们可以预期给定输入的特定输出。

```
expect(add(2, 2)).toBe(4);
```

现在，我们不能真的假设我们代码库中的每个函数都是纯函数。因此，测试将变得比简单地调用一个函数并查看输出是否是我们所期望的更复杂。

通常，我们的函数会在内部利用其他函数。例如，可能有一个名为`userService.createUser()`的方法，它会在内部调用`userRepository.create()`来创建用户实体，并调用`userRepository.save()`来将其保存在所选的数据库中。在这种情况下，测试输出充其量是愚蠢的，并且不会真正给我们任何关于代码是否按预期工作的信心。

单元测试只对函数本身包含的逻辑感兴趣，而不是外部的。

所以在这个例子中，测试将检查是否调用了`userRepository.create()`,如果调用了，使用了什么参数。将逻辑与所有外部依赖项隔离开来称为模仿。这意味着用测试环境监控的假实现替换所有特定的实现。这样，跟踪对某个外部方法的调用或覆盖它的返回值就变得轻而易举了。

## 单元测试的独特之处是什么

关于单元测试的事情是，它们不应该依赖于运行它们的环境，而且它们应该是快速的。这里只是给你一个你期望的速度大小的例子:单元测试应该在每次提交之后运行。当然，有许多技术，通常是基于 Git 的，允许我们只运行那些已经被改变或者依赖于已经被改变的文件的测试。

端到端(E2E)测试就是一个依赖于环境的例子。在 E2E 测试中，我们将应用程序作为一个整体来测试，因此我们提供了一个测试数据库并准备了预期的环境。但是它很慢，并且需要特殊的设置，因此，在开发期间运行它实际上是不可行的，而这正是我们通常运行单元测试的时候。在 CI/CD 服务上应该有一个专门的设置，负责运行 E2E 测试。

然而，控制外部依赖有一个非常重要的缺点。在测试中，我们覆盖了通常由依赖项返回的值。不管出于什么原因，如果外部依赖关系改变了，现在返回一个`{ token, user }`对象而不是一个`User`对象，测试仍然会通过，因为在我们的测试环境中它仍然会返回一个`User`对象。

这就是为什么单元测试只是众多测试中的一种。它确保当我们控制所有的外部依赖时，函数具有预期的行为。例如，还有集成测试和契约测试，它们可以确保我们的契约(外部依赖项的预期输入/输出值)没有改变。

但是说实话，我们并不是每隔一天就改变方法。大部分代码永远不会改变，或者至少 I/O 值不会改变，所以通过只从单元测试开始，我们仍然可以对我们编写的代码有更好的信心。

## NestJS 如何帮助我们编写单元测试

### 依赖注入

Nest 迫使我们通过其内置的依赖注入来编写更容易测试的代码——这种设计模式表明，中央机构负责创建和提供依赖，而类只是假设将提供依赖，而不是自己创建依赖。所以，不要写:

```
constructor() {
  this.a = new A();
}
```

我们会写:

```
constructor(private readonly a: A) {}
```

由于不久前提出的 JS [元数据反射 API](https://github.com/rbuckton/reflect-metadata) ，后一种语法是可能的。

依赖注入通常基于接口而不是具体的类。然而，在 TypeScript 中，接口(以及一般的类型)只在编译时可用，而在运行时不可用，因此以后就不能依赖了。因此，在 Nest 中，通常不使用接口，而是使用基于类的注入。

### 测试模块

有了依赖注入，替换我们的依赖变得微不足道。但是对于单元测试，我们不愿意为每个测试重新创建整个应用程序。相反，我们希望创建最简单的设置。谢天谢地，Nest 为我们提供了`@nestjs/testing`包。这个包使我们能够创建一个嵌套模块，就像我们通常会做的那样，只声明测试中使用的依赖项。这里有一个例子:

```
const module: TestingModule = await Test.createTestingModule({
  providers: [
    PlaylistService,
    {
      provide: getRepositoryToken(Playlist),
      useClass: PlaylistRepositoryFake,
    },
  ],
}).compile();

playlistService = module.get(PlaylistService);
playlistRepository = module.get(getRepositoryToken(Playlist));
```

所以在上面的例子中，我们向测试模块“导入”了两个东西:`playlistService`和`playlistRepository`。这种设置对于测试像创建播放列表这样的事情来说是理想的；我们将检查是否用预期的参数调用了存储库。

希望现在你已经理解了为什么 Nest 的代码库很容易测试，以及幕后发生了什么。现在让我们看一个实际的例子:测试前面提到的播放列表模块场景。

## 现实世界的例子

[存储库](https://github.com/maciejcieslar/nest-unit-tests)包含稍后将在示例中使用的代码。请记住，这是一个非常基本的设置，并不一定反映应用程序应该如何构造。

我们将关注负责创建、查找、更新和删除播放列表的`PlaylistService`类。在一个更复杂的设置中，我们可能会为每种情况创建单独的类(一个用于创建，一个用于更新)以及它们相关的逻辑，但是我们会保持简单。

```
@Injectable()
export class PlaylistService {
  public constructor(
    @InjectRepository(Playlist)
    private readonly playlistRepository: Repository<Playlist>,
  ) {}

  public async findOneByIdOrThrow(id: string): Promise<Playlist> {
    const playlist = await this.playlistRepository.findOne({
      id,
    });

    if (!playlist) {
      throw new NotFoundException('No playlist found.');
    }

    return playlist;
  }

  public async createOne(
    createPlaylistData: CreatePlaylistData,
  ): Promise<Playlist> {
    const { title, description } = createPlaylistData;

    const playlist = this.playlistRepository.create({
      title,
      description,
    });

    const createdPlaylist = await this.playlistRepository.save(playlist);

    return createdPlaylist;
  }

  public async removeOne(
    removePlaylistData: RemovePlaylistData,
  ): Promise<void> {
    const { id } = removePlaylistData;

    const playlist = await this.findOneByIdOrThrow(id);

    await this.playlistRepository.remove([play­list]);

    return null;
  }

  public async updateOne(
    updatePlaylistData: UpdatePlaylistData,
  ): Promise<Playlist> {
    const { id, ...updateData } = updatePlaylistData;

    const existingPlaylist = await this.findOneByIdOrThrow(id);

    const playlist = this.playlistRepository.create({
      ...existingPlaylist,
      ...updateData,
    });

    const updatedPlaylist = await this.playlistRepository.save(playlist);

    return updatedPlaylist;
  }
}
```

所有的方法都是 CRUD 行为的最基本的实现。本质上，它们调用各自的存储库方法，所以`.createOne()`方法调用`playlistRepository.save()`,`.findOne()`方法调用`playlistRepository.findOne()`，等等。

实现就绪后，让我们测试一下。

### 写作测试

默认情况下，使用 Nest CLI 生成的项目被设置为使用 Jest 运行测试，因此我们将坚持使用 Jest。

一旦掌握了窍门，编写单元测试就很容易了。它实际上只是创建方法行为不同的场景，然后测试其预期的行为。

在编写测试之前，让我们创建测试模块。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

```
describe('PlaylistService', () => {
  let playlistService: PlaylistService;
  let playlistRepository: Repository<Playlist>;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        PlaylistService,
        {
          provide: getRepositoryToken(Playlist),
          useClass: PlaylistRepositoryFake,
        },
      ],
    }).compile();

    playlistService = module.get(PlaylistService);
    playlistRepository = module.get(getRepositoryToken(Playlist));
  });
});
```

就像上面的例子一样，模块将创建`playlistService`和`playlistRepository`并解析它们的依赖关系。因为我们不想创建实际的数据库连接，所以我们用`PlaylistRepositoryFake`覆盖了`playlistRepository`。`getRepositoryToken`函数让我们获得存储库的注入令牌。

### 提供假货

`PlaylistRepositoryFake`是声明与`PlaylistRepository`相同方法的类，只是所有方法都是空的，没有行为。

```
export class PlaylistRepositoryFake {
  public create(): void {}
  public async save(): Promise<void> {}
  public async remove(): Promise<void> {}
  public async findOne(): Promise<void> {}
}
```

你可能想知道为什么我们必须这么做——我们不是在嘲笑所有的外部依赖吗？

假货有两个重要作用:

### 1.期望在创建类实例的过程中满足某些条件

使用 fakes 的第一个原因在我们的例子中尤其重要:一个类`playlistRepository`，它在实例创建期间执行一些特定的逻辑。

存储库在创建时，希望导入数据库模块(在模块上下文中),并打开与数据库的连接。因为我们没有导入数据库模块，而且根本没有数据库在运行，所以在创建模块的过程中，存储库会抛出一个错误。

通过使用另一个完整的类(假的)，所有与数据库模块相关的逻辑都消失了。它是一个简单的不做任何事情的对象。

### 2.监控对外部依赖项的调用

为了检查一个方法是否真正利用了外部依赖，我们必须以某种方式注册这个调用已经被发出。例如，如前所述，我们希望确定`.createOne()`方法实际上调用了`playlistRepository.save()`并在数据库中保存了实体。

在测试模块设置中，我们保存对每个创建的实例的引用，所以`playlistRepository`和`playlistService`都有对测试模块实例化的对象的引用。

```
playlistService = module.get(PlaylistService);
playlistRepository = module.get(getRepositoryToken(Playlist));
```

因为 JavaScript 中的对象是通过引用传递的，所以我们可以很容易地覆盖特定的方法，比如:

```
let calledTimes = 0

playlistRepository.save = () => {
  calledTimes += 1;
}
```

但是手动操作会令人厌倦并且容易出错。Jest 为我们提供了一个更好的方法:[间谍](https://jestjs.io/docs/en/jest-object#jestspyonobject-methodname)。

间谍的定义如下:

```
const playlistRepositorySaveSpy = jest
  .spyOn(playlistRepository, 'save')
  .mockResolvedValue(savedPlaylist);
```

这个 spy 做了两件事:它覆盖了`playlistRepository`的`.save()`方法，并为开发人员提供了一个 API 来选择应该返回什么。在这个代码片段中，我们使用`.mockResolvedValue()`而不是`.mockReturnValue()`，因为最初的实现返回了一个承诺。

通过保存对 spy 的引用(将它赋给一个变量)，我们可以稍后在测试中执行以下操作:

```
expect(playlistRepositorySaveSpy).toBeCalledWith(createdPlaylistEntity);
```

请注意，变量名由三部分组成:首先，被覆盖的对象的名称；第二，方法的名字；第三，单词`Spy`，所以我们可以很容易地将它与我们的其他变量区分开来。让变量名保持这样的结构有助于我个人保持我的测试更干净，整体可读性更好。

尽管我们覆盖了一个方法的行为，Jest 的 spies 仍然要求所提供的对象具有上述属性。因此，如果我们提供一个简单的`{}`空对象，Jest 将抛出以下错误:

```
Cannot spy the updateOne property because it is not a function; undefined given instead
```

### 伪造品、存根和测试替身

我已经决定将替换类命名为“Fake ”,因为就我所知，这是包含类的简化逻辑或者完全没有逻辑的对象的合适名称。

人们经常会弄不清假替身和测试替身之间的区别，以及测试世界中的所有其他名称。这里有一篇很棒的[文章](https://blog.pragmatists.com/test-doubles-fakes-mocks-and-stubs-1a7491dfa3da)解释了测试中使用的不同名称及其含义。

请注意，在 Jest 中，间谍是模拟的，也可以是存根，因为它们注册调用(模拟)并可以覆盖返回值(存根)。

抛开所有的行话，让我们来写一些测试。

### 测试`.createOne()`方法

让我们快速看一下`.createOne()`方法的实现:

```
public async createOne(
  createPlaylistData: CreatePlaylistData,
): Promise<Playlist> {
  const { title } = createPlaylistData;

  if (!title) {
    throw new BadRequestException('Title is required.');
  }

  const playlist = this.playlistRepository.create({
    title,
  });

  const createdPlaylist = await this.playlistRepository.save(playlist);

  return createdPlaylist;
}
```

作为参数，该方法采用一个保存播放列表标题的对象。如果标题不存在，该方法将抛出一个错误。这是我们要测试的第一个案例。

```
describe('creating a playlist', () => {
  it('throws an error when no title is provided', async () => {
    expect.assertions(2);

    try {
      await playlistService.createOne({ title: '' });
    } catch (e) {
      expect(e).toBeInstanceOf(BadRequestException);
      expect(e.message).toBe('Title is required.');
    }
  });
});
```

我喜欢将测试分组到描述动作发生的逻辑块中——因此，在这种情况下，创建一个播放列表。

在测试开始时，我们在创建假数据；通常我们会用像 [faker.js](https://github.com/marak/Faker.js/) 这样的库生成假数据，但是在这里，我们唯一需要的是一个空字符串。因为我们知道这个方法会提前抛出——由于没有提供标题——所以我们不需要监视任何外部方法，因为在这种情况下它永远不会被调用。

在我们用假参数调用函数后，我们期望它抛出一个错误。在这里，要具体一点。我们不应该期望抛出任何类型的错误，因为它也可能是我们代码中的一个打字错误。更好的做法是，假设错误是一个特定类的实例。在我们的例子中，那将是一个`BadRequestException`。

调用 Jest 的`.expect(value)`方法后，返回一个包含 Jest 匹配的对象。匹配是一种抽象，它让我们无需编写自己的代码就能断言所提供的值，反过来，也让我们的测试保持干爽。使用匹配器大大缩短了测试代码并提高了可读性。

匹配器的一个例子是检查两个对象是否相同的`.toEqual()`方法。它通过循环比较它们的键和值来做到这一点，而 Jest 的`.toBe()`方法只是使用`===`操作符来检查严格的相等性。

查看 Jest 内置匹配器上的[文档](https://jestjs.io/docs/en/expect)了解更多信息。

第一个场景已经完成。让我们继续走向所谓的幸福之路，一切都按计划进行。

```
describe('creating a playlist', () => {
  it('throws an error when no title is provided', async () => {
    // ...
  });

  it('calls the repository with correct paramaters', async () => {
    const title = faker.lorem.sentence();

    const createPlaylistData: CreatePlaylistData = {
      title,
    };

    const createdPlaylistEntity = Playlist.of(createPlaylistData);

    const savedPlaylist = Playlist.of({
      id: faker.random.uuid(),
      createdAt: new Date(),
      updatedAt: new Date(),
      title,
    });

    const playlistRepositorySaveSpy = jest
      .spyOn(playlistRepository, 'save')
      .mockResolvedValue(savedPlaylist);

    const playlistRepositoryCreateSpy = jest
      .spyOn(playlistRepository, 'create')
      .mockReturnValue(createdPlaylistEntity);

    const result = await playlistService.createOne(createPlaylistData);

    expect(playlistRepositoryCreateSpy).toBeCalledWith(createPlaylistData);
    expect(playlistRepositorySaveSpy).toBeCalledWith(createdPlaylistEntity);
    expect(result).toEqual(savedPlaylist);
  });
});
```

您可能会注意到，该设置与上一个场景非常相似。由于 AAA(排列、动作、断言)模式，设置看起来总是有些相同。这种模式是不言自明的，但是它建议按照指定的顺序做事。

首先，我们设置测试所需的所有假数据。然后，我们调用适当的代码段(在我们的例子中是方法),并使用 Jest 的`.expect()`函数来断言结果是我们所期望的。AAA 模式已经成为编写单元测试的标准，所以我强烈建议坚持使用它。

现在，在我们用假参数调用方法之前，我们创建了间谍。在这种情况下，我们需要两个外部方法的间谍:`playlistRepository.create()`和`playlistRepository.save()`，我们模仿这两个方法(跟踪调用)和存根(覆盖返回值)。

在我们调用该方法之后，我们期望结果是“保存”在数据库中的播放列表。

为了检查模拟是否已经被调用，我们可以使用`expect(mock).toHaveBeenCalledWith()`方法并提供我们期望模拟被调用的参数。

在编写了这两个测试场景之后，一个是错误的，另一个是快乐的路径，我们现在可以确信，假设依赖项完全像它们的契约(提供的模拟和存根)一样工作，`.createOne()`方法工作正常。

## 测试其他三种方法

鉴于每个测试都遵循 AAA 模式，没有必要重复解释每一个测试。

其余三种方法的测试— `.updateOne()`、`.findOne()`和`.removeOne()` —看起来或多或少是一样的。

```
describe('PlaylistService', () => {
  let playlistService: PlaylistService;
  let playlistRepository: Repository<Playlist>;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        PlaylistService,
        {
          provide: getRepositoryToken(Playlist),
          useClass: PlaylistRepositoryFake,
        },
      ],
    }).compile();

    playlistService = module.get(PlaylistService);
    playlistRepository = module.get(getRepositoryToken(Playlist));
  });

  describe('updating a playlist', () => {
    it('calls the repository with correct paramaters', async () => {
      const playlistId = faker.random.uuid();
      const title = faker.lorem.sentence();

      const updatePlaylistData: UpdatePlaylistData = {
        id: playlistId,
        title,
      };

      const existingPlaylist = Playlist.of({
        id: playlistId,
        createdAt: new Date(),
        updatedAt: new Date(),
        title: faker.lorem.word(),
      });

      const newPlaylistData = Playlist.of({
        ...existingPlaylist,
        title,
      });

      const savedPlaylist = Playlist.of({
        ...newPlaylistData,
      });

      const playlistServiceFindOneByIdOrThrowSpy = jest
        .spyOn(playlistService, 'findOneByIdOrThrow')
        .mockResolvedValue(existingPlaylist);

      const playlistRepositoryCreateSpy = jest
        .spyOn(playlistRepository, 'create')
        .mockReturnValue(newPlaylistData);

      const playlistRepositorySaveSpy = jest
        .spyOn(playlistRepository, 'save')
        .mockResolvedValue(savedPlaylist);

      const result = await playlistService.updateOne(updatePlaylistData);

      expect(playlistServiceFindOneByIdOrThrowSpy).toHaveBeenCalledWith(
        updatePlaylistData.id,
      );

      expect(playlistRepositoryCreateSpy).toHaveBeenCalledWith({
        ...existingPlaylist,
        title,
      });

      expect(playlistRepositorySaveSpy).toHaveBeenCalledWith(newPlaylistData);
      expect(result).toEqual(savedPlaylist);
    });
  });

  describe('removing a playlist', () => {
    it('calls the repository with correct paramaters', async () => {
      const playlistId = faker.random.uuid();

      const removePlaylistData: RemovePlaylistData = {
        id: playlistId,
      };

      const existingPlaylist = Playlist.of({
        id: playlistId,
        createdAt: new Date(),
        updatedAt: new Date(),
        title: faker.lorem.sentence(),
      });

      const playlistServiceFindOneByIdOrThrowSpy = jest
        .spyOn(playlistService, 'findOneByIdOrThrow')
        .mockResolvedValue(existingPlaylist);

      const playlistRepositoryRemoveSpy = jest
        .spyOn(playlistRepository, 'remove')
        .mockResolvedValue(null);

      const result = await playlistService.removeOne(removePlaylistData);

      expect(playlistServiceFindOneByIdOrThrowSpy).toHaveBeenCalledWith(
        removePlaylistData.id,
      );

      expect(playlistRepositoryRemoveSpy).toHaveBeenCalledWith([
        existingPlaylist,
      ]);

      expect(result).toBe(null);
    });
  });

  describe('creating a playlist', () => {
    it('throws an error when no title is provided', async () => {
      const title = '';

      expect.assertions(2);

      try {
        await playlistService.createOne({ title });
      } catch (e) {
        expect(e).toBeInstanceOf(BadRequestException);
        expect(e.message).toBe('Title is required.');
      }
    });

    it('calls the repository with correct paramaters', async () => {
      const title = faker.lorem.sentence();

      const createPlaylistData: CreatePlaylistData = {
        title,
      };

      const createdPlaylistEntity = Playlist.of(createPlaylistData);

      const savedPlaylist = Playlist.of({
        id: faker.random.uuid(),
        createdAt: new Date(),
        updatedAt: new Date(),
        title,
      });

      const playlistRepositorySaveSpy = jest
        .spyOn(playlistRepository, 'save')
        .mockResolvedValue(savedPlaylist);

      const playlistRepositoryCreateSpy = jest
        .spyOn(playlistRepository, 'create')
        .mockReturnValue(createdPlaylistEntity);

      const result = await playlistService.createOne(createPlaylistData);

      expect(playlistRepositoryCreateSpy).toBeCalledWith(createPlaylistData);
      expect(playlistRepositorySaveSpy).toBeCalledWith(createdPlaylistEntity);
      expect(result).toEqual(savedPlaylist);
    });
  });

  describe('finding a playlist', () => {
    it('throws an error when a playlist doesnt exist', async () => {
      const playlistId = faker.random.uuid();

      const playlistRepositoryFindOneSpy = jest
        .spyOn(playlistRepository, 'findOne')
        .mockResolvedValue(null);

      expect.assertions(3);

      try {
        await playlistService.findOneByIdOrThrow(playlistId);
      } catch (e) {
        expect(e).toBeInstanceOf(NotFoundException);
        expect(e.message).toBe('No playlist found.');
      }

      expect(playlistRepositoryFindOneSpy).toHaveBeenCalledWith({
        id: playlistId,
      });
    });

    it('returns the found playlist', async () => {
      const playlistId = faker.random.uuid();

      const existingPlaylist = Playlist.of({
        id: playlistId,
        createdAt: new Date(),
        updatedAt: new Date(),
        title: faker.lorem.sentence(),
      });

      const playlistRepositoryFindOneSpy = jest
        .spyOn(playlistRepository, 'findOne')
        .mockResolvedValue(existingPlaylist);

      const result = await playlistService.findOneByIdOrThrow(playlistId);

      expect(result).toBe(existingPlaylist);
      expect(playlistRepositoryFindOneSpy).toHaveBeenCalledWith({
        id: playlistId,
      });
    });
  });
});
```

每个测试首先生成假数据，然后设置间谍，执行方法，并期望结果和模拟是某种方式。

## 运行测试

Nest 的 CLI 生成的项目被自动配置为运行测试，只需在终端中键入`npm run test`。总而言之，用 Jest 来说，运行测试通常归结为执行`Jest`命令。

在开发过程中，让测试在每次代码更改后运行真的很有帮助，所以 Jest 也为我们提供了`--watch`选项来做到这一点。在[文档](https://jestjs.io/docs/en/configuration)中可以找到更多关于用更加面向案例的配置设置 Jest 的信息。

## 结论

对我们的应用程序进行单元测试是创建更有弹性和更健壮的软件的第一步。在每个提交(本地)和 CI 服务上运行所述测试可以极大地提高我们的信心。

由于 Nest——或者，一般来说，任何一种利用依赖注入的框架——代码很容易测试，因为它的所有依赖都在实现之外管理，这反过来又使它们很容易被替换。

在重构过程中进行单元测试也是有帮助的，因为我们确信在试图改进我们的代码库时没有破坏任何东西。

## 您是否添加了新的 JS 库来提高性能或构建新特性？如果他们反其道而行之呢？

毫无疑问，前端变得越来越复杂。当您向应用程序添加新的 JavaScript 库和其他依赖项时，您将需要更多的可见性，以确保您的用户不会遇到未知的问题。

LogRocket 是一个前端应用程序监控解决方案，可以让您回放 JavaScript 错误，就像它们发生在您自己的浏览器中一样，这样您就可以更有效地对错误做出反应。

[![LogRocket Dashboard Free Trial Banner](img/e8a0ab42befa3b3b1ae08c1439527dc6.png)](https://lp.logrocket.com/blg/javascript-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/javascript-signup)

[LogRocket](https://lp.logrocket.com/blg/javascript-signup) 可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用的性能，报告客户端 CPU 负载、客户端内存使用等指标。

自信地构建— [开始免费监控](https://lp.logrocket.com/blg/javascript-signup)。