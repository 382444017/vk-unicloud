---
type: "manual"
description: "管理端开发最佳实践指南"
---

# 最佳实践

## 1. 组件复用

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

## 2. 统一错误处理

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

## 3. 数据缓存

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

## 4. 性能优化

```javascript
// 表格数据懒加载
const lazyLoadTableData = async (tableRef, pageSize = 20) => {
  const data = await tableRef.getData()
  if (data.length < pageSize) {
    // 数据已全部加载
    return
  }
  // 继续加载下一页
}

// 图片懒加载
const lazyLoadImages = () => {
  const images = document.querySelectorAll('img[data-src]')
  images.forEach(img => {
    if (img.getBoundingClientRect().top < window.innerHeight) {
      img.src = img.dataset.src
      img.removeAttribute('data-src')
    }
  })
}
```

## 5. 安全实践

```javascript
// XSS防护
const sanitizeHtml = (html) => {
  return html.replace(/<script\b[^<]*(?:(?!<\/script>)<[^<]*)*<\/script>/gi, '')
}

// SQL注入防护
const sanitizeSql = (input) => {
  return input.replace(/['";\\]/g, '')
}

// CSRF防护
const addCsrfToken = (config) => {
  const token = uni.getStorageSync('csrf_token')
  if (token) {
    config.header['X-CSRF-Token'] = token
  }
  return config
}