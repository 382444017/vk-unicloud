# 1、text 单行文本

## 万能表单使用方式

```javascript
{ key: "text", title: "单行文本", type: "text" }
```

## API

### 公共属性
点击查看[公共属性](../0-公共属性.md)

### 组件属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| maxlength | 最大输入长度 | Number | - | - |
| showWordLimit | 是否显示输入字数统计 | Boolean | false | true |
| readonly | 原生属性，是否只读 | boolean | false | true |
| prefixIcon | 输入框头部图标 | String | - | - |
| suffixIcon | 输入框尾部图标 | String | - | - |

## 万能表格使用方式

```javascript
{ key:"nickname" , title:"用户昵称" , type:"text" , width:200 }
```

## template 使用方式

```html
<el-input v-model="form1.value" placeholder="请输入内容"></el-input>
```