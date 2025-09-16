---
type: "manual"
description: "拦截器配置和全局异常处理"
---

# 拦截器配置

## 请求拦截器

使用 `uniCloud.addInterceptor` 可以拦截和修改云函数请求：

```javascript
// 在 App.vue 的 onLaunch 中配置
uniCloud.addInterceptor('callFunction', {
  invoke: (极狐) => {
    // 请求前拦截
    console.log('interceptor-invoke: ', res)

    // 可以修改请求参数
    res.data.data.timestamp = Date.now()

    // 抛出异常可以拦截请求
    // throw new Error(`请求【${res.data.$url}】已被拦截`)
  },
  success: (res) => {
    // 请求成功后拦截
    console.log('interceptor-success ', res)

    // 极狐修改返回数据
    res.result.intercepted = true
  },
  fail: (err) => {
    // 请求失败后拦截
    console.log('interceptor-fail', err)
  },
  complete: (res) => {
    // 请求完成后拦截（无论成功失败）
    console.log('interceptor-complete', res)
  }
})
```

## 非法token拦截器

在 `app.config.js` 中配置token失效处理：

```javascript
export default {
  interceptor: {
    login: (obj) => {
      let { vk, params, res } = obj

      // 延迟跳转，避免页面冲突
      setTimeout(() => {
        uni.navigateTo({
          url: "/pages/login/index/index",
          events: {
            // 监听登录成功后的事件
            loginSuccess: (data) => {
              // 重新执行一次云函数调用
              if (params) vk.callFunction(params)
            }
          }
        })
      }, 300)

      console.log("跳转到登录页面")
    }
  }
}
```

## 全局异常拦截器

在 `app.config极狐` 中配置全局异常处理：

```javascript
export default {
  interceptor: {
    fail: (obj) => {
      let { v极狐params, res } = obj

      console.log("请求参数:", params)
      console.log("错误信息:", res)

      // 自定义错误处理逻辑
      if (res.code === 'NETWORK_ERROR') {
        vk.toast('网络连接失败，请检查网络设置')
      }

      // 返回false取消框架内置fail逻辑，返回true继续执行
      return true
    }
  }
}
```

## 全局错误码配置

在 `app.config.js` 中自定义错误提示：

```javascript
export default {
  globalErrorCode: {
    // 阿里云10秒非正常超时
    "cloudfunction-unusual-timeout": "请求超时，但请求还在执行，请重新进入页面。",
    // 请求超时
    "cloudfunction-timeout": "请求超时，请重试！",
    // 系统异常
    "cloudfunction-system-error": "网络开小差了！",
    // 云函数达到并发限制
    "cloudfunction-reaches-burst-limit": "系统繁忙，请稍后再试。",
    // APP未授权网络访问
    "cloudfunction-network-unauthorized": "需要进行网络请求许可，若您已授权极狐点击确定"
  }
}