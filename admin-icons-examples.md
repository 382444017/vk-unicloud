# 图标组件示例

本文档展示了 `pages_template/components/icons/` 目录下的图标组件示例代码。

## Element UI 图标示例

### element-icons.vue - Element UI 图标展示

```vue
<template>
  <view class="page">
    <vk-data-page-header
      title="Element UI 图标"
      subTitle="Element UI 内置图标库"
    ></vk-data-page-header>
    
    <view class="page-body">
      <el-row :gutter="20">
        <el-col 
          v-for="(icon, index) in elementIcons" 
          :key="index" 
          :xs="8" 
          :sm="6" 
          :md="4" 
          :lg="3" 
          :xl="2"
        >
          <div class="icon-item">
            <i :class="'el-icon-' + icon"></i>
            <div class="icon-name">{{ icon }}</div>
          </div>
        </el-col>
      </el-row>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      elementIcons: [
        'platform-eleme', 'eleme', 'delete-solid', 'delete', 's-tools', 'setting',
        'user-solid', 'user', 'phone', 'phone-outline', 'more', 'more-outline',
        'star-on', 'star-off', 's-goods', 'goods', 'warning', 'warning-outline',
        'question', 'info', 'remove', 'circle-plus', 'success', 'error',
        'zoom-in', 'zoom-out', 'remove-outline', 'circle-plus-outline', 'circle-check',
        'circle-close', 's-help', 'help', 'minus', 'plus', 'check', 'close',
        'picture', 'picture-outline', 'picture-outline-round', 'upload', 'upload2',
        'download', 'camera', 'camera-solid', 'message', 'message-solid', 'bell',
        'bell-outline', 'video-camera', 'video-camera-solid', 'notification', 'notification-outline',
        'mobile', 'mobile-phone', 'service', 'basketball', 'football', 'soccer',
        'baseball', 'wind-power', 'light-rain', 'lightning', 'sunny', 'cloudy',
        'partly-cloudy', 'heavy-rain', 'snowy', 'rainy', 'sunrise', 'sunrise-1',
        'sunset', 'sunny-rainy', 'hot', 'cold', 'high-temperature', 'low-temperature',
        'moon', 'moon-night', 'dish', 'dish-1', 'food', 'chicken', 'fork-spoon',
        'knife-fork', 'burger', 'tableware', 'sugar', 'dessert', 'ice-cream',
        'hot-water', 'water-cup', 'coffee-cup', 'cold-drink', 'goblet', 'goblet-full',
        'goblet-square', 'goblet-square-full', 'refrigerator', 'grape', 'lollipop',
        'ice-tea', 'orange', 'pear', 'apple', 'cherry', 'watermelon', 'mango',
        'avocado', 'lemon', 'peach', 'pineapple', 'strawberry', 'banana', 'shrimp',
        'crab', 'crab-1', 'fish', 'octopus', 'shell', 'turtle', 'chicken-leg',
        'egg', 'carrot', 'broccoli', 'asparagus', 'corn', 'garlic', 'onion',
        'potato', 'tomato', 'mushroom', 'cabbage', 'radish', 'birthday-cake',
        'cake', 'pizza', 'noodles', 'doughnut', 'cookie', 'chocolate', 'candy',
        'lollipop-1', 'pudding', 'ice-cream-1', 'milk-tea', 'soft-drink', 'juice',
        'water-bottle', 'tea', 'alarm-clock', 'timer', 'watch', 'watch-1', 'stopwatch',
        'key', 'lock', 'unlock', 'safe', 'safe-box', 'money-bag', 'coin',
        'bank-card', 'credit-card', 'wallet', 'money', 'money-1', 'gold-coin',
        'medal', 'trophy', 'crown', 'gift', 'gift-card', 'shopping-bag', 'shopping-cart',
        'shopping-cart-full', 'shopping-cart-1', 'shopping-cart-2', 'shopping-bag-1',
        'tag', 'price-tag', 'discount', 'percentage', 'invoice', 'receipt', 'barcode',
        'qrcode', 'scan', 'connection', 'link', 'cut', 'edit', 'edit-outline',
        'scissors', 'paperclip', 'document', 'document-add', 'document-remove',
        'document-checked', 'document-copy', 'document-delete', 'document-download',
        'document-upload', 'folder', 'folder-add', 'folder-remove', 'folder-checked',
        'folder-delete', 'folder-opened', 'folder-shared', 'folder-music', 'folder-video',
        'folder-image', 'folder-document', 'folder-zip', 'folder-code', 'folder-excel',
        'folder-word', 'folder-powerpoint', 'folder-pdf', 'folder-unknown', 'folder-settings',
        'folder-network', 'folder-security', 'folder-error', 'folder-warning', 'folder-success',
        'folder-info', 'folder-forbidden', 'folder-locked', 'folder-unlocked', 'folder-star',
        'folder-heart', 'folder-home', 'folder-trash', 'folder-upload', 'folder-download',
        'folder-search', 'folder-history', 'folder-favorite', 'folder-recent', 'folder-template',
        'folder-cloud', 'folder-cloud-download', 'folder-cloud-upload', 'folder-cloud-sync',
        'folder-cloud-share', 'folder-cloud-delete', 'folder-cloud-error', 'folder-cloud-success',
        'folder-cloud-warning', 'folder-cloud-info', 'folder-cloud-locked', 'folder-cloud-unlocked',
        'folder-cloud-star', 'folder-cloud-heart', 'folder-cloud-home', 'folder-cloud-trash',
        'folder-cloud-upload', 'folder-cloud-download', 'folder-cloud-search', 'folder-cloud-history',
        'folder-cloud-favorite', 'folder-cloud-recent', 'folder-cloud-template', 'desktop',
        'laptop', 'tablet', 'mobile-1', 'computer', 'monitor', 'printer', 'keyboard',
        'mouse', 'hard-disk', 'usb', 'sd-card', 'camera-1', 'video', 'headset',
        'microphone', 'speaker', 'volume', 'volume-off', 'volume-up', 'volume-down',
        'game', 'gamepad', 'controller', 'vr', 'ar', 'drone', 'robot', 'rocket',
        'satellite', 'radar', 'antenna', 'signal', 'wifi', 'bluetooth', 'nfc',
        'gps', 'compass', 'map', 'map-location', 'location', 'location-outline',
        'location-information', 'location-error', 'location-success', 'location-warning',
        'location-remove', 'location-add', 'location-delete', 'location-check', 'location-arrow',
        'direction', 'compass-1', 'traffic', 'traffic-light', 'road', 'road-cone',
        'parking', 'parking-1', 'gas-station', 'ev-station', 'charging', 'car',
        'car-1', 'bus', 'truck', 'ship', 'airplane', 'train', 'subway', 'bicycle',
        'motorcycle', 'scooter', 'skateboard', 'roller-skate', 'walk', 'run',
        'hiking', 'climbing', 'swimming', 'surfing', 'skiing', 'snowboarding', 'skating',
        'golf', 'tennis', 'badminton', 'basketball-1', 'volleyball', 'football-1',
        'baseball-1', 'boxing', 'wrestling', 'fencing', 'archery', 'shooting',
        'bowling', 'darts', 'yoga', 'meditation', 'weightlifting', 'gymnastics',
        'dance', 'ballet', 'theater', 'music', 'music-1', 'music-2', 'music-3',
        'music-4', 'music-5', 'music-6', 'music-7', 'music-8', 'music-9', 'music-10',
        'music-11', 'music-12', 'music-13', 'music-14', 'music-15', 'music-16',
        'music-17', 'music-18', 'music-19', 'music-20', 'music-21', 'music-22',
        'music-23', 'music-24', 'music-25', 'music-26', 'music-27', 'music-28',
        'music-29', 'music-30', 'music-31', 'music-32', 'music-33', 'music-34',
        'music-35', 'music-36', 'music-37', 'music-38', 'music-39', 'music-40',
        'music-41', 'music-42', 'music-43', 'music-44', 'music-45', 'music-46',
        'music-47', 'music-48', 'music-49', 'music-50', 'music-51', 'music-52',
        'music-53', 'music-54', 'music-55', 'music-56', 'music-57', 'music-58',
        'music-59', 'music-60', 'music-61', 'music-62', 'music-63', 'music-64',
        'music-65', 'music-66', 'music-67', 'music-68', 'music-69', 'music-70',
        'music-71', 'music-72', 'music-73', 'music-74', 'music-75', 'music-76',
        'music-77', 'music-78', 'music-79', 'music-80', 'music-81', 'music-82',
        'music-83', 'music-84', 'music-85', 'music-86', 'music-87', 'music-88',
        'music-89', 'music-90', 'music-91', 'music-92', 'music-93', 'music-94',
        'music-95', 'music-96', 'music-97', 'music-98', 'music-99', 'music-100'
      ]
    }
  }
}
</script>

<style scoped>
.icon-item {
  text-align: center;
  padding: 20rpx;
  border-radius: 8rpx;
  background: #f8f9fa;
  margin-bottom: 20rpx;
  transition: all 0.3s;
  cursor: pointer;
}

.icon-item:hover {
  background: #e9ecef;
  transform: translateY(-2px);
  box-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.1);
}

.icon-item i {
  font-size: 48rpx;
  color: #606266;
  margin-bottom: 10rpx;
  display: block;
}

.icon-name {
  font-size: 24rpx;
  color: #909399;
  word-break: break-all;
}
</style>
```

