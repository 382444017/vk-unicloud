# 清明节灰色页面实现方案

## 概述

清明节灰色页面实现方案提供了一种在特定日期（如清明节）将页面整体变为灰色的功能，以表达哀思和尊重。本方案基于CSS滤镜实现，简单易用。

## 基础实现方案

### 页面代码示例
```html
<template>
  <view :class="classCom">
    <!-- 页面内容 -->
    这里是页面内容。。。
  </view>
</template>

<script>
export default {
  data() {
    return {
      // 页面数据变量
    }
  },
  computed: {
    classCom() {
      let classStr = ""
      if (this.vk.pubfn.timeUtil.isQingming()) {
        classStr = "gray-view"
      }
      return classStr
    }
  }
}
</script>

<style>
.gray-view {
  filter: grayscale(100%);
}
</style>
```

## 方案优缺点

### 优点
- **简单易用**: 只需添加简单的CSS类和计算属性
- **无需额外依赖**: 使用原生CSS特性实现
- **兼容性好**: 支持大多数现代浏览器

### 缺点
- **每个页面都需要添加**: 需要在每个需要变灰的页面中添加`:class="classCom"`
- **手动维护**: 需要手动在需要变灰的页面中添加代码

## 增强版实现方案

### 1. 全局混入方案
```javascript
// main.js 中全局混入
import Vue from 'vue'

Vue.mixin({
  computed: {
    isQingmingPeriod() {
      return this.vk.pubfn.timeUtil.isQingming()
    }
  }
})
```

### 2. 自定义指令方案
```javascript
// 创建自定义指令
Vue.directive('gray-mode', {
  inserted(el, binding) {
    const isQingming = binding.value || vk.pubfn.timeUtil.isQingming()
    if (isQingming) {
      el.classList.add('global-gray-mode')
    }
  },
  update(el, binding) {
    const isQingming = binding.value || vk.pubfn.timeUtil.isQingming()
    if (isQingming) {
      el.classList.add('global-gray-mode')
    } else {
      el.classList.remove('global-gray-mode')
    }
  }
})

// 全局CSS
.global-gray-mode {
  filter: grayscale(100%) !important;
}
```

### 3. 使用方式
```html
<!-- 在App.vue中使用 -->
<template>
  <div v-gray-mode>
    <router-view />
  </div>
</template>

<!-- 或在具体页面中使用 -->
<template>
  <div v-gray-mode="isQingmingPeriod">
    <!-- 页面内容 -->
  </div>
</template>
```

## 时间判断工具

### 完整的清明节判断函数
```javascript
// common/utils/time-util.js
export default {
  /**
   * 判断当前时间是否在清明节期间
   * @param {Date} date 可选，默认为当前时间
   * @returns {boolean} 是否在清明节期间
   */
  isQingming(date = new Date()) {
    const year = date.getFullYear()
    
    // 清明节日期计算（通常为4月4日或4月5日）
    // 精确的清明节日期需要根据农历计算，这里使用近似算法
    const qingmingDate = new Date(year, 3, 4) // 4月4日
    
    // 清明节前后3天都算作清明节期间
    const startDate = new Date(qingmingDate)
    startDate.setDate(startDate.getDate() - 3)
    
    const endDate = new Date(qingmingDate)  
    endDate.setDate(endDate.getDate() + 3)
    
    return date >= startDate && date <= endDate
  },
  
  /**
   * 判断是否在特定哀悼日
   * @param {Date} date 可选，默认为当前时间
   * @returns {boolean} 是否在哀悼日
   */
  isMourningDay(date = new Date()) {
    const mourningDays = [
      '04-04', // 清明节
      '05-12', // 汶川地震纪念日
      '09-18', // 九一八事变纪念日
      '12-13'  // 南京大屠杀死难者国家公祭日
    ]
    
    const monthDay = this.formatDate(date, 'MM-DD')
    return mourningDays.includes(monthDay)
  },
  
  /**
   * 日期格式化
   * @param {Date} date 日期对象
   * @param {string} format 格式字符串
   * @returns {string} 格式化后的日期
   */
  formatDate(date, format = 'YYYY-MM-DD') {
    const year = date.getFullYear()
    const month = String(date.getMonth() + 1).padStart(2, '0')
    const day = String(date.getDate()).padStart(2, '0')
    
    return format
      .replace('YYYY', year)
      .replace('MM', month)
      .replace('DD', day)
  }
}
```

## 高级配置方案

### 1. 配置化管理
```javascript
// app.config.js 中添加配置
export default {
  // 哀悼日配置
  mourningDays: {
    // 启用哀悼日模式
    enabled: true,
    
    // 哀悼日列表
    days: [
      {
        name: '清明节',
        date: '04-04', // 月-日格式
        duration: 3,   // 持续天数
        grayscale: 100 // 灰度程度
      },
      {
        name: '国家公祭日', 
        date: '12-13',
        duration: 1,
        grayscale: 100
      }
    ],
    
    // 自定义哀悼日处理函数
    customHandler: null
  }
}
```

