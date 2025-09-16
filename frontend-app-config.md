---
type: "manual"
description: "应用配置规范 - 基于 vk-unicloud 框架"
---

# 应用配置

`app.config.js` 是 vk-unicloud 框架的核心配置文件，用于统一管理前端应用的各项配置。

## 完整的 app.config.js 配置

```javascript
// 引入自定义公共函数
import myPubFunction from '@/common/function/myPubFunction.js'

export default {
  // 开发模式启用调试模式(请求时会打印日志)
  debug: process.env.NODE_ENV !== 'production',

  // 主云函数名称
  functionName: "router",

  // 登录页面路径
  login: {
    url: '/pages_template/uni-id/login/index/index'
  },

  // 首页页面路径
  index: {
    url: '/pages/index/index'
  },

  // 404 Not Found 错误页面路径
  error: {
    url: '/pages/error/404/404'
  },

  // 前端默认时区（中国为8）
  targetTimezone: 8,

  // 日志风格配置
  logger: {
    colorArr: [
      "#0095f8",
      "#67C23A"
    ]
  },

  // 应用主题颜色配置
  color: {
    main: "#ff4444",
    secondary: "#555555"
  },

  // 登录检查配置
  checkTokenPages: {
    /**
     * mode 模式说明：
     * 0: 自动检测
     * 1: 白名单模式 - list内的页面需要登录，不在list内的页面不需要登录
     * 2: 黑名单模式 - list内的页面不需要登录，不在list内的页面需要登录
     * 
     * 注意事项：
     * 1. list内使用通配符表达式，非正则表达式
     * 2. 需要使用 vk.navigateTo 代替 uni.navigateTo 进行页面跳转才会生效
     * 3. tabbar 页面需要手动在 onLoad 里加 vk.pubfn.checkLogin()
     * 4. 在无需登录的页面上执行kh或sys函数，也会自动判断是否登录
     */
    mode: 2,
    list: [
      "/pages_template/*",
      "/pages/login/*",
      "/pages/index/*",
      "/pages/error/*"
    ]
  },

  // 分享检查配置（仅小程序有效）
  checkSharePages: {
    /**
     * mode 模式说明：
     * 0: 不做处理
     * 1: 白名单模式 - list内的页面可以被分享，不在list内的页面不可以被分享
     * 2: 黑名单模式 - list内的页面不可以被分享，不在list内的页面可以被分享
     */
    mode: 0,
    menus: ['shareAppMessage'],
    list: [
      "/pages/index/*",
      "/pages/goods/*",
      "/pages_template/*"
    ]
  },

  // 加密通信检查配置
  checkEncryptRequest: {
    /**
     * mode 模式说明：
     * 0: 不做处理
     * 1: 白名单模式 - list内的云函数或云对象需要加密通信，不在list内的不需要加密通信
     * 
     * 注意事项：
     * 1. list内使用正则表达式，非通配符表达式
     * 2. 建议与 router/middleware/modules/encryptFilter.js 内的 regExp 保持一致
     */
    mode: 1,
    list: [
      "^template/test/pub/testEncryptRequest$", // 指定云函数需要加密通信
      "^template/encrypt/(.*)" // 指定前缀的云函数或云对象需要加密通信
    ]
  },

  // 静态文件资源URL配置
  staticUrl: {
    logo: '/static/logo.png',
    defaultAvatar: '/static/default-avatar.png'
  },

  // 自定义公共函数（通过 vk.myfn.xxx() 调用）
  myfn: myPubFunction,

  // 第三方服务配置
  service: {
    // 云存储配置
    cloudStorage: {
      /**
       * 默认存储提供商：
       * - unicloud: 空间内置存储（默认）
       * - extStorage: 扩展存储
       * - aliyun: 阿里云OSS
       */
      defaultProvider: "unicloud",

      // 空间内置存储配置
      unicloud: {
        // 暂无配置项
      },

      // 扩展存储配置（七牛云）
      extStorage: {
        provider: "qiniu",
        dirname: "public", // 根目录名称
        authAction: "user/pub/getUploadFileOptionsForExtStorage", // 鉴权云函数
        domain: "", // 自定义域名
        groupUserId: false // 是否按用户ID分组存储
      },

      // 阿里云OSS配置
      aliyun: {
        uploadData: {
          OSSAccessKeyId: "",
          policy: "",
          signature: ""
        },
        action: "https://xxx.oss-cn-hangzhou.aliyuncs.com", // OSS上传地址
        dirname: "public", // 根目录名称
        host: "https://xxx.xxx.com", // OSS外网访问地址
        groupUserId: false // 是否按用户ID分组存储
      }
    }
  },

  // 全局异常码配置
  globalErrorCode: {
    "cloudfunction-unusual-timeout": "请求超时，但请求还在执行，请重新进入页面。",
    "cloudfunction-timeout": "请求超时，请重试！",
    "cloudfunction-system-error": "网络开小差了！",
    "cloudfunction-reaches-burst-limit": "系统繁忙，请稍后再试。",
    "cloudfunction-network-unauthorized": "需要进行网络请求许可，若您已授权，请点击确定"
  },

  // 自定义拦截器
  interceptor: {
    // login: function(obj) {
    //   let { vk, params, res } = obj;
    //   if (!params.noAlert) {
    //     vk.alert(res.msg);
    //   }
    //   console.log("跳自己的登录页面");
    //   // 自定义登录处理逻辑
    // },

    // fail: function(obj) {
    //   let { vk, params, res } = obj;
    //   return false; // 返回false取消框架内置fail逻辑
    // }
  }
}
```

