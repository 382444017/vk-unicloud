---
type: "manual"
description: "组件开发规范和最佳实践"
---

# 组件开发规范

## 组件基础结构

```vue
<template>
  <view class="custom-component" :class="{ disabled: disabled }">
    <text class="title">{{ title }}</text>
    <button @click="handleClick" :disabled="disabled">
      {{ buttonText }}
    </button>
  </view>
</template>

<script setup>
import { ref, computed, defineProps, defineEmits } from 'vue'

// 组件属性
const props = defineProps({
  title: {
    type: String,
    default: '默认标题'
  },
  disabled: {
    type: Boolean,
    default: false
  }
})

// 组件事件
const emit = defineEmits(['click', 'change'])

// 响应式数据
const count = ref(0)

// 计算属性
const buttonText = computed(() => {
  return props.disabled ? '禁用' : `点击次数: ${count.value}`
})

// 组件方法
const handleClick = () => {
  if (!props.disabled) {
    count.value++
    emit('click', { count: count.value })
  }
}
</script>

<style scoped>
.custom-component {
  padding: 20rpx;
  border: 1px solid #eee;
  border-radius: 8rpx;
}

.disabled {
  opacity: 0.6;
  pointer-events: none;
}
</style>
```

## 组件开发规范

1. **属性定义**: 使用 `defineProps` 明确定义属性类型和默认值
2. **事件定义**: 使用 `defineEmits` 声明组件事件
3. **样式隔离**: 使用 `scoped` 避免样式污染
4. **响应式数据**: 合理使用 `ref` 和 `reactive`
5. **计算属性**: 使用 `computed` 处理派生数据