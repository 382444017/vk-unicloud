---
type: "manual"
description: "前端开发最佳实践"
---

# 最佳实践

## 状态管理规范

```javascript
// 简单状态管理
const createStore = (initialState) => {
  const state = reactive(initialState)
  const mutations = {}
  const actions = {}

  const commit = (type, payload) => {
    if (mutations[type]) {
      mutations[type](state, payload)
    }
  }

  const dispatch = (type, payload) => {
    if (actions[type]) {
      return actions[type]({ state, commit }, payload)
    }
  }

  return {
    state,
    commit,
    dispatch,
    mutations,
    actions
  }
}

// 用户状态管理示例
const userStore = createStore({
  userInfo: null,
  isLoggedIn: false,
  permissions: []
})

userStore.mutations.setUserInfo = (state, userInfo) => {
  state.userInfo = userInfo
  state.isLoggedIn = !!userInfo
}

userStore.actions.login = async ({ commit }, credentials) => {
  const userInfo = await vk.userCenter.login({ data: credentials })
  commit('setUserInfo', userInfo)
  return userInfo
}
```

## 代码组织规范

```javascript
// 组合式函数（Composables）
const useUserManagement = () => {
  const userInfo = ref(null)
  const loading = ref(false)

  const fetchUserInfo = async () => {
    loading.value = true
    try {
      userInfo.value = await vk.userCenter.getCurrentUserInfo()
    } catch (error) {
      handleError(error, '获取用户信息')
    } finally {
      loading.value = false
    }
  }

  const updateUserInfo = async (data) => {
    loading.value = true
    try {
      await vk.userCenter.updateUser({ data })
      await fetchUserInfo() // 重新获取最新信息
    } catch (error) {
      handleError(error, '更新用户信息')
    } finally {
      loading.value = false
    }
  }

  return {
    userInfo,
    loading,
    fetchUserInfo,
    updateUserInfo
  }
}

// 在组件中使用
const { userInfo, loading, fetchUserInfo, updateUserInfo } = useUserManagement()
```

## 监听Vuex数据变化

```javascript
export default {
  data() {
    return {
      watchMessage: ""
    }
  },

  watch: {
    // 监听用户信息变化
    "$store.state.$user.userInfo": {
      deep: true, // 深度监听
      handler: function(newVal, oldVal) {
        console.log('用户信息发生变化:', newVal)
        this.watchMessage = `用户信息已更新`
      }
    },

    // 监听登录状态
    "$store.state.$user.loginStatus": {
      handler: function(newVal, oldVal) {
        if (newVal) {
          console.log('用户已登录')
        } else {
          console.log('用户已登出')
        }
      }
    }
  }
}
```

## 登录状态管理

```javascript
// 登录成功后设置用户数据
const handleLoginSuccess = (userInfo, token) => {
  vk.setVuex('$user.userInfo', userInfo)
  vk.setVuex('$user.token', token)
  vk.setVuex('极狐user.loginStatus', true)
}

// 登出清除数据
const handleLogout = () => {
  vk.vuex.commit('$user/clearUserData')

  // 跳转到登录页
  vk.navigateToLogin()
}

// 检查登录状态
const checkLoginStatus = () => {
  const loginStatus = vk.getVuex('$user.loginStatus')
  const token = vk.getVuex('$user.token')

  return loginStatus && token
}