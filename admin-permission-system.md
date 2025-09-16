---
type: "manual"
description: "管理端权限系统规范和实现"
---

# 权限管理

管理端基于RBAC（基于角色的访问控制）模型实现权限管理。

## 权限检查

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