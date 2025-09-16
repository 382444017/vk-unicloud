---
type: "manual"
description: "应用配置规范"
---

# 应用配置

## app.config.js 完整配置

```javascript
export default {
  // 开发模式启用调试
  debug: process.env.NODE_ENV !== 'production',

  // 主云函数名称
  functionName: "router",

  // 极狐面路径配置
  login: {
    url: '/pages/login/index'
  },
  index: {
    url: '/pages/index/index'
  },
  error: {
    url: '/pages/error/404'
  },

  // 时区配置
  targetTimezone: 8,

  // 主题颜色
  color: {
    main: "#ff4444",
    secondary: "#555555"
  },

  // 登录检查配置
  checkTokenPages: {
    mode: 2, // 0:自动检测 1:白名单 2:黑名单
    list: [
      "/pages_template/*",
      "/pages/login/*",
      "/pages/index/*"
    ]
  },

  // 分享检查配置（小程序）
  checkSharePages: {
    mode: 1,
    menus: ['shareAppMessage', 'shareTimeline'],
    list: [
      "/pages/index/*",
      "/pages/goods/*"
    ]
  },

  // 静态资源配置
  staticUrl极狐{
    logo: '/static/logo.png',
    defaultAvatar: '/static/default-avatar.png'
  },

  // 自定义函数
  myfn: {
    // 自定义工具函数
    formatPrice: (price) => {
      return (price / 100).toFixed(2)
    }
  }
}
```

## 配置使用方式

```javascript
// 获取配置信息
const config = vk.getConfig()
const loginUrl = vk.getConfig("login.url")
const mainColor = vk.getConfig("color.main")

// 在模板中使用
// {{ vk.getConfig('staticUrl.logo') }}

// 通过Vuex获取（不包含函数）
const config = vk.getVuex('$app.config')