---
type: "manual"
description: "万能表单组件开发规范和配置"
---

# 万能表单组件

万能表单组件支持60+字段类型，通过JSON配置快速生成表单。

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

## 表单字段类型

```javascript
// 常用表单字段配置
const formColumns = [
  // 文本输入
  {
    key: "name",
    title: "姓名",
    type: "text",
    placeholder: "请输入姓名",
    maxlength: 50
  },

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