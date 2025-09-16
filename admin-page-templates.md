---
type: "manual"
description: "管理端页面模板规范和最佳实践"
---

# 页面模板

管理端提供标准的CRUD页面模板，快速生成管理页面。

## 列表页模板

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
  },

  methods: {
    // 初始化页面配置
    initPageConfig() {
      const config = this.getTableConfig(this.tableName)
      if (config) {
        this.addPermission = config.addPermission
        this.editPermission = config.editPermission

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
            { key: "status", title: "状态", width: 100, type: "tag" }
          ]
        }
      }

      return configs[tableName]
    }
  }
}
</script>