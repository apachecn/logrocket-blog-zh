# 如何用 PyQt 构建一个 GUI

> 原文：<https://blog.logrocket.com/how-to-build-gui-pyqt/>

## 介绍

图形用户界面，通常称为 GUI，是当今大多数个人计算机的特征。它为不同计算技能水平的用户提供了直观的体验。尽管 GUI 应用程序可能会使用更多的资源，但由于其点击的特性，它们通常是用户友好的。

PyQt 是一个工具包，你可以用它来用 Python 开发跨平台的 GUI 应用程序。如果你已经牢固地掌握了这门语言，它是强大而容易学习的。

本文将向您介绍用 PyQt 构建 GUI 的基础知识。它要求您具备 Python 和面向对象编程的基础知识。我们将主要关注 PyQt，而不是解释 Python 概念。

## PyQt 是什么？

PyQt 是跨平台应用开发框架的 Python 绑定， [Qt](https://www.qt.io/) 。使用 PyQt 可以让您受益于使用 Python 这样简单但功能强大的语言开发 GUI 应用程序。它公开了 Qt API 的所有功能。

[河岸计算](https://riverbankcomputing.com/about)是 PyQt 开发和维护背后的公司。它的最新稳定版本是 PyQt6。从发布历史来看，PyQt 主版本的发布周期似乎与 Qt 同步。

在本文中，我们将使用 PyQt5。它需要 Python v3.5 或更高版本，尽管您也可以使用早期版本的 Python 进行构建。但是请注意，PyQt6 需要 Python v3.6.1 或更高版本。

在开始构建 GUI 应用程序之前，您应该知道 PyQt 是双重许可的。这些许可证是 GPL v3 和河岸商业许可证。

如果您在 GPL v3 许可下获得 PyQt，那么您需要在兼容的许可下发布您的代码。同样，您的 PyQt 许可证应该与您的 Qt 许可证保持一致。

我在这里提供的是 PyQt 许可需求的高级概述。我建议您熟悉您想要使用的特定版本 PyQt 的许可要求。

## 如何安装 PyQt

对于 PyQt 的商业许可版本，您必须在安装之前首先获得许可。要安装 GPL 许可版本，运行下面的命令创建一个虚拟环境并安装 PyQt5。虽然您可以在全局范围内安装 PyQt，但是建议您使用虚拟环境。

```
# Create virtual environment
python3 -m venv env

# Activate virtual environment
source env/bin/activate

# Install PyQt5
pip install PyQt5

```

关于详细的安装说明，您应该查看您想要使用的 PyQt 的特定版本的文档。它有安装 GPL 和商业版本的说明。该文档还提供了故障排除提示，以防您遇到一些错误。

## 用 PyQt 构建一个简单的 GUI

让我们通过构建一个简单的“hello world”GUI 来体验一下 PyQt。构建这个简单的应用程序将使后面的部分变得容易得多。

在我们开始之前，这里值得一提的是 PyQt 使用 camelCase 作为方法和属性名。在整篇文章中，我们将在命名变量和函数时使用 camelCase 以保持一致性，而不是 Python 中推荐的命名约定。

目前我们将有意保持事情的简单和最小化。我假设您在项目目录中创建了一个`app.py`文件；按照下面的步骤，您可以将每一步中的代码行添加到您的`app.py`文件中。

### 步骤 1:导入所需的类

PyQt 附带了几个内置模块。然而，在构建 GUI 时，您最经常与之交互的模块是`QtWidgets`模块。它有你将用来创建你的 GUI 的类。

因为我们的目标是创建最基本的“hello world”GUI，所以我们将只使用`QApplication`和`QWidgets`类。首先像这样导入它们:

```
from PyQt.QtWidgets import QApplication, QWidgets

```

您可以用同样的方式导入您想在应用程序中使用的其他类。

### 步骤 2:初始化应用程序

我们需要通过创建一个`QApplication`的实例来初始化应用程序。它负责管理应用程序的主要设置和控制流。因此，您应该在创建与用户界面相关的任何其他对象之前实例化该类。

```
application = QApplication([])

```

要了解更多关于`QApplication`类的职责，请查看 Qt 文档。

在上面的代码中，我们向`QApplication`传递了一个空数组，但是如果您希望应用程序从命令行接收参数，您也可以传递`sys.argv`来代替。如果要将`sys.argv`作为参数传递，一定要导入`sys`。

### 步骤 3:创建主窗口

主窗口也称为顶层窗口，是一个没有父窗口的小部件。每个 GUI 都必须有一个主窗口。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

现在，我们将创建一个`QWidget`的实例，并使它成为我们的主窗口，如下所示:

```
mainWindow = QWidget()
mainWindow.setGeometry(0, 0, 350, 400)
mainWindow.setWindowTitle('Hello World')

```

在创建了一个`Qwidget`的实例之后，还有其他几个方法可以调用。对于我们的简单 GUI，我们调用了`setGeometry`方法和`setWindowTitle`方法。

`setGeometry`方法用于在屏幕上定位 GUI 并设置其尺寸。它的函数签名是`setGeometry(x, y, width, height)`。前两个参数指定窗口在屏幕上的`x`和`y`坐标，`width`和`height`分别用于设置窗口的宽度和高度。

`setWindowTitle`方法，顾名思义，用于设置应用程序的标题。您可以将标题作为字符串参数传递。如果你不自己设置，窗口就不会有标题。

### 步骤 4:显示主窗口

默认情况下，我们在上一步中创建的窗口不可见。我们需要通过调用`show`方法来显示它:

```
mainWindow.show()

```

### 步骤 5:开始事件循环

最后，您需要通过调用`application.exec`方法来启动事件循环:

```
application.exec()

```

您也可以使用`application.exec_()`来启动事件循环。

在完成上述五个步骤后，您的`app.py`文件应该包含以下代码:

```
from PyQt5.QtWidgets import QWidget, QApplication

application = QApplication([])

mainWindow = QWidget()

mainWindow.setGeometry(0, 0, 350, 400)
mainWindow.setWindowTitle('Hello World')

mainWindow.show()

application.exec()

```

像任何其他 Python 脚本一样，您需要通过使用命令`python3 app.py`来运行`app.py`。您应该能够看到显示的窗口。窗口的外观很大程度上取决于您的系统。在 Linux 上，它应该类似于下图。

![Basic hello world app](img/92d16c1f6a56600c263c56152023ac8c.png)

## PyQt 的主要概念

我们刚刚创建了第一个“hello world”GUI。现在让我们看看一些主要的概念，它们将拓宽我们对 PyQt 的知识。对于构建产品级 GUI 来说，熟悉这些概念是必要的。

### 小工具

像大多数 GUI 工具包一样，小部件是 PyQt GUIs 的构建块。您可以使用小部件来显示数据、接收用户输入，或者将它用作对其他相关小部件进行分组的容器。

大多数小部件嵌套在其他小部件中，但是，总有一个小部件没有父部件。如前所述，没有父窗口的小部件被称为窗口。

在 PyQt 中创建小部件的主要类是`QWidgets`类。在 PyQt 中创建 ui 的所有元素要么是`QWidgets`类的子类，要么与`QWidgets`类结合使用。

您可以在 PyQt 或 Qt 文档中读到几个小部件类。我们无法在此一一提及。一些基本的小部件类包括:

*   `QLabel`用于显示文本和图像
*   `QPushButton`用于创建命令按钮
*   `QLineEdit`用于创建单行文本编辑器
*   `QRadioButton`用于创建带有文本标签的单选按钮

让我们向“hello world”GUI 添加一个简单的`pushButton`小部件:

```
from PyQt5.QtWidgets import QWidget, QApplication, QPushButton

application = QApplication([])

mainWindow = QWidget()
mainWindow.setGeometry(0, 0, 350, 400)
mainWindow.setWindowTitle('Button Widget')

pushButton = QPushButton(parent=mainWindow, text='Click me')

mainWindow.show()
application.exec()

```

运行上面的代码将创建一个类似下图的窗口。

![button widget that says "click me"](img/77ca46de0b4aeba78fb83b6e7dc88914.png)

### 事件循环

大多数 GUI 工具包都是事件驱动的。PyQt 也不例外。事件可以源自用户与应用程序的交互，如点击按钮、填写输入文本、点击链接或关闭窗口。事件也可以来自窗口系统或其他来源。管理这些事件是事件循环的责任。

调用`.exec`方法将触发事件循环，正如我们在构建“hello world”GUI 时强调的那样。循环等待事件发生，并在事件发生时做出响应。如果收到`Terminate`事件，它将终止并退出应用程序。

正是通过这种事件响应功能，您可以使用信号和插槽向 GUI 添加交互性。我们将在下一节学习信号和槽。

### 信号和插槽

在前面的一节中，我们研究了如何使用小部件来创建 GUI 的可视组件。通过信号和插槽，你可以增加图形用户界面的交互性。我们在我们的“hello world”GUI 中添加了一个按钮小部件，但是单击按钮目前没有任何作用。

通常，单击按钮应该触发一个操作，比如打开另一个小部件、关闭小部件或登录。您需要信号和插槽来响应这种用户操作或小部件状态的变化。

信号是每当有事发生时小部件发出的通知。它可以是按钮点击、鼠标移动或文本输入字段的改变。不同的部件发出不同的信号。例如，一个按钮部件在被点击时会发出`clicked`信号。按钮部件还发出其他不太为人所知的信号，如`pressed`、`released`和`toggled`信号。要知道特定小部件发出什么信号，您需要阅读小部件对应类的文档。

插槽是在小部件发出特定信号后调用的函数或方法。一些小部件带有预定义的插槽。但是，您也可以定义插槽来处理感兴趣的信号。

为了说明我们刚刚学到的内容，让我们向按钮小部件添加一个插槽，以便它响应`clicked`信号而运行:

```
from PyQt5.QtWidgets import QWidget, QApplication, QPushButton

application = QApplication([])

mainWindow = QWidget()
mainWindow.setGeometry(0, 0, 350, 400)
mainWindow.setWindowTitle('Slot and Signal')

def clickedSlot():
    print('The button has been clicked')

pushButton = QPushButton(parent=mainWindow, text='Click me')
pushButton.clicked.connect(clickedSlot)

mainWindow.show()
application.exec()

```

运行上面的代码并点击按钮后，您应该在终端上看到文本`The button has been clicked`。

## PyQT 布局管理

到目前为止，我们只研究了 PyQt 的最基本的组件。在现实世界的应用程序中，你将在同一个窗口中处理多个小部件。幸运的是，Qt 有几个功能来管理应用程序 UI 中的小部件布局。您可以使用这些功能来描述如何排列小部件。每当空间发生变化时，布局会自动调整小部件的大小和位置。因此，用户界面仍然可用。

虽然 PyQt 有几种布局形式，但在本文中我们将研究水平、垂直、网格和表单布局。您可以在 PyQt 或 Qt 文档中阅读其他内容。每个提到的布局都有一个对应的内置布局类。这些类别是:

*   `QHBoxLayout`
*   `QVBoxLayout`
*   `QGridLayout`
*   `QFormLayout`

### 水平布局

您可以使用内置的`QHBoxLayout`类来水平布局小部件，通常是从左到右。对于从右到左的语言，它还可以从右到左排列小部件。

在下面的代码中，我修改了“hello world”GUI，以水平布局显示五个图像。为了避免重复，我使用了一个`for`循环将图像添加到布局中。在运行这段代码之前，确保在`cat.jpg`文件中有一个图像:

```
from PyQt5.QtGui import QPixmap
from PyQt5.QtWidgets import (
    QLabel,
    QWidget,
    QApplication,
    QHBoxLayout,
)

application = QApplication([])

mainWindow = QWidget()

mainWindow.setGeometry(0, 0, 350, 400)
mainWindow.setWindowTitle('Horizontal Layout')

horizontalLayout = QHBoxLayout()

for num in range(6):
    label = QLabel()
    pixmap = QPixmap('cat.jpg')
    label.setPixmap(pixmap)
    horizontalLayout.addWidget(label)

mainWindow.setLayout(horizontalLayout)
mainWindow.show()

application.exec()

```

在 PyQt 中，您可以使用`QLabel`小部件来呈现图像。从`QtGui`模块导入后，首先创建一个`QPixmap`类的实例。使用`QLabel`类的`setPixmap`方法将其设置在标签小部件上，如上面的`for`循环所示。

在进入下一部分之前，您可以先研究一下代码。除了布局图像，你还可以布局其他小部件，比如按钮。

上面的代码应该创建一个类似下图的 GUI。

![GUI with horizontal line of cats](img/11640cad67ac0d6a3352c63781f37b5a.png)

### 垂直布局

与从左到右或从右到左水平放置小部件的`QHBoxLayout`类不同，`QVBoxLayout`类从上到下垂直放置小部件。

下面的代码展示了如何使用`QVBoxLayout`类进行垂直布局管理。它非常类似于水平布局:

```
from PyQt5.QtGui import QPixmap
from PyQt5.QtWidgets import (
    QLabel,
    QWidget,
    QApplication,
    QVBoxLayout,
)

application = QApplication([])

mainWindow = QWidget()

mainWindow.setGeometry(0, 0, 350, 400)
mainWindow.setWindowTitle('Vertical Layout')

verticalLayout = QVBoxLayout()

for num in range(6):
    label = QLabel()
    pixmap = QPixmap('cat.jpg')
    label.setPixmap(pixmap)
    verticalLayout.addWidget(label)

mainWindow.setLayout(verticalLayout)
mainWindow.show()

application.exec()

```

运行上面的代码应该会创建一个图形用户界面，从上到下排列图像，如下图所示。

![GUI with vertical cat photos](img/8d123be3fb12db94e19eca67a441f21c.png)

### 网格布局

网格布局管理包括在二维网格中布局小部件。`QGridLayout`是一个方便的内置类，可以做到这一点。在网格布局中，一个项目可以占据两个网格。您还可以在网格项目中嵌套另一个布局。它使得构建更复杂的 GUI 变得容易得多。

您可以创建一个`QGridLayout`类的实例，并使用`addWidget`方法向其添加小部件。与前几节一样，我在循环中将图像添加到网格中，以避免在下面的代码中出现重复。在用小部件填充网格时，也可以跳过网格:

```
from PyQt5.QtGui import QPixmap
from PyQt5.QtWidgets import (
    QLabel,
    QWidget,
    QApplication,
    QGridLayout,
)

application = QApplication([])

mainWindow = QWidget()

mainWindow.setGeometry(0, 0, 350, 400)
mainWindow.setWindowTitle('Grid Layout')

gridLayout = QGridLayout()

for row in range(3):
    for col in range(3):
        label = QLabel()
        pixmap = QPixmap('cat.jpg')
        label.setPixmap(pixmap)
        gridLayout.addWidget(label, row, col)

mainWindow.setLayout(gridLayout)
mainWindow.show()

application.exec()

```

因为您将小部件放置在二维网格中，所以在将它添加到布局中时，您需要指定每个小部件的位置。

运行上面的代码后，您的“hello world”GUI 应该类似于下图。

![GUI with cat pics in a grid](img/026c6c061c5bd76bc98f52a65331ccd7.png)

### 表单布局

表单布局主要用于管理输入小部件及其相关标签。它由成行的小部件组成，这些小部件以标签-字段对的形式排列。您需要使用`QFormLayout`类在表单布局中排列小部件，如下面的代码所示:

```
from PyQt5.QtWidgets import (
    QGroupBox,
    QLabel,
    QLineEdit,
    QPlainTextEdit,
    QRadioButton,
    QSpinBox,
    QVBoxLayout,
    QWidget,
    QApplication,
    QFormLayout,
)

application = QApplication([])

mainWindow = QWidget()

mainWindow.setGeometry(0, 0, 350, 400)
mainWindow.setWindowTitle('Form Layout')

formLayout = QFormLayout()

nameLabel = QLabel('Name')
nameField = QLineEdit()

ageLabel = QLabel('Age')
ageField = QSpinBox()
ageField.setMinimum(0)
ageField.setMaximum(130)

sexLabel = QLabel('Sex')
sexGroup = QGroupBox()
verticalLayout = QVBoxLayout()

for sex in ['Male', 'Female', 'Other']:
    radioButton = QRadioButton(sex)
    verticalLayout.addWidget(radioButton)

sexGroup.setLayout(verticalLayout)

commentLabel = QLabel('Comments')
commentField = QPlainTextEdit()

formLayout.addRow(nameLabel, nameField)
formLayout.addRow(ageLabel, ageField)
formLayout.addRow(sexLabel, sexGroup)
formLayout.addRow(commentLabel, commentField)

mainWindow.setLayout(formLayout)
mainWindow.show()

application.exec()

```

运行上面的代码，可以看到一个类似下图的 GUI。您可以看到小部件的标签-字段对。每一行都有一个标签小部件和右边相应的字段小部件。

![Form layout GUI](img/8160aa8685dd592650fc5590beb14877.png)

上面提到的布局并不详尽。您可以在 PyQt 或 Qt 文档中了解其他形式的布局。

## 主窗口框架

使用 PyQt，您可以使用任意小部件来创建主窗口；这就是我们在前面几节中对插图所做的，目的是让事情简单易懂。然而，这并不是您在现实世界的应用程序中所做的。PyQt 附带了用于管理应用程序主窗口的`QMainWindow`类。

`QMainWindow`类提供了添加窗口小部件的布局，比如菜单栏、工具箱和开箱即用的状态栏。通常的做法是创建一个`QMainWindow`类的子类，并将其用作主窗口。这就是我们应该做的。

下面是“hello world”GUI 的重构代码:

```
from PyQt5.QtWidgets import QMainWindow, QApplication

class MainWindow(QMainWindow):
    def __init__(self, parent=None):
        super().__init__(parent)

        self.setWindowTitle('Hello Wordl')
        self.setGeometry(0, 0, 350, 400)

if (__name__ == '__main__'):
    application = QApplication([])
    mainWindow = MainWindow()
    mainWindow.show()
    application.exec()

```

您会注意到我们的`MainWindow`类是如何从`QMainWindow`类继承而来的。当您运行上面的代码时，它创建的窗口将与我们的第一个“hello world”GUI 相同。从现在开始，我们将总是创建`QMainWindow`类的一个子类。

## 如何创建菜单栏

菜单是大多数 GUI 应用程序的特征。桌面图形用户界面通常在主窗口的顶部有菜单。一些下拉菜单有在悬停时打开的子菜单。下图显示了桌面 GUI 上的典型菜单。

![desktop GUI with menu and submenu](img/2ed6402ba05d6b8b136265ccf9d2c2cc.png)

如前一节所述，`QMainWindow`类提供了一个布局，用于将菜单栏添加到开箱即用的 GUI 中。要创建菜单栏，需要使用`menuBar`方法。

下面是在上图中创建下拉菜单的代码。我已经使用了`QAction`类来创建动作，并将它们添加到相应的菜单中来创建下拉菜单:

```
from PyQt5.QtWidgets import QMainWindow, QApplication, QAction

class MainWindow(QMainWindow):
    def __init__(self, parent=None):
        super().__init__(parent)

        self.setWindowTitle('Dropdown Menu')
        self.setGeometry(0, 0, 350, 400)
        self.addMenu()

    def addMenu(self):

        # Create menu bar
        menuBar = self.menuBar()

        # Add menu items
        fileMenu = menuBar.addMenu('File')
        helpMenu = menuBar.addMenu('Help')

        # Create actions

        visitWebsiteAction = QAction('Visit Our Website', self)
        fileBugReportAction = QAction('File a Bug Report', self)

        # Add dropdown menu items on the Help menu

        helpMenu.addAction(visitWebsiteAction)
        helpMenu.addAction(fileBugReportAction)

        # Add 'Follow Us' dropdown menu item on the Help menu

        followUs = helpMenu.addMenu('Follow Us')

        # Social media actions

        twitterAction = QAction('Twitter', self)
        githubAction = QAction('GitHub', self)

        # Add actions

        followUs.addAction(twitterAction)
        followUs.addAction(githubAction)

if (__name__ == '__main__'):
    application = QApplication([])
    mainWindow = MainWindow()
    mainWindow.show()
    application.exec()

```

## 用 PyQt 构建标准 GUI

希望以上部分已经向您介绍了 PyQt 的基础知识。现在让我们通过构建一个标准的 GUI 来使用我们刚刚学到的知识。

下图显示了桌面版密码管理应用程序的登录屏幕。它有一个菜单栏和一个密码栏。在前几节中，我们已经查看了 GUI 中的大多数小部件。不过，我会简单解释一下其中的一些方法。

我一直保持简单，只关注基础。如果你感兴趣，你可以通过给它添加更多的功能和交互性来进一步推动自己。

![password manager GUI](img/9e0b665a9618308aab26046def0e571c.png)

下面是上述 GUI 的相应代码。我在大多数部分都留下了评论来解释正在发生的事情。我敦促你在阅读我的解释之前通读代码:

```
from PyQt5.QtWidgets import (QAction, QApplication, QFormLayout, QGroupBox,
                             QLabel, QPushButton, QVBoxLayout, QWidget,
                             QMainWindow, QLineEdit)
from PyQt5.QtCore import Qt
from PyQt5.QtGui import QPixmap

class MainWindow(QMainWindow):
    def __init__(self, parent=None):
        super().__init__(parent)
        self.createUI()
        self.createActions()
        self.creatMenu()
    def createUI(self):
        # Create window
        self.setWindowTitle('Password Manager')
        self.resize(800, 500)
        self.setMinimumSize(500, 450)
        # Create central widget and layout
        self._centralWidget = QWidget()
        self._verticalLayout = QVBoxLayout()
        self._centralWidget.setLayout(self._verticalLayout)
        # Set central widget
        self.setCentralWidget(self._centralWidget)
        # Vertically center widgets
        self._verticalLayout.addStretch(1)
        # Add lock image
        self.addLockImage()
        self.addText()
        self.addInputText()
        # Vertically center widgets
        self._verticalLayout.addStretch(1)
        # Add Copyright
        self.addCopyRight()
    def addLockImage(self):
        imageLabel = QLabel()
        pixmap = QPixmap('lock.png')
        imageLabel.setPixmap(pixmap)
        self._verticalLayout.addWidget(imageLabel, alignment=Qt.AlignCenter)
    def addText(self):
        messageLabel = QLabel(
            'Hi there 👋. Your vault is locked. Verify your master password to continue.'
        )
        messageLabel.setAlignment(Qt.AlignCenter)
        messageLabel.setFixedWidth(350)
        messageLabel.setMinimumHeight(50)
        messageLabel.setWordWrap(True)
        self._verticalLayout.addWidget(messageLabel, alignment=Qt.AlignCenter)

    def addCopyRight(self):
        copyRight = QLabel(
            'Copyright © <a href="https://logrocket.com/">LogRocket</a> 2021')
        copyRight.setOpenExternalLinks(True)
        self._verticalLayout.addWidget(copyRight, alignment=Qt.AlignCenter)

    def addInputText(self):
        groupBox = QGroupBox()
        groupBox.setFixedWidth(350)

        formLayout = QFormLayout()

        passwordLabel = QLabel('Master Password')
        passwordField = QLineEdit()
        passwordField.setTextMargins(3, 0, 3, 0)
        passwordField.setMinimumWidth(200)
        passwordField.setMaximumWidth(300)
        passwordField.setEchoMode(QLineEdit.Password)
        passwordField.setClearButtonEnabled(True)

        submitLabel = QLabel('Open Your Vault')
        submitField = QPushButton()

        formLayout.addRow(passwordLabel, passwordField)
        formLayout.addRow(submitLabel, submitField)

        groupBox.setLayout(formLayout)
        self._verticalLayout.addWidget(groupBox, alignment=Qt.AlignCenter)

    def creatMenu(self):
        # Create menu bar
        menuBar = self.menuBar()
        # Add menu items
        fileMenu = menuBar.addMenu('File')
        editMenu = menuBar.addMenu('Edit')
        accountMenu = menuBar.addMenu('Account')
        helpMenu = menuBar.addMenu('Help')
        # Add sub-items under Help menu item
        helpMenu.addAction(self.sendEmailAction)
        helpMenu.addAction(self.visitWebsiteAction)
        helpMenu.addAction(self.fileBugReportAction)
        # Add horizontal line
        helpMenu.addSeparator()
        # Add 'Follow Us' sub-item under Help menu item
        # Use addMenu method because it contains sub-items
        followUs = helpMenu.addMenu('Follow Us')
        followUs.addAction(self.twitterAction)
        followUs.addAction(self.facebookAction)
        followUs.addAction(self.githubAction)

    def createActions(self):
        # Help menu actions
        self.sendEmailAction = QAction('Email Us', self)
        self.visitWebsiteAction = QAction('Visit Our Website', self)
        self.fileBugReportAction = QAction('File a Bug Report', self)
        # Social media actions
        self.twitterAction = QAction('Twitter', self)
        self.facebookAction = QAction('Facebook', self)
        self.githubAction = QAction('GitHub', self)

if (__name__ == '__main__'):
    application = QApplication([])
    mainWindow = MainWindow()
    mainWindow.show()
    application.exec()

```

在上面的代码中，我声明了用于创建 GUI 的`createUI`方法。我已经将创建其他小部件的功能提取到不同的方法中。在前面的章节中，我们已经遇到了组成 UI 的大多数小部件，我已经在代码中添加了注释来解释正在发生的事情。因此，我不会在这里一一解释。但是，我将讨论 UI 是如何组织的。

GUI 由垂直布局的四个小部件组成。我已经使用`addStretch`方法在垂直布局的开始添加了一个可拉伸的空间。同样，在分组框之后还有一个可拉伸的空间。可拉伸的空间有助于将版权文本移到 GUI 的底部，并将其余的小部件垂直居中。

前面几节向您介绍了以编程方式构建 GUI 应用程序的基础知识。有一个用于构建 GUI 的替代拖放界面，称为 Qt Designer。这将显著提高你的工作效率。更多可以阅读 [Qt 设计师手册](https://doc.qt.io/qt-5/qtdesigner-manual.html)。

## 结论

如果您希望使用 Python 开发跨平台的 GUI 应用程序，PyQt 是一个方便的工具包。您可以使用各种内置类来创建小部件，然后使用信号和插槽添加交互性。将它与 Qt Designer 一起使用可以显著减少开发时间并提高生产率。

希望这篇文章对你有所帮助。我们在这里讨论的只是基础，PyQt 有大量的类和方法。尽管如此，我希望它已经为您提供了足够的背景知识来阅读文档并开始使用 PyQt。

不幸的是，在撰写本文时，PyQt5 和 PyQt6 文档中有许多缺失的部分。因此，在没有其他资源的情况下使用 PyQt 文档可能没有太大帮助。您需要同时使用 PyQt 和 Qt 文档。

但是，请注意 Qt 文档是面向 C++的。您还可以使用 PyQt 早期版本(如 PyQt4)的文档，而不是最新版本(如 PyQt5 或 PyQt6)。API 的某些部分没有太大变化。

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)