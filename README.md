# antd-data-table

A component that combines antd's Table and Form to do the search, display, and operating jobs for data.

## Feature

Free from:

- Handling pagination
- Handling table row selection
- Writing search field form item components
- Writing row actions components

Just focuse on:

- Doing the data fetching request and return the data
- Rendering a specific data field if needed
- Writing plugin to operate one or many data object(s)

## Install

```bash
$ yarn add antd-data-table --save
```

## Simplest data table

[Demo]()

```tsx
import { DataTable } from 'antd-data-table'

const searchFields: SearchField[] = [
  {
    label: 'ID',
    name: 'id',
    type: 'input',
    payload: {
      props: {
        placeholder: 'placeholder'
      }
    }
  },
  {
    label: 'Select',
    name: 'select',
    type: 'select',
    payload: {
      options: [
        { key: '1', label: 'one', value: '1' },
        { key: '2', label: 'two', value: '2' },
        { key: '3', label: 'three', value: '3' }
      ]
    }
  }
]

const columns: TableColumnConfig<any>[] = [
  {
    key: 'id',
    title: 'ID',
    dataIndex: 'id'
  }, {
    key: 'title',
    title: 'Title',
    dataIndex: 'title'
  }
]

const onSearch = async ({ page, pageSize, values }) => {
  const res = await axios.get('http://jsonplaceholder.typicode.com/posts', {
    params: {
      _page: page,
      _limit: pageSize,
      ...values
    }
  })
  return {
    dataSource: res.data,
    total: Number(res.headers['x-total-count'])
  }
}
render(
  <DataTable
    rowKey={record => record.id}
    searchFields={searchFields}
    initialColumns={columns}
    onSearch={onSearch}
  />
, mountNode)
```

## Guide

### Collapsable search field

Sometimes there are many search fields, you could set a `maxVisibleFieldCount` to automatically have a collapsable form:

[Demo]()

```diff
import { DataTable } from 'antd-data-table'

render(
  <DataTable
    rowKey={record => record.id}
    searchFields={searchFields}
    initialColumns={columns}
    onSearch={onSearch}
+   maxVisibleFieldCount={4}
  />
, mountNode)
```

### Row actions

We usually need to write some action buttons for operating a specific record. `antd-data-table` made it super easy:

[Demo]()

```tsx
const actions: RowAction[] = [
  {
    label: 'Edit',
    action (record) {
      action('onClick edit')(record)
    }
  },
  {
    label: 'More',
    children: [
      {
        label: 'Remove',
        action (record) {
          action('onClick remove')(record)
        }
      },
      {
        label: 'Open',
        action (record) {
          action('onClick open')(record)
        }
      }
    ]
  }
]

render(
  <DataTable
    rowKey={record => record.id}
    searchFields={searchFields}
    initialColumns={columns}
    onSearch={onSearch}
    actions={actions}
  />
, mountNode)
```

### Plugins

Plugins are for operating multiple records. Every plugin will render a component at the top of table.

[Demo]()

Let's write a simplest plugin: A button that show current selected rows' ids:

```tsx
const ShowIdsBtn = ({ selectedRows }) => {
  const showIds = () => {
    message.info(selectedRows.map(row => row.id).join(','))
  }

  return <Button onClick={showIds}>Show Ids</Button>
}

const plugins = [
  renderer (selectedRowKeys, selectedRows) {
    return <ShowIdsBtn selectedRows={selectedRows} />
  }
]

render (
  <DataTable
    rowKey={record => record.id}
    searchFields={searchFields}
    plugins={plugins}
    initialColumns={columns}
    onSearch={onSearch}
  />
, mountNode)
```

## Props

### name?: string

Unique table name.

### `rowKey`: (record) => string

The `key` value of a row. 

### `searchFields: SearchField[]`

SearchField is an object that contains:

- **label: string** Pass to `<Form.Item>`'s `label` property.
- **name: string** Pass to `getFieldDecorator` as the decorator name.
- **type?: RenderType** antd-data-table comes with some common form item type. Such as `input`, `select`.
- **renderer?: (payload?: object) => React.ReactNode** When the form item types are not statisfied, your could write your own renderer. the `ReactNode` that returned will be wrapped by `getFieldDecorator`.
- **validationRule?: ValidateionRule[]** antd validation rules. Pass to `getFieldDecorator(name, { rules })`.
- **payload?: { props: any, [key: string]: any }** Some params that pass to the renderer.

#### out of the box render type

- input
- select

### `initialColumns: TableColumnConfig[]`

antd's TableColumnConfig. See more at https://ant.design/components/form/

### `onSearch<T> (info: SearchInfo): Promise<SearchResponse<T>>`

`onSearch` property need a function that return a Promise, which resolves an object that contains `total` and `dataSource`. This function receive a `SearchInfo`:

```ts
type SearchInfo = {
  /** values from `getFieldsValue()` */
  values: any,
  /** current page */
  page: number,
  /** page size */
  pageSize: number
}
```

### `title?: React.ReactNode`

### `searchBtnText?: string`

### `clearBtnText?: string`

### `listSelectionBtnText?: string`

### `onError? (err): void`

Error handler that trigger when onSearch throw error.

### `onValidateFailed?: (err: ValidateError) => void`

Form validation failed handler

### `pageSize?: number`

default is 10

### `plugins?: Plugin[]`

### `rowActions?: RowAction[]`

### enableListSelection?: boolean

If `true`, a list selection button will display on table title.

*Be sure to pass the `name` props if it is enable.*

# License

MIT License
