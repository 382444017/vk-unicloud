---
type: "manual"
description: "管理端权限系统规范和实现"
---

# 权限管理系统

管理端基于RBAC（基于角色的访问控制）模型实现完整的权限管理系统，支持细粒度的权限控制。

## RBAC模型架构

### 1. 核心概念
- **用户 (User)** - 系统的使用者
- **角色 (Role)** - 权限的集合，如管理员、编辑、查看者
- **权限 (Permission)** - 具体的操作权限，如 `user:add`, `user:edit`
- **资源 (Resource)** - 被保护的对象，如用户、订单、商品

### 2. 权限层级结构
```
用户 → 角色 → 权限 → 资源
```

### 3. 权限格式规范
权限采用 `资源:操作` 的格式：
- `user:view` - 查看用户
- `user:add` - 添加用户
- `user:edit` - 编辑用户
- `user:delete` - 删除用户
- `order:export` - 导出订单
- `system:config` - 系统配置

## 内置角色

框架内置了以下标准角色：

### 超级管理员 (super_admin)
- 拥有所有权限
- 可以管理所有用户和角色
- 不受权限限制

### 系统管理员 (admin)
- 拥有大部分管理权限
- 可以管理用户和分配角色
- 受权限系统控制

### 普通用户 (user)
- 只有基本的查看权限
- 需要具体权限分配

## 完整的权限检查方案

### 1. 核心权限检查方法

```javascript
/**
 * 检查用户是否拥有指定权限
 * @param {string} permission - 权限标识，格式：资源:操作
 * @returns {boolean} 是否拥有权限
 */
const checkPermission = (permission) => {
  const userInfo = vk.getStorageSync("userInfo")
  
  // 用户信息不存在
  if (!userInfo) {
    console.warn("用户信息不存在，权限检查失败")
    return false
  }

  // 权限列表不存在
  if (!userInfo.permission || !Array.isArray(userInfo.permission)) {
    console.warn("用户权限列表不存在或格式错误")
    return false
  }

  // 超级管理员拥有所有权限
  if (userInfo.role && userInfo.role.includes("super_admin")) {
    return true
  }

  // 检查通配符权限
  if (userInfo.permission.includes("*")) {
    return true
  }

  // 检查具体权限
  return userInfo.permission.includes(permission)
}

/**
 * 检查用户是否拥有任一权限
 * @param {string[]} permissions - 权限标识数组
 * @returns {boolean} 是否拥有任一权限
 */
const checkAnyPermission = (permissions) => {
  return permissions.some(permission => checkPermission(permission))
}

/**
 * 检查用户是否拥有所有权限
 * @param {string[]} permissions - 权限标识数组
 * @returns {boolean} 是否拥有所有权限
 */
const checkAllPermissions = (permissions) => {
  return permissions.every(permission => checkPermission(permission))
}
```

### 2. 页面权限检查

```javascript
// 在页面组件中使用权限检查
export default {
  data() {
    return {
      // 页面操作权限状态
      hasAddPermission: false,
      hasEditPermission: false,
      hasDeletePermission: false,
      hasExportPermission: false,
      
      // 批量操作权限
      hasBatchDeletePermission: false,
      hasBatchExportPermission: false,
      
      // 页面查看权限
      hasViewPermission: false
    }
  },

  onLoad() {
    // 检查页面访问权限
    if (!this.checkPageAccess()) {
      return
    }
    
    // 初始化页面操作权限
    this.initPagePermissions()
  },

  methods: {
    /**
     * 检查页面访问权限
     */
    checkPageAccess() {
      const currentRoute = getCurrentPages().pop().route
      const pagePermission = this.getPagePermission(currentRoute)
      
      if (pagePermission && !checkPermission(pagePermission)) {
        uni.showToast({
          title: "没有访问权限",
          icon: "none",
          duration: 2000
        })
        
        // 延迟返回，确保Toast显示
        setTimeout(() => {
          uni.navigateBack()
        }, 2000)
        
        return false
      }
      
      return true
    },
    
    /**
     * 根据路由获取页面权限
     */
    getPagePermission(route) {
      const permissionMap = {
        "/pages/user/list": "user:view",
        "/pages/user/add": "user:add",
        "/pages/user/edit": "user:edit",
        "/pages/user/detail": "user:view",
        "/pages/order/list": "order:view",
        "/pages/order/export": "order:export"
      }
      
      return permissionMap[route]
    },
    
    /**
     * 初始化页面操作权限
     */
    initPagePermissions() {
      this.hasAddPermission = checkPermission("user:add")
      this.hasEditPermission = checkPermission("user:edit")
      this.hasDeletePermission = checkPermission("user:delete")
      this.hasExportPermission = checkPermission("user:export")
      
      this.hasBatchDeletePermission = checkAllPermissions(["user:delete", "user:batch"])
      this.hasBatchExportPermission = checkAllPermissions(["user:export", "user:batch"])
      
      this.hasViewPermission = checkPermission("user:view")
    },
    
    /**
     * 操作前的权限检查
     */
    beforeAction(action, row) {
      const actionPermissions = {
        edit: "user:edit",
        delete: "user:delete",
        export: "user:export"
      }
      
      if (actionPermissions[action] && !checkPermission(actionPermissions[action])) {
        uni.showToast({
          title: "没有操作权限",
          icon: "none"
        })
        return false
      }
      
      return true
    }
  }
}
```
```

## 权限指令

```javascript
// 自定义权限指令
Vue.directive("permission", {
  inserted(el, binding) {
    const permission = binding.value
    if (!checkPermission(permission)) {
      el.style.display = "none"
    }
  }
})

