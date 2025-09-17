# 12、table-select 通过表格选择

## 万能表单使用方式

### 不带请求参数

```js
{
  key: "role", title: "通过表格选择(单选)", type: "table-select", placeholder: "请选择角色",
  action: "admin/system/role/sys/getList",
  columns: [
    { key: "role_name", title: "角色昵称", type: "text", nameKey: true },
    { key: "role_id", title: "角色标识", type: "text", idKey: true }, // idKey:true 代表此字段为主键字段，若设置show:["none"],则可以在表格中隐藏该字段的显示
    { key: "comment", title: "备注", type: "text" }
  ],
  queryColumns: [
    { key: "role_name", title: "角色昵称", type: "text", width: 150, mode: "%%" },
    { key: "role_id", title: "角色标识", type: "text", width: 150, mode: "%%" }
  ]
},
```

### 带请求参数

```js
{
  key: "role", title: "通过表格选择(单选)", type: "table-select", placeholder: "请选择角色",
  action: "admin/system/role/sys/getList",
  columns: [
    { key: "role_name", title: "角色昵称", type: "text", nameKey: true },
    { key: "role_id", title: "角色标识", type: "text", idKey: true }, // idKey:true 代表此字段为主键字段，若设置show:["none"],则可以在表格中隐藏该字段的显示
    { key: "comment", title: "备注", type: "text" }
  ],
  queryColumns: [
    { key: "role_name", title: "角色昵称", type: "text", width: 150, mode: "%%" },
    { key: "role_id", title: "角色标识", type: "text", width: 150, mode: "%%" },
    { key: "a", title: "a字段", type: "text", width: 150, mode: "=", show:["none"] }, // 只有这里设置了 a 字段, 则下面的formData中的 a 参数才会生效
  ],
  formData: () => {
    return {
      a: "1", // 支持通过 this 或 that 获取到其他参数
    }
  }
},
```

## API

### 公共属性

[点击查看『公共属性』](opens new window)

### 组件属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| action | 支持： 1、vk框架下的云函数地址 2、http请求地址 3、自定义function请求模式 | String、Function | 无 | - |
| columns | 表格字段显示规则 | Array | - | - |
| queryColumns | 搜索栏字段显示规则 | Array | - | - |
| multiple | 是否允许多选 | Boolean | false | true |
| multipleLimit | 最多可选择的数量 | Number | - | - |
| formData | 默认搜索字段的值 | Object、Function | - | - |
| showCancel | 是否显示取消按钮 | Boolean | true | false |
| cancelText | 取消按钮的文字 | String | 关闭 | - |
| submitText | 确定按钮的文字 | String | 确定 | - |
| dialogWidth | 弹窗的窗体宽度（单位px） | Number | - | - |
| pageSize | 表格分页每页显示数量 | Number | 5 | 5、10、20、50、100、500 |
| valueFields | 用于控制value的值由哪些字段组成 查看valueFields | Array | - | - |
| rowHeight | 表格行高，单位为px（特殊情况下，可能需要手动设置行高） | Number | - | - |
| leftFixed | 序号、多选框是否固定在左侧 | Boolean | true | false |
| rightFixed | 操作按钮是否固定在右侧 | Boolean | true | false |
| isRequest | 是否是http请求模式 查看http请求模式 | Boolean | false | true |
| requestHeader | http请求头 | Object | - | - |
| requestMethod | http请求method | String | - | - |
| props | 动态模式 - 渲染数据的配置选项 查看http请求模式 | Object | - | - |
| onChange | function(val, formData, column, index, option) | Function | - | - |

### onChange 使用示例

```js
{
  key: "role", title: "通过表格选择(单选)", type: "table-select", placeholder: "请选择角色",
  action: "admin/system/role/sys/getList",
  columns: [
    { key: "role_name", title: "角色昵称", type: "text", nameKey: true },
    { key: "role_id", title: "角色标识", type: "text", idKey: true }, // idKey:true 代表此字段为主键字段，若设置show:["none"],则可以在表格中隐藏该字段的显示
    { key: "comment", title: "备注", type: "text" }
  ],
  queryColumns: [
    { key: "role_name", title: "角色昵称", type: "text", width: 150, mode: "%%" },
    { key: "role_id", title: "角色标识", type: "text", width: 150, mode: "%%" }
  ],
  onChange: (val, formData, column, index, option) => {
    console.log(1, val, formData, column, index, option);
  }
},
```

