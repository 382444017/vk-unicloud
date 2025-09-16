# 本地临时会话缓存

## 概述

本地临时会话缓存提供了一种在会话期间临时存储数据的机制。**注意：仅H5端支持，其他端均不支持。**

## 前端JS调用示例

### 储存缓存
```javascript
vk.setSessionStorageSync(key, value);
```

### 获取缓存
```javascript
let value = vk.getSessionStorageSync(key);
```

### 删除缓存
```javascript
vk.removeSessionStorageSync(key);
```

### 清空缓存
```javascript
vk.clearSessionStorageSync();
```

### 清除键值为指定字符串开头的缓存
```javascript
vk.clearSessionStorageSync(keyPrefix);
```

## 平台支持情况

| 平台 | 支持情况 | 说明 |
|------|----------|------|
| H5端 | ✅ 支持 | 使用sessionStorage |
| App端 | ❌ 不支持 | 无sessionStorage实现 |
| 微信小程序 | ❌ 不支持 | 无sessionStorage API |
| 支付宝小程序 | ❌ 不支持 | 无sessionStorage API |
| 百度小程序 | ❌ 不支持 | 无sessionStorage API |
| 抖音小程序 | ❌ 不支持 | 无sessionStorage API |

## 特性说明

### 1. 会话范围
- 同一个域名下的会话共享
- 不同域名之间的缓存隔离
- 同一个浏览器不同的页面窗口，缓存隔离

### 2. 存储限制
- 同一个域名，缓存最多存储5M
- 数据仅在当前会话期间有效
- 关闭浏览器标签页或窗口后数据自动清除

### 3. 生命周期
- 页面会话期间有效
- 刷新页面数据保留
- 关闭页面后数据清除
- 新开标签页创建新的会话

## 使用场景

### 1. 表单数据临时保存
```javascript
// 保存表单数据
vk.setSessionStorageSync('formData', {
  name: '张三',
  phone: '13800138000',
  address: '北京市朝阳区'
})

// 页面刷新后恢复数据
const formData = vk.getSessionStorageSync('formData')
if (formData) {
  // 填充表单数据
}
```

### 2. 页面状态管理
```javascript
// 保存页面滚动位置
window.addEventListener('scroll', () => {
  vk.setSessionStorageSync('scrollPosition', window.scrollY)
})

// 恢复滚动位置
const scrollPosition = vk.getSessionStorageSync('scrollPosition')
if (scrollPosition) {
  window.scrollTo(0, scrollPosition)
}
```

### 3. 临时用户状态
```javascript
// 记录用户操作状态
vk.setSessionStorageSync('userViewedTutorial', true)

// 检查用户是否已完成某个操作
const hasViewed = vk.getSessionStorageSync('userViewedTutorial')
if (!hasViewed) {
  // 显示引导教程
  showTutorial()
}
```

## 注意事项

### 1. 平台兼容性
```javascript
// 安全的sessionStorage操作函数
function safeSessionStorage() {
  // 检查是否支持sessionStorage
  if (typeof sessionStorage === 'undefined') {
    console.warn('当前平台不支持sessionStorage')
    return false
  }
  return true
}

// 使用前进行检查
if (safeSessionStorage()) {
  vk.setSessionStorageSync('key', 'value')
}
```

### 2. 数据类型限制
- 只能存储字符串类型数据
- 复杂对象需要JSON序列化
- 大数据量可能影响性能

### 3. 替代方案
对于不支持sessionStorage的平台，可以考虑以下替代方案：

#### LocalStorage + 时间戳
```javascript
// 模拟sessionStorage的行为
function setTempStorage(key, value, timeout = 24 * 60 * 60 * 1000) {
  const data = {
    value: value,
    expiry: Date.now() + timeout
  }
  vk.setStorageSync(key, data)
}

function getTempStorage(key) {
  const data = vk.getStorageSync(key)
  if (!data) return null
  
  if (Date.now() > data.expiry) {
    vk.removeStorageSync(key)
    return null
  }
  
  return data.value
}
```

#### Vuex状态管理
```javascript
// 使用Vuex管理会话状态
export default {
  state: {
    sessionData: {}
  },
  mutations: {
    setSessionData(state, { key, value }) {
      state.sessionData[key] = value
    },
    clearSessionData(state) {
      state.sessionData = {}
    }
  }
}
```

## 最佳实践

### 1. 数据序列化
```javascript
// 存储对象数据
const userData = { name: '张三', age: 25 }
vk.setSessionStorageSync('userData', JSON.stringify(userData))

// 读取对象数据
const storedData = vk.getSessionStorageSync('userData')
const userData = storedData ? JSON.parse(storedData) : null
```

### 2. 错误处理
```javascript
function safeSetSessionStorage(key, value) {
  try {
    vk.setSessionStorageSync(key, value)
    return true
  } catch (error) {
    console.error('设置sessionStorage失败:', error)
    return false
  }
}

function safeGetSessionStorage(key) {
  try {
    return vk.getSessionStorageSync(key)
  } catch (error) {
    console.error('获取sessionStorage失败:', error)
    return null
  }
}
```

### 3. 内存管理
```javascript
// 定期清理过期的会话数据
function cleanupSessionStorage() {
  const keys = Object.keys(sessionStorage)
  keys.forEach(key => {
    // 根据业务逻辑清理特定类型的数据
    if (key.startsWith('temp_')) {
      vk.removeSessionStorageSync(key)
    }
  })
}

// 页面卸载时清理
window.addEventListener('beforeunload', cleanupSessionStorage)
```

## 迁移建议

对于需要跨平台支持的项目，建议：

1. **使用统一的抽象层**: 封装平台特定的存储实现
2. **降级方案**: 为不支持的平台提供替代实现
3. **功能检测**: 在使用前检查平台支持情况
4. **用户提示**: 为不支持的功能提供友好的用户提示