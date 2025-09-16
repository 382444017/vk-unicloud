---
type: "manual"
description: "本地存储规范"
---

# 本地存储

## 持久化存储 (localStorage)

```javascript
// 存储数据
vk.setStorageSync('userSettings', {
  theme: 'dark',
  language: 'zh-CN'
})

// 获取数据
const userSettings = vk.getStorageSync('userSettings')

// 删除数据
vk.removeStorageSync('userSettings')

// 清空所有数据
vk.clearStorageSync()

// 清除指定前缀的数据
vk.clearStorageSync('user_') // 清除所有以 user_ 开头的key

// 获取存储信息
const storageInfo = vk.getStorageInfoSync()
console.log('存储大小:', storageInfo.currentSize)
```

## 临时会话存储 (sessionStorage)

**注意：仅H5端支持**

```javascript
// 存储临时数据
vk.setSessionStorageSync('tempData', {
  formData: {},
  currentStep: 1
})

// 获取临时数据
const tempData = vk.getSessionStorageSync('tempData')

// 删除临时数据
vk.removeSessionStorageSync('tempData')

// 清空所有临时数据
vk.clearSessionStorageSync()
```

## 存储最佳实践

```javascript
// 封装存储工具类
const StorageUtil = {
  // 用户相关数据
  user: {
    setInfo: (userInfo) => vk.setStorageSync('user_info', userInfo),
    getInfo: () => vk.getStorageSync('user_info') || {},
    clear: () => vk.removeStorageSync('user_info')
  },

  // 应用设置
  settings: {
    set: (settings) => vk.setStorageSync('app_settings', settings),
    get: () => vk.getStorageSync('app_settings') || {},
    update: (key, value) => {
      const settings = StorageUtil.settings.get()
      settings[key] = value
      StorageUtil.settings.set(settings)
    }
  },

  // 缓存管理
  cache: {
    set: (key, data, expireTime) => {
      const cacheData = {
        data: data,
        expire: Date.now() + (expireTime || 3600000) // 默认1小时
      }
      vk.setStorageSync(`cache_${key}`, cacheData)
    },

    get: (key) => {
      const cacheData = vk.getStorageSync(`cache_${key}`)
      if (!cacheData) return null

      if (Date.now() > cacheData.expire) {
        vk.removeStorageSync(`cache_${key}`)
        return null
      }

      return cacheData.data
    },

    clear: () => {
      const storageInfo = vk.getStorageInfoSync()
      storageInfo.keys.forEach(key => {
        if (key.startsWith('cache_')) {
          vk.removeStorageSync(key)
        }
      })
    }
  }
}