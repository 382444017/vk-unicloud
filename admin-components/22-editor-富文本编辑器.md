# 22、editor 富文本编辑器

## 万能表单使用方式

```js
{ key: "editor", title: "富文本类型", type: "editor", width: 750 },
```

## API

### 公共属性

[点击查看『公共属性』](opens new window)

## 万能表格使用方式

show:["detail"] 是为了控制只在点击详情时显示

```js
{ key: "editor", title: "富文本", type: "html", show:["detail"] },
```

## template 使用方式

```html
<vk-data-input-editor v-model="value1"  width="750px"></vk-data-input-editor>