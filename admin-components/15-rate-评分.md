# 15、rate 评分

## 万能表单使用方式

```js
{ key: "rate", title: "评分类型", type: "rate", allowHalf: false },
```

## API

### 公共属性

[点击查看『公共属性』](opens new window)

### 组件属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| allowHalf | 是否允许半选 | Boolean | false | true |
| lowThreshold | 低分和中等分数的界限值，值本身被划分在低分中 | Number | 2 | - |
| highThreshold | 高分和中等分数的界限值，值本身被划分在高分中 | Number | 4 | - |
| colors | icon 的颜色。若传入数组，共有 3 个元素，为 3 个分段所对应的颜色；若传入对象，可自定义分段，键名为分段的界限值，键值为对应的颜色 | Array/Object | ['#F7BA2A', '#F7BA2A', '#F7BA2A'] | - |
| voidColor | 未选中 icon 的颜色 | String | #C6D1DE | - |
| disabledVoidColor | 只读时未选中 icon 的颜色 | String | #EFF2F7 | - |
| showText | 是否显示辅助文字，若为真，则会从 texts 数组中选取当前分数对应的文字内容 | Boolean | false | true |
| showScore | 是否显示当前分数，show-score 和 show-text 不能同时为真 | Boolean | false | true |
| textColor | 辅助文字的颜色 | String | #1F2D3D | - |
| texts | 辅助文字数组 | Array | ['极差', '失望', '一般', '满意', '惊喜'] | - |
| scoreTemplate | 分数显示模板 | String | {value} | - |

## 万能表格使用方式

```js
{ key: "rate", title: "评分", type: "rate", width: 120 },
```

## template 使用方式

```html
<el-rate v-model="rate"></el-rate>