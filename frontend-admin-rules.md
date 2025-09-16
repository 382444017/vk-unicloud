---
type: "manual"
description: "使用到前端-管理端开发时"
---

# VK-UniCloud 管理端开发规范

本文档包含VK-UniCloud框架管理端特有的开发规范，包括万能表格、万能表单等管理端专用组件。

**前置要求**: 请先阅读 [前端开发规范](./frontend-rules.md) 了解通用的前端开发规范。

## 📚 目录

1. [管理端概述](#管理端概述)
2. [项目结构](#项目结构)
3. [万能表格组件](#万能表格组件)
4. [万能表单组件](#万能表单组件)
5. [权限管理](#权限管理)
6. [菜单配置](#菜单配置)
7. [页面模板](#页面模板)
8. [最佳实践](#最佳实践)

## 管理端概述

VK-UniCloud管理端是基于Vue + Element UI/Plus构建的后台管理系统，提供完整的CRUD页面模板和组件。

### 核心特性

- **万能表格**: 通过JSON配置快速生成CRUD页面
- **万能表单**: 支持60+字段类型的表单组件
- **权限控制**: 基于RBAC的权限管理系统
- **菜单管理**: 动态菜单配置系统
- **响应式设计**: 适配不同屏幕尺寸

### 技术栈

- **前端框架**: Vue 2/3 + Element UI/Plus
- **状态管理**: Vuex/Pinia (可选)
- **路由管理**: Vue Router
- **HTTP客户端**: VK-UniCloud内置请求方法
- **构建工具**: HBuilderX + uni-app

## 项目结构

### 管理端特有目录结构

```
管理端/
├── src/
│   ├── pages/              # 页面文件
│   ├── components/         # 组件文件
│   ├── static/            # 静态资源
│   └── utils/             # 工具函数
├── pages_template/        # 页面模板 (管理端特有)
│   ├── components/        # 模板组件
│   ├── list/              # 列表页模板
│   ├── add/               # 新增页模板
│   └── edit/              # 编辑页模板
├── static_menu/           # 菜单配置 (管理端特有)
│   ├── menu.json          # 生产环境菜单
│   └── menu-dev.json      # 开发环境菜单
├── pages.json            # 页面配置
└── pages-dev.json        # 开发页面配置
```

**重要说明**: 管理端不包含独立的云开发环境，通过配置关联到客户端的云开发环境，共享云函数和数据库。

## 万能表格组件

万能表格是管理端的核心组件，通过JSON配置快速生成CRUD页面。

### 基础用法

```vue
<template>
  <view class="page-body">
    <!-- 搜索组件 -->
    <vk-data-table-query
      v-model="queryForm.formData"
      :columns="queryForm.columns"
      @search="search"
    >
      <template slot="right-btns">
        <el-button type="success" @click="addBtn">添加</el-button>
      </template>
    </vk-data-table-query>

    <!-- 表格组件 -->
    <vk-data-table
      ref="table1"
      :columns="table1.columns"
      :request="table1.request"
      :pagination="table1.pagination"
      @selection-change="selectionChange"
    >
      <!-- 操作列 -->
      <template slot="action" slot-scope="{ row }">
        <el-button size="mini" @click="editBtn(row)">编辑</el-button>
        <el-button size="mini" type="danger" @click="deleteBtn(row)">删除</el-button>
      </template>
    </vk-data-table>
  </view>
</template>

<script>
const vk = uni.vk

export default {
  data() {
    return {
      // 搜索表单配置
      queryForm: {
        formData: {
          name: "",
          status: ""
        },
        columns: [
          {
            key: "name",
            title: "名称",
            type: "text",
            placeholder: "请输入名称"
          },
          {
            key: "status",
            title: "状态",
            type: "select",
            options: [
              { value: "", text: "全部" },
              { value: 1, text: "启用" },
              { value: 0, text: "禁用" }
            ]
          }
        ]
      },
      
      // 表格配置
      table1: {
        columns: [
          { key: "name", title: "名称", width: 200 },
          { key: "status", title: "状态", width: 100, type: "tag" },
          { key: "create_time", title: "创建时间", width: 180, type: "time" },
          { key: "action", title: "操作", width: 150, type: "action" }
        ],
        request: {
          url: "admin/system/user/getList",
          data: {}
        },
        pagination: {
          pageSize: 20
        }
      }
    }
  },
  
  methods: {
    // 搜索
    search() {
      this.table1.request.data = { ...this.queryForm.formData }
      this.$refs.table1.refresh()
    },
    
    // 添加
    addBtn() {
      uni.navigateTo({
        url: "/pages_template/add/add?tableName=users"
      })
    },
    
    // 编辑
    editBtn(row) {
      uni.navigateTo({
        url: `/pages_template/edit/edit?tableName=users&id=${row._id}`
      })
    },
    
    // 删除
    async deleteBtn(row) {
      const confirmResult = await vk.alert("确定要删除吗？", {
        confirmButtonText: "确定",
        cancelButtonText: "取消"
      })
      
      if (confirmResult === "confirm") {
        const res = await vk.callFunction({
          url: "admin/system/user/delete",
          data: { _id: row._id }
        })
        
        if (res.code === 0) {
          vk.toast("删除成功")
          this.$refs.table1.refresh()
        }
      }
    },
    
    // 选择变化
    selectionChange(selection) {
      console.log("选中的行:", selection)
    }
  }
}
</script>
```

### 表格列配置

```javascript
// 表格列配置规范
const tableColumns = [
  {
    key: "name",           // 字段名
    title: "名称",         // 列标题
    width: 200,           // 列宽度
    type: "text",         // 列类型
    sortable: true,       // 是否可排序
    fixed: "left"         // 固定列位置
  },
  {
    key: "avatar",
    title: "头像",
    width: 80,
    type: "image",        // 图片类型
    imageWidth: 60,
    imageHeight: 60
  },
  {
    key: "status",
    title: "状态",
    width: 100,
    type: "tag",          // 标签类型
    options: [
      { value: 1, text: "启用", color: "success" },
      { value: 0, text: "禁用", color: "danger" }
    ]
  },
  {
    key: "create_time",
    title: "创建时间",
    width: 180,
    type: "time",         // 时间类型
    format: "yyyy-MM-dd hh:mm:ss"
  },
  {
    key: "action",
    title: "操作",
    width: 200,
    type: "action",       // 操作列
    fixed: "right"
  }
]
```

### 搜索表单配置

搜索表单通过 `vk-data-table-query` 组件实现，支持多种查询模式。

#### 基础配置

```javascript
// 搜索表单配置
const queryForm = {
  formData: {
    name: "",
    status: "",
    create_time: []
  },
  columns: [
    {
      key: "name",              // 字段名
      title: "名称",            // 显示标题
      type: "text",             // 组件类型
      placeholder: "请输入名称", // 占位符
      mode: "%%"                // 查询模式：模糊匹配
    },
    {
      key: "status",
      title: "状态",
      type: "select",
      mode: "=",                // 查询模式：精确匹配
      options: [
        { value: "", text: "全部" },
        { value: 1, text: "启用" },
        { value: 0, text: "禁用" }
      ]
    },
    {
      key: "create_time",
      title: "创建时间",
      type: "datetimerange",
      mode: "[]",               // 查询模式：范围查询
      width: 300
    }
  ]
}
```

#### mode 参数详解

`mode` 参数用于指定字段的查询模式，支持以下值：

| mode值 | 说明 | 示例 | 适用场景 |
|--------|------|------|----------|
| `=` | 完全匹配 | `name = "张三"` | 精确查询，如状态、类型等 |
| `!=` | 不等于 | `status != 0` | 排除特定值 |
| `%%` | 模糊匹配 | `name LIKE "%张%"` | 文本搜索，如姓名、标题等 |
| `%*` | 以xxx开头 | `name LIKE "张%"` | 前缀匹配 |
| `*%` | 以xxx结尾 | `name LIKE "%三"` | 后缀匹配 |
| `>` | 大于 | `age > 18` | 数值比较 |
| `>=` | 大于等于 | `score >= 60` | 数值比较 |
| `<` | 小于 | `price < 100` | 数值比较 |
| `<=` | 小于等于 | `count <= 10` | 数值比较 |
| `in` | 在数组里 | `status IN [1,2]` | 多选查询 |
| `nin` | 不在数组里 | `status NOT IN [0]` | 排除多个值 |
| `[]` | 范围查询 | `date BETWEEN start AND end` | 时间范围、数值范围 |
| `[)` | 左闭右开 | `start <= x < end` | 范围查询 |
| `(]` | 左开右闭 | `start < x <= end` | 范围查询 |
| `()` | 开区间 | `start < x < end` | 范围查询 |
| `custom` | 自定义 | 不参与自动where条件 | 复杂查询逻辑 |

#### 常用查询配置示例

```javascript
// 文本模糊搜索
{
  key: "title",
  title: "标题",
  type: "text",
  placeholder: "请输入标题关键词",
  mode: "%%"  // 模糊匹配
}

// 状态精确匹配
{
  key: "status",
  title: "状态",
  type: "select",
  mode: "=",  // 精确匹配
  options: [
    { value: "", text: "全部" },
    { value: 1, text: "启用" },
    { value: 0, text: "禁用" }
  ]
}

// 时间范围查询
{
  key: "create_time",
  title: "创建时间",
  type: "datetimerange",
  mode: "[]",  // 范围查询
  width: 300
}

// 数值范围查询
{
  key: "price_min",
  title: "最低价格",
  type: "number",
  mode: ">=",  // 大于等于
  fieldName: "price"  // 指定数据库字段名
},
{
  key: "price_max",
  title: "最高价格",
  type: "number",
  mode: "<=",  // 小于等于
  fieldName: "price"  // 指定数据库字段名
}

// 多选查询
{
  key: "category",
  title: "分类",
  type: "checkbox",
  mode: "in",  // 在数组里
  options: [
    { value: "tech", text: "技术" },
    { value: "life", text: "生活" },
    { value: "work", text: "工作" }
  ]
}
```

#### 高级配置参数

```javascript
{
  key: "mobile",
  title: "手机号",
  type: "text",
  mode: "%%",
  fieldName: "userInfo.mobile",  // 连表字段
  lastWhereJson: true,           // 是否是连表后的where条件
  hidden: false,                 // 是否隐藏该字段
  autoSearch: true,              // 选择型组件是否自动搜索
  show: ["page", "drawer"],      // 显示位置：page页面上，drawer高级搜索中
  width: 200                     // 组件宽度
}
```

### 表格请求配置

```javascript
// 表格请求配置
const tableRequest = {
  url: "admin/system/user/getList",  // 云函数路径
  method: "POST",                    // 请求方法
  data: {                           // 请求参数
    // 搜索条件会自动合并到这里
  },
  dataPreprocess: (data) => {       // 数据预处理
    // 可以在这里对返回的数据进行处理
    return data
  }
}

// 分页配置
const tablePagination = {
  pageSize: 20,           // 每页条数
  pageSizes: [10, 20, 50, 100],  // 每页条数选项
  layout: "total, sizes, prev, pager, next, jumper"  // 分页组件布局
}
```

## 万能表单组件

万能表单组件支持60+字段类型，通过JSON配置快速生成表单。

### 基础用法

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

<script>
const vk = uni.vk

export default {
  data() {
    return {
      form1: {
        formData: {
          name: "",
          email: "",
          mobile: "",
          status: 1,
          avatar: "",
          remark: ""
        },
        columns: [
          {
            key: "name",
            title: "姓名",
            type: "text",
            placeholder: "请输入姓名",
            rules: [{ required: true, message: "请输入姓名" }]
          },
          {
            key: "email",
            title: "邮箱",
            type: "email",
            placeholder: "请输入邮箱地址"
          },
          {
            key: "mobile",
            title: "手机号",
            type: "mobile",
            placeholder: "请输入手机号"
          },
          {
            key: "status",
            title: "状态",
            type: "radio",
            options: [
              { value: 1, text: "启用" },
              { value: 0, text: "禁用" }
            ]
          },
          {
            key: "avatar",
            title: "头像",
            type: "image",
            limit: 1,
            action: "upload"
          },
          {
            key: "remark",
            title: "备注",
            type: "textarea",
            placeholder: "请输入备注信息",
            rows: 4
          }
        ],
        rules: {},
        loading: false
      }
    }
  },

  onLoad(options) {
    if (options.id) {
      this.loadData(options.id)
    }
  },

  methods: {
    // 加载数据
    async loadData(id) {
      try {
        const res = await vk.callFunction({
          url: "admin/system/user/getDetail",
          data: { _id: id }
        })

        if (res.code === 0) {
          this.form1.formData = { ...res.data }
        }
      } catch (error) {
        console.error("加载数据失败:", error)
      }
    },

    // 提交表单
    async submit() {
      this.form1.loading = true
      try {
        const res = await vk.callFunction({
          url: "admin/system/user/save",
          data: this.form1.formData
        })

        if (res.code === 0) {
          vk.toast("保存成功")
          setTimeout(() => {
            this.goBack()
          }, 1000)
        }
      } catch (error) {
        console.error("保存失败:", error)
      } finally {
        this.form1.loading = false
      }
    },

    // 返回
    goBack() {
      uni.navigateBack()
    }
  }
}
</script>
```

### 表单字段类型

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

  // 密码输入
  {
    key: "password",
    title: "密码",
    type: "password",
    placeholder: "请输入密码"
  },

  // 邮箱输入
  {
    key: "email",
    title: "邮箱",
    type: "email",
    placeholder: "请输入邮箱地址"
  },

  // 手机号输入
  {
    key: "mobile",
    title: "手机号",
    type: "mobile",
    placeholder: "请输入手机号"
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

  // 复选框
  {
    key: "permissions",
    title: "权限",
    type: "checkbox",
    options: [
      { value: "read", text: "读取" },
      { value: "write", text: "写入" },
      { value: "delete", text: "删除" }
    ]
  },

  // 日期选择
  {
    key: "birthday",
    title: "生日",
    type: "date",
    format: "yyyy-MM-dd"
  },

  // 时间选择
  {
    key: "work_time",
    title: "工作时间",
    type: "time",
    format: "HH:mm:ss"
  },

  // 日期时间选择
  {
    key: "create_time",
    title: "创建时间",
    type: "datetime",
    format: "yyyy-MM-dd HH:mm:ss"
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

  // 文件上传
  {
    key: "attachment",
    title: "附件",
    type: "file",
    limit: 5,
    action: "upload"
  },

  // 富文本编辑器
  {
    key: "content",
    title: "内容",
    type: "editor",
    height: 300
  },

  // 多行文本
  {
    key: "remark",
    title: "备注",
    type: "textarea",
    rows: 4,
    placeholder: "请输入备注信息"
  },

  // 开关
  {
    key: "is_enabled",
    title: "启用状态",
    type: "switch",
    activeText: "启用",
    inactiveText: "禁用"
  },

  // 滑块
  {
    key: "score",
    title: "评分",
    type: "slider",
    min: 0,
    max: 100,
    step: 1
  },

  // 颜色选择器
  {
    key: "theme_color",
    title: "主题色",
    type: "color"
  }
]
```

### 表单验证规则

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
  ],

  age: [
    { required: true, message: "请输入年龄", trigger: "blur" },
    { type: "number", min: 1, max: 150, message: "年龄必须在1-150之间", trigger: "blur" }
  ],

  password: [
    { required: true, message: "请输入密码", trigger: "blur" },
    { min: 6, max: 20, message: "密码长度在 6 到 20 个字符", trigger: "blur" }
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

// 使用自定义验证器
const customRule = {
  key: "custom_field",
  title: "自定义字段",
  type: "text",
  rules: [
    { validator: customValidator, trigger: "blur" }
  ]
}
```

## 权限管理

管理端基于RBAC（基于角色的访问控制）模型实现权限管理。

### 权限检查

```javascript
// 权限检查方法
const checkPermission = (permission) => {
  const userInfo = vk.getStorageSync("userInfo")
  if (!userInfo || !userInfo.permission) {
    return false
  }

  // 超级管理员拥有所有权限
  if (userInfo.role && userInfo.role.includes("super_admin")) {
    return true
  }

  // 检查具体权限
  return userInfo.permission.includes(permission)
}

// 在页面中使用权限检查
export default {
  data() {
    return {
      hasAddPermission: false,
      hasEditPermission: false,
      hasDeletePermission: false
    }
  },

  onLoad() {
    this.checkPagePermissions()
  },

  methods: {
    checkPagePermissions() {
      this.hasAddPermission = checkPermission("user:add")
      this.hasEditPermission = checkPermission("user:edit")
      this.hasDeletePermission = checkPermission("user:delete")
    }
  }
}
```

### 权限指令

```javascript
// 自定义权限指令
Vue.directive("permission", {
  inserted(el, binding) {
    const permission = binding.value
    if (!checkPermission(permission)) {
      el.style.display = "none"
      // 或者移除元素
      // el.parentNode && el.parentNode.removeChild(el)
    }
  }
})

// 在模板中使用
// <el-button v-permission="'user:add'" @click="addUser">添加用户</el-button>
```

### 路由权限守卫

```javascript
// 路由权限配置
const routePermissions = {
  "/pages/user/list": "user:view",
  "/pages/user/add": "user:add",
  "/pages/user/edit": "user:edit",
  "/pages/role/list": "role:view",
  "/pages/system/menu": "system:menu"
}

// 页面权限检查
const checkPagePermission = (route) => {
  const permission = routePermissions[route]
  if (permission && !checkPermission(permission)) {
    uni.showToast({
      title: "没有访问权限",
      icon: "none"
    })
    uni.navigateBack()
    return false
  }
  return true
}

// 在页面onLoad中使用
onLoad() {
  const currentRoute = getCurrentPages().pop().route
  if (!checkPagePermission(`/${currentRoute}`)) {
    return
  }
  // 继续页面初始化
}
```

## 菜单配置

管理端支持动态菜单配置，通过JSON文件定义菜单结构。

### 菜单配置文件

```json
// static_menu/menu.json
{
  "list": [
    {
      "menu_id": "system",
      "name": "系统管理",
      "icon": "el-icon-setting",
      "sort": 1,
      "children": [
        {
          "menu_id": "system.user",
          "name": "用户管理",
          "url": "/pages/system/user/list",
          "icon": "el-icon-user",
          "sort": 1,
          "permission": "user:view"
        },
        {
          "menu_id": "system.role",
          "name": "角色管理",
          "url": "/pages/system/role/list",
          "icon": "el-icon-s-custom",
          "sort": 2,
          "permission": "role:view"
        },
        {
          "menu_id": "system.menu",
          "name": "菜单管理",
          "url": "/pages/system/menu/list",
          "icon": "el-icon-menu",
          "sort": 3,
          "permission": "menu:view"
        }
      ]
    },
    {
      "menu_id": "content",
      "name": "内容管理",
      "icon": "el-icon-document",
      "sort": 2,
      "children": [
        {
          "menu_id": "content.article",
          "name": "文章管理",
          "url": "/pages/content/article/list",
          "icon": "el-icon-edit",
          "sort": 1,
          "permission": "article:view"
        }
      ]
    }
  ]
}
```

### 菜单渲染组件

```vue
<template>
  <el-menu
    :default-active="activeMenu"
    :collapse="isCollapse"
    background-color="#304156"
    text-color="#bfcbd9"
    active-text-color="#409EFF"
  >
    <menu-item
      v-for="menu in filteredMenus"
      :key="menu.menu_id"
      :menu="menu"
    />
  </el-menu>
</template>

<script>
import MenuItem from "./MenuItem.vue"

export default {
  components: {
    MenuItem
  },

  data() {
    return {
      menuList: [],
      activeMenu: "",
      isCollapse: false
    }
  },

  computed: {
    // 根据权限过滤菜单
    filteredMenus() {
      return this.filterMenusByPermission(this.menuList)
    }
  },

  onLoad() {
    this.loadMenus()
    this.setActiveMenu()
  },

  methods: {
    // 加载菜单
    async loadMenus() {
      try {
        // 从本地文件加载菜单配置
        const menuData = require("@/static_menu/menu.json")
        this.menuList = menuData.list || []
      } catch (error) {
        console.error("加载菜单失败:", error)
      }
    },

    // 根据权限过滤菜单
    filterMenusByPermission(menus) {
      return menus.filter(menu => {
        // 检查当前菜单权限
        if (menu.permission && !checkPermission(menu.permission)) {
          return false
        }

        // 递归过滤子菜单
        if (menu.children && menu.children.length > 0) {
          menu.children = this.filterMenusByPermission(menu.children)
          // 如果子菜单全部被过滤掉，则隐藏父菜单
          return menu.children.length > 0
        }

        return true
      })
    },

    // 设置当前激活菜单
    setActiveMenu() {
      const pages = getCurrentPages()
      const currentPage = pages[pages.length - 1]
      this.activeMenu = currentPage.route
    }
  }
}
</script>
```

## 页面模板

管理端提供标准的CRUD页面模板，快速生成管理页面。

### 列表页模板

```vue
<!-- pages_template/list/list.vue -->
<template>
  <view class="page-body">
    <!-- 搜索区域 -->
    <vk-data-table-query
      v-model="queryForm.formData"
      :columns="queryForm.columns"
      @search="search"
      @reset="reset"
    >
      <template slot="right-btns">
        <el-button
          v-permission="addPermission"
          type="success"
          @click="addBtn"
        >
          添加
        </el-button>
        <el-button
          v-permission="deletePermission"
          type="danger"
          :disabled="selection.length === 0"
          @click="batchDeleteBtn"
        >
          批量删除
        </el-button>
      </template>
    </vk-data-table-query>

    <!-- 表格区域 -->
    <vk-data-table
      ref="table1"
      :columns="table1.columns"
      :request="table1.request"
      :pagination="table1.pagination"
      @selection-change="selectionChange"
    >
      <template slot="action" slot-scope="{ row }">
        <el-button
          v-permission="editPermission"
          size="mini"
          @click="editBtn(row)"
        >
          编辑
        </el-button>
        <el-button
          v-permission="deletePermission"
          size="mini"
          type="danger"
          @click="deleteBtn(row)"
        >
          删除
        </el-button>
      </template>
    </vk-data-table>
  </view>
</template>

<script>
const vk = uni.vk

export default {
  data() {
    return {
      tableName: "",           // 表名
      addPermission: "",       // 添加权限
      editPermission: "",      // 编辑权限
      deletePermission: "",    // 删除权限
      selection: [],           // 选中的行

      // 搜索表单配置
      queryForm: {
        formData: {},
        columns: []
      },

      // 表格配置
      table1: {
        columns: [],
        request: {
          url: "",
          data: {}
        },
        pagination: {
          pageSize: 20
        }
      }
    }
  },

  onLoad(options) {
    this.tableName = options.tableName || ""
    this.initPageConfig()
    this.loadTableConfig()
  },

  methods: {
    // 初始化页面配置
    initPageConfig() {
      const config = this.getTableConfig(this.tableName)
      if (config) {
        this.addPermission = config.addPermission
        this.editPermission = config.editPermission
        this.deletePermission = config.deletePermission

        this.queryForm.columns = config.queryColumns || []
        this.table1.columns = config.tableColumns || []
        this.table1.request.url = config.listUrl || ""
      }
    },

    // 获取表格配置
    getTableConfig(tableName) {
      const configs = {
        "users": {
          addPermission: "user:add",
          editPermission: "user:edit",
          deletePermission: "user:delete",
          listUrl: "admin/system/user/getList",
          queryColumns: [
            { key: "name", title: "姓名", type: "text" },
            { key: "status", title: "状态", type: "select", options: [
              { value: "", text: "全部" },
              { value: 1, text: "启用" },
              { value: 0, text: "禁用" }
            ]}
          ],
          tableColumns: [
            { key: "name", title: "姓名", width: 150 },
            { key: "email", title: "邮箱", width: 200 },
            { key: "status", title: "状态", width: 100, type: "tag" },
            { key: "create_time", title: "创建时间", width: 180, type: "time" },
            { key: "action", title: "操作", width: 150, type: "action" }
          ]
        }
        // 可以添加更多表的配置
      }

      return configs[tableName]
    },

    // 搜索
    search() {
      this.table1.request.data = { ...this.queryForm.formData }
      this.$refs.table1.refresh()
    },

    // 重置
    reset() {
      this.queryForm.formData = {}
      this.search()
    },

    // 添加
    addBtn() {
      uni.navigateTo({
        url: `/pages_template/add/add?tableName=${this.tableName}`
      })
    },

    // 编辑
    editBtn(row) {
      uni.navigateTo({
        url: `/pages_template/edit/edit?tableName=${this.tableName}&id=${row._id}`
      })
    },

    // 删除
    async deleteBtn(row) {
      await this.confirmDelete([row._id])
    },

    // 批量删除
    async batchDeleteBtn() {
      const ids = this.selection.map(item => item._id)
      await this.confirmDelete(ids)
    },

    // 确认删除
    async confirmDelete(ids) {
      try {
        await vk.alert("确定要删除吗？", {
          confirmButtonText: "确定",
          cancelButtonText: "取消"
        })

        const res = await vk.callFunction({
          url: "admin/system/common/delete",
          data: {
            tableName: this.tableName,
            ids: ids
          }
        })

        if (res.code === 0) {
          vk.toast("删除成功")
          this.$refs.table1.refresh()
        }
      } catch (error) {
        // 用户取消删除
      }
    },

    // 选择变化
    selectionChange(selection) {
      this.selection = selection
    }
  }
}
</script>
```

## 最佳实践

### 1. 组件复用

```javascript
// 创建可复用的表格配置
const createTableConfig = (tableName, options = {}) => {
  const baseConfig = {
    request: {
      url: `admin/system/${tableName}/getList`,
      data: {}
    },
    pagination: {
      pageSize: 20
    }
  }

  return {
    ...baseConfig,
    ...options
  }
}

// 使用
const userTableConfig = createTableConfig("user", {
  columns: [
    { key: "name", title: "姓名", width: 150 },
    { key: "email", title: "邮箱", width: 200 }
  ]
})
```

### 2. 统一错误处理

```javascript
// 全局错误处理
const handleAdminError = (error, context = "") => {
  console.error(`${context}错误:`, error)

  if (error.code === "NO_PERMISSION") {
    vk.toast("没有操作权限")
    return
  }

  if (error.code === "TOKEN_EXPIRED") {
    vk.toast("登录已过期，请重新登录")
    uni.reLaunch({ url: "/pages/login/login" })
    return
  }

  vk.toast(error.message || "操作失败")
}
```

### 3. 数据缓存

```javascript
// 管理端数据缓存
const adminCache = {
  // 缓存用户信息
  setUserInfo(userInfo) {
    uni.setStorageSync("admin_user_info", userInfo)
  },

  getUserInfo() {
    return uni.getStorageSync("admin_user_info")
  },

  // 缓存菜单数据
  setMenus(menus) {
    uni.setStorageSync("admin_menus", menus)
  },

  getMenus() {
    return uni.getStorageSync("admin_menus")
  },

  // 清除所有缓存
  clear() {
    uni.removeStorageSync("admin_user_info")
    uni.removeStorageSync("admin_menus")
  }
}
```
