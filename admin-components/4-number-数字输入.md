# 4、number 数字输入

## 万能表单使用方式

```javascript
{ key: "number", title: "数字类型", type: "number" }
```

## API

### 公共属性
点击查看[公共属性](../0-公共属性.md)

### 组件属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| min | 最小输入 | Number | - | - |
| max | 最大输入 | Number | - | - |
| rightText | 右侧文字后缀，如个 | String | - | - |
| precision | 精度（小数点位数） | Number | 2 | - |
| controls | 是否使用步进器模式 | boolean | false | true |
| controlsPosition | 控制按钮位置 | String | - | right |
| step | 步长 | Number | - | - |
| stepStrictly | 是否必须是步长的倍数 | boolean | false | true |
| readonly | 原生属性，是否只读 | boolean | false | true |

## 万能表格使用方式

```javascript
{ key:"number" , title:"数字类型" , type:"number" , width:100 }
```

## template 使用方式

### 普通模式

```html
<vk-data-input-number v-model="form1.value2" :precision="2" width="300px" placeholder="请输入数字"></vk-data-input-number>
```

### 步进器模式

```html
<vk-data-input-number-box v-model="form1.value2" :precision="2" :step="0.01" width="200px" placeholder="请输入数字"></vk-data-input-number-box>
```