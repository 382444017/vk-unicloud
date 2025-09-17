# 2、textarea 多行文本

## 万能表单使用方式

```javascript
{
  key: "textarea", title: "多行文本", type: "textarea",
  autosize: { minRows: 4, maxRows: 10 },
  maxlength: 200,
  showWordLimit: true,
}
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
| autosize | 自适应内容高度 | object | - | - |

## 万能表格使用方式

```javascript
{ key:"comment" , title:"备注" , type:"textarea" , width:200 }
```

## template 使用方式

```html
<el-input v-model="textarea" type="textarea" :rows="2" placeholder="请输入内容"></el-input>
```