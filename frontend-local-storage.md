# 本地持久化缓存

## 概述

本地持久化缓存提供了一种在客户端长期存储数据的能力，不同平台有不同的实现方式和限制。

## 前端JS调用示例

### 储存缓存
```javascript
vk.setStorageSync(key, value);
```

### 获取缓存
```javascript
let value = vk.getStorageSync(key);
```

### 删除缓存
```javascript
vk.removeStorageSync(key);
```

### 清空缓存
```javascript
vk.clearStorageSync();
```

### 清除键值为指定字符串开头的缓存
```javascript
vk.clearStorageSync(keyPrefix);
```

### 获取缓存信息
```javascript
let storageInfo = vk.getStorageInfoSync();
```

## 各平台限制说明

### H5端
- 使用 localStorage
- 浏览器限制5M大小
- 缓存概念，可能会被浏览器清理
- 数据存储生命周期与浏览器缓存策略相关

### App端
- 使用原生的 plus.storage
- 无大小限制
- 持久化存储，不是缓存概念
- 数据长期有效，除非用户主动删除应用

### 微信小程序
- 使用小程序自带的 storage API
- 单个 key 允许存储的最大数据长度为 1MB
- 所有数据存储上限为 10MB
- 数据存储生命周期跟小程序本身一致

### 支付宝小程序
- 单条数据转换成字符串后，字符串长度最大200*1024
- 同一个支付宝用户，同一个小程序缓存总上限为10MB
- 数据存储生命周期跟小程序本身一致

### 百度小程序
- 使用百度小程序 storage API
- 具体限制参考[百度小程序官方文档](https://smartprogram.baidu.com/docs/develop/api/storage/save_process/)

### 抖音小程序  
- 使用抖音小程序 storage API
- 具体限制参考[抖音小程序官方文档](https://developer.open-douyin.com/docs/resource/zh-CN/mini-app/develop/api/data-caching/tt-get-storage)

## 注意事项

1. **平台兼容性**: 不同平台有不同的实现和限制，需要根据目标平台进行适配
2. **数据清理**: 非App平台清空Storage会导致uni.getSystemInfo获取到的deviceId改变
3. **数据安全**: 敏感数据不建议直接存储在本地缓存中
4. **数据格式**: 存储的数据会自动进行JSON序列化和反序列化

## 最佳实践

### 1. 数据分组存储
```javascript
// 使用前缀进行数据分组
const USER_PREFIX = 'user_'
const SETTINGS_PREFIX = 'settings_'

// 存储用户相关数据
vk.setStorageSync(USER_PREFIX + 'info', userInfo)
vk.setStorageSync(USER_PREFIX + 'token', userToken)

// 清除用户相关缓存
vk.clearStorageSync(USER_PREFIX)
```

### 2. 错误处理
```javascript
try {
  const data = vk.getStorageSync('largeData')
  if (!data) {
    // 数据不存在时的处理
    console.log('缓存数据不存在')
  }
} catch (error) {
  console.error('读取缓存失败:', error)
  // 可能是数据过大或格式错误
}
```

### 3. 数据验证
```javascript
function getValidStorage(key, defaultValue = null) {
  try {
    const value = vk.getStorageSync(key)
    // 添加数据验证逻辑
    if (value === null || value === undefined) {
      return defaultValue
    }
    return value
  } catch (error) {
    return defaultValue
  }
}
```

## 性能优化

1. **避免频繁操作**: 减少不必要的存储操作
2. **数据压缩**: 对大文本数据进行压缩存储
3. **批量操作**: 对相关数据进行批量存储和读取
4. **缓存策略**: 实现合理的缓存过期和更新机制