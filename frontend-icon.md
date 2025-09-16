# 扩展图标库

## 概述

扩展图标库提供了丰富的图标资源，帮助开发者快速构建美观的界面。vk-unicloud框架内置了多种图标库支持，包括UniApp内置图标、扩展图标库和自定义图标。

## 内置图标使用

### UniApp内置图标
```html
<uni-icons type="contact" size="30" color="#007AFF"></uni-icons>
<uni-icons type="star" size="24" color="#FFCC00"></uni-icons>
<uni-icons type="settings" size="20" color="#666666"></uni-icons>
```

### 常用图标类型
- **contact**: 联系人
- **star**: 收藏/星星
- **settings**: 设置
- **person**: 个人
- **home**: 首页
- **chat**: 聊天
- **phone**: 电话
- **mail**: 邮件

## 扩展图标库集成

### 1. 引入图标库
在 `main.js` 中引入扩展图标库：

```javascript
import Vue from 'vue'
import App from './App'

// 引入图标库
import './common/icons/iconfont.css'

// 其他初始化代码...
```

### 2. 图标组件封装
创建通用的图标组件：

```javascript
// components/vi-icon/vi-icon.vue
<template>
  <text 
    class="vi-icon" 
    :class="[customClass]" 
    :style="{
      fontSize: size + 'rpx',
      color: color
    }"
    @click="onClick"
  >{{ iconName }}</text>
</template>

<script>
export default {
  name: 'vi-icon',
  props: {
    // 图标名称
    name: {
      type: String,
      default: ''
    },
    // 图标大小（rpx）
    size: {
      type: [Number, String],
      default: 32
    },
    // 图标颜色
    color: {
      type: String,
      default: '#333333'
    },
    // 自定义类名
    customClass: {
      type: String,
      default: ''
    }
  },
  computed: {
    iconName() {
      return String.fromCharCode(parseInt(this.name, 16))
    }
  },
  methods: {
    onClick(event) {
      this.$emit('click', event)
    }
  }
}
</script>

<style scoped>
.vi-icon {
  font-family: 'iconfont';
  font-size: 32rpx;
  color: #333333;
}
</style>
```

### 3. 全局注册组件
在 `main.js` 中全局注册图标组件：

```javascript
import ViIcon from '@/components/vi-icon/vi-icon.vue'

Vue.component('vi-icon', ViIcon)
```

## 图标使用示例

### 基础使用
```html
<vi-icon name="e60a" size="32" color="#007AFF"></vi-icon>
<vi-icon name="e60b" size="24" color="#FF5500"></vi-icon>
<vi-icon name="e60c" size="28" color="#52C41A"></vi-icon>
```

### 带有点击事件的图标
```html
<vi-icon 
  name="e60d" 
  size="36" 
  color="#1890FF" 
  @click="handleIconClick"
></vi-icon>
```

### 组合使用
```html
<view class="action-bar">
  <vi-icon name="e601" size="28" color="#666"></vi-icon>
  <vi-icon name="e602" size="28" color="#666"></vi-icon>
  <vi-icon name="e603" size="28" color="#666"></vi-icon>
</view>
```

## 图标库管理

### 1. 图标字体文件配置
在 `common/icons/iconfont.css` 中：

```css
@font-face {
  font-family: 'iconfont';
  src: url('./iconfont.ttf') format('truetype');
}

.iconfont {
  font-family: 'iconfont' !important;
  font-size: 16px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* 图标类名定义 */
.icon-home:before { content: '\e600'; }
.icon-user:before { content: '\e601'; }
.icon-settings:before { content: '\e602'; }
.icon-search:before { content: '\e603'; }
.icon-notification:before { content: '\e604'; }
```

### 2. 图标常量定义
创建图标常量文件：

```javascript
// common/icons/icon-types.js
export const ICON_TYPES = {
  HOME: 'e600',
  USER: 'e601', 
  SETTINGS: 'e602',
  SEARCH: 'e603',
  NOTIFICATION: 'e604',
  ARROW_LEFT: 'e605',
  ARROW_RIGHT: 'e606',
  CLOSE: 'e607',
  CHECK: 'e608',
  // 更多图标...
}

// 使用方式
import { ICON_TYPES } from '@/common/icons/icon-types.js'

<vi-icon :name="ICON_TYPES.HOME" size="32"></vi-icon>
```

