---
type: "manual"
description: "万能表单组件开发规范和配置"
---

# 万能表单组件

万能表单组件支持60+字段类型，通过JSON配置快速生成表单，提供完整的表单解决方案。

## 核心思想

通过 JSON 配置渲染表单（简单配置一下，表单就完成了）

**示例配置**：
```javascript
[
  { key:"nickname", title:"昵称", type:"text" },
  {
    key:"gender", title:"性别", type:"radio",
    data:[
      { value:1, label:"男" },
      { value:2, label:"女" }
    ]
  }
]
```

## 基础用法

```vue
<template>
  <view class="page-body">
    <vk-data-form
      v-model="form1.formData"
      :columns="form1.columns"
      :rules="form1.rules"
      :loading="form1.loading"
      @submit="submit"
    >
      <template slot="footer">
        <el-button @click="goBack">取消</el-button>
        <el-button type="primary" @click="submit" :loading="form1.loading">
          保存
        </el-button>
      </template>
    </vk-data-form>
  </view>
</template>
```

## 核心功能特性

### 1. 表单处理
- ✅ 自动提交 - 自动处理表单提交逻辑
- ✅ 表单验证 - 提交前自动进行表单验证
- ✅ 防止重复提交 - 提交后按钮自动进入loading状态
- ✅ 表单复用 - 同一表单可复用于添加和修改操作
- ✅ 字段显示规则 - 支持根据条件动态显示/隐藏字段
- ✅ 拦截器机制 - 支持提交前拦截执行自定义逻辑
- ✅ 表单重置 - 一键重置表单到初始状态