**推荐使用 watch 代替 onChange** [传送门 - watch](opens new window)

### valueFields

不设置 valueFields 时 表单绑定的值为 字符串数组形式

```js
["001","002"]
```

设置 valueFields 时 表单绑定的值为对象数组形式

如 valueFields:["_id","nickname","mobile"] 表单绑定的值为

```js
[
  {"_id":"001","nickname":"昵称1","mobile":"手机号1"}，
  {"_id":"002","nickname":"昵称2","mobile":"手机号2"}
]
```

### http请求模式

#### props 对象属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| rows | 表格数据源的键名 | String | rows | - |
| total | 总记录条数的键名 | String | total | - |
| pageIndex | 查询时当前第几页的键名 | String | pageIndex | - |
| pageSize | 查询时每页显示几条的键名 | String | pageSize | - |
| formData | 查询表单的数据源的键名（填/代表参数在根节点上） | String | formData | - |

#### 万能表单使用方式

```js
{
  key: "user_id", title: "选择用户", type: "table-select", placeholder: "请选择用户",
  action: "https://www.xxx.com/xxx/xxx",
  isRequest: true,
  requestHeader: {
    "Content-Type": "application/json; charset=utf-8"
  },
  props: { rows: 'rows', total: 'total', pageIndex: 'pageIndex', pageSize: 'pageSize', formData: 'formData' },
  columns: [
    { key: "id", title: "用户标识", type: "text", idKey: true, show: ["none"] }, // idKey:true 代表此字段为主键字段，若设置show:["none"],则可以在表格中隐藏该字段的显示
    { key: "avatar", title: "头像", type: "image", width: 80 },
    { key: "nickname", title: "用户昵称", type: "text", width: 260, align: "left", nameKey: true },
    { key: "mobile", title: "手机号", type: "text", width: 140 },
  ],
  queryColumns: [
    { key: "nickname", title: "用户昵称", type: "text", width: 150, mode: "%%" },
    { key: "mobile", title: "手机号", type: "text", width: 150, mode: "%%" }
  ],
  formData: () => {
    return {
      nickname: that.form1.data.nickname,
      mobile: that.form1.data.mobile,
    }
  }
},
```

### 自定义function请求模式

vk-unicloud-admin-ui 的npm依赖版本需 >= 1.17.17

此方式同样支持http，且更自由化，比如可以在发起请求前处理请求参数，在请求成功后，处理返回参数等等。

**优势**：更自由化、基本可以满足所有需求场景

**劣势**：代码量较多

#### 自定义function-http请求模式示例

```js
{
  key: "user_id", title: "选择用户", type: "table-select", placeholder: "请选择用户",
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
          success({
            rows: res.rows, // 列表数据
            total: res.total, // 总记录数（如果没有总记录数，则需要返回getCount和hasMore）
            getCount: res.getCount, // 是否执行了count请求，当total的值不是总记录数时需要返回 getCount: false
            hasMore: res.hasMore, // 是否还有下一页，当total的值不是总记录数时需要返回是否还有下一页
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
  },
  columns: [
    { key: "id", title: "用户标识", type: "text", idKey: true, show: ["none"] }, // idKey:true 代表此字段为主键字段，若设置show:["none"],则可以在表格中隐藏该字段的显示
    { key: "avatar", title: "头像", type: "image", width: 80 },
    { key: "nickname", title: "用户昵称", type: "text", width: 260, align: "left", nameKey: true },
    { key: "mobile", title: "手机号", type: "text", width: 140 },
  ],
  queryColumns: [
    { key: "nickname", title: "用户昵称", type: "text", width: 150, mode: "%%" },
    { key: "mobile", title: "手机号", type: "text", width: 150, mode: "%%" }
  ],
  formData: {
    nickname: "",
    mobile: ""
  }
},
```

#### 自定义function-云函数请求模式示例

