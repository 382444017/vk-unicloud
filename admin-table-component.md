---
type: "manual"
description: "万能表格组件开发规范和配置"
---

# 万能表格组件

万能表格是管理端的核心组件，通过JSON配置快速生成CRUD页面，支持丰富的功能和高度自定义。

## 核心思想

通过 JSON 配置渲染页面（简单配置一下，表格就完成了）

**示例配置**：
```javascript
[
  { key: "avatar", title: "头像", type: "avatar", width: 80, shape:"circle" },
  { key: "_add_time", title: "添加时间", type: "time", width: 160, valueFormat:"yyyy-MM-dd hh:mm:ss" }
]
```

## 基础用法

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
      :pagination极速开发1.pagination"
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
```

## 核心功能特性

### 1. 查询功能
- ✅ 分页查询 - 支持标准分页和自定义分页配置
- ✅ 多条件搜索 - 支持多个搜索条件的组合查询
- ✅ 搜索项折叠 - 支持折叠搜索区域，节省页面空间
- ✅ 多表连表查询 - 支持复杂的多表关联查询
- ✅ 数据源预处理 - 支持查询前的数据预处理

### 2. 表格操作
- ✅ 多选框 - 支持批量选择和操作
- ✅ 自动详情页 - 点击详情按钮自动显示详情弹窗
- ✅ 自动修改表单 - 点击修改按钮自动显示修改表单
- ✅ 删除确认 - 删除操作前进行二次确认
- ✅ 自定义操作按钮 - 支持发货、审核等自定义按钮

### 3. 高级功能
- ✅ 展开行 - 支持行展开显示详细信息
- ✅ 树状结构 - 支持树形表格结构
- ✅ 导出Excel - 一行代码导出表格数据
- ✅ 大数据导出 - 支持导出数据库内全部数据（突破500条限制）
- ✅ 异常重试机制 - 连接失败时自动重试查询
- ✅ 请求兼容 - 支持unicloud云函数和HTTP请求
- ✅ 合计显示 - 支持表格底部显示数据合计

### 4. 高自由度设计
- ✅ 字段插槽 - 每个字段都支持插槽自定义渲染
- ✅ 原生Element支持 - 支持直接使用Element原生代码
- ✅ 样式自定义 - 支持完全自定义表格样式

## 表格列配置

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
  }
]
```

## 搜索表单配置

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
    }
  ]
}
```

## 字段类型支持

万能表格支持丰富的字段类型，满足各种业务场景：

### 基础字段类型
- **text** - 文本显示
- **number** - 数字显示
- **html** - HTML内容渲染
- **image** - 图片显示（支持头像、商品图等）
- **avatar** - 圆形头像
- **time** - 时间格式化显示
- **date** - 日期格式化显示
- **datetime** - 日期时间格式化显示
- **tag** - 标签显示（支持颜色配置）
- **progress** - 进度条显示
- **rate** - 评分显示
- **color** - 颜色块显示
- **link** - 链接跳转
- **button** - 按钮操作

### 高级字段类型
- **json** - JSON数据格式化显示
- **code** - 代码高亮显示
- **map** - 地图位置显示
- **qrcode** - 二维码生成和显示
- **file** - 文件下载链接
- **audio** - 音频播放
- **video** - 视频播放

### 业务字段类型
- **money** - 金额格式化
- **percent** - 百分比显示
- **status** - 状态标识
- **badge** - 徽章显示
- **switch** - 开关状态显示
- **checkbox** - 多选状态显示
- **radio** - 单选状态显示

## 查询模式详解

`mode` 参数用于指定字段的查询模式，支持多种查询方式：

| mode值 | 说明 | 示例 | 适用场景 |
|--------|------|------|----------|
| `=` | 完全匹配 | `name = "张三"` | 精确查询 |
| `%%` | 模糊匹配 | `name LIKE "%张%"` | 文本搜索 |
| `[]` | 范围查询 | `date BETWEEN start AND end` | 时间范围 |
| `in` | 在数组里 | `status IN [1,2]` | 多选查询 |

## 表格请求配置

```javascript
// 表格请求配置
const tableRequest = {
  url: "admin/system/user/getList",  // 云函数路径
  method: "POST",                    // 请求方法
  data: {                           // 请求参数
    // 搜索条件会自动合并到这里
  }
}

// 分页配置
const tablePagination = {
  pageSize: 20,           // 每页条数
  pageSizes: [10, 20, 50, 100],  // 每页条数选项
  layout: "total, sizes, prev, pager, next, jumper"  // 分页组件布局
}