# VK Admin 表单组件示例

VK框架提供了强大的表单组件系统，支持多种字段类型和高级功能。

## 基础表单组件

### vk-data-form 核心组件
```vue
<vk-data-form
  v-model="formData"
  :action="formAction"
  :columns="formColumns"
  :rules="formRules"
  label-width="140px"
  @success="onFormSuccess"
></vk-data-form>
```

## 字段类型示例

### 基础字段类型

#### 文本字段
```javascript
{ key: "text", title: "单行文本", type: "text" }
{ key: "textarea", title: "多行文本", type: "textarea", 
  autosize: { minRows: 4, maxRows: 10 }, 
  maxlength: 200, 
  showWordLimit: true 
}
```

#### 数字字段
```javascript
{ key: "number", title: "数字类型", type: "number", precision: 2 }
{ key: "money", title: "金额类型", type: "money" }
{ key: "percentage", title: "百分比", type: "percentage", precision: 0 }
{ key: "discount", title: "折扣类型", type: "discount" }
```

#### 选择字段
```javascript
// 单选按钮
{ key: "radio", title: "单选", type: "radio",
  data: [
    { value: 1, label: "选项1" },
    { value: 2, label: "选项2" }
  ]
}

// 多选按钮  
{ key: "checkbox", title: "多选", type: "checkbox",
  data: [
    { value: 1, label: "选项1" },
    { value: 2, label: "选项2" }
  ]
}

// 下拉选择
{ key: "select", title: "下拉选择", type: "select",
  data: [
    { value: 1, label: "选项1" },
    { value: 2, label: "选项2" }
  ]
}
```

### 高级字段类型

#### 日期时间字段
```javascript
{ key: "date", title: "日期", type: "date", dateType: "date" }
{ key: "dateTime", title: "日期时间", type: "date", dateType: "datetime" }
{ key: "dateArr", title: "日期范围", type: "date", dateType: "daterange" }
{ key: "time", title: "时间", type: "time" }
{ key: "timeArr", title: "时间范围", type: "time", isRange: true }
```

#### 文件上传字段
```javascript
{ key: "image", title: "图片上传", type: "image", limit: 6 }
{ key: "file", title: "文件上传", type: "file", limit: 6,
  accept: ".txt,.xls,.xlsx,.doc,.docx,.ppt,.pptx,.pdf" 
}
{ key: "fileSelect", title: "素材库选择", type: "file-select", 
  fileType: "image", multiple: true 
}
```

#### 特殊字段类型
```javascript
{ key: "editor", title: "富文本", type: "editor", width: "1000px" }
{ key: "json", title: "JSON编辑", type: "json" }
{ key: "map", title: "地图位置", type: "map", width: 600, height: 300 }
{ key: "color", title: "颜色选择", type: "color", showAlpha: true }
{ key: "icon", title: "图标选择", type: "icon", filter: "vk-" }
```

### 数据库联动字段

#### 远程选择器
```javascript
{ key: "user_id", title: "用户选择", type: "remote-select",
  action: "admin/select/kh/user" 
}

{ key: "role", title: "表格选择", type: "table-select",
  action: "admin/system/role/sys/getList",
  columns: [
    { key: "role_name", title: "角色名", nameKey: true },
    { key: "role_id", title: "角色ID", idKey: true }
  ]
}
```

#### 级联选择器
```javascript
{ key: "cascader", title: "级联选择", type: "cascader",
  action: "admin/system/permission/sys/getAll",
  props: {
    value: "permission_id",
    label: "label", 
    children: "children"
  }
}
```

### 数组和对象字段

#### 数组字段
```javascript
{ key: "tags", title: "标签数组", type: "array<string>" }
{ key: "numbers", title: "数字数组", type: "array<number>" }

{ key: "items", title: "对象数组", type: "array<object>",
  showAdd: true,
  showClear: true, 
  showSort: true,
  rightBtns: ['copy', 'delete'],
  columns: [
    { key: "name", title: "名称", type: "text" },
    { key: "quantity", title: "数量", type: "number" }
  ]
}
```

#### 对象字段
```javascript
{ key: "address", title: "地址对象", type: "object",
  columns: [
    { key: "province", title: "省份", type: "text" },
    { key: "city", title: "城市", type: "text" }
  ]
}
```

## 表单验证规则

### 基本验证
```javascript
rules: {
  text: [
    { required: true, message: '文本不能为空', trigger: 'change' }
  ],
  number: [
    { required: true, message: '数字不能为空', trigger: 'change' },
    { type: "number", message: '必须是数字', trigger: 'change' }
  ]
}
```

### 自定义验证
```javascript
customField: [
  { 
    validator: (rule, value, callback) => {
      if (value < 18) {
        callback(new Error('年龄必须大于18岁'));
      } else {
        callback();
      }
    },
    trigger: 'change' 
  }
]
```

## 完整示例

### 基础表单配置
```javascript
form1: {
  data: {
    text: "",
    number: 0,
    radio: 1,
    checkbox: [1, 2]
  },
  props: {
    action: "template/test/sys/test",
    columns: [
      { key: "text", title: "文本字段", type: "text" },
      { key: "number", title: "数字字段", type: "number" },
      { key: "radio", title: "单选", type: "radio",
        data: [
          { value: 1, label: "选项1" },
          { value: 2, label: "选项2" }
        ]
      }
    ],
    rules: {
      text: [{ required: true, message: '必填字段', trigger: 'change' }]
    },
    formType: 'add',
    loading: false
  }
}
```

### 弹窗表单示例
```vue
<vk-data-dialog v-model="showForm" title="编辑表单" width="950px" mode="form">
  <vk-data-form
    v-model="formData"
    :action="formAction"
    :columns="formColumns"
    :rules="formRules"
    label-width="140px"
    auto-close
    @success="onFormSuccess"
  ></vk-data-form>
</vk-data-dialog>
```

## 常用属性说明

| 属性 | 说明 | 类型 | 默认值 |
|------|------|------|--------|
| action | 表单提交地址 | string | - |
| formType | 表单类型: add/update | string | - |
| label-width | 标签宽度 | string | 140px |
| loading | 加载状态 | boolean | false |
| auto-close | 提交成功后自动关闭 | boolean | false |

## 事件说明

| 事件名 | 说明 | 参数 |
|--------|------|------|
| success | 表单提交成功 | response数据 |
| error | 表单提交失败 | error对象 |
| cancel | 表单取消 | - |

## 使用技巧

1. **字段联动**: 使用 `onChange` 回调实现字段间联动
2. **动态验证**: 根据业务逻辑动态设置验证规则
3. **自定义渲染**: 使用插槽自定义字段渲染方式
4. **批量操作**: 数组字段支持复制、删除、排序等操作
5. **数据转换**: 金额、百分比等字段自动进行数据转换

VK表单组件提供了完整的表单解决方案，支持从简单到复杂的各种业务场景。