## 自定义图标方案

### 1. SVG图标方案
```javascript
// components/vi-svg-icon/vi-svg-icon.vue
<template>
  <image 
    :src="iconPath" 
    :style="{
      width: size + 'rpx',
      height: size + 'rpx'
    }"
    mode="widthFix"
    @click="onClick"
  ></image>
</template>

<script>
export default {
  name: 'vi-svg-icon',
  props: {
    name: String,
    size: {
      type: [Number, String],
      default: 32
    }
  },
  computed: {
    iconPath() {
      return `/static/icons/${this.name}.svg`
    }
  },
  methods: {
    onClick(event) {
      this.$emit('click', event)
    }
  }
}
</script>
```

### 2. 图片图标方案
```javascript
// components/vi-image-icon/vi-image-icon.vue
<template>
  <image 
    :src="iconUrl" 
    :style="{
      width: size + 'rpx',
      height: size + 'rpx'
    }"
    mode="aspectFit"
    @click="onClick"
  ></image>
</template>

<script>
export default {
  name: 'vi-image-icon',
  props: {
    name: String,
    size: {
      type: [Number, String],
      default: 32
    },
    // 图标类型：normal|active|disabled
    type: {
      type: String,
      default: 'normal'
    }
  },
  computed: {
    iconUrl() {
      return `/static/icons/${this.name}_${this.type}.png`
    }
  }
}
</script>
```

## 最佳实践

### 1. 图标大小规范
```javascript
// common/icons/icon-sizes.js
export const ICON_SIZES = {
  SMALL: 24,    // 小图标
  MEDIUM: 32,   // 中等图标
  LARGE: 48,    // 大图标
  XLARGE: 64    // 超大图标
}
```

### 2. 图标颜色主题
```javascript
// common/icons/icon-colors.js  
export const ICON_COLORS = {
  PRIMARY: '#1890FF',     // 主要颜色
  SUCCESS: '#52C41A',     // 成功颜色
  WARNING: '#FAAD14',     // 警告颜色
  DANGER: '#FF4D4F',      // 危险颜色
  DISABLED: '#BFBFBF',    // 禁用颜色
  DEFAULT: '#666666'      // 默认颜色
}
```

### 3. 图标状态管理
```javascript
// 根据状态显示不同图标
<vi-icon 
  :name="isFavorite ? ICON_TYPES.STAR_FILLED : ICON_TYPES.STAR"
  :color="isFavorite ? ICON_COLORS.WARNING : ICON_COLORS.DEFAULT"
  size="28"
></vi-icon>
```

## 性能优化

### 1. 图标预加载
```javascript
// 预加载常用图标
function preloadIcons() {
  const icons = [
    '/static/icons/home.png',
    '/static/icons/user.png',
    '/static/icons/settings.png'
  ]
  
  icons.forEach(iconPath => {
    const image = new Image()
    image.src = iconPath
  })
}
```

### 2. 图标缓存
```javascript
// 使用localStorage缓存图标数据
const cachedIcons = vk.getStorageSync('cached_icons') || {}

function getIcon(name) {
  if (cachedIcons[name]) {
    return cachedIcons[name]
  }
  
  // 从服务器获取图标
  const iconData = fetchIconFromServer(name)
  cachedIcons[name] = iconData
  vk.setStorageSync('cached_icons', cachedIcons)
  
  return iconData
}
```

### 3. 图标懒加载
```javascript
// 使用IntersectionObserver实现图标懒加载
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const iconElement = entry.target
      const iconName = iconElement.dataset.icon
      iconElement.innerHTML = getIconContent(iconName)
      observer.unobserve(iconElement)
    }
  })
})

// 观察所有图标元素
document.querySelectorAll('[data-icon]').forEach(el => {
  observer.observe(el)
})
```

## 常见问题

### Q: 图标显示为方块或乱码？
A: 检查字体文件路径是否正确，确认字体文件已正确引入

### Q: 图标颜色不生效？
A: 确认图标字体支持颜色修改，有些图标字体是单色的

### Q: 图标在不同平台显示不一致？
A: 使用平台检测和条件渲染来适配不同平台

### Q: 如何添加自定义图标？
A: 使用Iconfont等工具生成字体文件，或使用SVG/图片方案