---
type: "manual"
description: "页面开发规范和模板"
---

# 页面开发规范

## 页面基础模板

```vue
<template>
  <!-- 条件编译：H5端 -->
  <!-- #ifdef H5 -->
  <el-container class="page-container">
    <el-header class="app-header">
      <AppHeader :active="currentPage" />
    </el-header>
    <el-main class="page-main">
      <!-- 页面内容 -->
    </el-main>
  </el-container>
  <!-- #endif -->

  <!-- 条件编译：小程序/APP端 -->
  <!-- #ifndef H5 -->
  <view class="page-uni">
    <!-- 页面内容 -->
  </view>
  <!-- #endif -->
</template>

<script setup>
// 导入依赖
import { ref, reactive, onMounted } from 'vue'
import { onLoad, onShow } from '@dcloudio/uni-app'

const vk = uni.vk

// 响应式数据
const pageData = reactive({
  loading: false,
  userInfo: null
})

// 页面生命周期
onLoad((options) => {
  console.log('页面参数:', options)
  initPage()
})

onShow(() => {
  // 页面显示时执行
})

// 页面方法
const initPage = async () => {
  pageData.loading = true
  try {
    // 初始化逻辑
  } catch (error) {
    console.error('页面初始化失败:', error)
  } finally {
    pageData.loading = false
  }
}
</script>

<style scoped>
.page-container {
  min-height: 100vh;
}

.page-uni {
  padding: 20rpx;
}
</style>
```

## 页面生命周期规范

```javascript
// UniApp页面生命周期
onLoad((options) => {
  // 页面加载，获取页面参数
})

onShow(() => {
  // 页面显示，每次切换都会触发
})

onReady(() => {
  // 页面初次渲染完成
})

onHide(() => {
  // 页面隐藏
})

onUnload(() => {
  // 页面卸载，清理资源
})