// 在模板中使用
// <el-button v-permission="'user:add'" @click="addUser">添加用户</el-button>
```

## 路由权限守卫

```javascript
// 路由权限配置
const routePermissions = {
  "/pages/user/list": "user:view",
  "/pages/user/add": "user:add",
  "/pages/user/edit": "user:edit"
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

## 完整的权限指令实现

### 1. 基础权限指令

```javascript
/**
 * 权限指令 v-permission
 * 用法: v-permission="'user:add'" 或 v-permission="['user:add', 'user:edit']"
 */
Vue.directive("permission", {
  inserted(el, binding) {
    const permissions = Array.isArray(binding.value) ? binding.value : [binding.value]
    
    if (!checkAnyPermission(permissions)) {
      el.style.display = "none"
    }
  },
  
  update(el, binding) {
    const permissions = Array.isArray(binding.value) ? binding.value : [binding.value]
    
    if (checkAnyPermission极速开发)) {
      el.style.display = ""
    } else {
      el.style.display = "none"
    }
  }
})

// 使用示例
// <el-button v-permission="'user:add'">添加用户</el-button>
// <el-button v-permission="['user:add', 'user:edit']">编辑用户</el-button>
```

### 2. 高级权限指令

```javascript
/**
 * 权限指令 v-permission-all
 * 需要拥有所有指定权限
 */
Vue.directive("permission-all", {
  inserted(el, binding) {
    const permissions = Array.isArray(binding.value) ? binding.value : [binding.value]
    
    if (!checkAllPermissions(permissions)) {
      el.style.display = "none"
    }
  }
})

/**
 * 权限指令 v-permission-any
 * 拥有任一权限即可显示
 */
Vue.directive("permission-any", {
  inserted(el, binding) {
    const permissions = Array.isArray(binding.value) ? binding.value : [binding.value]
    
    if (!checkAnyPermission(permissions)) {
      el.style.display = "none"
    }
  }
})

/**
 * 权限指令 v-permission-role
 * 检查用户角色
 */
Vue.directive("permission-role", {
  inserted(el, binding) {
    const roles = Array.isArray(binding.value) ? binding.value : [binding.value]
    const userInfo = vk.getStorageSync("userInfo")
    
    if (!userInfo || !userInfo.role || !roles.some(role => userInfo.role.includes(role))) {
      el.style.display = "none"
    }
  }
})

// 使用示例
// <el-button v-permission-all="['user:add', 'user:edit']">需要同时有添加和编辑权限</el-button>
// <el-button v-permission-any="['user:add', 'user:edit']">有任一权限即可</el-button>
// <el-button v-permission-role="'admin'">管理员可见</el-button>
```

### 3. 权限指令工具函数

```javascript
/**
 * 权限指令工具函数
 * 可以在组件中直接使用
 */
export const permissionUtils = {
  /**
   * 检查并返回元素显示状态
   */
  checkElementPermission(permissions, checkAll = false) {
    if (checkAll) {
      return checkAllPermissions(permissions) ? "" : "none"
    } else {
      return checkAnyPermission(permissions) ? "" : "none"
    }
  },
  
  /**
   * 检查按钮是否可用
   */
  checkButtonDisabled(极速开发, checkAll = false) {
    if (checkAll) {
      return !checkAllPermissions(permissions)
    } else {
      return !checkAnyPermission(permissions)
    }
  },
  
  /**
   * 检查菜单是否显示
   */
  checkMenuVisible(menu) {
    if (!menu.permission) {
      return true
    }
    
    const permissions = Array.isArray(menu.permission) ? menu.permission : [menu.permission]
    return checkAnyPermission(permissions)
  }
}
```