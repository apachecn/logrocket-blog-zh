# 为什么你应该(或不应该)用 Unity 的 PlayerPrefs - LogRocket 博客保存数据

> 原文：<https://blog.logrocket.com/why-should-shouldnt-save-data-unitys-playerprefs/>

无论你是开发 Unity 的新手，还是已经有了几个项目，你很可能已经意识到玩家保存数据是多么有用。

如果你像我们大多数人一样，你的第一反应可能是快速搜索如何在 Unity 中保存球员数据。从这个搜索中，你可能会惊讶地发现有相当多不同的答案，它们的复杂程度各不相同。

最简单的解决方案是一个名为`PlayerPrefs`的 Unity 类。在本文中，我们将讨论什么是`PlayerPrefs`，如何、何时以及何时不使用它。我们还将讨论使用`PlayerPrefs`的合理替代方案。

## 在 Unity 中使用`PlayerPrefs`

有了`PlayerPrefs`，你可以拥有一个易于使用的界面，允许你开始持久化一些数据！

假设你做了一个简单的游戏，有一些关卡。你决定要保存玩家退出游戏时的等级，这样当他们重新开始游戏时，正确的等级会自动恢复。

嗯，这在`PlayerPrefs`中看起来相当容易。要保存关卡，您只需做以下事情:

```
<pre>
    // currentLevel is an integer variable representing the level the player is on
PlayerPrefs.SetInt("Level", currentLevel);
PlayerPrefs.Save();
</pre>

```

当您准备好加载时，获取保存的值也同样简单:

```
<pre>
// You can set a default return value if you haven't saved a "Level" yet with the second parameter
    currentLevel = PlayerPrefs.GetInt("Level", 0);
</pre>

```

这看起来很简单，所以是时候保存一些数据了，对吗？

嗯，算是吧。简单的回答是，像这样保存游戏状态通常是一个坏主意。

更长的答案是，在 Unity 中保存和加载数据实际上是一个非常深入的话题，没有明确的最佳解决方案。

## 使用`PlayerPrefs`的缺点

