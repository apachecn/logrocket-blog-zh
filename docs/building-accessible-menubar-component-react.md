# 使用 React - LogRocket 博客构建一个可访问的菜单栏组件

> 原文：<https://blog.logrocket.com/building-accessible-menubar-component-react/>

上周，我看了佩德罗·杜阿尔特的精彩“[所以你认为你可以在 Next.js Conf 上建立一个下拉菜单](https://www.youtube.com/watch?v=lY-RQjWeweo)”演讲。它启发我写了一个我自己的可访问组件 menubar 小部件。

我对可访问性非常感兴趣，尤其是前端 web 开发。在我迄今为止研究的所有模式中，菜单栏是最复杂的。 [Reach](https://reach.tech/) 、 [Radix](https://www.radix-ui.com/) 、 [React Aria](https://react-spectrum.adobe.com/react-aria/index.html) 都提供了灵活易得的 React [组件](https://blog.logrocket.com/build-component-library-react-typescript/)。

然而，我很难找到任何提供开箱即用菜单栏组件的库。鉴于材料的复杂性和缺乏，我想我应该与社区分享我的发现。

## 创建可访问的`Menubar`组件

本文将解释我如何用 React 创建一个可访问的`Menubar`组件。目标是为 menubar 小部件创建一个符合 WAI-ARIA [设计模式](https://www.w3.org/TR/wai-aria-practices/#menu)的组件。

以下是我们将要介绍的内容:

为简洁起见，本文将集中讨论带有单个子菜单的水平菜单栏。它还假设您对 React 挂钩和[复合组件](https://kentcdodds.com/blog/compound-components-with-react-hooks)模式感到满意。我已经将该解决方案作为 CodeSandbox 链接包含在下面。

我们将从需求开始。神话大学已经为他们的网站请求了一个可访问的站点导航。

首先，我们将在一个无序列表中对超链接集合进行分组。我们还将在导航部分包装列表。

HTML 可能看起来像这样:

```
><nav>
        <ul>
                <li>
                      <a href="/#about">About</a>
                </li>

                <li>
                      <a href="/#admissions">Admissions</a>
                </li>

                <li>
                      <a href="/#academics">Academics</a>
                </li>
        </ul>
</nav>

```

乍一看，这种标记看起来很全面，但是对于那些依赖辅助技术的人来说，它有多容易理解呢？此外，用户可以使用预期的键盘控件来导航菜单栏吗？

尽管我们已经提供了语义 HTML，但是当前的迭代被认为是不可访问的。标记缺少关键的`aria-`角色，这些角色为链接和小部件本身提供上下文。糟糕的键盘支持也意味着用户只能浏览链接列表。

让我们改善这两个方面。

我们将从创建两个功能组件开始。一个是父`Menubar`列表，另一个是子`MenuItem`列表项。我们将一起使用这些来组成一个复合的`<Menubar />`组件。

父元素`Menubar`返回一个无序列表元素。因为它是小部件的根元素，我们将赋予它`menubar`角色。`aria-orientation`属性允许辅助技术决定菜单的方向。最后，让我们包含一个定制的`data-`属性，用于稍后的定位和样式。

```
function Menubar({ children, ...props }) {
  const listProps = {
        ...props,
        "aria-orientation": "horizontal",
            "data-menubar-list": "",
        role: "menubar",
  };

  return <ul {...listProps}>{children}</ul>;
};

```

第二个组件是`MenuItem`。它接受单个节点作为其`children`属性，并返回包装在列表项元素中的节点。

辅助技术应该只宣告子节点。默认情况下，列表项元素具有`lisitem`角色。通过将它重写为`none`，我们将它从可访问性树中完全移除。然后，我们通过[克隆元素](https://reactjs.org/docs/react-api.html#cloneelement)并浅层合并道具，为子节点分配`menuitem`角色。

```
function MenuItem({ children, ...props }) {
  const listItemProps = {
        ...props,
            "data-menubar-listitem": "",
        role: "none"
  };

  const childProps = {
            "data-menubar-menuitem": "",
        role: "menuitem",
  };

  return (
        <li {...listItemProps}>
                {React.cloneElement(children, childProps)}
        </li>
    );
};

```

最后，让我们给导航元素添加一个匹配的`aria-label`。

当前的 React 标记如下所示:

```
<nav aria-label="Mythical University">
        <Menubar aria-label="Mythical University">
            <MenuItem>
                  <a href="/#about">About</a>
            </MenuItem>

            <MenuItem>
                  <a href="/#admissions">Admissions</a>
            </MenuItem>

            <MenuItem>
                  <a href="/#academics">Academics</a>
            </MenuItem>
      </Menubar>
</nav>

```

它将编译成以下 HTML:

```
<nav aria-label="Mythical University">
        <ul
          aria-label="Mythical University"
          aria-orientation="horizontal"
            data-menubar-list
          role="menubar"
        >
              <li data-menubar-listitem role="none">
                    <a data-menubar-menuitem href="/#about" role="menuitem">
                      About
                    </a>
              </li>

              <li data-menubar-listitem role="none">
                    <a data-menubar-menuitem href="/#admissions" role="menuitem">
                      Admissions
                    </a>
              </li>

              <li data-menubar-listitem role="none">
                    <a data-menubar-menuitem href="/#academics" role="menuitem">
                      Academics
                    </a>
              </li>
        </ul>
</nav>

```

到目前为止，我们已经为那些使用辅助技术的人改进了菜单栏，但是那些依赖键盘控制的人呢？为了导航菜单项列表，`Menubar`组件需要知道每个子组件`MenuItem`。我们可以通过使用 React `createContext()`和`useEffect()`钩子来实现这一点。

让我们首先创建一个新的`MenubarContext`:

```
export const MenubarContext = React.createContext(null);

```

`MenubarContext`将在父`Menubar`中存储嵌套`MenuItem`节点的[集合](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)。我们将`Set`包含在用`useRef()`钩子创建的可变 ref 对象中，并将`current`值存储在一个变量中。

这允许我们操作`Set`的内容，而不需要重新渲染`Menubar`。接下来，我们将使用`useMemo()`钩子来记忆一个对象，并将`menuItems`指定为一个属性。最后，我们将把对象传递给`MenubarContext.Provider`的值属性。

```
function Menubar({ children, ...props }) {
  const menuItems = React.useRef(new Set()).current;
    const value = React.useMemo(() => ({ menuItems }), [menuItems]);
    const listProps = { ... };

  return (
        <MenubarContext.Provider value={value}>
                <ul {...listProps}>
                        {children}
                </ul>
        </MenubarContext.Provider>
    );
};

```

`MenuItem`只应该是`Menubar`组件的子组件。为了加强这一点，让我们在`useContext()`钩子找不到`MenubarContext`的时候抛出一个错误。这允许我们断言`menuItems`存在于下面的条件语句中:

```
const menubarContext = React.useContext(MenubarContext);

if (!menubarContext) {
    throw new Error("MenuItem must be used within a Menubar Context");
}

const { menuItems } = menubarContext;

```

让我们用`useRef()`钩子创建一个对`MenuItem` DOM 节点的对象引用。然后，让我们使用`useEffect()`钩子来触发一个副作用，将节点添加到`menuItems` `Set`。如果`MenuItem`卸载，我们还将返回一个清理函数来将它从`Set`中移除。

```
const { menuItems } = menubarContext;

const menuItemRef = React.useRef(null);

const listItemProps = {
    [ ... ],
    ref: menuItemRef,
};

React.useEffect(() => {
  const menuItemNode = menuItemRef.current;

  if (menuItemNode) {
    menuItems.add(menuItemNode);
  }

  return () => {
    menuItems.delete(menuItemNode);
  };
}, [menuItems]);

return (
    <li {...listItemProps}>
                {React.cloneElement(children, childProps)}
        </li>
);

```

### 漫游标签索引

我们现在有了对每个`MenuItem`节点的引用。有了它们，我们可以应用[漫游标签索引](https://www.w3.org/TR/wai-aria-practices-1.1/#kbd_roving_tabindex)模式来管理组件内的焦点。为此，`Menubar`需要跟踪当前和之前聚焦的`MenuItem`。我们可以通过在`Menubar`的组件状态中存储当前和先前节点的索引来做到这一点。

当前索引是使用 React `useState()`钩子存储的有状态值。当菜单栏第一次挂载时，第一个`MenuItem`子菜单应该有一个标签索引`0`。因此，我们可以将`0`指定为当前索引的默认状态。

我们可以使用自定义挂钩来跟踪之前的索引。钩子接受当前索引作为函数参数。如果钩子没有返回值，我们可以假设一个不存在，退回到`null`。

```
/* https://usehooks.com/usePrevious/ */

const [currentIndex, setCurrentIndex] = React.useState(0);
const previousIndex = usePrevious(currentIndex) ?? null;

function usePrevious(value) {
    const ref = React.useRef();

    React.useEffect(() => {
        ref.current = value;
    }, [value]);

    return ref.current;
}

```

为了应用移动标签索引，`menuItems[currentIndex]`节点必须具有标签索引`0`。组件标签序列中的所有其他元素都应该有一个标签索引`-1`。每当用户从一个菜单项导航到另一个菜单项时，都会发生以下情况:

*   当前节点应该模糊，其标签索引应该设置为`-1`
*   下一个节点的标签索引被设置为`0`
*   下一个节点接收焦点

让我们为此利用 React `useEffect()`钩子。我们将传递当前和以前的索引作为效果依赖。每当任一索引改变时，该效果将更新所有适当的索引。

(注意:我们将 tab index 属性应用于`MenuItem`的第一个子元素，而不是列表项包装器)

```
React.useEffect(() => {
        if (currentIndex !== previousIndex) {
            const items = Array.from(menuItems);
            const currentNode = items[currentIndex]?.firstChild;
            const previousNode = items[previousIndex]?.firstChild;

            previousNode?.setAttribute("tabindex", "-1");
            currentNode?.setAttribute("tabindex", "0");
            currentNode?.focus();
        }
}, [currentIndex, previousIndex, menuItems]);

```

我们不必为每个菜单项添加标签索引；我们可以更新`MenuItem`组件来为我们做这件事！我们可以假设如果`menuItems` `Set`为空，那么该节点就是序列中的第一个菜单项。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

让我们添加一些组件状态来跟踪`MenuItem`是否是集合中的第一个节点。如果是，我们可以给它的标签索引赋值`0`——否则，我们将返回到`-1`。

```
const [isFirstChild, setIsFirstChild] = React.useState(false);
const menuItemRef = React.useRef(null);
const { menuItems } = menubarContext;

const listItemProps = {
    [ ... ],
    ref: menuItemRef,
};

const childProps = {
        [ ... ],
      tabIndex: isFirstChild ? "0" : "-1",
};

React.useEffect(() => {
      const menuItemNode = menuItemRef.current;

        if (menuItemNode) {
            if (!menuItems.size) {
                  setIsFirstChild(true);
            }

            menuItems.add(menuItemNode);
        }

      return () => {
            menuItems.delete(menuItemNode);
      };
}, [menuItems]);

return (
        <li {...listItemProps}>
                {React.cloneElement(children, childProps)}
        </li>;
);

```

### 键盘控制

接下来，我们将使用`Menubar`的`onKeyDown()`事件根据用户的按键更新当前索引。用户可以通过五种主要方法浏览菜单项。

他们可以:

*   返回上一个项目
*   前进到下一个
*   跳到第一个
*   跳到最后一页
*   移到下一场比赛

让我们将该逻辑封装到一些助手方法中，我们可以将它们传递给`keyDown`事件。

```
// Moves focus to the first item in the menubar.
const first = () => setCurrentIndex(0);

// Moves focus to last item in the menubar.
const last = () => setCurrentIndex(menuItems.size - 1);

// Moves focus to the next item in the menubar. 
// If focus is on the last item, moves focus to the first item.
const next = () => {
  const index = currentIndex === menuItems.size - 1 ? 0 : currentIndex + 1;
  setCurrentIndex(index);
};

// Moves focus to the previous item in the menubar. 
// If focus is on the first item, moves focus to the last item.
const previous = () => {
  const index = currentIndex === 0 ? menuItems.size - 1 : currentIndex - 1;
  setCurrentIndex(index);
};

// Moves focus to next item in the menubar that starts with the character. 
// If none of the items start with the typed character, focus does not move.
const match = (e) => {
  const items = Array.from(menuItems);

  const reorderedItems = [
      ...items.slice(currentIndex),
      ...items.slice(0, currentIndex)
  ];

  const matches = reorderedItems.filter((menuItem) => {
      const { textContent } = menuItem.firstChild;
      const firstLetter = textContent.toLowerCase().charAt(0);
      return e.key === firstLetter;
  });

  if (!matches.length) {
      return;
  }

  const currentNode = items[currentIndex];
  const nextMatch = matches.includes(currentNode) ? matches[1] : matches[0];
  const index = items.findIndex((item) => item === nextMatch);

  setCurrentIndex(index);
};

```

定义了助手方法后，我们可以将它们分配给适当的键码。我们将检查按键是否匹配任何与移动相关的按键；如果没有，我们将默认使用`match()` helper 方法。

```
const keyDown = (e) => {
    e.stopPropagation();

    switch (e.code) {
                case "ArrowLeft":
            e.preventDefault();
            previous();
            break;
        case "ArrowRight":
            e.preventDefault();
            next();
            break;
                case "End":
            e.preventDefault();
            last();
            break;
        case "Home":
            e.preventDefault();
            first();
            break;
        default:
                        match(e);
            break;
    }
};

const listProps = {
    [ ... ],
        onKeyDown: (e) => {
          keyDown(e);
    },
};

```

注意，我们在大多数助手方法上调用了`e.preventDefault()`。这是为了抑制用户与菜单栏交互时的任何默认浏览器行为。例如，默认情况下，`End`键将用户滚动到页面底部。

假设我们没有阻止默认行为；每当用户试图跳到最后一个菜单项时，滚动位置就会跳到页面的底部！

我们不能在默认情况下调用`e.preventDefault()`。如果我们这样做，它将忽略任何默认的浏览器行为，而不是开关的情况下捕获。这可能会导致不良行为。例如，如果菜单栏中的菜单项有焦点，用户按下`ctrl + r`来刷新页面。如果我们在默认情况下调用`e.preventDefault()`，它会忽略刷新请求。然后它会将`r`键传递给`match`助手方法。

我们现在有了一个完全可访问的菜单栏小部件来收集导航链接！每个菜单项都为辅助技术提供丰富的上下文信息。它还允许那些依赖键盘支持的人按照他们的期望导航链接列表。

组件 API 与上一个示例相比没有变化…

```
<nav aria-label="Mythical University">
        <Menubar aria-label="Mythical University">
              <MenuItem>
                    <a href="/#about">About</a>
              </MenuItem>

              <MenuItem>
                    <a href="/#admissions">Admissions</a>
              </MenuItem>

              <MenuItem>
                    <a href="/#academics">Academics</a>
              </MenuItem>
        </Menubar>
</nav>

```

…然而，编译后的 HTML 标记现在包括菜单项上的制表符索引。

进步！

```
<nav aria-label="Mythical University">
        <ul
          aria-label="Mythical University"
          aria-orientation="horizontal"
          data-menubar-list
          role="menubar"
        >
              <li data-menubar-listitem role="none">
                    <a data-menubar-menuitem href="/#about" role="menuitem" tabindex="0">
                          About
                    </a>
              </li>

              <li data-menubar-listitem role="none">
                    <a data-menubar-menuitem href="/#admissions" role="menuitem" tabindex="-1">
                          Admissions
                    </a>
              </li>

              <li data-menubar-listitem role="none">
                    <a data-menubar-menuitem href="/#academics" role="menuitem" tabindex="-1">
                          Academics
                    </a>
              </li>
        </ul>
</nav>

```

前一个例子对于一个链接集合来说很好，但是如果我们用一个下拉菜单来显示第二组导航链接，会怎么样呢？

```
<nav aria-label="Mythical University">
        <Menubar aria-label="Mythical University">
              <MenuItem>
                    <a href="/#about">About</a>
              </MenuItem>

              <MenuItem>
                        <button>Admissions</button>

                        <ul>
                                <li><a href="/#visit">Visit</a></li>
                                <li><a href="/#photo-tour">Photo Tour</a></li>
                                <li><a href="/#connect">Connect</a></li>
                        </ul>
              </MenuItem>

              <MenuItem>
                    <a href="/#academics">Academics</a>
              </MenuItem>
        </Menubar>
</nav>

```

为此，我们需要创建第二个复合组件——`<Submenu />`。它由三个功能组件组成:

*   `Submenu`将保存共享逻辑和组件状态
*   `Trigger`将允许用户扩展菜单
*   `List`将显示扩展的菜单项

`MenubarContext`跟踪`Menubar`中的菜单项。接下来，让我们创建一个`SubmenuContext`来跟踪嵌套在`Submenu`中的菜单项。

```
export const SubmenuContext = React.createContext(null);

```

让我们从定义`Submenu`组件开始。它将与`Menubar`共享一些类似的行为和功能。除了索引跟踪，它还需要知道它的菜单是否扩展了。我们可以用`useState()`声明另一个状态变量。相反，将逻辑合并到一个 reducer 函数中更有意义。

`Submenu`父组件的目的是保存复合组件的状态。它还负责向其子组件分发共享逻辑。我们将逻辑分配给一个记忆化的对象，然后将该对象传递给一个`SubmenuContext.Provider`的 value 属性。

```
const submenuInitialState = {
  currentIndex: null,
  previousIndex: null,
    isExpanded: false,
};

function submenuReducer(state, action) {
  switch (action.type) {
    case "expand":
      return { ...state, isExpanded: true };
    case "collapse":
      return submenuInitialState;
    case "move":
      return {
        ...state,
        isExpanded: true,
        currentIndex: action.index,
        previousIndex: state.currentIndex
      };
    default:
      throw new Error(`${action.type} not recognised`);
  }
}

const Submenu = ({ children }) => 
  const menuItems = React.useRef(new Set()).current;
    const [state, dispatch] = React.useReducer(submenuReducer, submenuInitialState);
  const value = React.useMemo(() => ({ menuItems }), [menuItems]);

  return (
    <SubmenuContext.Provider value={value}>
            {children}
        </SubmenuContext.Provider>
  );
};

```

现在，让我们定义导航子菜单菜单项的助手方法。这些几乎和`Menubar`助手一模一样。关键的区别是它们分派 reducer 动作，而不是直接更新组件状态。

```
const open = React.useCallback(() => dispatch({ type: "expand" }), []);

const close = React.useCallback(() => dispatch({ type: "collapse" }), []);

const first = React.useCallback() => dispatch({ type: "move", index: 0 }), []);

const last = React.useCallback(() => (
        dispatch({ type: "move", index: menuItems.size - 1 }), [menuItems.size]
));

const move = React.useCallback((index) => dispatch({ type: "move", index }), []);

const value = React.useMemo(() => ({ open, close, first, last, move }),
  [open, close, first, last, move]
);

return (
    <SubmenuContext.Provider value={value}>
            {children}
        </SubmenuContext.Provider>
);

```

一些功能需求需要子组件了解它们的兄弟组件。我们可以通过定义`id`和`Submenu`中每个子组件的引用来实现这一点。注意，我们将`menuId`存储在一个引用对象中。这是为了防止`uniqueId()`函数在每次渲染时重新生成`id`。每个子组件现在都可以从`useContext()`钩子中检索值。

```
const id = React.useRef(_.uniqueId("submenu--")).current;
const buttonId = `button--${id}`;
const listId = `list--${id}`;

const buttonRef = React.useRef(null);
const listRef = React.useRef(null);

const value = React.useMemo(
  () => ({ buttonId, buttonRef, listId, listRef })
  [buttonId, buttonRef, listId, listRef]
);

```

现在让我们来管理`Submenu`中的焦点。我们将从添加另一个副作用开始。如果被跟踪的索引不匹配，它将聚焦于当前索引的第一个子索引。每当我们更新当前索引时，我们关注新的当前节点的第一个子节点。

```
React.useEffect(() => {
    const items = Array.from(menuItems);

    if (currentIndex !== previousIndex) {
      const currentNode = items[currentIndex]?.firstChild;
      currentNode?.focus();
    }
}, [menuItems, currentIndex, previousIndex]);

```

子菜单不遵循漫游选项卡索引模式。相反，子菜单中每个菜单项的标签索引将始终是`-1`。这需要对`MenuItem`组件做一点小小的改动。如果一个`SubmenuContext`存在，我们可以假设`MenuItem`在一个`Submenu`中，并将`-1`应用于它的标签索引。

```
const [isFirstChild, setIsFirstChild] = React.useState(false);
const submenuContext = React.useContext(SubmenuContext);

const childProps = {
        [ ... ],
      tabIndex: !submenuContext && isFirstChild ? "0" : "-1",
};

```

### 引发

定义了`Submenu`之后，让我们创建`Trigger`组件。我们将从从`SubmenuContext`中检索`buttonId`和`buttonRef`开始。因为按钮的默认类型是`submit`，所以将其覆盖为`button`通常是个好主意。

最后，`Trigger`只能是`Submenu`的子元素。像以前一样，如果我们在一个`SubmenuContext`之外使用它，让我们抛出一个错误。

```
const Trigger = ({ onKeyDown, ...props }) => {
  const context = React.useContext(SubmenuContext);

  if (!context) {
    throw new Error("Trigger must be used within a Submenu Context");
  }

  const { buttonId, buttonRef } = context;

    const buttonProps = {
            ...props,
        id: buttonId,
          ref: buttonRef,
        type: "button",  
  }

  return <button {...buttonProps} />;
};

```

接下来，让我们添加适当的`aria-`属性。`aria-haspopup='true'`将通知辅助技术该按钮控制一个子菜单。为了更进一步，我们还可以添加`aria-controls`属性。这将通知屏幕阅读器由`Trigger`控制的确切子菜单。

让我们也从`SubmenuContext`中检索`listId`和`isExpanded`状态。我们将把`listId`分配给`aria-controls`。然后，剩下的就是将`isExpanded`状态分配给`aria-expanded`属性。辅助技术现在可以识别菜单按钮控件，以及它们是打开还是关闭。

```
const { buttonId, buttonRef, listId, isExpanded } = submenuContext;

const buttonProps = {
    ...props,
        "aria-haspopup": true,
    "aria-expanded": isExpanded,
    "aria-controls": listId,
        "data-menubar-submenu-trigger": "",
    id: buttonId,
    ref: buttonRef,
    type: "button",
};

```

现在，让我们给`Trigger`添加键盘支持。`Trigger`将是菜单栏菜单项的兄弟。这意味着它应该执行与菜单栏链接相同的`keyDown`事件。它还需要一些额外的功能。除了菜单项行为，触发器还应该:

*   `ArrowUp`:打开子菜单，聚焦最后一项
*   `ArrowDown`:打开子菜单，聚焦第一项
*   `Space`、`Enter`:打开子菜单，聚焦到第一项

为此，我们将从`SubmenuContext`中检索一些方法，并将它们分配给相关的`e.code`。注意，我们只想对唯一的事件执行`e.stopPropagation()`方法。

这样做允许所有其他事件冒泡到`MenuBar`。这是防止我们必须复制菜单项的`keydown`事件的原因。

```
const { first, last } = submenuContext;

const keyDown = (e) => {
    switch (e.code) {
      case "ArrowUp":
        e.stopPropagation();
        last();
        break;
      case "ArrowDown":
        e.stopPropagation();
        first();
        break;
      case "Enter":
      case "Space":
        e.stopPropagation();
        first();
        break;
      default:
        break;
    }
};

const buttonProps = {
      [ ... ],
      onKeyDown: (e) => {
        onKeyDown?.(e);
        keyDown(e);
      },
};

```

假设当用户按下`ArrowLeft`或`ArrowRight`键时，打开一个子菜单。子菜单应关闭并聚焦于上一个或下一个`Menubar`菜单项。如果根菜单项也是一个子菜单，它应该展开菜单，但保持焦点在触发器上。

`Trigger`通过检查事件是否源自子菜单菜单项来实现这一点。这确保了当其他`keydown`方法聚焦触发器时，菜单不会展开。

```
const buttonProps = {
        [ ... ],
        onFocus: (e) => {
              const isFromSubmenu = e.relatedTarget?.getAttribute(
                    "data-menubar-submenu-menuitem"
                ) === "";

              if (isFromSubmenu) {
                open();
              }
        }
};

```

### 目录

现在我们有了一个`Trigger`，我们需要做的就是创建一个子菜单`List`。像`Trigger`一样，如果在`SubmenuContext`中没有使用`List`组件，我们将抛出一个错误。

让我们也定义一些属性。首先，我们将应用`role='menu'`并从`SubmenuContext`中检索`listId`。我们将从上下文中检索`isExpanded`，并将其分配给`aria-hidden`属性。如果菜单没有展开，这将从可访问性树中隐藏`List`。

接下来，让我们通过将`buttonId`分配给`aria-labelledby`属性来标记菜单。最后，我们将使用`aria-orientation`属性为辅助技术提供菜单的方向。

```
const List = ({ children, ...props }) => {
  const submenuContext = React.useContext(SubmenuContext);

  if (!submenuContext ) {
        throw new Error("List must be used within a Submenu Context");
  }

  const { listId, listRef, isExpanded } = submenuContext;

  const listProps = {
      ...props,
        "aria-hidden": !isExpanded,
      "aria-labelledby": buttonId,
      "aria-orientation": "vertical",
        "data-menubar-submenu-list": "",
        id: listId,
        ref: listRef,
      role: "menu",
};

  return (
      <ul {...listProps}>
            {children}
      </ul>
  );
};

```

现在让我们添加一些特定于`List`组件的`keydown`事件。我们将从`SubmenuContext`中取回合适的助手。同样，我们只想停止那些我们不希望上升到`Menubar`的`keydown`事件的事件的传播。

```
const { close, first, last, move } = submenuContext;

const keyDown = (e) => {
      switch (e.code) {
            case "ArrowUp":
                  e.stopPropagation();
                  e.preventDefault();
                  previous();
                  break;
            case "ArrowDown":
                  e.stopPropagation();
                  e.preventDefault();
                  next();
                  break;
            case "ArrowLeft":
                  e.preventDefault();
                  close();
                  break;
            case "ArrowRight":
                  e.preventDefault();
                  close();
                  break;
            case "Home":
                  e.stopPropagation();
                  e.preventDefault();
                  first();
                  break;
            case "End":
                  e.stopPropagation();
                  e.preventDefault();
                  last();
                  break;
            case "Enter":
            case "Space":
                  close();
                  break;
            case "Escape":
                  e.stopPropagation();
                  e.preventDefault();
                  close();
                  break;
            case "Tab":
                  close();
                  break;
            default:
                  e.stopPropagation();
                  match(e);
                  break;
      }
};

const listProps = {
      [ ... ],
        onKeyDown: (e) => {
            e.preventDefault();
            keyDown(e);
      },
};

```

在大多数情况下，`MenuItem`组件将在`Submenu`内工作。我们需要做一些修改来确保`Menubar`和`Submenu`都可以使用这个组件。

第一个变化是确保正确的`menuItems` `Set`接收到`menuItem`节点。如果`MenuItem`可以检索到`SubmenuContext`，我们可以断言子菜单是祖先元素。如果它返回一个假值，那么`MenuItem`必须属于菜单栏。

让我们更新错误以检查`SubmenuContext`。只有当两个上下文都不存在时，才会引发错误。一个`MenuItem`现在可以是一个`Menubar`或者一个`Submenu`的子节点。

```
const menubarContext = React.useContext(MenubarContext);
const submenuContext = React.useContext(SubmenuContext);

if (!menubarContext && !submenuContext) {
    throw new Error(
        "MenuItem must be used within either a Menubar or Submenu Context"
    );
}

```

我们需要对`MenuItem`组件做最后一个更改。让我们重温一下`Submenu`的结构。

`MenuItem`当前克隆了它的`children`道具并附加了额外的道具。在下面的例子中，我们可以看到`MenuItem`的子组件是`Submenu`组件。`Submenu`返回一个上下文提供者作为其父元素。提供者不会从 render 返回任何内容，因此 props 不会附加到任何 DOM 节点。

```
<Menubar aria-label="Menubar example">
  <MenuItem>
        <SubmenuContext.Provider {...menuItemProps}>
          <Trigger />
          <List />
        </SubmenuContext.Provider>
  </MenuItem> 
</Menubar>

```

相反，我们希望将`MenuItem`的`childProps`追加到子菜单`Trigger`上。为此，`MenuItem`组件需要检查其`children`的类型。

如果类型是一个节点，那么我们克隆它并添加属性。如果该类型是一个函数，那么我们提供 props 作为函数签名中的一个参数。

这允许我们灵活地选择哪个元素应该接收属性，并且还保留了默认情况下将属性附加到子元素上的便利性。

```
return (
    <li {...listItemProps}>
          { typeof children === "function"
                ? children(childProps)
                : React.cloneElement(children, childProps)
                }
    </li>
);

MenuItem.propTypes = {
  children: PropTypes.oneOfType([PropTypes.node, PropTypes.func]).isRequired,
};

```

这给我们留下了这个灵活的 React 标记:

```
<nav aria-label="Mythical University">
        <Menubar aria-label="Mythical University">
              <MenuItem>
                        <a href="/#about">About</a>
                </MenuItem> 

              <MenuItem>
                    {(menuItemProps) => (
                            <Submenu>
                                  <Trigger {...menuItemProps}>
                                            Admissions
                                    </Trigger>

                                  <List>
                                            <MenuItem>
                                                    <a href="/#visit">Visit</a>
                                            </MenuItem> 

                                            <MenuItem>
                                                    <a href="/#photo-tour">Photo Tour</a>
                                            </MenuItem> 

                                            <MenuItem>
                                                    <a href="/#connect">Connect</a>
                                            </MenuItem> 
                                    </List>
                            </Submenu>
                    )}
                </MenuItem> 

                <MenuItem>
                        <a href="/#academics">Academics</a>
                </MenuItem> 
        </Menubar>
</nav>

```

…编译成这个漂亮的、可访问的 HTML:

```
<nav aria-label="Mythical University">
        <ul
          aria-label="Mythical University"
            aria-orientation="horizontal"
            data-menubar-list
          role="menubar"
        >
              <li data-menubar-listitem role="none">
                    <a data-menubar-menuitem href="/#about" role="menuitem" tabindex="0">
                              About
                    </a>
                </li>

              <li data-menubar-listitem role="none">
                    <button
                            aria-controls="list--submenu--1"
                            aria-expanded="false"
                            aria-haspopup="true"
                            data-menubar-menuitem
                            data-menubar-submenu-trigger
                            id="button--submenu--1"
                      role="menuitem"
                      tabindex="-1"
                      type="button"
                    >
                          Admissions
                    </button>

                    <ul
                          aria-hidden="true"
                          aria-labelledby="button--submenu--1"
                          aria-orientation="vertical"
                                data-menubar-submenu-list
                                id="list--submenu--1"
                          role="menu"
                    >
                              <li data-menubar-submenu-listitem role="none">
                                <a
                                                data-menubar-submenu-menuitem
                                      href="/#visit"
                                      role="menuitem"
                                      tabindex="-1" 
                                >
                                      Visit
                                </a>
                            </li>

                                <li data-menubar-submenu-listitem role="none">
                                <a
                                                data-menubar-submenu-menuitem
                                      href="/#photo-tour"
                                      role="menuitem"
                                      tabindex="-1" 
                                >
                                      Photo Tour
                                </a>
                            </li>

                                <li data-menubar-submenu-listitem role="none">
                                <a
                                                data-menubar-submenu-menuitem
                                      href="/#connect"
                                      role="menuitem"
                                      tabindex="-1" 
                                >
                                      Connect
                                </a>
                            </li>
                    </ul>
              </li>

                <li data-menubar-listitem role="none">
                    <a data-menubar-menuitem href="/#academics" role="menuitem" tabindex="-1">
                      Academics
                    </a>
                </li>
        </ul>
</nav>

```

现在，剩下的就是为鼠标指针事件、嵌套子菜单和全套单元测试添加额外的逻辑了！

不幸的是，我们认为这些特性超出了本文的讨论范围，需要后续的文章来讨论。我已经在页面顶部的 [CodeSandbox 演示](https://codesandbox.io/s/a11y-menubar-ej7kh?file=/src/App.js)中包含了所有额外的逻辑和单元测试。

特别感谢 [Jenna Smith](https://twitter.com/jjenzz) 为最初的 API 设计做出的宝贵贡献。

如果您有任何问题或反馈，请随时联系我们，如果您喜欢这篇文章，可以考虑在 [Twitter](https://ajames.dev/twitter) 上关注我。

## [LogRocket](https://lp.logrocket.com/blg/react-signup-general) :全面了解您的生产 React 应用

调试 React 应用程序可能很困难，尤其是当用户遇到难以重现的问题时。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误以及跟踪缓慢的网络请求和组件加载时间感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/react-signup-general)

.

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/react-signup-general) 

LogRocket 结合了会话回放、产品分析和错误跟踪，使软件团队能够创建理想的 web 和移动产品体验。这对你来说意味着什么？

LogRocket 不是猜测错误发生的原因，也不是要求用户提供截图和日志转储，而是让您回放问题，就像它们发生在您自己的浏览器中一样，以快速了解哪里出错了。

不再有嘈杂的警报。智能错误跟踪允许您对问题进行分类，然后从中学习。获得有影响的用户问题的通知，而不是误报。警报越少，有用的信号越多。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://lp.logrocket.com/blg/react-signup-general)。