```js
{
  key: "user_id", title: "选择用户", type: "table-select", placeholder: "请选择用户",
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
          success({
            rows: res.rows, // 列表数据
            total: res.total, // 总记录数（如果没有总记录数，则需要返回getCount和hasMore）
            getCount: res.getCount, // 是否执行了count请求，当total的值不是总记录数时需要返回 getCount: false
            hasMore: res.hasMore, // 是否还有下一页，当total的值不是总记录数时需要返回是否还有下一页
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
  },
  columns: [
    { key: "id", title: "用户标识", type: "text", idKey: true, show: ["none"] }, // idKey:true 代表此字段为主键字段，若设置show:["none"],则可以在表格中隐藏该字段的显示
    { key: "avatar", title: "头像", type: "image", width: 80 },
    { key: "nickname", title: "用户昵称", type: "text", width: 260, align: "left", nameKey: true },
    { key: "mobile", title: "手机号", type: "text", width: 140 },
  ],
  queryColumns: [
    { key: "nickname", title: "用户昵称", type: "text", width: 150, mode: "%%" },
    { key: "mobile", title: "手机号", type: "text", width: 150, mode: "%%" }
  ],
  formData: {
    nickname: "",
    mobile: ""
  }
},
```

### columns 参数详情

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| key | 字段名 | String | - | - |
| title | 字段显示的名称 | String | - | - |
| type | 字段渲染的组件类型 | String | - | - |
| nameKey | 是否将此字段的值定义为lable（类似单选框的lable和value的关系） | Boolean | false | true |
| idKey | 是否将此字段的值定义为value（类似单选框的lable和value的关系） | Boolean | false | true |
| summaryKey | 是否将此字段的值定义为求和字段 | Boolean | false | true |
| 其他 | 其他参数参考万能表格（和万能表格参数一致） | - | - | - |

**注意**：
- idKey 和 nameKey 只能各设置1个。
- 设置了 idKey:true 的字段，必须在列表中非空唯一（如主键_id），默认 idKey 为 _id
- 设置了 nameKey:true 的字段，用于展示在已选择的列表中（如用户昵称字段）默认 nameKey 为 name

#### summaryKey 求和

```js
{
  key: "role", title: "通过表格选择(单选)", type: "table-select", placeholder: "请选择",
  action: "云函数请求地址",
  multiple: true,
  columns: [
    { key: "_id", title: "id", type: "text", width: 260, idKey: true },
    { key: "name", title: "昵称", type: "text", width: 260, nameKey: true },
    { key: "money", title: "金额", type: "money", width: 100, sortable: "custom", summaryKey: true, summaryUnit: "元" }, // summaryKey开启该字段为求和字段， summaryUnit：单位
  ],
  queryColumns: [
    { key: "name", title: "昵称", type: "text", width: 150, mode: "%%" },
    { key: "_id", title: "标识", type: "text", width: 150, mode: "%%" }
  ]
},
```

### queryColumns 参数详情

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| key | 字段名 | String | - | - |
| title | 字段显示的名称 | String | - | - |
| type | 字段渲染的组件类型 | String | - | - |
| mode | 查询匹配方式 | String | = | 见下方 |
| 其他 | 其他参数参考万能表单（和万能表单参数大部分一致） | - | - | - |

#### queryColumns 中 mode 参数详情

| 值 | 说明 |
|----|------|
| = | 完全匹配 |
| != | 不等于 |
| %% | 模糊匹配 |
| %* | 以xxx开头 |
| *% | 以xxx结尾 |
| > | 大于 |
| >= | 大于等于 |
| < | 小于 |
| <= | 小于等于 |
| in | 在数组里 |
| nin | 不在数组里 |
| [] | 范围 arr[0] <= x <= arr[1] |
| [) | 范围 arr[0] <= x < arr[1] |
| (] | 范围 arr[0] < x <= arr[1] |
| () | 范围 arr[0] < x < arr[1] |

## 万能表格使用方式

无

## template 使用方式

```html
<vk-data-input-table-select
  v-model="role"
  action="admin/system/role/sys/getList"
  placeholder="请选择"
  :columns='[
    { key:"role_name", title:"角色昵称", type:"text", nameKey:true },
    { key:"role_id", title:"角色标识", type:"text", idKey:true },
    { key:"comment", title:"备注", type:"text" }
  ]'
></vk-data-input-table-select>