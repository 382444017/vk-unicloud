# 10、remote-select 远程下拉

## 万能表单使用方式

### 下拉选择方式

**应用场景**：直接加载所有远程待选项（数据量不大时使用。）

通过设置参数 showAll:true 可以直接将所有远程待选项直接加载完。（最多支持1000条数据）

```js
{
  key: "category_ids1", title: "远程select（单选）", type: "remote-select", placeholder: "请选择分类",
  action: "admin/system/user/sys/getList",
  props: { list: "rows", value: "_id", label: "nickname" },
  showAll: true,
  actionData: {
    pageSize: 1000
  }
},
```

### 远程搜索方式

**应用场景**：数据量很大，不方便一次性全部加载时使用。

```js
{
  key: "user_id", title: "用户选择器", type: "remote-select", placeholder: "请输入用户账号/昵称",
  action: "admin/select/kh/user",
},
```

### 远程搜索带参数方式1

```js
{
  key: "user_id", title: "用户选择器", type: "remote-select", placeholder: "请输入用户账号/昵称",
  action: "admin/select/kh/user",
  actionData: {
    a: 1
  }
},
```

### 远程搜索带参数方式2

```js
{
  key: "user_id", title: "用户选择器", type: "remote-select", placeholder: "请输入用户账号/昵称",
  action: "admin/select/kh/user",
  actionData: () => {
    return {
      a: that.form1.data.a
    }
  }
},
```

### 数据预处理

```js
{
  key: "user_id", title: "用户选择器", type: "remote-select", placeholder: "请输入用户账号/昵称",
  action: "你的云函数",
  props: { list: "rows", value: "_id", label: "name" },
  dataPreprocess: (list) => {
    list.map((item, index) => {
      item.name = `${item.name}(${item._id})`
    });
    return list;
  }
},
```

### 多个下拉远程联动

```js
{
  key: "select1", title: "远程select（单选）", type: "remote-select", placeholder: "请选择分类",
  action: "admin/system/user/sys/getList",
  props: { list: "rows", value: "_id", label: "nickname" },
  showAll: true,
  actionData: {
    pageSize: 1000
  },
  watch: ({ value, formData, column, index, option, $set }) => {
    // 此处演示根据选择的值动态改变select2的actionData的值
    let item = vk.pubfn.getListItem(this.formData1.columns, "key", "select2");
    item.actionData.selectValue1 = value;
  }
},
{
  key: "select2", title: "远程select（单选）", type: "remote-select", placeholder: "请选择分类",
  action: "admin/system/user/sys/getList",
  props: { list: "rows", value: "_id", label: "nickname" },
  showAll: true,
  actionData: {
    pageSize: 1000,
    selectValue1: "", // 关联select1选择的值
  }
},
```

### 选项右侧显示描述desc

**要点**：设置属性 showDesc: true，同时数据源返回以下格式

```js
[
  { value: 1, label: "选项一", desc: "选项一的描述" },
  { value: 2, label: "选项二", desc: "选项二的描述" }
]
```

```js
{
  key: "category_ids1", title: "远程select（单选）", type: "remote-select", placeholder: "请选择分类",
  action: "admin/system/user/sys/getList",
  props: { list: "rows", value: "_id", label: "nickname", desc: "desc" },
  showAll: true,
  showDesc: true,
  actionData: {
    pageSize: 1000
  }
},
```

### 分组显示

**要点**：设置属性 group:true，同时数据源返回以下格式

```js
[
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
```

```js
{
  key: "category_ids1", title: "远程select（单选）", type: "remote-select", placeholder: "请选择分类",
  action: "admin/system/user/sys/getList",
  props: { list: "rows", value: "_id", label: "nickname", desc: "desc" },
  showAll: true,
  showDesc: true,
  actionData: {
    pageSize: 1000
  }
},
```

### 可以动态创建选项

**要点**：设置属性 allowCreate: true 和 filterable: true

```js
{
  key: "category_ids1", title: "远程select（单选）", type: "remote-select", placeholder: "请选择分类",
  action: "admin/system/user/sys/getList",
  props: { list: "rows", value: "_id", label: "nickname" },
  showAll: true,
  allowCreate: true,
  filterable: true,
  actionData: {
    pageSize: 1000
  }
},
```

### 默认选中第几项（仅单选时生效）

**要点**：设置属性 defaultIndex: 0

```js
{
  key: "category_ids1", title: "远程select（单选）", type: "remote-select", placeholder: "请选择分类",
  action: "admin/system/user/sys/getList",
  props: { list: "rows", value: "_id", label: "nickname" },
  showAll: true,
  defaultIndex: 0,
  actionData: {
    pageSize: 1000
  }
},
```

## API

### 公共属性

[点击查看『公共属性』](opens new window)