### 2. 哀悼日管理工具
```javascript
// common/utils/mourning-day-util.js
import config from '@/app.config.js'

export default {
  /**
   * 检查当前是否在哀悼日期间
   * @returns {Object|null} 哀悼日配置信息，不在哀悼日时返回null
   */
  checkMourningDay() {
    if (!config.mourningDays.enabled) return null
    
    const now = new Date()
    const currentMonthDay = this.formatMonthDay(now)
    
    for (const dayConfig of config.mourningDays.days) {
      if (this.isInMourningPeriod(now, dayConfig)) {
        return dayConfig
      }
    }
    
    return null
  },
  
  /**
   * 判断是否在哀悼日期内
   */
  isInMourningPeriod(date, dayConfig) {
    const mourningDate = this.parseMonthDay(dayConfig.date)
    const startDate = new Date(mourningDate)
    startDate.setDate(startDate.getDate() - Math.floor(dayConfig.duration / 2))
    
    const endDate = new Date(mourningDate)
    endDate.setDate(endDate.getDate() + Math.floor(dayConfig.duration / 2))
    
    return date >= startDate && date <= endDate
  },
  
  /**
   * 格式化月-日
   */
  formatMonthDay(date) {
    const month = String(date.getMonth() + 1).padStart(2, '0')
    const day = String(date.getDate()).padStart(2, '0')
    return `${month}-${day}`
  },
  
  /**
   * 解析月-日字符串为日期对象
   */
  parseMonthDay(monthDay) {
    const [month, day] = monthDay.split('-').map(Number)
    const date = new Date()
    date.setMonth(month - 1)
    date.setDate(day)
    return date
  }
}
```

## 全局实现方案

### 1. App.vue 全局处理
```html
<template>
  <div :class="{'global-mourning-mode': isMourningDay}">
    <router-view />
  </div>
</template>

<script>
import mourningDayUtil from '@/common/utils/mourning-day-util.js'

export default {
  data() {
    return {
      isMourningDay: false
    }
  },
  created() {
    this.checkMourningDay()
  },
  methods: {
    checkMourningDay() {
      const mourningConfig = mourningDayUtil.checkMourningDay()
      this.isMourningDay = !!mourningConfig
      
      if (this.isMourningDay) {
        this.applyMourningStyle(mourningConfig)
      }
    },
    
    applyMourningStyle(config) {
      // 可以在这里添加更多哀悼日的样式处理
      console.log(`今日是${config.name}，页面已切换为灰色模式`)
    }
  }
}
</script>

<style>
.global-mourning-mode {
  filter: grayscale(100%);
  /* 可以添加其他哀悼日样式 */
  pointer-events: auto !important; /* 确保交互正常 */
}
</style>
```

### 2. 自定义哀悼日样式
```css
/* 哀悼日主题样式 */
.global-mourning-mode {
  /* 基础灰度 */
  filter: grayscale(100%);
  
  /* 文字颜色调整 */
  color: #666666 !important;
  
  /* 背景颜色调整 */
  background-color: #f5f5f5 !important;
  
  /* 链接颜色 */
  a {
    color: #666666 !important;
    text-decoration: none;
  }
  
  /* 按钮样式 */
  button {
    background-color: #cccccc !important;
    border-color: #999999 !important;
    color: #666666 !important;
  }
  
  /* 图片样式 */
  img {
    filter: grayscale(100%);
  }
  
  /* 确保交互正常 */
  pointer-events: auto !important;
}
```

## 注意事项

### 1. 性能考虑
- 避免在大量元素上使用滤镜效果
- 考虑使用will-change属性优化性能
- 在低性能设备上适当降低效果

### 2. 用户体验
- 提供切换开关，允许用户手动关闭
- 添加适当的提示信息
- 确保页面功能正常可用

### 3. 可访问性
- 确保灰度模式下的内容仍然可读
- 考虑色盲用户的体验
- 提供高对比度选项

### 4. 浏览器兼容性
```css
/* 兼容性处理 */
.gray-view {
  filter: grayscale(100%);
  -webkit-filter: grayscale(100%); /* Safari */
  -moz-filter: grayscale(100%);    /* Firefox */
  -ms-filter: grayscale(100%);     /* IE */
  -o-filter: grayscale(100%);      /* Opera */
}
```

## 扩展功能

### 1. 哀悼日定时任务
```javascript
// 每天检查一次哀悼日状态
setInterval(() => {
  this.checkMourningDay()
}, 24 * 60 * 60 * 1000) // 24小时
```

### 2. 用户偏好设置
```javascript
// 保存用户偏好
const mourningPreferences = {
  enabled: true,    // 是否启用哀悼日模式
  intensity: 100,   // 灰度强度
  autoDetect: true  // 是否自动检测
}

vk.setStorageSync('mourning_preferences', mourningPreferences)
```

### 3. 多主题支持
```javascript
// 支持不同的哀悼主题
const mourningThemes = {
  default: {
    grayscale: 100,
    opacity: 1
  },
  light: {
    grayscale: 50,
    opacity: 0.8
  },
  minimal: {
    grayscale: 30,
    opacity: 0.9
  }
}
```

## 最佳实践

1. **渐进式增强**: 先实现基础功能，再逐步添加高级特性
2. **用户控制**: 允许用户自定义哀悼日体验
3. **性能监控**: 监控灰度模式对性能的影响
4. **测试覆盖**: 确保在不同设备和浏览器上正常工作
5. **文档完善**: 为其他开发者提供清晰的使用说明