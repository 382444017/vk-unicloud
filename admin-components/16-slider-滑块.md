# 16、slider 滑块

## 万能表单使用方式

```js
{ key: "slider", title: "滑块类型", type: "slider" },
```

## API

### 公共属性

[点击查看『公共属性』](opens new window)

### 组件属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| min | 最小值 | Number | 0 | - |
| max | 最大值 | Number | 100 | - |
| step | 步长 | Number | 1 | - |
| showInput | 是否显示输入框，仅在非范围选择时有效 | Boolean | false | true |
| showInputControls | 在显示输入框的情况下，是否显示输入框的控制按钮 | Boolean | true | false |
| inputSize | 输入框的尺寸 | String | small | large / medium / small / mini |
| showStops | 是否显示间断点 | Boolean | false | true |
| showTooltip | 是否显示间断点 | Boolean | true | false |
| formatTooltip | 格式化 tooltip message | function(value) | - | - |
| range | 是否为范围选择 | Boolean | false | true |
| vertical | 是否竖向模式 | Boolean | false | true |
| height | Slider 高度，竖向模式时必填 | Number | - | - |
| label | 屏幕阅读器标签 | String | - | - |
| debounce | 输入时的去抖延迟，毫秒，仅在show-input等于true时有效 | Number | 300 | - |
| marks | 标记， key 的类型必须为 number 且取值在闭区间 [min, max] 内，每个标记可以单独设置样式 | Object | - | - |

## 万能表格使用方式

暂无

## template 使用方式

```html
<el-slider v-model="value1"></el-slider>
```

### marks用法

```html
<template>
  <view>
    <el-slider v-model="value" range :marks="marks"></el-slider>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        value: [30, 60],
        marks: {
          0: '0°C',
          8: '8°C',
          37: '37°C',
          50: {
            style: {
              color: '#1989FA'
            },
            label: this.$createElement('strong', '50%')
          }
        }
      }
    }
  }
</script>