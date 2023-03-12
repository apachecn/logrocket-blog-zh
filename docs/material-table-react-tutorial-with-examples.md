# 使用 React 中的 material-table 构建功能丰富的数据表

> 原文：<https://blog.logrocket.com/material-table-react-tutorial-with-examples/>

表格是几乎所有 web 应用程序的重要组成部分，尤其是大量数据驱动的应用程序。

在本教程中，我们将回顾为您的项目选择正确的表格组件的重要性，并演示如何在 React 中使用最好、功能最丰富的库之一创建表格:material-table。

以下是我们将要介绍的内容:

## 为什么需要表格组件？

普通的 HTML 表格非常强大，可以用来快速显示数据。您甚至可以添加定制的样式，使之成为您自己的风格。如果您只关心向最终用户呈现简单的数据，一个普通的 HTML 表格就足够了。

但是普通的 HTML 表格有一些限制，包括:

*   外观——就样式而言，普通的 HTML 表格是非常基本的。你必须添加许多样式来使 HTML 表格看起来更专业
*   功能— HTML 表格不支持任何附加功能，如搜索、排序等。
*   性能— HTML 表没有针对性能进行优化，因为没有对分页或延迟加载的默认支持

表格组件旨在帮助您解决这些以及其他与普通 HTML 表格相关的问题。一个好的表格组件应该具有以下内置特性:

*   在数据中搜索
*   分类数据
*   过滤列
*   下载选项
*   风格定制
*   行操作
*   行选择
*   分组数据

上述特性在以表格格式显示数据的现代应用程序中很常见。

## 为什么使用材料表？

