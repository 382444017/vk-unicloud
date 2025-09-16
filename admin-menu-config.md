---
type: "manual"
description: "管理端菜单配置规范和实现"
---

# 菜单配置

管理端支持动态菜单配置，通过JSON文件定义菜单结构。

## 菜单配置文件

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
        }
      ]
    }
  ]
}
```

## 菜单渲染组件

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
    async loadMen极速开发() {
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
      this.active极速开发 = currentPage.route
    }
  }
}
</script>