### 2. 高级功能
- ✅ 可视化拖拽工具 - [表单可视化拖拽工具](https://vkunicloud.fsq.pub/vk-form-visualizer/)
- ✅ 字段插槽 - 每个字段都支持插槽自定义渲染
- ✅ 原生Element支持 - 支持直接使用Element原生代码
- ✅ 动态表单 - 支持根据数据动态生成表单结构
- ✅ 表单分组 - 支持将字段分组显示
- ✅ 条件显示 - 支持根据其他字段值动态显示/隐藏字段

### 3. 数据验证
- ✅ 内置验证规则 - 支持required、email、mobile等内置验证
- ✅ 自定义验证器 - 支持自定义验证函数
- ✅ 异步验证 - 支持异步验证逻辑
- ✅ 多级验证 - 支持字段级和表单级验证

## 表单字段类型

万能表单支持60+字段类型，覆盖各种业务场景：

### 基础输入类型
```javascript
// 文本输入
{ key: "name", title: "姓名", type: "text", placeholder: "请输入姓名", maxlength: 50 }

// 数字输入
{ key: "age", title: "年龄", type: "number", min: 0, max: 150, step: 1 }

// 文本域
{ key: "description", title: "描述", type: "textarea", rows: 4, maxlength: 500 }

// 密码输入
{ key: "password", title: "密码", type: "password", showPassword: true }
```

### 选择器类型
```javascript
// 下拉选择
{
  key: "category",
  title: "分类",
  type: "select",
  options: [
    { value: "1", text: "分类1" },
    { value: "2", text: "分类2" }
  ]
}

// 单选框
{
  key: "status",
  title: "状态",
  type: "radio",
  options: [
    { value: 1, text: "启用" },
    { value: 0, text: "禁用" }
  ]
}

// 多选框
{
  key: "tags",
  title: "标签",
  type: "checkbox",
  options: [
    { value: "tag1", text: "标签1" },
    { value: "tag2", text: "标签2" }
  ]
}
```

### 日期时间类型
```javascript
// 日期选择
{ key: "birthday", title: "生日", type: "date", valueFormat: "yyyy-MM-dd" }

// 时间选择
{ key: "work_time", title: "工作时间", type: "time", valueFormat: "HH:mm:ss" }

// 日期时间选择
{ key: "create_time", title: "创建时间", type: "datetime", valueFormat: "yyyy-MM-dd HH:mm:ss" }

// 日期范围
{ key: "date_range", title: "日期范围", type: "daterange", valueFormat: "yyyy-MM-dd" }
```

### 文件上传类型
```javascript
// 图片上传
{
  key: "avatar",
  title: "头像",
  type: "image",
  limit: 1,
  action: "upload",
  accept: "image/*",
  listType: "picture-card"
}

// 文件上传
{
  key: "attachment",
  title: "附件",
  type: "file",
  limit: 5,
  action: "upload",
  accept: ".doc,.docx,.pdf,.xls,.xlsx",
  showFileList: true
}

// 多图上传
{
  key: "gallery",
  title: "相册",
  type: "images",
  limit: 10,
  action: "upload",
  multiple: true
}
```

### 高级编辑器类型
```javascript
// 富文本编辑器
{
  key: "content",
  title: "内容",
  type: "editor",
  height: 300,
  toolbar: ["bold", "italic", "underline", "strike"]
}

// Markdown编辑器
{
  key: "markdown_content",
  title: "Markdown内容",
  type: "markdown",
  height: 400
}

// 代码编辑器
{
  key: "code",
  title: "代码",
  type: "code",
  language: "javascript",
  theme: "vs-dark"
}
```

### 业务组件类型
```javascript
// 颜色选择器
{ key: "theme_color", title: "主题色", type: "color" }

// 评分组件
{ key: "rating", title: "评分", type: "rate", max: 5, allowHalf: true }

// 滑块选择
{ key: "volume", title: "音量", type: "slider", min: 0, max: 100, step: 1 }

// 开关组件
{ key: "enabled", title: "启用状态", type: "switch", activeValue: 1, inactiveValue: 0 }

// 数字步进器
{ key: "quantity", title: "数量", type: "inputNumber", min: 1, max: 100, step: 1 }

// 级联选择
{
  key: "region",
  title: "地区",
  type: "cascader",
  options: [
    {
      value: "zhejiang",
      label: "浙江",
      children: [
        { value: "hangzhou", label: "杭州" },
        { value: "ningbo", label: "宁波" }
      ]
    }
  ]
}
```

### 特殊业务类型
```javascript
// 地图位置选择
{
  key: "location",
  title: "位置",
  type: "map",
  center: [116.397428, 39.90923],
  zoom: 13
}

// 树形选择
{
  key: "department",
  title: "部门",
  type: "tree",
  data: [
    {
      id: 1,
      label: "一级部门",
      children: [
        { id: 2, label: "二级部门1" },
        { id: 3, label: "二级部门2" }
      ]
    }
  ]
}

// 标签输入
{
  key: "skills",
  title: "技能标签",
  type: "tag-input",
  suggestions: ["JavaScript", "Vue", "React", "Node.js"]
}

// 自动完成
{
  key: "city",
  title: "城市",
  type: "autocomplete",
  fetchSuggestions: (query, callback) => {
    // 异步获取建议列表
    callback([{ value: "北京" }, { value: "上海" }])
  }
}
```

  // 数字输入
  {
    key: "age",
    title: "年龄",
    type: "number",
    min: 0,
    max: 150
  },

  // 下拉选择
  {
    key: "category",
    title: "分类",
    type: "select",
    options: [
      { value: "1", text: "分类1" },
      { value: "2", text: "分类2" }
    ]
  },

  // 单选框
  {
    key: "status",
    title: "状态",
    type: "radio",
    options: [
      { value: 1, text: "启用" },
      { value: 0, text: "禁用" }
    ]
  },

  // 图片上传
  {
    key: "avatar",
    title: "头像",
    type: "image",
    limit: 1,
    action: "upload",
    accept: "image/*"
  },

  // 富文本编辑器
  {
    key: "content",
    title: "内容",
    type: "editor",
    height: 300
  }
]
```

## 表单验证规则

```javascript
// 表单验证规则配置
const formRules = {
  name: [
    { required: true, message: "请输入姓名", trigger: "blur" },
    { min: 2, max: 20, message: "长度在 2 到 20 个字符", trigger: "blur" }
  ],

  email: [
    { required: true, message: "请输入邮箱地址", trigger: "blur" },
    { type: "email", message: "请输入正确的邮箱地址", trigger: "blur" }
  ],

  mobile: [
    { required: true, message: "请输入手机号", trigger: "blur" },
    { pattern: /^1[3-9]\d{9}$/, message: "请输入正确的手机号", trigger: "blur" }
  ]
}

// 自定义验证器
const customValidator = (rule, value, callback) => {
  if (!value) {
    callback(new Error("请输入内容"))
  } else if (value.length < 2) {
    callback(new Error("内容长度不能少于2个字符"))
  } else {
    callback()
  }
}