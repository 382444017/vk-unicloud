---
type: "manual"
description: "万能表格组件开发规范和配置"
---

# 万能表格组件

万能表格是管理端的核心组件，通过JSON配置快速生成CRUD页面。

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

## 查询模式详解

`mode` 参数用于指定字段的查询模式：

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