## 配置使用方式

### 获取配置信息

```javascript
// 获取完整配置（包含函数）
const config = vk.getConfig()

// 获取特定配置项
const loginUrl = vk.getConfig("login.url")
const mainColor = vk.getConfig("color.main")
const debugMode = vk.getConfig("debug")

// 在Vue模板中使用
// {{ vk.getConfig('staticUrl.logo') }}
// {{ vk.getConfig('color.main') }}
```

### 通过Vuex获取配置（不包含函数）

```javascript
// 获取Vuex中的配置（不包含函数）
const config = vk.getVuex('$app.config')
const themeColor = vk.getVuex('$app.config.color.main')
const staticLogo = vk.getVuex('$app.config.staticUrl.logo')
```

### 自定义函数调用

```javascript
// 调用自定义函数（需要在myPubFunction.js中定义）
const result = vk.myfn.formatPrice(1999) // 返回 "19.99"

// 在模板中调用
// {{ vk.myfn.formatPrice(product.price) }}
```

## 配置说明

### 1. 调试模式 (debug)
- 开发环境自动启用，生产环境自动关闭
- 启用后会在控制台输出详细的请求日志

### 2. 页面路径配置
- **login**: 登录页面路径
- **index**: 首页页面路径  
- **error**: 404错误页面路径

### 3. 权限检查配置
- **checkTokenPages**: 登录状态检查
- **checkSharePages**: 分享权限检查（小程序）
- **checkEncryptRequest**: 加密通信检查

### 4. 云存储配置
支持多种存储提供商，可根据需求灵活配置：
- Unicloud内置存储
- 七牛云扩展存储
- 阿里云OSS

### 5. 全局异常处理
统一处理各种网络异常情况，提供友好的用户提示。

## 最佳实践

1. **环境区分**: 利用 `debug` 配置区分开发和生产环境
2. **路径管理**: 统一管理所有页面路径，便于维护
3. **权限控制**: 合理配置权限检查，确保系统安全
4. **错误处理**: 配置友好的错误提示信息
5. **存储策略**: 根据业务需求选择合适的存储方案

## 注意事项

1. 配置已自动注入Vuex，可通过 `vk.getVuex('$app.config')` 获取
2. 自定义函数需要通过 import 引入后再赋值给 myfn
3. 通配符表达式与正则表达式在使用时要注意区分
4. 加密通信配置需要前后端保持一致