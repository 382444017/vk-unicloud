# 万能表单组件

组件名：`vk-data-form`

核心思想：通过 JSON 配置渲染规则

## 基础用法

### 渲染输入框
```javascript
{ key: "nickname", title: "昵称", type: "text" }
```

### 渲染单选框组
```javascript
{
  key: "gender", 
  title: "性别", 
  type: "radio",
  data: [
    { value: 1, label: "男" },
    { value: 2, label: "女" }
  ]
}
```

> 提示：可以通过[表单可视化拖拽工具](https://example.com)直接生成 vk 框架代码

## 基础用法示例

### Vue 模板代码
```html
<vk-data-dialog
  v-model="form1.props.show"
  title="表单标题"
  width="600px"
  mode="form"
>
  <vk-data-form
    ref="form1"
    v-model="form1.data"
    :action="form1.props.action"
    :columns="form1.props.columns"
    :rules="form1.props.rules"
    :form-type="form1.props.formType"
    :loading.sync="form1.props.loading"
    :auto-close="true"
    label-width="140px"
    @success="onFormSuccess"
  ></vk-data-form>
</vk-data-dialog>
```

### JavaScript 代码
```javascript
export default {
  data() {
    return {
      form1: {
        data: {
          radio: 1,
          checkbox: [1, 2]
        },
        props: {
          action: "template/test/sys/test",
          columns: [
            { key: "text", title: "text类型字段", type: "text" },
            { key: "number", title: "number类型字段", type: "number" },
            { 
              key: "radio", 
              title: "radio类型字段", 
              type: "radio",
              data: [
                { value: 1, label: "选项一" },
                { value: 2, label: "选项二" },
              ]
            },
            { 
              key: "checkbox", 
              title: "checkbox类型字段", 
              type: "checkbox",
              data: [
                { value: 1, label: "选项一" },
                { value: 2, label: "选项二" },
              ]
            },
            { key: "switch", title: "switch类型字段", type: "switch" }
          ],
          rules: {
            text: [
              { required: true, message: "text不能为空", trigger: "change" },
            ]
          },
          formType: "",
          loading: false,
          show: false
        }
      }
    };
  }
}
```

## API

### 属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| v-model | 表单数据源 | Object | {} | - |
| rules | 表单验证规则 | Object | 无 | - |
| action | 表单提交地址 | String、Function | 无 | - |
| before-action | action请求前拦截器 | Function | 无 | - |
| is-request | 是否是http请求模式 | Boolean | false | true |
| form-type | 表单类型，用于复用表单 | String | 无 | - |
| columns | 字段规则 | Array | [] | - |
| loading | 表单是否在请求中 | Boolean | false | true |
| label-width | 左侧label宽度 | String,Number | "80px" | - |
| width | 表单宽度 | Number,String | 无 | - |
| footer-show | 底部按钮是否显示 | Boolean | true | false |
| footer-center | 底部按钮是否居中 | Boolean | true | false |
| show-cancel | 是否显示取消按钮 | Boolean | true | false |
| cancel-text | 取消按钮的文字 | String | 关 闭 | - |
| submit-text | 确定按钮的文字 | String | 确 定 | - |
| submit-disabled | 确定按钮是否禁用 | Boolean | false | true |
| auto-close | 表单请求成功后自动关闭 | Boolean | true | false |
| label-position | 对齐方式 | String | right | right left top |
| max-height | 表单最大高度 | String | 无 | - |
| size | 表单内组件的size | String | 无 | - |
| label-suffix | label的后缀 | String | 无 | - |
| disabled | 禁用表单 | Boolean | false | true |
| clearable | 表单内的组件有清空效果 | Boolean | true | false |
| success-msg | 表单提交成功后右上角的提示 | String | 操作成功！ | - |
| inline | 是否设置整个表单为横向表单 | Boolean | false | true |
| columns-number | 横向表单每行显示数量 | Number | 2 | - |
| need-alert | 表单请求失败后是否自动alert弹窗 | Boolean | true | false |

## columns（字段渲染规则）

columns 是一个数组，数组内每个元素有以下属性，每个元素代表一个表单元素。

### 参数说明

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| key | 字段名 | String | - | - |
| title | 字段显示的名称 | String | - | - |
| type | 组件类型 | String | - | - |
| width | 宽度 | Number | - | - |
| placeholder | 占位符 | String | - | - |
| tips | 下方的提示 | String | 无 | - |
| labelWidth | 单独设置该字段的左侧label宽度 | Number | - | - |
| showLabel | 是否显示左侧label文字 | Boolean | true | false |
| show | 表单复用时的显示规则 | Array | - | - |
| showRule | 是否显示该字段 | String、Function | - | - |
| disabled | 是否禁用 | Boolean、String、Function | false | true |
| watch | 监听key对应的值的改变 | Function | - | - |

### key（字段名）
字段名，如下方代码中，key 为 nickname，代表绑定 nickname 字段：
```javascript
{ key: "nickname", title: "昵称", type: "text" }
```

### title（标题）
字段显示的名称：
```javascript
{ key: "nickname", title: "昵称", type: "text" }
```

### type（组件类型）
页面需要渲染的组件类型：
```javascript
{ key: "nickname", title: "昵称", type: "text" }
```

### width（宽度）
单位是px，只能是数字：
```javascript
{ key: "nickname", title: "昵称", type: "text", width: 200 }
```

### placeholder（占位符）
类似 input 组件中的 placeholder（用户还未输入任何内容时输入框内的提示）。

### tips（下方的固定提示）
下方的固定提示，支持解析 html 字符串：
```javascript
{ 
  key: "text1", 
  title: "text类型字段", 
  type: "text", 
  tips: `<text>这里使其他文字介绍</text><a href="#/pages/order/list" target="_blank">详情</a>`, 
  width: 600 
}
```

tips 还支持解析对象数组：
```javascript
{
  key: "text1", 
  title: "选择快递公司", 
  type: "text",
  tips: [
    { text: "查不到快递公司？" },
    {
      text: "点击添加",
      click: () => { console.log(1) }
    },
    {
      text: "刷新",
      click: () => { console.log(1) }
    }
  ]
}
```

### labelWidth（单独设置label宽度）
默认在万能表单的属性上设置统一的 labelWidth，如果在 columns 内设置了 labelWidth，则以此为准。

### showLabel（是否显示label）
默认为 true，当设置为 false 时，对应的 title 不显示。

### show（复用时的显示规则）
表单组件的组件 form-type 可以动态复用同一个表单达到显示不同字段的功能。

完整示例代码可以查看页面 `pages_template/components/form/form-dialog-2`。

show 是一个字符串数组，columns 数组内每一个元素都可以单独设置 show：

- 如果 show 字段不存在，代表显示
- 如果 show 的某元素中包含 form-type 的值，则代表显示
- 如果 show 的某元素中不包含 form-type 的值，则不显示

### showRule（自定义显示规则）
与 show 不同，showRule 更灵活。

如当 login_appid_type = 1 时，渲染 mode 字段，否则不渲染（支持符号 =、==、>、>=、<、<=、!=、{in}、{nin}、&&、||）：

```javascript
{
  key: "login_appid_type", 
  title: "登录权限", 
  type: "radio", 
  optionType: "button",
  data: [
    { value: 1, label: "部分应用" },
    { value: 0, label: "全部应用" }
  ]
}, {
  key: "mode", 
  title: "模式", 
  type: "radio", 
  optionType: "button",
  data: [
    { value: 1, label: "覆盖" },
    { value: 2, label: "新增" },
    { value: 3, label: "移除" }
  ],
  showRule: "login_appid_type==1",
}
```

{in}和{nin}的用法：
```javascript
{ key: "arr", title: "arr", type: "array<string>" },
{ key: "text", title: "text", type: "text" },
{ key: "text1", title: "arr包含1则显示", type: "text", showRule: "arr{in}1" },
{ key: "text2", title: "arr不包含2则显示", type: "text", showRule: "arr{nin}2" },
```

同时 showRule 还支持函数形式：
```javascript
{
  key: "login_appid_type", 
  title: "登录权限", 
  type: "radio", 
  optionType: "button",
  data: [
    { value: 1, label: "部分应用" },
    { value: 0, label: "全部应用" }
  ]
},
{
  key: "mode", 
  title: "模式", 
  type: "radio", 
  optionType: "button",
  data: [
    { value: 1, label: "覆盖" },
    { value: 2, label: "新增" },
    { value: 3, label: "移除" }
  ],
  showRule: (formData) => {
    return formData.login_appid_type == 1;
  }
}
```

### disabled（自定义禁用规则）
disabled 和 showRule 基本写法一致，功能区别是，showRule 是满足条件则显示，disabled 是满足条件则禁用。

如当 login_appid_type = 0 时，禁用 mode 字段：
```javascript
{
  key: "login_appid_type", 
  title: "登录权限", 
  type: "radio", 
  optionType: "button",
  data: [
    { value: 1, label: "部分应用" },
    { value: 0, label: "全部应用" }
  ]
},
{
  key: "mode", 
  title: "模式", 
  type: "radio", 
  optionType: "button",
  data: [
    { value: 1, label: "覆盖" },
    { value: 2, label: "新增" },
    { value: 3, label: "移除" }
  ],
  disabled: "login_appid_type==0",
}
```

同时 disabled 还支持函数形式：
```javascript
{
  key: "login_appid_type", 
  title: "登录权限", 
  type: "radio", 
  optionType: "button",
  data: [
    { value: 1, label: "部分应用" },
    { value: 0, label: "全部应用" }
  ]
},
{
  key: "mode", 
  title: "模式", 
  type: "radio", 
  optionType: "button",
  data: [
    { value: 1, label: "覆盖" },
    { value: 2, label: "新增" },
    { value: 3, label: "移除" }
  ],
  disabled: (formData) => {
    return formData.login_appid_type === 0;
  }
}
```

### watch（监听）
用于监听 key 对应的值的改变（只监听组件内部造成的值的改变）：
```javascript
{
  key: "text", 
  title: "text类型字段", 
  type: "text",
  watch: (res) => {
    console.log("watch", res)
  }
}
```

## 进阶用法

### 行内表单
```html
<vk-data-form
  :inline="true"
  :columnsNumber="2"
></vk-data-form>
```

行内表单中 columns 若设置 oneLine:true 可强制单独一行：
```javascript
columns: [
  { key: "text1", title: "text类型字段", type: "text" },
  { key: "text2", title: "text类型字段", type: "text" },
  { key: "text3", title: "text类型字段", type: "text", oneLine: true }, // 单独一行
  { key: "text4", title: "text类型字段", type: "text", oneLine: true, width: 500 }, // 单独一行，并设置input的宽度
]
```

### type（组件类型）
更多类型请见：`/pages_template/components/form/form-pro`

```javascript
form1: {
  data: {},
  props: {
    columns: [
      { key: "", title: "基础字段", type: "bar-title" },
      { key: "text", title: "单行文本", type: "text" },
      {
        key: "textarea", 
        title: "多行文本", 
        type: "textarea",
        autosize: { minRows: 4, maxRows: 10 },
        maxlength: 200,
        showWordLimit: true,
      },
      { key: "money", title: "money类型", type: "money", tips: "100 = 1元。页面显示的是1，实际的值是100，请看右上角表单数据的值。" },
      { key: "number", title: "number类型", type: "number", precision: 2, tips: "number类型值会转为数字，可以指定小数位数" },
      { key: "number2", title: "计数器类型", type: "number", controls: true, precision: 0, min: 5, max: 100, placeholder: "请输入数字" },
      { key: "percentage", title: "百分比类型", type: "percentage", precision: 0, tips: "页面显示的是1，实际的值是0.01，请看右上角表单数据的值。" },
      { key: "discount", title: "折扣类型", type: "discount", tips: "页面显示的是1，实际的值是0.1，请看右上角表单数据的值。" },
      { key: "text2", title: "文本2", type: "text", prepend: "前置文字", append: "后置文字", prefixIcon: "el-icon-user" },
      // 选择型字段
      { key: "", title: "选择型字段", type: "bar-title" },
      {
        key: "radio1", 
        title: "radio类型1", 
        type: "radio",
        data: [
          { value: 1, label: "选项一" },
          { value: 2, label: "选项二" }
        ]
      },
      {
        key: "checkbox1", 
        title: "checkbox类型1", 
        type: "checkbox",
        data: [
          { value: 1, label: "选项一" },
          { value: 2, label: "选项二" }
        ]
      },
      {
        key: "select1", 
        title: "select类型1", 
        type: "select",
        data: [
          { value: 1, label: "选项一" },
          { value: 2, label: "选项二" }
        ]
      },
      { key: "address", title: "address类型", type: "address" },
      {
        key: "cascader2", 
        title: "云端数据级联", 
        type: "cascader",
        action: "admin/system/permission/sys/getAll",
        props: { list: "rows", value: "permission_id", label: "label", children: "children", multiple: true }
      },
      { key: "switch", title: "switch类型", type: "switch" },
      { key: "rate", title: "评分类型", type: "rate", allowHalf: false },
      { key: "slider", title: "滑块类型", type: "slider" },
      { key: "color1", title: "颜色类型1", type: "color" },
      { key: "color2", title: "颜色类型2", type: "color", showAlpha: true },
      // 文件上传
      { key: "", title: "文件上传", type: "bar-title" },
      { key: "image1", title: "image类型", type: "image", limit: 6 },
      // 日期型字段
      { key: "", title: "日期型字段", type: "bar-title" },
      { key: "date", title: "date类型", type: "date", dateType: "date", tips: "可选择年月日" },
      { key: "dateTime", title: "dataTime类型", type: "date", dateType: "datetime", tips: "可选择年月日时分秒" },
      { key: "dateArr", title: "date类型范围", type: "date", dateType: "daterange" },
      { key: "dataTimeArr", title: "dataTime类型范围", type: "date", dateType: "datetimerange" },
      // 时间型字段
      { key: "", title: "时间型字段", type: "bar-title" },
      { key: "time1", title: "time类型1", type: "time" },
      { key: "timeArr1", title: "time类型范围1", type: "time", isRange: true },
      // 数据库联动字段
      { key: "", title: "数据库联动字段", type: "bar-title" },
      { key: "user_id", title: "用户选择器", type: "remote-select", placeholder: "请输入用户账号/昵称", action: "admin/select/kh/user" },
      {
        key: "user_id", 
        title: "选择用户", 
        type: "table-select", 
        placeholder: "选择",
        action: "admin/system/user/sys/getList",
        columns: [
          { key: "nickname", title: "用户昵称", type: "text", nameKey: true },
          { key: "_id", title: "用户标识", type: "text", idKey: true },
          { key: "mobile", title: "手机号", type: "text" },
        ]
      },
      {
        key: "role1", 
        title: "通过表格选择(单选)", 
        type: "table-select", 
        placeholder: "请选择角色",
        action: "admin/system/role/sys/getList",
        columns: [
          { key: "role_name", title: "角色昵称", type: "text", nameKey: true },
          { key: "role_id", title: "角色标识", type: "text", idKey: true }
        ],
      },
      {
        key: "role2", 
        title: "通过表格选择(多选)", 
        type: "table-select", 
        placeholder: "请选择角色",
        action: "admin/system/role/sys/getList",
        columns: [
          { key: "role_name", title: "角色昵称", type: "text", nameKey: true },
          { key: "role_id", title: "角色标识", type: "text", idKey: true }
        ],
        multiple: true
      },
      // 布局
      { key: "", title: "横向布局", type: "bar-title" },
      {
        key: "", 
        title: "", 
        type: "group", 
        justify: "start",
        columns: [
          { key: "text1", title: "单行文本1", type: "text" },
          { key: "text2", title: "单行文本2", type: "text" },
          { key: "text3", title: "单行文本3", type: "text" },
          { key: "text4", title: "单行文本4", type: "text" },
        ]
      },
      {
        key: "", 
        title: "", 
        type: "group", 
        justify: "start",
        columns: [
          { key: "text5", title: "单行文本5", type: "text" },
          { key: "text6", title: "单行文本6", type: "text" },
          { key: "text7", title: "单行文本7", type: "text" },
          { key: "text8", title: "单行文本8", type: "text" },
        ]
      },
      {
        key: "", 
        title: "", 
        type: "group", 
        justify: "end",
        columns: [
          { key: "text11", title: "单行文本11", type: "text", col: { span: 6 } },
          { key: "text12", title: "单行文本12", type: "text", col: { span: 6 } },
        ]
      },
      {
        key: "", 
        title: "", 
        type: "group", 
        justify: "start",
        columns: [
          { key: "text13", title: "单行文本13", type: "text" },
          { key: "text14", title: "单行文本14", type: "text" },
        ]
      },
      {
        key: "", 
        title: "", 
        type: "group", 
        justify: "start",
        columns: [
          { key: "text21", title: "单行文本21", type: "text", col: { span: 16 } },
          { key: "text22", title: "单行文本22", type: "text", col: { span: 24 } },
          { key: "text23", title: "单行文本23", type: "text", col: { span: 16 } },
          { key: "text24", title: "单行文本24", type: "text", col: { span: 24 } },
        ]
      },
      // 对象类型
      { key: "", title: "对象类型", type: "bar-title" },
      {
        key: "object1", 
        title: "对象类型1", 
        type: "object",
        columns: [
          { key: "a", title: "对象内属性a", type: "text" },
          { key: "b", title: "对象内属性b", type: "text" },
        ]
      },
      // 可以通过设置showLabel:false, 隐藏左侧的label
      {
        key: "object2", 
        title: "对象类型2", 
        type: "object", 
        showLabel: false,
        columns: [
          { key: "a", title: "对象内属性a", type: "text" },
          { key: "b", title: "对象内属性b", type: "text" },
        ]
      },
      // 多层嵌套object
      {
        key: "object3", 
        title: "对象类型3", 
        type: "object", 
        showLabel: true,
        columns: [
          { key: "a", title: "对象内属性a", type: "text" },
          {
            key: "b", 
            title: "嵌套对象b", 
            type: "object", 
            showLabel: true,
            columns: [
              { key: "b1", title: "嵌套对象b内属性b1", type: "text" },
              { key: "b2", title: "嵌套对象b内属性b2", type: "text" },
            ]
          },
        ]
      },
      // 特殊类型
      { key: "", title: "特殊类型", type: "bar-title" },
      { key: "editor", title: "富文本类型", type: "editor" },
      // 纯展示类型
      { key: "", title: "纯展示类型", type: "bar-title" },
      { key: "text100", title: "文本展示类型", type: "text-view" },
      { key: "text101", title: "金额展示类型", type: "money-view" },
      { key: "html102", title: "html展示类型", type: "html" },
    ]
  }
}
```

## before-action（请求前拦截）

```html
<vk-data-form
  :before-action="form1.props.beforeAction"
></vk-data-form>
```

```javascript
data: function() {
  return {
    form1: {
      props: {
        beforeAction: (formData) => {
          // 可在此处修改 formData 后返回 formData
          // 若在此处 return false，则表单不触发提交请求
          return formData;
        }
      }
    }
  }
}
```

## rules（表单验证）

表单验证规则，和 element 表单验证规则一致：

```html
<vk-data-form
  :rules="form1.props.rules"
></vk-data-form>
```

```javascript
data: function() {
  return {
    form1: {
      data: {},
      props: {
        rules: {
          user_id: [
            { required: true, message: "用户ID不能为空", trigger: ['blur', 'change'] }
          ],
          money: [
            { required: true, message: "金额不能为空", trigger: ['blur', 'change'] },
            { type: "number", message: "金额必须是数字", trigger: ['blur', 'change'] }
          ],
          mobile: [
            { required: true, message: '提现人手机号不能为空', trigger: ['blur', 'change'] },
            { validator: vk.pubfn.validator("mobile"), message: '手机号格式错误', trigger: 'blur' }
          ],
          // 更多验证规则...
        }
      }
    }
  }
}
```

## 事件

| 事件名 | 说明 | 回调参数 |
|--------|------|----------|
| success | 表单提交成功 | { data, formType } |
| fail | 表单提交失败 | { data, formType } |
| complete | 表单提交结束 | { data, formType } |
| cancel | 表单点击取消按钮 | - |

监听表单提交成功事件：
```html
<vk-data-form
  @success="onFormSuccess"
></vk-data-form>
```

```javascript
methods: {
  onFormSuccess(res){
    console.log("表单提交成功", res);
  }
}
```

## 方法

通过 `this.$refs.form1.xxx();` 方式调用

| 方法名 | 说明 |
|--------|------|
| submitForm | 提交表单（提交前会自动进行一次表单验证） |
| resetForm | 重置表单 |
| clearValidate | 移除整个表单的校验 |
| validate | 进行表单验证 |
| validateField | 对部分表单字段进行校验 |
| setResetFormData | 设置重置表单函数执行后，数据重置的数据源 |

### validate
```javascript
that.$refs.form1.validate((valid) => {
  if (valid){
    // 验证通过
  }
});
```

### validateField
```javascript
that.$refs.form1.validateField("username",(errMsg, arr) => {
  if (errMsg) {
    // 未验证通过
  }
});
```

### setResetFormData
```javascript
// 执行了这个setResetFormData方法后
this.$refs.form1.setResetFormData(data);
// 后面再执行 resetForm时，表单数据会变成 data 的值
this.$refs.form1.resetForm();
```

## 插槽

columns 中每一个 key 都是插槽名称。

详情见示例：`/pages_template/components/form/form-slot`

### 重写 rate 字段的渲染示例
```html
<vk-data-form
  ref="form1"
  v-model="form1.data"
  :rules="form1.props.rules"
  :action="form1.props.action"
  :form-type="form1.props.formType"
  :columns='form1.props.columns'
  :loading.sync="form1.props.loading"
  label-width="140px"
  @success="onFormSuccess"
>
  <template v-slot:rate="{ form, keyName }">
    <view style="height: 36px;display: flex;align-items: center;">
      <el-rate v-model="form[keyName]"></el-rate>
    </view>
  </template>
</vk-data-form>
```

### 重写提交按钮的示例
```html
<vk-data-form
  ref="form1"
  v-model="form1.data"
  :rules="form1.props.rules"
  :action="form1.props.action"
  :form-type="form1.props.formType"
  :columns='form1.props.columns'
  :loading.sync="form1.props.loading"
  label-width="140px"
  @success="onFormSuccess"
>
  <template v-slot:footer="{ loading }">
    <view style="text-align: center;" >
      <el-button :loading="loading" type="danger" size="small" style="padding: 10px 40px;margin-right: 30px;" @click="adopt(-1)">拒绝</el-button>
      <el-button :loading="loading" type="success" size="small" style="padding: 10px 40px;" @click="adopt(1)">通过</el-button>
    </view>
  </template>
</vk-data-form>
```

```javascript
// 审核通过执行的方法
adopt(status){
  that.$refs.form1.submitForm({
    // data为额外提交的参数，真正提交的参数为form1.data+这里的data
    data: {
      status: status
    },
    success: (data) => {
      // 提交成功
    },
    fail: (data) => {
      // 提交失败
    }
  });
}
```

## http请求模式

示例代码：
```html
<vk-data-form
  ref="form1"
  v-model="form1.data"
  :rules="form1.props.rules"
  action="https://www.xxx.com/xxx/xxx"
  :is-request="true"
  :request-header="{ 'content-type':'application/json; charset=utf-8'} "
  :columns="form1.props.columns"
></vk-data-form>
```

## 自定义function请求模式

vk-unicloud-admin-ui 的 npm 依赖版本需 >= 1.17.0

此方式同样支持 http，且更自由化，可以在发起请求前处理请求参数，在请求成功后处理返回参数等。

### 自定义function-http请求模式示例
```html
<vk-data-form
  ref="form1"
  :action="form1.props.action"
></vk-data-form>
```

```javascript
export default {
  data() {
    return {
      form1: {
        props: {
          action: (obj = {}) => {
            let { data, success, fail, complete } = obj;
            vk.request({
              url: `https://www.xxx.com/api/form`,
              method: "POST",
              header: { "content-type": "application/json; charset=utf-8" },
              data: data,
              success: (res) => { if (typeof success === "function") success(res); },
              fail: (res) => { if (typeof fail === "function") fail(res); },
              complete: (res) => { if (typeof complete === "function") complete(res); }
            });
          }
        }
      }
    }
  }
}
```

### 自定义function-云函数请求示例
```javascript
export default {
  data() {
    return {
      form1: {
        props: {
          action: (obj = {}) => {
            let { data, success, fail, complete } = obj;
            vk.callFunction({
              url: `云函数或云对象路径`,
              data: data,
              success: (res) => { if (typeof success === "function") success(res); },
              fail: (res) => { if (typeof fail === "function") fail(res); },
              complete: (res) => { if (typeof complete === "function") complete(res); }
            });
          }
        }
      }
    }
  }
}
```

## 弹窗表单独立组件形式

接口名：`vk.pubfn.openForm`

### 介绍
如果把所有表单全写在一个 vue 页面内，会导致后期不方便维护，因此可以写成一个弹窗表单对应一个组件的形式。

### 打开弹窗API
```javascript
vk.pubfn.openForm(name, value, this);
vk.pubfn.openForm('bindRole',{ item: {} }); // 来源页面 /pages_plugs/system/user/list
```

### 参数说明
| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| name | 弹窗表单组件名 | String | - | - |
| value | 传给表单的数据（格式为 { item: 当前行数据 }） | Object | - | - |
| this | 页面或组件实例，在组件内调用时必传 | Object | 当前页面实例（this） | - |

### 使用示例
以 `/pages_plugs/system/user/list` 用户管理页面为例，功能是点击弹出表单【绑定角色】

1. 新建一个表单组件，一般在该页面新建 form 目录，在 form 目录下创建弹窗表单组件，如 bindRole

2. list.vue 的 template 新增以下代码：
```html
<el-button type="primary" size="small" icon="el-icon-s-tools" @click="bindRoleBtn">角色绑定</el-button>
<bindRole v-model="formDatas.bindRole"></bindRole>
```

3. list.vue 的 script 新增以下代码：
```javascript
import bindRole from './form/bindRole'

export default {
  components: { bindRole },
  data() {
    return {
      formDatas: {} // 在此处定义下formDatas
    }
  },
  methods: {
    bindRoleBtn() {
      let item = this.getCurrentRow(true);
      vk.pubfn.openForm('bindRole', { item });
    }
  }
}
```

4. 完成，此时点击【角色绑定】按钮即可弹出表单【角色绑定】

## 进阶用法

更多高级用法和完整示例，请参考：
- `/pages_template/components/form/form-pro`
- `/pages_template/components/form/form-dialog-2`
- `/pages_template/components/form/form-slot`