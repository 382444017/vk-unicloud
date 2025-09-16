---
type: "manual"
description: "Vue开发规范和最佳实践"
---

# Vue开发规范

## 基础约定

- 使用Vue框架进行开发，支持Vue2和Vue3
- 推荐使用Composition API（Vue3）或Options API
- 统一使用 `const vk = uni.vk` 获取VK框架实例
- 组件命名使用PascalCase，文件名使用kebab-case
- 使用统一的样式风格，界面风格，背景颜色统一，设计风格统一

## 导入规范

```javascript
// Vue核心（根据项目Vue版本选择）
import { ref, reactive, computed, watch, onMounted } from 'vue'

// UniApp生命周期
import { onLoad, onShow, onReady, onHide, onUnload } from '@dcloudio/uni-app'

// UI组件库（根据项目选择）
import { ElMessage, ElMessageBox } from 'element-plus'  // Vue3
// 或
import { Message, MessageBox } from 'element-ui'        // Vue2

// VK框架
const vk = uni.vk
```

## 响应式数据规范

```javascript
// 推荐：使用响应式数据
const formData = reactive({
  username: '',
  password: '',
  loading: false
})

const userInfo = ref(null)
const list = ref([])

// 避免：直接修改非响应式数据
let data = { count: 0 }  // ❌ 不推荐