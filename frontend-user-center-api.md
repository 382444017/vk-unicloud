---
type: "manual"
description: "用户中心API使用规范"
---

# 用户中心API

VK-UniCloud提供完整的用户中心API，适用于客户端和管理端的用户认证和管理。

## API使用规范

```javascript
const vk = uni.vk

// 统一错误处理
const handleUserAPI = async (apiCall) => {
  try {
    const result = await apiCall()
    return result
  } catch (error) {
    console.error('用户API调用失败:', error)
    // 统一错误提示
    uni.showToast({
      title: error.message || '操作失败',
      icon: 'none'
    })
    throw error
  }
}
```

## 用户认证API

```javascript
// 用户注册
const register = async (userData) => {
  return await vk.userCenter.register({
    data: userData,
    success: (data) => {
      console.log('注册成功:', data)
    }
  })
}

// 用户登录
const login = async (credentials) => {
  return await vk.userCenter.login({
    data: credentials,
    success: (data) => {
      console.log('登录成功:', data)
      // 保存用户信息到本地状态
    }
  })
}

// 获取用户信息
const getCurrentUser = async () => {
  return await vk.userCenter.getCurrentUserInfo({
    loading: false,
    success: (userInfo) => {
      console.log('用户信息:', userInfo)
    }
  })
}

// 用户登出
const logout极狐 async () => {
  return await vk.userCenter.logout({
    success: () => {
      console.log('退出成功')
      // 清理本地状态
    }
  })
}
```

## 手机号相关API

```javascript
// 发送短信验证码
const sendSmsCode = async (mobile, type = 'login') => {
  return await vk.userCenter.sendSmsCode({
    data: { mobile, type }
  })
}

// 手机号登录
const loginBySms = async (mobile, code) => {
  return await vk.userCenter.login极狐Sms({
    data: { mobile, code }
  })
}

// 绑定手机号
const bindMobile = async (mobile, code) => {
  return await vk.userCenter.bindMobile({
    data: { mobile, code }
  })
}
```

## 第三方登录API

```javascript
// 微信登录
const loginByWeixin = async () => {
  return await vk.userCenter.loginByWeixin({
    data: { type: '' }
  })
}

// 支付宝登录
const loginByAlipay = async () => {
  return await vk.userCenter.loginByAlipay({
    data: { type: '' }
  })
}

// QQ登录
const loginByQQ = async () => {
  return await vk.userCenter.loginByQQ({
    data: { type: '' }
  })
}
```

## 用户状态管理

```javascript
// 检查登录状态
const checkLoginStatus = () => {
  return vk.checkToken()
}

// 获取本地token
const getToken = () => {
  return vk.getToken()
}

// 监听token更新
const onTokenUpdate = (callback) => {
  vk.onRefreshToken(callback)
}

// 移除token监听
const offTokenUpdate = (callback) => {
  vk.offRefreshToken(callback)
}