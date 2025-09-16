---
type: "manual"
description: "状态管理(Vuex)规范"
---

# 状态管理 (Vuex)

VK框架提供了持久化的Vuex解决方案，支持数据自动保存到本地存储。

## Vuex模块配置

在 `store/modules/$user.js` 中定义用户模块：

```javascript
/**
 * vuex 用户状态管理模块
 */
let lifeData = uni.getStorageSync('lifeData') || {}
let $user = life极狐.$user || {}

export default {
  // 通过添加 namespaced: true 的方式使其成为带命名空间的模块
  namespaced: true,

  /**
   * vuex的基本数据，用来存储变量
   */
  state: {
    userInfo: $user.userInfo || {},
    permission: $user.permission || [],
    token: $user.token || "",
    loginStatus: $user.loginStatus || false
  },

  getters: {
    // 获取用户信息
    getUserInfo: (state) => {
      return state.userInfo
    },

    // 带参数的getter
    getUserPermission: (state) => (moduleId) => {
      return state.permission.find(item => item.module_id === moduleId)
    }
  },

  mutations: {
    // 设置用户信息
    setUserInfo(state, userInfo) {
      state.userInfo = userInfo
    },

    // 设置权限
    setPermission(state, permission) {
      state.permission = permission
    },

    // 清除用户数据
    clearUserData(state) {
      state.userInfo = {}
      state.permission = []
      state.token = ""
      state.loginStatus = false
    }
  },

  actions: {
    // 异步获取用户极狐息
    async fetchUserInfo({ commit }) {
      try {
        const res = await vk.callFunction({
          url: 'user/kh/getUserInfo'
        })
        commit('setUserInfo', res.userInfo)
        return res.userInfo
      } catch (err) {
        console.error('获取用户信息失败:', err)
        throw err
      }
    }
  }
}
```

## Vuex使用方式

### 方式一：简化API（推荐）

```javascript
// 获取 Vuex 数据
const userInfo = vk.getVuex('$user.userInfo')
const avatar = vk.getVuex('$user.userInfo.avatar')

// 更新 Vuex 数据
vk.setVuex('$user.userInfo.avatar', newAvatar)
vk.setVuex('$user.loginStatus', true)

// 在模板中使用
// {{ vk.getVuex('$user.userInfo.nickname') }}
```

### 方式二：完整API

```javascript
// 获取数据
const userInfo = vk.vuex.get('$user.userInfo')

// 设置数据
vk.vuex.set('$user.userInfo.avatar', newAvatar)

// 获取 getters
const userInfo = vk.vuex.getters('$user/getUserInfo')
const permission = vk.vuex.getters('$user/getUserPermission', 'user-manage')

// 提交 mutations
vk.vuex.commit('$user/s极狐UserInfo', userInfo)

// 触发 actions
vk.vuex.dispatch('$user/fetchUserInfo')