让我们先仔细看看`PlayerPrefs`，看看它是否是我们想要用来保存游戏数据的东西。这个过程的第一步是让[检查`PlayerPrefs`和](https://docs.unity3d.com/ScriptReference/PlayerPrefs.html)的文档。马上，有些事情应该提出一些眉毛。

### 保存数据的方法

首先，`PlayerPrefs`在每个应用程序的基础上保存数据。这意味着，如果你想让你的应用程序有多个保存文件，你需要发挥创造力。例如，您可以选择将一个保存文件`ID`附加到`PlayerPrefs`中的每个条目，并且只基于那个`ID`进行加载和保存。

当然，你还需要找到一种方法来保存玩家列表，这就给我们带来了使用`PlayerPrefs`的下一个问题:你只能将数据保存为字符串、浮点数或整数。

### 特定数据类型

您可能知道除了字符串、浮点和整数之外，还有更多数据类型。您可能会想到一个场景，在这个场景中，您希望使用除了这三种数据类型之外的数据类型。当我们试图弄清楚这个类是否适合保存游戏数据时，这应该是一个很大的危险信号。

如果我们需要构建某种复杂的字符串解析系统来模拟简单列表的行为，我们可能找错了对象。

### 保存数据的位置

我们可以从文档中提取的另一个重要信息是来自`PlayerPrefs`的数据的实际保存位置。如果您相对来说是编程新手，这可能是您没有考虑过的事情，但是保存的数据只是存在于文件中某个地方的数据。

在在线应用程序的情况下，该文件可能在全国不同的计算机上，或者它可能只是在一个名为`data`的子文件夹中。文件的结构可以像数据库一样，也可以是一个简单的文本文件，这取决于应用程序开发人员想做什么。

在这种情况下，如果我们在 Windows 计算机上，我们可以从 docs 中看到,`PlayerPrefs`条目被保存到注册表中。当考虑用`PlayerPrefs`保存游戏数据时，这应该是另一个危险信号。注册表实际上并不用于这类事情，它主要保存系统和应用程序配置数据。

随着所有这些旗帜和眉毛的出现，很自然会想知道`PlayerPrefs`到底是用来做什么的，如果不是用来保存玩家进度数据的话。好吧，第一个线索(和困惑点)来自名字。

## 何时使用 Unity 的`PlayerPrefs`

如果你认为“PlayerPrefs”这个名字暗示了玩家的偏好，那么你肯定不是唯一一个这样想的人，但是你也错了。

“PlayerPrefs”中的“Player”实际上指的是 Unity Player，这是一个独立的 Unity Engine 应用程序的名称，当你将你的游戏变成一个自己的应用程序时，就会构建这个应用程序。在 Unity 中更改项目设置时，您可能会遇到这种命名约定，因为运行时选项位于**播放器设置**下。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

考虑到这一点，我们可以开始为`PlayerPrefs`寻找一些合理的用例。

大多数使用 Unity 制作的项目可能会希望以特定的分辨率或质量水平启动。虽然我们可以选择将与这些事情相关的设置与游戏数据的其余部分一起保存，但也有理由假设一个舒适的分辨率与每次保存并不真正相关，而是与运行应用程序的计算机更相关。

在这种情况下，用`PlayerPrefs`保存分辨率可能非常有意义。对于用户创建的每个新的保存文件，应用程序分辨率保持在他们最初设置的水平。这比每次用户开始新的保存时，默认设置一个甚至在他们的机器上都不起作用的值更好。

对于 Unity 项目来说,`PlayerPrefs`类不是保存游戏或每个用户数据的理想方式。数据类型非常严格，数据不能轻易地从每个用户中分离出来。然而，它是一个保存与每个特定应用相关的配置和设置信息的优秀界面，如分辨率、质量水平、默认音频设备等。

所以如果我们不打算用`PlayerPrefs`保存游戏数据，我们该怎么办呢？第一步是真正考虑应用程序的细节，以及如何处理用户数据。

例如，考虑一下如果用户可以访问应用程序保存的数据会发生什么是一个好主意。实际上，任何本地储蓄系统(包括使用`PlayerPrefs`)都会导致这种可能性。

如果答案是灾难性的糟糕，那么你可能已经知道你需要将用户数据保存在一个独立的服务器上(几乎所有的在线应用程序都是如此)。然而，如果你正在制作一个离线应用程序，答案可能会大相径庭。

以一个游戏为例，让你的用户访问他们自己的保存数据可能不是最糟糕的事情，但它可能导致游戏状态的操纵，甚至意外破坏他们的进度。对于后者，你无能为力，因为无论你的数据以何种格式存储，总有可能找到随机删除其中一部分的方法。对于前者，有几个选项，从不担心它到加密您的保存数据，使其更难操作，尽管不是不可能。

## 在没有`PlayerPrefs`的情况下存储数据

为了简单起见，让我们把重点放在为本地应用程序保存数据上。本质上，我们保存玩家数据所需要做的就是将信息写入一个文件。在 Unity 中，最简单的方法是使用一个`StreamWriter`和`JsonUtility`。

`StreamWriter`将让我们实际上把数据写到一个文件中，而`JsonUtility`将处理把我们想要保存的数据转换成 JavaScript 对象符号(JSON)。

虽然我们实际上没有使用任何 JavaScript，但 JSON 已经成为传输数据的标准之一，因为它为 web 应用程序提供了便利。它非常容易被人阅读，并且非常简单地映射到 JavaScript 对象。

出于我们的目的，我们将让 Unity 通过`JsonUtility`处理与 JSON 之间的实际转换。`JsonUtility`是一种祝福也是一种诅咒，因为它非常快，但有几个限制。

在你的项目中实现它之前，我鼓励你阅读文档。在全力投入一个项目之前，理解`JsonUtility`的一些限制是非常有益的。

在杂草变得太厚之前，让我们来看一个例子。

```
<pre>
using System.IO;
using UnityEngine;

public class DataManager
{
    private Data gameData;
    private static string dataFilePath = Path.Combine(Application.persistentDataPath, "GameData.json");

    public DataManager(int level = 0)
    {
        gameData = new Data();
        gameData.level = level;
    }

    // Here we set our level with some sort of GameManager
    public void SetLevel(int level)
    {
        if (gameData == null)
        {
            gameData = new Data();
        }

        gameData.level = level;
    }

    // The method to return the loaded game data when needed
    public Data GetGameData()
    {
        return gameData;
    }

    public void Save()
    {
        // This creates a new StreamWriter to write to a specific file path
        using (StreamWriter writer = new StreamWriter(dataFilePath))
        {
            // This will convert our Data object into a string of JSON
            string dataToWrite = JsonUtility.ToJson(gameData);

            // This is where we actually write to the file
            writer.Write(dataToWrite);
        }
    }

    public void Load()
    {
        // This creates a StreamReader, which allows us to read the data from the specified file path
        using (StreamReader reader = new StreamReader(dataFilePath))
        {
            // We read in the file as a string
            string dataToLoad = reader.ReadToEnd();

            // Here we convert the JSON formatted string into an actual Object in memory
            gameData = JsonUtility.FromJson<Data>(dataToLoad);
        }
    }

    [System.Serializable]
    public class Data
    {
        // The actual data we want to save goes here, for this example we'll only use an integer to represent the level
        public int level = 0;
    }
}
</pre>

```

这个例子应该足以开始在您的本地 Unity 项目中保存和加载数据。基于 Web 的项目非常复杂，我们不会在这里讨论它们的数据管理实践。

显然，这里的开销比`PlayerPrefs`类多一点。然而，一旦你有了一个简单的保存和加载设置，它在实践中几乎一样容易使用。这里需要注意的一点是，保存在`GameData.json`中的数据易于阅读，因此可以修改。

这意味着在其当前状态下，终端用户修改他们的保存数据不会太困难。为了增加难度，我们需要研究不同的数据加密方法。

加密保存数据有点超出了本文的范围，但是如果您想搜索的话，有很多关于这个主题的完整资源。现在，作为对本地非敏感数据的`PlayerPrefs`的替代，这里概述的简单文件操作是开始保存游戏数据的一个很好的方法。

## 实施`PlayerPrefs`的例子

那么`PlayerPrefs`呢？由于它是一个保存特定于计算机的信息的优秀系统，我们可以修改我们的示例，以不干扰我们的文件操作的方式保存一些设置数据，并将在同一台计算机上的用户之间保持。

```
<pre>
using System.IO;
using UnityEngine;

public class DataManager
{
    private Data gameData;
    private SettingsData settingsData;
    private static string dataFilePath = Path.Combine(Application.persistentDataPath, "GameData.json");

    public DataManager(int level = 0)
    {
        gameData = new Data();
        gameData.level = level;

        settingsData = new SettingsData(0, 1920, 1080);
    }

    // Here we set our level with some sort of GameManager
    public void SetLevel(int level)
    {
        if (gameData == null)
        {
            gameData = new Data();
        }

        gameData.level = level;
    }

    // The method to return the loaded game data when needed
    public Data GetGameData()
    {
        return gameData;
    }

    // This can be used when we change settings values
    public void SetSettings(int quality, int horizontalResolution, int verticalResolution)
    {
        settingsData = new SettingsData(quality, horizontalResolution, verticalResolution);
    }

    public void SaveSettings()
    {
        PlayerPrefs.SetInt("QualityLevel", settingsData.qualityLevel);
        PlayerPrefs.SetInt("HorizontalResolution", settingsData.horizontalResolution);
        PlayerPrefs.SetInt("VerticalResolution", settingsData.verticalResolution);
        PlayerPrefs.Save();
    }

    public void LoadSettings()
    {
        // We can get a default value if there is no settings information, which is what the second parameter
        // in the PlayerPrefs.GetInt(p1, p2) is for
        settingsData = new SettingsData(
                            PlayerPrefs.GetInt("QualityLevel", 0),
                            PlayerPrefs.GetInt("HorizontalResolution", 1920),
                            PlayerPrefs.GetInt("VerticalResolution", 1080)
                        );
    }

    public void Save()
    {
        // This creates a new StreamWriter to write to a specific file path
        using (StreamWriter writer = new StreamWriter(dataFilePath))
        {
            // This will convert our Data object into a string of JSON
            string dataToWrite = JsonUtility.ToJson(gameData);

            // This is where we actually write to the file
            writer.Write(dataToWrite);
        }

        // This doesn't need to be coupled to the Save function, and probably shouldn't be,
        // but for demonstration purposes we'll put this here
        SaveSettings();
    }

    public void Load()
    {
        // This creates a StreamReader, which allows us to read the data from the specified file path
        using (StreamReader reader = new StreamReader(dataFilePath))
        {
            // We read in the file as a string
            string dataToLoad = reader.ReadToEnd();

            // Here we convert the JSON formatted string into an actual Object in memory
            gameData = JsonUtility.FromJson<Data>(dataToLoad);
        }

        LoadSettings();
    }

    public class SettingsData
    {
        public int qualityLevel = 0;
        public int horizontalResolution = 1920;
        public int verticalResolution = 1080;

        public SettingsData(int quality, int hr, int vr)
        {
            this.qualityLevel = quality;
            this.horizontalResolution = hr;
            this.verticalResolution = vr;
        }
    }

    [System.Serializable]
    public class Data
    {
        // The actual data we want to save goes here, for this example we'll only use an integer to represent the level
        public int level = 0;
    }
}

</pre>

```

虽然这里展示的代码并不完全适合游戏，但希望通过它解释的概念将对您的 Unity 项目开发有用。

## 结论

就是这样！希望`PlayerPrefs`的用法再清楚一点。

当我开始在 Unity 工作时，我发现这个话题有些令人困惑。然而，在一天结束时，如何处理项目中的数据真的取决于您。每个问题都有各种各样的潜在解决方案。

我希望你能做出令人敬畏的东西！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)