### 组件属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| data | 静态模式数据源 | Array | - | - |
| action | 支持： 1、vk框架下的云函数地址 2、http请求地址 3、自定义function请求模式 | String、Function | 无 | - |
| actionData | 动态模式 - 远程请求的云函数时的额外参数 | Object、Function | - | - |
| props | 数据源的属性匹配规则 | Object | { list:'list', value:'value', label:'label', desc: 'desc' } | - |
| dataPreprocess | 动态模式 - 云函数返回的数据进行预处理 | function(list) | - | - |
| showAll | 是否一开始就全部加载 | Boolean | false | true |
| showRefresh | 当showAll为true时，是否显示[刷新选项]按钮 | Boolean | true | false |
| multiple | 是否允许多选 | Boolean | false | true |
| limit | 最多可选数量 | Number | - | - |
| loadingText | 远程加载时显示的文字 | String | 加载中 | - |
| noMatchText | 搜索条件无匹配时显示的文字 | String | 无匹配数据 | - |
| noDataText | 选项为空时显示的文字 | String | 无数据 | - |
| clearable | 是否可以清空选项 | Boolean | true | false |
| maxOptions | 最大待选项 | Number | - | - |
| isRequest | 是否是http请求模式 | Boolean | false | true |
| requestHeader | http请求头 | Object | - | - |
| requestMethod | http请求method | String | - | - |
| onChange | function(val, formData, column, index, option) | Function | - | - |
| defaultIndex | 默认选择第几个，仅在万能表单的单选模式下，且showAll为true时生效 | Number | - | - |
| showDesc（新增于1.20.23） | 选项右侧是否显示描述 | Boolean | false | true |
| group（新增于1.20.23） | 是否需要分组 | Boolean | false | true |
| collapseTags | 多选时是否将选中值按文字的形式展示 | Boolean | false | true |
| filterable | 是否可搜索 | Boolean | false | true |
| allowCreate | 是否允许用户创建新条目，需配合 filterable 使用 | Boolean | false | true |
| popperClass | Select 下拉框的类名 | String | - | - |
| reserveKeyword | 多选且可搜索时，是否在选中一个选项后保留当前的搜索关键词 | Boolean | false | true |
| popperAppendToBody | 是否将弹出框插入至 body 元素。在弹出框的定位出现问题时，可将该属性设置为 false | Boolean | true | false |
| loadingText | 远程加载时显示的文字 | String | 加载中 | - |
| noMatchText | 搜索条件无匹配时显示的文字 | String | 无匹配数据 | - |
| noDataText | 选项为空时显示的文字 | String | 无数据 | - |

### onChange 使用示例

```js
{
  key: "user_id", title: "用户选择器", type: "remote-select", placeholder: "请输入用户账号/昵称",
  action: "admin/select/kh/user",
  onChange: (val, formData, column, index, option) => {
    console.log(1, val, formData, column, index, option);
  }
},
```

**推荐使用 watch 代替 onChange** [传送门 - watch](opens new window)

### 参数说明

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| val | 表单绑定的值 | Any | - | - |
| formData | 整个表单数据源 | Object | - | - |
| column | 该组件属性 | Object | - | - |
| index | 组件的索引下标 | Number | - | - |
| option | 其他参数，如选项的对象值等 | Any | - | - |

### 自定义function请求模式

vk-unicloud-admin-ui 的npm依赖版本需 >= 1.17.17

此方式同样支持http，且更自由化，比如可以在发起请求前处理请求参数，在请求成功后，处理返回参数等等。

**优势**：更自由化、基本可以满足所有需求场景

**劣势**：代码量较多

#### 自定义function-http请求模式示例

```js
{ 
  key:"user_id", title:"选择用户", type:"remote-select",
  action: (obj={})=>{
    let {
      data, // 请求数据
      success, // 成功回调
      fail, // 失败回调
      complete, // 成功回调
    } = obj;
    // 发起http请求
    vk.request({
      url: `https://www.xxx.com/api/list`,
      method: "POST",
      header: {
        "content-type": "application/json; charset=utf-8",
      },
      data: data,
      success: (res) => {
        if (typeof success === "function") {
          let list = res.list.map((item, index) => {
            return {
              value: item._id,
              label: item.nickname
            }
          });
          success({
            list: list
          });
        }
      },
      fail: (res) => {
        if (typeof fail === "function") fail(res);
      },
      complete: (res) => {
        if (typeof complete === "function") complete(res);
      }
    });
  }
},
```

#### 自定义function-云函数请求模式示例

```js
{ 
  key:"user_id", title:"选择用户", type:"remote-select",
  action: (obj={})=>{
    let {
      data, // 请求数据
      success, // 成功回调
      fail, // 失败回调
      complete, // 成功回调
    } = obj;
    // 发起http请求
    vk.callFunction({
      url: `云函数或云对象路径`,
      data: data,
      success: (res) => {
        if (typeof success === "function") {
          let list = res.list.map((item, index) => {
            return {
              value: item._id,
              label: item.nickname
            }
          });
          success({
            list: list
          });
        }
      },
      fail: (res) => {
        if (typeof fail === "function") fail(res);
      },
      complete: (res) => {
        if (typeof complete === "function") complete(res);
      }
    });
  }
},
```

## 万能表格使用方式

暂无，请通过连表查询方式获取数据。

## template 使用方式

### 下拉选择方式

**应用场景**：数据量不大时使用。

```html
<vk-data-input-remote-select
  v-model="form1.category_ids"
  placeholder="请选择分类"
  action="admin/system/user/sys/getList"
  :props="{ list: 'rows', value: '_id', label: 'nickname' }"
  width="300px"
  show-all
  multiple
  :limit="3"
></vk-data-input-remote-select>
```

### 远程搜索方式

**应用场景**：数据量很大，不方便一次性全部加载时使用。

```html
<vk-data-input-remote-select
  v-model="form1.user_id"
  placeholder="请输入用户名"
  action="admin/select/kh/user"
  width="300px"
></vk-data-input-remote-select>