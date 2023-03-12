# React Table v7 有什么新特性？- LogRocket 博客

> 原文：<https://blog.logrocket.com/building-styling-tables-react-table-v7/>

众所周知，用 React 创建表格是一件痛苦的事情。因此，毫不奇怪，有许多库可以让 React 应用程序更容易地创建表格。其中一个包是 [`react-table`](https://blog.logrocket.com/complete-guide-building-smart-data-table-react/) 。它提供了一个现代的、基于 Hooks 的 API，让我们可以在 React 中轻松地创建表格。

在最新的主要版本中， [React Table v7](https://github.com/tannerlinsley/react-table/releases/tag/v7.0.0) ，创建者 Tanner Linsley 旨在将整个库重构为一个 UI、样式和标记无关的表构建工具，该工具专门使用钩子。

在本教程中，我们将告诉你所有你需要知道的关于最新版本的`react-table`(在撰写本文时，最新的版本是 [React Table v7.6.3](https://github.com/tannerlinsley/react-table/releases) )，概述 React Table v7 附带的主要变化和新特性，并通过一个基本示例来查看它们的运行情况。

我们将讨论以下内容:

## React 表 v7 简介

2020 年 3 月，React Table 创建者 Tanner Linsley 发布了 [React Table v7](https://github.com/tannerlinsley/react-table/releases/tag/v7.0.0) ，他将其描述为“一年多来将整个库重构为一个只有钩子的 UI/样式/标记不可知的表构建实用程序的工作的高潮。”

[https://www.youtube.com/embed/fwOZUU3OqmY](https://www.youtube.com/embed/fwOZUU3OqmY)

视频

React Table v7 由一组 React 挂钩和插件组成，旨在帮助您将复杂数据网格的逻辑特性组合成一个单一的、高性能的、可扩展的和非独立的 API，该 API 由主`useTable`挂钩返回。

作为一个无头工具，React Table v7 不呈现或提供现成的数据表 UI 元素。这意味着您负责使用 React Table 提供的钩子的状态和回调来呈现您自己的表标记。

## React 表 7 中的新特性

根据[发布说明](https://github.com/tannerlinsley/react-table/releases/tag/v7.0.0)，React Table 7 向该库引入了以下特性:

*   自动开箱即用、完全可控的 API
*   排序(多且稳定)
*   过滤
*   旋转和聚集
*   行选择和扩展
*   列排序
*   服务器端/受控数据/状态

最新版本中的更改和新功能旨在使 React Table 更加:

*   轻量级(5kb-14kb+，取决于使用的功能和树摇动)
*   无头(100%可定制，自带用户界面)
*   可制作动画
*   可虚拟化
*   可调整大小的
*   可扩展(通过一个新的基于钩子的插件系统)

如上所述，`react-table`的最新小版本是 2021 年 1 月 11 日发布的[反应表 v7.6.3](https://www.npmjs.com/package/react-table?activeTab=versions) 。前往 npm 获取 React 表的完整[版本历史。](https://www.npmjs.com/package/react-table?activeTab=versions)

## 用 React Table v7 创建基本表

使用 react-table 在 React 应用程序中创建一个基本表格很容易。运行以下命令进行安装:

```
npm i react-table
```

那么我们可以如下使用它:

```
import React from "react";
import { useTable } from "react-table";

const data = [
  { firstName: "jane", lastName: "doe", age: 20 },
  { firstName: "john", lastName: "smith", age: 21 }
];

const columns = [
  {
    Header: "Name",
    columns: [
      {
        Header: "First Name",
        accessor: "firstName"
      },
      {
        Header: "Last Name",
        accessor: "lastName"
      }
    ]
  },
  {
    Header: "Other Info",
    columns: [
      {
        Header: "Age",
        accessor: "age"
      }
    ]
  }
];

const Table = ({ columns, data }) => {
  const {
    getTableProps,
    getTableBodyProps,
    headerGroups,
    rows,
    prepareRow
  } = useTable({
    columns,
    data
  });

  return (
    <table {...getTableProps()}>
      <thead>
        {headerGroups.map(headerGroup => (
          <tr {...headerGroup.getHeaderGroupProps()}>
            {headerGroup.headers.map(column => (
              <th {...column.getHeaderProps()}>{column.render("Header")}</th>
            ))}
          </tr>
        ))}
      </thead>
      <tbody {...getTableBodyProps()}>
        {rows.map((row, i) => {
          prepareRow(row);
          return (
            <tr {...row.getRowProps()}>
              {row.cells.map(cell => {
                return <td {...cell.getCellProps()}>{cell.render("Cell")}</td>;
              })}
            </tr>
          );
        })}
      </tbody>
    </table>
  );
};

export default function App() {
  return (
    <div className="App">
      <Table columns={columns} data={data} />
    </div>
  );
}
```

在上面的代码中，我们从 react-table 包中导入了`useTable`钩子。然后我们创建了`data`来用数据填充表格:

```
const data = [
  { firstName: "jane", lastName: "doe", age: 20 },
  { firstName: "john", lastName: "smith", age: 21 }
];
```

我们只是将属性放在对象中，为表行添加额外的数据。

我们可以用下面的代码在列表中创建列:

```
const columns = [
  {
    Header: "Name",
    columns: [
      {
        Header: "First Name",
        accessor: "firstName"
      },
      {
        Header: "Last Name",
        accessor: "lastName"
      }
    ]
  },
  {
    Header: "Other Info",
    columns: [
      {
        Header: "Age",
        accessor: "age"
      }
    ]
  }
];
```

`Header`属性包含将要显示的名称字符串，而`accessor`属性是数组条目对象中的属性名。

在`Table`组件代码中，我们有:

```
const Table = ({ columns, data }) => {
  const {
    getTableProps,
    getTableBodyProps,
    headerGroups,
    rows,
    prepareRow
  } = useTable({
    columns,
    data
  });

  return (
    <table {...getTableProps()}>
      <thead>
        {headerGroups.map(headerGroup => (
          <tr {...headerGroup.getHeaderGroupProps()}>
            {headerGroup.headers.map(column => (
              <th {...column.getHeaderProps()}>{column.render("Header")}</th>
            ))}
          </tr>
        ))}
      </thead>
      <tbody {...getTableBodyProps()}>
        {rows.map((row, i) => {
          prepareRow(row);
          return (
            <tr {...row.getRowProps()}>
              {row.cells.map(cell => {
                return <td {...cell.getCellProps()}>{cell.render("Cell")}</td>;
              })}
            </tr>
          );
        })}
      </tbody>
    </table>
  );
};
```

`useTable`钩子从道具中取出`column`和`data`，道具来源于我们之前定义的那些对象和数组。我们从从`useTable`钩子返回的对象中得到`getTableProps`和`getTableBodyProps`的函数。

在`th`标签中调用`getHeaderProps()`函数，并扩展以填充标题。这样，我们将由`getTableBodyProps()`函数返回的道具传递给`tbody`来展开道具，以正确地样式化和对齐列。

从`useTable`钩子返回的`prepareRow(row);`创建行条目，在调用函数改变了适当位置的`row`对象后，可以自动填充这些行条目。

然后我们有:

```
{rows.map((row, i) => {
  prepareRow(row);
  return (
   <tr {...row.getRowProps()}>
     {row.cells.map(cell => {
       return <td {...cell.getCellProps()}>{cell.render("Cell")}</td>;
     })}
   </tr>
  );
})}
```

这将通过从`getCellProps()`方法中获取项目，然后从返回的对象中填充值来自动填充单元格。我们调用了`cell.render("Cell")`来将每个`td`呈现为一个单元格。

最后，在`App`中，我们使用了`Table`组件，它带有`column`和`data`道具。这些值是我们之前创建的`columns`和`data`对象。

表中的项目显示在两个窗格中。左侧窗格有两列的**姓名**标题:**名**和**姓**。然后，右边的窗格显示了带有**年龄**列的**其他信息**标题。

## 添加页脚

我们可以通过向 out 列对象添加一个`Footer`属性来添加一个页脚。我们可以编写以下代码来实现这一点:

```
import React from "react";
import { useTable } from "react-table";

const data = [
  { firstName: "jane", lastName: "doe", age: 20 },
  { firstName: "john", lastName: "smith", age: 21 }
];

const columns = [
  {
    Header: "Name",
    Footer: "Name",
    columns: [
      {
        Header: "First Name",
        accessor: "firstName"
      },
      {
        Header: "Last Name",
        accessor: "lastName"
      }
    ]
  },
  {
    Header: "Other Info",
    Footer: "Other Info",
    columns: [
      {
        Header: "Age",
        accessor: "age",
        Footer: info => {
          const total = React.useMemo(
            () => info.rows.reduce((sum, row) => row.values.age + sum, 0),
            [info.rows]
          );

          return <>Average Age: {total / info.rows.length}</>;
        }
      }
    ]
  }
];

const Table = ({ columns, data }) => {
  const {
    getTableProps,
    getTableBodyProps,
    headerGroups,
    footerGroups,
    rows,
    prepareRow
  } = useTable({
    columns,
    data
  });

  return (
    <table {...getTableProps()}>
      <thead>
        {headerGroups.map(headerGroup => (
          <tr {...headerGroup.getHeaderGroupProps()}>
            {headerGroup.headers.map(column => (
              <th {...column.getHeaderProps()}>{column.render("Header")}</th>
            ))}
          </tr>
        ))}
      </thead>
      <tbody {...getTableBodyProps()}>
        {rows.map((row, i) => {
          prepareRow(row);
          return (
            <tr {...row.getRowProps()}>
              {row.cells.map(cell => {
                return <td {...cell.getCellProps()}>{cell.render("Cell")}</td>;
              })}
            </tr>
          );
        })}
      </tbody>
      <tfoot>
        {footerGroups.map(group => (
          <tr {...group.getFooterGroupProps()}>
            {group.headers.map(column => (
              <td {...column.getFooterProps()}>{column.render("Footer")}</td>
            ))}
          </tr>
        ))}
      </tfoot>
    </table>
  );
};

export default function App() {
  return (
    <div className="App">
      <Table columns={columns} data={data} />
    </div>
  );
}
```

在上面的代码中，我们将`Footer`属性添加到了`columns`数组，如下所示:

```
const columns = [
  {
    Header: "Name",
    Footer: "Name",
    columns: [
      {
        Header: "First Name",
        accessor: "firstName"
      },
      {
        Header: "Last Name",
        accessor: "lastName"
      }
    ]
  },
  {
    Header: "Other Info",
    Footer: "Other Info",
    columns: [
      {
        Header: "Age",
        accessor: "age",
        Footer: info => {
          const total = React.useMemo(
            () => info.rows.reduce((sum, row) => row.values.age + sum, 0),
            [info.rows]
          );

          return <>Average Age: {total / info.rows.length}</>;
        }
      }
    ]
  }
];
```

我们将`Footer`属性添加到每个对象的顶层。

此外，我们为 Age 列的对象中的`Footer`属性添加了一个函数。

年龄列的对象中的`Footer`属性是:

```
info => {
  const total = React.useMemo(
    () => info.rows.reduce((sum, row) => row.values.age + sum, 0),
    [info.rows]
  );

  return <>Average Age: {total / info.rows.length}</>;
}
```

它采用了包含所有表格数据的`info`对象。然后，我们对每个条目的所有`age`属性值求和，并除以`info.row.length`，得出平均年龄。这显示在表格底部的**年龄**栏下。

平均值会随着行的变化而变化，因为我们有`[info.rows]`，它会观察行中变化的值，并在行发生变化时重新计算值。

## 整理

我们可以通过调用几个函数给表格添加排序。我们必须将`useSortBy`钩子作为`useTable`钩子的第二个参数传入，以获得表中的排序能力。

然后，在我们的 JSX 代码中，我们必须传入`column.getSortByToggleProps()`到`column.getHeaderProps`来获得呈现列中列的排序顺序。

我们可以通过使用`column.isSorted`和`column.isSortedDesc`来检查一列的排序顺序，分别检查一列是按升序还是降序排序的。

此外，我们可以向列数组条目添加一个`sortType`属性，这样我们就可以指定排序类型。例如，我们可以编写以下代码来为我们的表添加基本排序:

```
import React from "react";
import { useTable, useSortBy } from "react-table";

const data = [
  { firstName: "jane", lastName: "doe" },
  { firstName: "john", lastName: "smith" }
];

const columns = [
  {
    Header: "Name",
    columns: [
      {
        Header: "First Name",
        accessor: "firstName",
        sortType: "basic"
      },
      {
        Header: "Last Name",
        accessor: "lastName",
        sortType: "basic"
      }
    ]
  }
];

const Table = ({ columns, data }) => {
  const {
    getTableProps,
    getTableBodyProps,
    headerGroups,
    rows,
    prepareRow
  } = useTable(
    {
      columns,
      data
    },
    useSortBy
  );

  return (
    <table {...getTableProps()}>
      <thead>
        {headerGroups.map(headerGroup => (
          <tr {...headerGroup.getHeaderGroupProps()}>
            {headerGroup.headers.map(column => (
              <th {...column.getHeaderProps(column.getSortByToggleProps())}>
                {column.render("Header")}
                <span>
                  {column.isSorted ? (column.isSortedDesc ? " 🔽" : " 🔼") : ""}
                </span>
              </th>
            ))}
          </tr>
        ))}
      </thead>
      <tbody {...getTableBodyProps()}>
        {rows.map((row, i) => {
          prepareRow(row);
          return (
            <tr {...row.getRowProps()}>
              {row.cells.map(cell => {
                return <td {...cell.getCellProps()}>{cell.render("Cell")}</td>;
              })}
            </tr>
          );
        })}
      </tbody>
    </table>
  );
};

export default function App() {
  return (
    <div className="App">
      <Table columns={columns} data={data} />
    </div>
  );
}
```

在上面的代码中，我们指定`sortType`是`'basic'`，这样单词按字母顺序排序，数字按数字顺序排序。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

然后我们通过书写来呈现`thead`:

```
<thead>
  {headerGroups.map(headerGroup => (
    <tr {...headerGroup.getHeaderGroupProps()}>
      {headerGroup.headers.map(column => (
        <th {...column.getHeaderProps(column.getSortByToggleProps())}>
          {column.render("Header")}
          <span>
            {column.isSorted ? (column.isSortedDesc ? " 🔽" : " 🔼") : ""}
          </span>
        </th>
      ))}
    </tr>
  ))}
</thead>
```

这将添加图标，用于指示每列的排序顺序，并获取列的排序顺序。

写完代码后，我们会在列标题的右边看到一个排序按钮，我们可以点击它来对列进行排序。

## 过滤

过滤比创建简单的表或排序更复杂。我们必须创建一个带有输入控件的组件，我们可以用它来过滤我们的项目。输入组件将把从`useTable`返回的函数作为道具，并在输入中调用它们。

例如，我们可以编写以下代码来实现这一点:

```
import React from "react";
import { useTable, useFilters, useGlobalFilter } from "react-table";

const data = [
  { firstName: "jane", lastName: "doe", age: 20 },
  { firstName: "john", lastName: "smith", age: 21 }
];

const columns = [
  {
    Header: "Name",
    columns: [
      {
        Header: "First Name",
        accessor: "firstName",
        filter: "text"
      },
      {
        Header: "Last Name",
        accessor: "lastName",
        filter: "text"
      }
    ]
  },
  {
    Header: "Other Info",
    columns: [
      {
        Header: "Age",
        accessor: "age",
        filter: "text"
      }
    ]
  }
];

const DefaultColumnFilter = ({
  column: { filterValue, preFilteredRows, setFilter }
}) => {
  const count = preFilteredRows.length;

  return (
    <input
      value={filterValue || ""}
      onChange={e => {
        setFilter(e.target.value || undefined);
      }}
      placeholder={`Search ${count} records...`}
    />
  );
};

const GlobalFilter = ({
  preGlobalFilteredRows,
  globalFilter,
  setGlobalFilter
}) => {
  const count = preGlobalFilteredRows && preGlobalFilteredRows.length;

  return (
    <span>
      Search:{" "}
      <input
        value={globalFilter || ""}
        onChange={e => {
          setGlobalFilter(e.target.value || undefined); // Set undefined to remove the filter entirely
        }}
        placeholder={`${count} records...`}
        style={{
          border: "0"
        }}
      />
    </span>
  );
};

const Table = ({ columns, data }) => {
  const filterTypes = React.useMemo(
    () => ({
      text: (rows, id, filterValue) => {
        return rows.filter(row => {
          const rowValue = row.values[id];
          return rowValue !== undefined
            ? String(rowValue)
                .toLowerCase()
                .startsWith(String(filterValue).toLowerCase())
            : true;
        });
      }
    }),
    []
  );

  const defaultColumn = React.useMemo(
    () => ({
      Filter: DefaultColumnFilter
    }),
    []
  );

  const {
    getTableProps,
    getTableBodyProps,
    headerGroups,
    rows,
    prepareRow,
    state,
    visibleColumns,
    preGlobalFilteredRows,
    setGlobalFilter
  } = useTable(
    {
      columns,
      data,
      defaultColumn,
      filterTypes
    },
    useFilters,
    useGlobalFilter
  );

  return (
    <table {...getTableProps()}>
      <thead>
        {headerGroups.map(headerGroup => (
          <tr {...headerGroup.getHeaderGroupProps()}>
            {headerGroup.headers.map(column => (
              <th {...column.getHeaderProps()}>
                {column.render("Header")}
                <div>{column.canFilter ? column.render("Filter") : null}</div>
              </th>
            ))}
          </tr>
        ))}
        <tr>
          <th
            colSpan={visibleColumns.length}
            style={{
              textAlign: "left"
            }}
          >
            <GlobalFilter
              preGlobalFilteredRows={preGlobalFilteredRows}
              globalFilter={state.globalFilter}
              setGlobalFilter={setGlobalFilter}
            />
          </th>
        </tr>
      </thead>
      <tbody {...getTableBodyProps()}>
        {rows.map((row, i) => {
          prepareRow(row);
          return (
            <tr {...row.getRowProps()}>
              {row.cells.map(cell => {
                return <td {...cell.getCellProps()}>{cell.render("Cell")}</td>;
              })}
            </tr>
          );
        })}
      </tbody>
    </table>
  );
};

export default function App() {
  return (
    <div className="App">
      <Table columns={columns} data={data} />
    </div>
  );
}
```

在上面的代码中，我们添加了如下的`GlobalFilter`组件:

```
const GlobalFilter = ({
  preGlobalFilteredRows,
  globalFilter,
  setGlobalFilter
}) => {
  const count = preGlobalFilteredRows && preGlobalFilteredRows.length;

  return (
    <span>
      Search:{" "}
      <input
        value={globalFilter || ""}
        onChange={e => {
          setGlobalFilter(e.target.value || undefined);
        }}
        placeholder={`${count} records...`}
        style={{
          border: "0"
        }}
      />
    </span>
  );
};
```

这用于通过调用作为 props 传入的`setGlobalFilter`函数来搜索数据中存在的所有列。`preGlobalFilteredRows`是一个数组，我们可以在其中计算要搜索的行数。

然后，在`Table`组件中，我们添加了以下代码:

```
const filterTypes = React.useMemo(
  () => ({
    text: (rows, id, filterValue) => {
      return rows.filter(row => {
        const rowValue = row.values[id];
        return rowValue !== undefined
          ? String(rowValue)
              .toLowerCase()
              .startsWith(String(filterValue).toLowerCase())
          : true;
      });
    }
  }),
  []
);

const defaultColumn = React.useMemo(
  () => ({
    Filter: DefaultColumnFilter
  }),
  []
);

const {
  getTableProps,
  getTableBodyProps,
  headerGroups,
  rows,
  prepareRow,
  state,
  visibleColumns,
  preGlobalFilteredRows,
  setGlobalFilter
} = useTable(
  {
    columns,
    data,
    defaultColumn,
    filterTypes
  },
  useFilters,
  useGlobalFilter
);
```

`defaultColumn`有一个缓存的对象，其`DefaultColumnFilter`设置如下:

```
const DefaultColumnFilter = ({
  column: { filterValue, preFilteredRows, setFilter }
}) => {
  const count = preFilteredRows.length;

  return (
    <input
      value={filterValue || ""}
      onChange={e => {
        setFilter(e.target.value || undefined);
      }}
      placeholder={`Search ${count} records...`}
    />
  );
};
```

`defaultColumn`缓存用于搜索单个列的输入组件。我们还有`filterTypes`常量，它包含我们用来搜索表的过滤器的缓存值。

我们有一个带有`text`方法的对象，用于在我们键入时搜索我们要寻找的条目。在该方法中，我们调用了`rows`上的`filter`来返回以给定搜索字符串开始的项目，该字符串存储在`filterValue`中。

我们还使用了更多从`useTable`钩子返回的属性，并向钩子传递了更多的参数，包括`useFilters`和`useGlobalFilter`钩子，让我们分别按列和全局进行过滤。

此外，我们将`defaultColumn`和`filterTypes`对象添加到第一个参数中的对象，让我们设置默认情况下用于过滤的组件。`filterTypes`让我们将值设置为我们创建的函数的名称，该函数用于从数据数组中返回过滤后的数据。

最后，我们得到两个输入来分别过滤每一列，还有一个输入可以从全局过滤所有列中的项目。

我们可以使用`usePagination`钩子添加分页，它作为`useTable`钩子的参数传入。

然后,`useTable`钩子返回一堆与分页相关的变量，我们用它们来跟踪分页并导航到不同的页面。

要制作一个带有分页的简单表格，我们可以编写以下代码:

```
import React from "react";
import { useTable, usePagination } from "react-table";

const firstNames = ["jane", "john", "alex"];
const lastName = ["smith", "jones"];

const data = Array(100)
  .fill()
  .map(a => ({
    firstName: firstNames[Math.floor(Math.random() * firstNames.length)],
    lastName: lastName[Math.floor(Math.random() * lastName.length)],
    age: Math.ceil(75 * Math.random())
  }));

const columns = [
  {
    Header: "Name",
    columns: [
      {
        Header: "First Name",
        accessor: "firstName"
      },
      {
        Header: "Last Name",
        accessor: "lastName"
      }
    ]
  },
  {
    Header: "Other Info",
    columns: [
      {
        Header: "Age",
        accessor: "age"
      }
    ]
  }
];

const Table = ({ columns, data }) => {
  const {
    getTableProps,
    getTableBodyProps,
    headerGroups,
    prepareRow,
    page,
    canPreviousPage,
    canNextPage,
    pageOptions,
    pageCount,
    gotoPage,
    nextPage,
    previousPage,
    setPageSize,
    state: { pageIndex, pageSize }
  } = useTable(
    {
      columns,
      data,
      initialState: { pageIndex: 0 }
    },
    usePagination
  );

  return (
    <>
      <table {...getTableProps()}>
        <thead>
          {headerGroups.map(headerGroup => (
            <tr {...headerGroup.getHeaderGroupProps()}>
              {headerGroup.headers.map(column => (
                <th {...column.getHeaderProps()}>{column.render("Header")}</th>
              ))}
            </tr>
          ))}
        </thead>
        <tbody {...getTableBodyProps()}>
          {page.map((row, i) => {
            prepareRow(row);
            return (
              <tr {...row.getRowProps()}>
                {row.cells.map(cell => {
                  return (
                    <td {...cell.getCellProps()}>{cell.render("Cell")}</td>
                  );
                })}
              </tr>
            );
          })}
        </tbody>
      </table>
      <div>
        <button onClick={() => gotoPage(0)} disabled={!canPreviousPage}>
          {"<<"}
        </button>{" "}
        <button onClick={() => previousPage()} disabled={!canPreviousPage}>
          {"<"}
        </button>{" "}
        <button onClick={() => nextPage()} disabled={!canNextPage}>
          {">"}
        </button>{" "}
        <button onClick={() => gotoPage(pageCount - 1)} disabled={!canNextPage}>
          {">>"}
        </button>{" "}
        <span>
          Page{" "}
          <strong>
            {pageIndex + 1} of {pageOptions.length}
          </strong>{" "}
        </span>
        <span>
          | Go to page:{" "}
          <input
            type="number"
            defaultValue={pageIndex + 1}
            onChange={e => {
              const page = e.target.value ? Number(e.target.value) - 1 : 0;
              gotoPage(page);
            }}
            style={{ width: "100px" }}
          />
        </span>{" "}
        <select
          value={pageSize}
          onChange={e => {
            setPageSize(Number(e.target.value));
          }}
        >
          {[10, 20, 30, 40, 50].map(pageSize => (
            <option key={pageSize} value={pageSize}>
              Show {pageSize}
            </option>
          ))}
        </select>
      </div>
    </>
  );
};

export default function App() {
  return (
    <div className="App">
      <Table columns={columns} data={data} />
    </div>
  );
}
```

在上面的代码中，我们生成了 100 个带有随机姓名和页面的数组条目。标题与上面的简单表格示例相同。

在`Table`组件中，我们有:

```
const {
  getTableProps,
  getTableBodyProps,
  headerGroups,
  prepareRow,
  page,
  canPreviousPage,
  canNextPage,
  pageOptions,
  pageCount,
  gotoPage,
  nextPage,
  previousPage,
  setPageSize,
  state: { pageIndex, pageSize }
} = useTable(
  {
    columns,
    data,
    initialState: { pageIndex: 0 }
  },
  usePagination
);
```

这样，我们可以获得分页所需的各种数据，比如`pageSize`改变每页显示的项目数。

`canPreviousPage`和`canNextPage`分别告诉我们是否可以移动到上一页或下一页。`pageCount`有总页数，`gotoPage`是一个让我们跳到给定页码的功能。`previousPage`和`nextPage`也是让我们导航到给定页面的功能。

它们在下面的`div`中用于在页面间导航:

```
<div>
  <button onClick={() => gotoPage(0)} disabled={!canPreviousPage}>
    {"<<"}
  </button>{" "}
  <button onClick={() => previousPage()} disabled={!canPreviousPage}>
    {"<"}
  </button>{" "}
  <button onClick={() => nextPage()} disabled={!canNextPage}>
    {">"}
  </button>{" "}
  <button onClick={() => gotoPage(pageCount - 1)} disabled={!canNextPage}>
    {">>"}
  </button>{" "}
  <span>
    Page{" "}
    <strong>
      {pageIndex + 1} of {pageOptions.length}
    </strong>{" "}
  </span>
  <span>
    | Go to page:{" "}
    <input
      type="number"
      defaultValue={pageIndex + 1}
      onChange={e => {
        const page = e.target.value ? Number(e.target.value) - 1 : 0;
        gotoPage(page);
      }}
      style={{ width: "100px" }}
    />
  </span>{" "}
  <select
    value={pageSize}
    onChange={e => {
      setPageSize(Number(e.target.value));
    }}
  >
    {[10, 20, 30, 40, 50].map(pageSize => (
      <option key={pageSize} value={pageSize}>
        Show {pageSize}
      </option>
    ))}
  </select>
</div>
```

然后我们得到一个表，它的列与上面的例子相同，但是添加了分页按钮。我们也可以使用下拉菜单来改变每个页面的大小。

## 将 React 表 v7 与材质 UI 集成

`react-table`包与材料 UI 集成，让我们创建一个遵循材料设计规范的表格。

要安装材料 UI，我们运行:

```
npm install @material-ui/core
```

然后我们可以使用 Material UI 的表格组件和`react-table`来创建表格，如下所示:

```
import React from "react";
import { useTable } from "react-table";
import MaUTable from "@material-ui/core/Table";
import TableBody from "@material-ui/core/TableBody";
import TableCell from "@material-ui/core/TableCell";
import TableHead from "@material-ui/core/TableHead";
import TableRow from "@material-ui/core/TableRow";

const data = [
  { firstName: "jane", lastName: "doe", age: 20 },
  { firstName: "john", lastName: "smith", age: 21 }
];

const columns = [
  {
    Header: "Name",
    columns: [
      {
        Header: "First Name",
        accessor: "firstName"
      },
      {
        Header: "Last Name",
        accessor: "lastName"
      }
    ]
  },
  {
    Header: "Other Info",
    columns: [
      {
        Header: "Age",
        accessor: "age"
      }
    ]
  }
];

const Table = ({ columns, data }) => {
  const { getTableProps, headerGroups, rows, prepareRow } = useTable({
    columns,
    data
  });

  return (
    <MaUTable {...getTableProps()}>
      <TableHead>
        {headerGroups.map(headerGroup => (
          <TableRow {...headerGroup.getHeaderGroupProps()}>
            {headerGroup.headers.map(column => (
              <TableCell {...column.getHeaderProps()}>
                {column.render("Header")}
              </TableCell>
            ))}
          </TableRow>
        ))}
      </TableHead>
      <TableBody>
        {rows.map((row, i) => {
          prepareRow(row);
          return (
            <TableRow {...row.getRowProps()}>
              {row.cells.map(cell => {
                return (
                  <TableCell {...cell.getCellProps()}>
                    {cell.render("Cell")}
                  </TableCell>
                );
              })}
            </TableRow>
          );
        })}
      </TableBody>
    </MaUTable>
  );
};

export default function App() {
  return (
    <div className="App">
      <Table columns={columns} data={data} />
    </div>
  );
}
```

在上面的代码中，我们使用了材质 UI 组件来呈现表格，但是数据是由`react-table`填充的。我们调用了在简单表格示例中使用的相同方法来用数据填充行和列。

因此，我们得到了与简单表格示例相同的数据和列，但是它遵循了材料设计，而不是没有样式。

## 不要忘记表格呈现对性能的影响。尝试使用 [LogRocket](https://www2.logrocket.com/react-performance-monitoring) 来监控 React 应用。

大型表会占用用户的 CPU。使用 LogRocket，您可以看到表和组件对所有用户的影响。如果您对监视和跟踪 Redux 状态、自动显示 JavaScript 错误、跟踪缓慢的网络请求和组件加载时间感兴趣，请尝试 LogRocket 。【T2![](img/f300c244a1a1cf916df8b4cb02bec6c6.png)[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://www2.logrocket.com/react-performance-monitoring)

LogRocket 就像是网络应用程序的 DVR，记录 React 应用程序上发生的一切。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。LogRocket 还可以监控应用程序的性能，报告客户端 CPU 负载、客户端内存使用等指标。

LogRocket Redux 中间件包为您的用户会话增加了一层额外的可见性。LogRocket 记录 Redux 存储中的所有操作和状态。

现代化您调试 React 应用的方式— [开始免费监控](https://www2.logrocket.com/react-performance-monitoring)。

## 结论

正如我们所看到的，react-table 能够创建具有许多功能的表，而不必自己从头开始创建。

它为我们提供了一个基于钩子的 API 来创建表，这很重要，因为一些开发人员现在想转而使用带有钩子的函数组件。

还有很多例子可以展示 react-table 在官方 GitHub repo 上可以做什么。有些例子是从他们官网上的例子简化而来的。