[material-table](https://material-table.com/#/) 实际上是唯一支持上述所有特性的表格库。

该库建立在 React 生态系统中最受欢迎的 UI 库 [Material UI](https://blog.logrocket.com/using-material-ui-with-react-hook-form/) 之上。如果你已经在使用材质界面，材质表将会完美的融入其中。

由于能够使用 material-table 定制表格构建的外观，您可以在任何 React 项目中使用它。

如果你是一个视觉学习者，看看这个视频教程，帮助你开始使用材料表。

 [https://www.youtube.com/embed/T4dtGAGCXVU?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent](https://www.youtube.com/embed/T4dtGAGCXVU?version=3&rel=1&showsearch=0&showinfo=1&iv_load_policy=1&fs=1&hl=en-US&autohide=2&wmode=transparent)

视频

## 安装材料表

首先，使用 [Create React App](https://blog.logrocket.com/create-react-app-a-quick-setup-guide-b812f0aad03c/) 创建一个新项目。

```
npx create-react-app material-table-demo

```

创建项目后，进入项目的根文件夹:

```
cd material-table-demo

```

并添加以下依赖项以使用 material-table:

```
npm install material-table --save
npm install @material-ui/core --save

```

或者，如果你想用纱线:

```
yarn add material-table
yarn add @material-ui/core

```

这就是开始使用材料表所需的全部内容。

## 创建表格

要用 material-table 呈现一个表，您必须提供数据(一个对象数组)和要用数据映射的列的名称。这些列将指定哪条数据将放在哪个列中。

让我们创建一个名为`BasicTable.jsx`的新文件，并添加以下代码:

```
import MaterialTable from "material-table";

const data = [
  { name: "Mohammad", surname: "Faisal", birthYear: 1995 },
  { name: "Nayeem Raihan ", surname: "Shuvo", birthYear: 1994 },
];

const columns = [
  { title: "Name", field: "name" },
  { title: "Surname", field: "surname" },
  { title: "Birth Year", field: "birthYear", type: "numeric" },
];

export const BasicTable = () => {
  return <MaterialTable title="Basic Table" columns={columns} data={data} />;
};

```

这段代码将呈现下表:

![Basic Table](img/dd1e5f1456262797d40e4c55d9cc1f41.png)

不错！我们的数据显示正确，我们还有默认的`search`和`pagination`功能——我们不需要写一行代码。

但是等等，有些不对劲。如果你注意图像上画的箭头，你会注意到一些奇怪的文字。这是因为 material-table 在内部试图使用材质图标，我们需要手动操作。

## 添加材料图标

要将材料图标添加到我们的表中，我们必须首先在我们的项目中安装依赖项。

```
npm install @material-ui/icons --save

```

或者，用纱线:

```
yarn add @material-ui/icons

```

我们在项目中添加了材质图标。

现在，假设我们想要导入一个图标。我们需要添加整套图标吗？显然不是——那会让应用程序太重。

下面我们将演示导入材料图标的错误和正确方法，以便在你的材料表中使用。

**错误方式:**

```
import { AddBox, ArrowDownward } from "@material-ui/icons";

```

这将导致导入所有图标。

**正确方式:**

相反，我们应该尝试只获取特定的图标:

```
import AddBox from "@material-ui/icons/AddBox";
import ArrowDownward from "@material-ui/icons/ArrowDownward";

```

遵循这个最佳实践，让我们创建一个特殊的组件，将所有需要的图标添加到项目中，并在任何地方重用它。

创建一个名为`MaterialTableIcons.js`的新文件，并添加以下代码:

```
import React, { forwardRef } from "react";

import AddBox from "@material-ui/icons/AddBox";
import ArrowDownward from "@material-ui/icons/ArrowDownward";
import Check from "@material-ui/icons/Check";
import ChevronLeft from "@material-ui/icons/ChevronLeft";
import ChevronRight from "@material-ui/icons/ChevronRight";
import Clear from "@material-ui/icons/Clear";
import DeleteOutline from "@material-ui/icons/DeleteOutline";
import Edit from "@material-ui/icons/Edit";
import FilterList from "@material-ui/icons/FilterList";
import FirstPage from "@material-ui/icons/FirstPage";
import LastPage from "@material-ui/icons/LastPage";
import Remove from "@material-ui/icons/Remove";
import SaveAlt from "@material-ui/icons/SaveAlt";
import Search from "@material-ui/icons/Search";
import ViewColumn from "@material-ui/icons/ViewColumn";

const tableIcons = {
  Add: forwardRef((props, ref) => <AddBox {...props} ref={ref} />),
  Check: forwardRef((props, ref) => <Check {...props} ref={ref} />),
  Clear: forwardRef((props, ref) => <Clear {...props} ref={ref} />),
  Delete: forwardRef((props, ref) => <DeleteOutline {...props} ref={ref} />),
  DetailPanel: forwardRef((props, ref) => <ChevronRight {...props} ref={ref} />),
  Edit: forwardRef((props, ref) => <Edit {...props} ref={ref} />),
  Export: forwardRef((props, ref) => <SaveAlt {...props} ref={ref} />),
  Filter: forwardRef((props, ref) => <FilterList {...props} ref={ref} />),
  FirstPage: forwardRef((props, ref) => <FirstPage {...props} ref={ref} />),
  LastPage: forwardRef((props, ref) => <LastPage {...props} ref={ref} />),
  NextPage: forwardRef((props, ref) => <ChevronRight {...props} ref={ref} />),
  PreviousPage: forwardRef((props, ref) => <ChevronLeft {...props} ref={ref} />),
  ResetSearch: forwardRef((props, ref) => <Clear {...props} ref={ref} />),
  Search: forwardRef((props, ref) => <Search {...props} ref={ref} />),
  SortArrow: forwardRef((props, ref) => <ArrowDownward {...props} ref={ref} />),
  ThirdStateCheck: forwardRef((props, ref) => <Remove {...props} ref={ref} />),
  ViewColumn: forwardRef((props, ref) => <ViewColumn {...props} ref={ref} />),
};

export default tableIcons;

```

最酷的是，如果你不喜欢`[material-icons](https://mui.com/components/material-icons/)`中的图标，你可以通过切换到其他图标库来改变外观。

## 在表格中使用图标

现在让我们将图标导入到我们想要修复问题的表组件中。

```
import tableIcons from "./MaterialTableIcons";

```

接下来，将图标传递到表格中。用附加图标 prop 更新您的代码:

```
<MaterialTable title="Table" icons={tableIcons} columns={columns} data={data} />;

```

现在我们的表看起来会像这样:

![Basic Table Icons](img/ae8dc8f63f3c15e40473bf5f4ce97a94.png)

现在完美了！让我们探索一些其他功能。

## 向物料表添加操作

我们可以在材料表中添加两种类型的动作:

*   每行的操作(例如，删除或更新行)
*   对整个表格的操作(例如，向表格添加新项目)

我们可以通过向表中传递一个`actions`数组属性来轻松地添加动作。

如果动作是特定于行的，我们不需要说什么。如果动作是针对整个表的，我们必须将`isFreeAAction = true`传递到表中。

这里有一个例子:

```
import MaterialTable from "material-table";
import tableIcons from "./MaterialTableIcons";

export const TableWithActions = () => {
  return (
    <MaterialTable
      title="Table with actions"
      actions={[
        {
          icon: tableIcons.Delete,
          tooltip: "Delete User",
          onClick: (event, rowData) => alert("You want to delete " + rowData.name),
        },
        {
          icon: tableIcons.Add,
          tooltip: "Add User",
          isFreeAction: true,
          onClick: (event) => alert("You want to add a new row"),
        },
      ]}
      ... other props as before
    />
  );
};

```

我们正在移除`data`和`column`道具来移除重复的道具。我们的表现在将有两个额外的按钮——一个在右上角(**添加按钮**)和一个**删除按钮**用于每行。

![Delete Notification](img/a7c89cc8be52b1d1fa994ececcb02284.png)

这使您能够为表格指定操作。有一个`disabled`属性，您可以通过它来决定是否要启用动作。

## 覆盖表格组件

现在，假设您不喜欢动作按钮的外观，并且想要呈现一个自定义的动作按钮。或者，您可能不喜欢表格单元格的外观。材料表使您能够改变任何组件的外观和感觉。

为了实现这一点，`MaterialTable`组件接受一个名为`components`的额外参数，您可以覆盖表的几乎任何部分。

为了展示这是如何工作的，我们将修改前面例子中的 **Delete** 按钮。假设我们不想要一个**删除**图标；相反，我们想要一个真正的文本按钮。

我们所要做的就是将以下道具添加到表格中:

```
<MaterialTable
    components={{
        Action: (props) => (
            <button onClick={(event) => props.action.onClick(event, props.data)}>
                Custom Delete Button
            </button>
        ),
    }}
    ... other props
/>

```

现在我们的表看起来像这样:

![Table With Actions](img/b0770ddd0548b386525b33cc3c829c20.png)

关于定制选项的完整列表，参见[材料表文档](https://material-table.com/#/docs/features/component-overriding)。

## 自定义渲染

很高兴我们可以覆盖 material-table 提供的组件。但是[呈现我们自己的专栏](https://material-table.com/#/docs/features/custom-column-rendering)呢？我们已经看到，我们将列和数据属性传递到表和 material 中——表本身负责渲染。

现在，假设我们有一个来自远程源的图像 URL。我们不想呈现 URL(因为那是愚蠢的😛).

在这个场景中，我们可以渲染任何我们喜欢的东西，比如一个图像组件。

为此，我们必须修改传递到表中的列。

让我们像下面这样修改我们之前的例子，添加`imageUrl`并渲染它:

```
import MaterialTable from "material-table";
import tableIcons from "./MaterialTableIcons";

const data = [
    {
        name: "Mohammad",
        surname: "Faisal",
        birthYear: 1995,
        imageUrl: "https://avatars0.githubusercontent.com/u/7895451?s=460&v=4",
    },
    {
        name: "Nayeem Raihan ",
        surname: "Shuvo",
        birthYear: 1994,
        imageUrl: "https://avatars0.githubusercontent.com/u/7895451?s=460&v=4",
    },
];

const columns = [
    {
        title: "Avatar",
        field: "imageUrl",
        render: (rowData) => <img src={rowData.imageUrl} style={{ width: 40, borderRadius: "50%" }} />,
    },
    { title: "Name", field: "name" },
    { title: "Surname", field: "surname" },
    { title: "Birth Year", field: "birthYear", type: "numeric" },
];

export const ImageTable = () => {
    return <MaterialTable title="Basic Table" icons={tableIcons} columns={columns} data={data} />;
};

```

现在我们的表看起来像这样:

![Avatar Icons](img/c49cddafef89c6d5bc5b34eba8e7a2cf.png)

## 将数据导出到`.csv`和`.pdf`

到目前为止, [export](https://material-table.com/#/docs/features/export) 特性是我最喜欢的材料表库特性。当您构建数据密集型应用程序时，您可能需要允许用户将数据导出到 Excel 或 PDF。传统上，您必须使用一些自定义 Excel 库来完成这项工作。

使用 material-table，您只需传递一个表示`exportButton : true`的选项，就可以开始了。

```
import MaterialTable from "material-table";

export const BasicTable = () => {
    return (
        <MaterialTable
            ... other props
            options={{
                exportButton: true,
            }}
        />
    );
};

```

现在，在表格的顶部会有一个额外的图标，使用户能够下载`.csv`和`.pdf`格式的数据。

![Export Table](img/79b992498fda9f1fa93d7865ef9e0711.png)

仅仅一行代码，你就已经让你的表超负荷了。

## 分组数据

假设您有一个人员列表，每个人都有一个`name`、`age`等。如果你想看到同龄的人，你如何在你的表中显示这一点？

material-table 使您能够非常容易地对数据进行分组。在任何其他类型的表中，动态地做到这一点都非常困难。

使用 material-table，您需要做的就是传递另一个名为`grouping :true`的选项。

```
<MaterialTable
    ... other props
    options={{
        grouping: true,
    }}
/> 

```

现在，如果您将`Name`列标题拖到表格的顶部，您应该会看到如下内容:

![Group Table](img/4b83eee287b0cfda7108d5387d44f0fd.png)

material-table 内部使用了另一个名为 [React DnD](https://blog.logrocket.com/react-drag-and-drop/) 的强大库来实现这一点。

## 搜索

我们已经看到，默认情况下，material-table 使我们能够通过数据进行[搜索。您可以通过在`options.searchFieldStyle`中添加样式来覆盖搜索栏的外观。](https://material-table.com/#/docs/features/search)

如果因为某种原因不想显示搜索栏，就要通过`search : false`，像这样:

```
<MaterialTable
    // other props
    options={{
      search: false
    }}
/>

```

现在，您的表中应该没有搜索字段:

![No Search Field](img/7f7edf4826b6cf92438ea927779abb09.png)

## 分类数据

material-table 也能让你非常容易地对你的数据进行分类。你要做的就是传递另一个选项，`sorting : true`。

```
<MaterialTable
    ... other props
    options={{ sorting: true }}
/>

```

现在，您的所有列都应该可以排序了:

![Sortable Columns](img/4a170d3f03ebb421c09e15a8b0fb330d.png)

记住，默认情况下，material-table 将尝试按字典顺序对数据进行排序。如果您想使用其他机制进行排序，您可以通过覆盖 columns 属性来修改它，如下所示:

```
 columns={[
      {
          title: 'Name', 
          field: 'name',
          customSort: (a, b) => a.name.length - b.name.length
      },
      .. other columns
  ]}

```

这使您可以完全控制如何对数据进行排序。

## 材料替代品-表格

在本教程中，我们将重点放在 material-table 上，但是在 React 应用程序中当然也可以使用其他表格组件。让我们强调几个突出的材料表的替代品。

### rsuite 表

rsuite-table 具有广泛的功能，非常适合创建专业外观的表格。它支持我们上面提到的几乎所有功能，但没有下载数据选项。
另外，不支持开箱即用的数据分组。

### 反应表

[React 表](https://blog.logrocket.com/complete-guide-building-smart-data-table-react/)不像这里说的其他的是组件库；它是钩子的集合，使您能够向任何表组件添加特性。根据[官网](https://react-table.tanstack.com/)的说法，React Table 是一个“表格实用程序，而不是表格组件”

您可以使用 React Table 添加排序、过滤、分组、分页等功能。，应用于任何表组件。

### MUI-数据表

MUI-Datatables 是 material-table 的一个轻量级版本。如果你不需要上述的全部特性，这个库提供了一个更简单的选择。

## 物料表是否得到有效维护？

尽管 material-table 非常健壮且功能丰富，但自从最初的创建者离开项目后，维护和支持就减少了。其他贡献者仍然在维护这个库，尽管没有那么积极。

但是当前项目有一个分叉叫做 [material-table/core](https://www.npmjs.com/package/@material-table/core) 。这个版本还没有像 material-table 那样完善，但是它正在积极开发中，并且经常更新。

虽然它还不支持上述的全部功能，但 material-table/core 与新的 [Material-UI v5](https://github.com/mui-org/material-ui/issues/20012) 兼容，这太棒了！

material-table 仍然是更受欢迎的库，每周大约有 150，000 次下载，而在撰写本文时 material-table/core 的每周下载量为 23，000 次。但是如果你正在使用 Material-UI 的最新版本，并且正在寻找一个具有更积极支持的库，那么 material-table/core 是一个可行的选择。

## 最后的想法

正如你所看到的，material-table 解决了几乎所有的问题，涵盖了你在构建数据密集型 React 应用时可能遇到的所有用例。这简直好得令人难以置信。

但是有一个问题:material-table 比上面提到的所有选择都要重。使用 [bundlephobia](https://bundlephobia.com/) 可以看到`material-table`用于生产时超过 200KB。

![Bundle Size](img/411f2b72e3b6dd7166a659efcc47c7b4.png)

因此，如果您的数据需求很简单，那么使用这个库可能有些过头了。您可能想要选择一些其他的轻量级库，比如大小约为 30KB 的 rsuite-table，甚至只有 15KB 的 Rect Table。

我希望这篇指南能够帮助您在下次需要为 React 项目选择表库时做出更明智的决定。

本演示中使用的完整代码可在 [GitHub](https://github.com/Mohammad-Faisal/material-table-demo) 上获得。

## 使用 LogRocket 消除传统反应错误报告的噪音

[LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

是一款 React analytics 解决方案，可保护您免受数百个误报错误警报的影响，只针对少数真正重要的项目。LogRocket 告诉您 React 应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

[![](img/f300c244a1a1cf916df8b4cb02bec6c6.png) ](https://lp.logrocket.com/blg/react-signup-general) [ ![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png) ](https://lp.logrocket.com/blg/react-signup-general) [LogRocket](https://lp.logrocket.com/blg/react-signup-issue-free)

自动聚合客户端错误、反应错误边界、还原状态、缓慢的组件加载时间、JS 异常、前端性能指标和用户交互。然后，LogRocket 使用机器学习来通知您影响大多数用户的最具影响力的问题，并提供您修复它所需的上下文。

关注重要的 React bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/react-signup-issue-free)