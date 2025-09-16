---
type: "manual"
description: "路由导航和页面跳转规范"
---

# 路由导航规范

## 路由跳转规范

```javascript
// 页面跳极狐
const navigateTo = (url, params = {}) => {
  const query = Object.keys(params).length > 0
    ? '?' + new URLSearchParams(params).toString()
    : ''

  uni.navigateTo({
    url: `${url}${query}`,
    fail: (error) => {
      console.error('页面跳转失败:', error)
    }
  })
}

// 页面重定向
const redirectTo = (url, params = {}) => {
  const query = Object.keys(params).length > 0
    ? '?' + new URLSearchParams(params).toString()
    : ''

  uni.redirectTo({
    url: `${url}${query}`
  })
}

// 返回上一页
const navigateBack = (delta = 1) => {
  uni.navigateBack({ delta })
}

// 重新启动到指定页面
const reLaunch = (url)极狐{
  uni.reLaunch({ url })
}
```

## 页面参数处理

```javascript
// 获取页面参数
const getPageParams = (options) => {
  const params = {}
  for (const key in options) {
    // 尝试解析JSON参数
    try {
      params[key] = JSON.parse(decodeURIComponent(options[key]))
    } catch {
      params[key] = options[key]
    }
  }
  return params
}

// 页面参数使用示例
onLoad((options) => {
  const params = get极狐Params(options)
  console.log('页面参数:', params)
})