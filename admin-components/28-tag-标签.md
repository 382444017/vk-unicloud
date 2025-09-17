# 29、tag 标签

## 万能表单使用方式

```js
{ key: "goods_tags", title: "商品标签", type: "tag" },
```

## API

### 公共属性

[点击查看『公共属性』](opens new window)

### 组件属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| limit | 最大可以输入多少项 | number | - | - |
| inputType | 输入框类型 | String | text | number、money、percentage、discount |
| precision | 数值精度 小数点位数 | number | 2 | - |
| closable | 是否可以删除 | Boolean | true | false |
| showAdd | 是否显示添加按钮 | Boolean | true | false |
| addButtonText | 添加按钮的标题 | String | + 添加 | - |
| maxlength | 字符串输入最大长度 | Number | - | - |

## 万能表格使用方式

### 方式一

值的类型是数组，输出的是数组内每一项的文本

如：输出商品标签 goods_tags:["标签1","标签2"]，最长显示的是 标签1 标签2

```js
{ key: "goods_tags", title: "商品标签", type: "tag", width: 200 },
```

### 方式二

值的类型是数字或字符串或布尔，用值去匹配data内的value，输出label

如：输出类型 type:1 最终显示的是 收入

```js
{
  key: "type", title: "类型", type: "tag", width: 120, size: "small",
  data: [
    { value: 1, label: "收入", tagType: "success" },
    { value: 2, label: "支出", tagType: "danger" }
  ]
},
```

## template 使用方式

```html
<vk-data-input-tag v-model="value" :limit="5"></vk-data-input-tag>