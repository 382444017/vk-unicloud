# 9、select 下拉选择

## 万能表单使用方式

### 静态数据方式1

**应用场景**：选项数据为静态数据的情况。

```js
{
  key: "select1", title: "select类型", type: "select", filterable: true, clearable: true,
  data: [
    { value: 1, label: "选项一" },
    { value: 2, label: "选项二" }
  ]
},
```

### 静态数据方式2

**应用场景**：选项数据需要通过函数计算

```js
{
  key: "select1", title: "select类型", type: "select", filterable: true, clearable: true,
  data: () => {
    let list = that.list;
    return list;
  }
},
```

### 分组用法

**要点**：设置属性 group: true，同时数据源有属性 children

```js
{
  key: "select3", title: "select类型3", type: "select", group: true, filterable: true, clearable: true,
  data: [
    {
      label: "分组1",
      children: [
        { value: 1, label: "选项一" },
        { value: 2, label: "选项二" }
      ]
    },
    {
      label: "分组2",
      children: [
        { value: 3, label: "选项三" },
        { value: 4, label: "选项四" }
      ]
    }
  ]
},
```

### 选项右侧显示描述desc

**要点**：设置属性 showDesc: true，同时数据源有属性 desc

```js
{
  key: "select1", title: "select类型", type: "select", filterable: true, clearable: true,
  showDesc: true,
  data: [
    { value: 1, label: "选项一", desc: "选项一的描述" },
    { value: 2, label: "选项二", desc: "选项二的描述" }
  ]
},
```

### 远程数据用法

请直接查看 remote-select 远程下拉组件 [点击前往](opens new window)

## API

### 公共属性

[点击查看『公共属性』](opens new window)

### 组件属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| data | 数据源 | Array、Function | - | - |
| props | 数据源的属性匹配规则 | Object | { value:'value', label:'label', children:'children', desc: 'desc' } | - |
| multiple | 是否允许多选 | Boolean | false | true |
| multipleLimit | 最多可选数量 | Number | - | - |
| group | 是否需要分组 | Boolean | false | true |
| clearable | 是否可以清空选项 | Boolean | true | false |
| onChange | function(val, formData, column, index, option) | Function | - | - |
| defaultIndex | 默认选择第几个，仅在万能表单的单选模式下生效 | Number | - | - |
| showDesc（新增于1.20.23） | 选项右侧是否显示描述 | Boolean | false | true |
| collapseTags | 多选时是否将选中值按文字的形式展示 | Boolean | false | true |
| filterable | 是否可搜索 | Boolean | false | true |
| allowCreate | 是否允许用户创建新条目，需配合 filterable 使用 | Boolean | false | true |
| filterMethod | 自定义搜索方法 | function | - | - |
| remote | 是否为远程搜索 | Boolean | false | true |
| remoteMethod | 远程搜索方法 | function | - | - |
| loading | 是否正在从远程获取数据 | Boolean | false | true |
| loadingText | 远程加载时显示的文字 | String | 加载中 | - |
| noMatchText | 搜索条件无匹配时显示的文字 | String | 无匹配数据 | - |
| noDataText | 选项为空时显示的文字 | String | 无数据 | - |
| popperClass | Select 下拉框的类名 | String | - | - |
| reserveKeyword | 多选且可搜索时，是否在选中一个选项后保留当前的搜索关键词 | Boolean | false | true |
| defaultFirstOption | 在输入框按下回车，选择第一个匹配项。需配合 filterable 或 remote 使用 | Boolean | false | true |
| popperAppendToBody | 是否将弹出框插入至 body 元素。在弹出框的定位出现问题时，可将该属性设置为 false | Boolean | true | false |
| automaticDropdown | 对于不可搜索的 Select，是否在输入框获得焦点后自动弹出选项菜单 | Boolean | false | true |

### onChange 使用示例

```js
{
  key: "select1", title: "select类型", type: "select", filterable: true, clearable: true,
  data: [
    { value: 1, label: "选项一" },
    { value: 2, label: "选项二" }
  ],
  onChange: (val, formData, column, index, option) => {
    console.log(1, val, formData, column, index, option);
  }
},
```

**推荐使用 watch 代替 onChange** [传送门 - watch](opens new window)

## 万能表格使用方式

```js
{ 
  key: "gender", title: "性别", type: "select", width: 120, defaultValue: 0,
  data: [
    { value: 1, label: "男" },
    { value: 2, label: "女" },
    { value: 0, label: "保密" },
  ]
},
```

## template 使用方式

### 常规用法

```html
<vk-data-input-select
  v-model="form1.value1"
  :localdata='[
    { value:1, label:"选项一" },
    { value:2, label:"选项二" }
  ]'
  placeholder="请选择"
></vk-data-input-select>
```

### 分组用法

```html
<vk-data-input-select
  v-model="form1.value2"
  :group="true"
  :localdata='[
    {
      label: "分组1",
      children:[
        { value:1, label:"选项一" },
        { value:2, label:"选项二" }
      ]
    },
    {
      label: "分组2",
      children:[
        { value:3, label:"选项三" },
        { value:4, label:"选项四" }
      ]
    }
  ]'
  placeholder="请选择"
></vk-data-input-select>