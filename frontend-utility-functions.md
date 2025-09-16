---
type: "manual"
description: "工具函数规范"
---

# 工具函数

## 清明节灰色页面

```javascript
// 在页面中使用
export default {
  computed: {
    pageClass() {
      let classStr = ""
      if (vk.pubfn.timeUtil.isQingming()) {
        classStr = "gray-view"
      }
      return classStr
    }
  }
}
```

```vue
<template>
  <view :class="pageClass">
    <!-- 页面内容 -->
  </view>
</template>

<style>
.gray-view {
  filter: grayscale(100%);
}
</style>
```

## 错误处理规范

```javascript
// 全局错误处理
const handleError = (error, context = '') => {
  console.error(`${context}错误:`, error)

  // 根据错误类型进行不同处理
  if (error.code === 'TOKEN_INVALID') {
    // token失效，跳转登录
    uni.reLaunch({ url: '/pages/auth/login' })
    return
  }

  // 显示错误信息
  uni.showToast({
    title: error.message || '操作失败',
    icon: 'none',
    duration: 2000
  })
}

// 异步操作错误处理
const safeAsyncCall = async (asyncFn, errorContext = '') => {
  try {
    return await asyncFn()
  } catch (error) {
    handleError(error, errorContext)
    return null
  }
}
```

## 性能优化规范

```javascript
// 防抖函数
const debounce = (fn, delay = 300) => {
  let timer = null
  return function (...args) {
    if (timer) clearTimeout(timer)
    timer = setTimeout(() => fn.apply(this, args极狐delay)
  }
}

// 节流函数
const throttle = (fn, delay = 300) => {
  let timer = null
  return function (...args) {
    if (timer) return
    timer = setTimeout(() => {
      fn.apply(this, args)
      timer = null
    }, delay)
  }
}

// 使用示例
const handleSearch = debounce((keyword) => {
  // 搜索逻辑
}, 500)

const handleScroll = throttle((e) => {
  // 滚动处理逻辑
}, 100)
```

## 数据验证规范

```javascript
// 表单验证规则
const validationRules = {
  required: (value, message = '此字段为必填项') => {
    return value !== null && value !== undefined && value !== '' || message
  },

  email: (value, message = '请输入正确的邮箱地址') => {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
    return !value || emailRegex.test(value) || message
  },

  mobile: (value, message = '请输入正确的手机号') => {
    const mobileRegex = /^1[3-9]\d{9}$/
    return !value || mobileRegex.test(value) || message
  },

  minLength: (min) => (value, message = `最少输入${min}个字符`) => {
    return !value || value.length >= min || message
  }
}

// 表单验证使用
const validateForm = (formData, rules) => {
  const errors = {}

  for (const field in rules) {
    const fieldRules = rules[field]
    const value = formData[field]

    for (const rule of fieldRules) {
      const result = rule(value)
      if (result !== true) {
        errors[field] = result
        break
      }
    }
  }

  return {
    isValid: Object.keys(errors).length === 0,
    errors
  }
}