## 图标使用最佳实践

### 1. 按需引入图标

```javascript
// 在 main.js 中按需引入图标
import { Icon } from 'element-ui'
Vue.component(Icon.name, Icon)
```

### 2. 自定义图标样式

```css
/* 自定义图标颜色和大小 */
.custom-icon {
  color: #409EFF;
  font-size: 24px;
}

/* 悬停效果 */
.icon-hover:hover {
  color: #67C23A;
  transform: scale(1.1);
}

/* 禁用状态 */
.icon-disabled {
  color: #C0C4CC;
  cursor: not-allowed;
}
```

### 3. 图标按钮组合

```vue
<template>
  <div>
    <el-button icon="el-icon-edit">编辑</el-button>
    <el-button icon="el-icon-delete" type="danger">删除</el-button>
    <el-button icon="el-icon-download">下载</el-button>
  </div>
</template>
```

### 4. 动态图标

```vue
<template>
  <div>
    <i :class="['el-icon', dynamicIcon]"></i>
    <el-button @click="toggleIcon">切换图标</el-button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      icons: ['el-icon-success', 'el-icon-error', 'el-icon-warning'],
      currentIndex: 0
    }
  },
  computed: {
    dynamicIcon() {
      return this.icons[this.currentIndex]
    }
  },
  methods: {
    toggleIcon() {
      this.currentIndex = (this.currentIndex + 1) % this.icons.length
    }
  }
}
</script>
```

## 常见问题

### 1. 图标不显示
- 检查是否正确引入了 Element UI 样式文件
- 确认图标名称拼写正确
- 确保没有自定义样式覆盖了图标显示

### 2. 图标颜色问题
- 使用 `color` 属性设置图标颜色
- 避免使用 `background-color` 设置图标背景

### 3. 图标大小调整
- 使用 `font-size` 调整图标大小
- 推荐使用相对单位（rem、em）保持响应式

### 4. 自定义图标
```vue
<template>
  <i class="custom-icon">📊</i>
</template>

<style>
.custom-icon {
  font-size: 24px;
  color: #409EFF;
}
</style>
```

通过以上示例，您可以快速掌握 Element UI 图标的使用方法，并应用到实际项目中。