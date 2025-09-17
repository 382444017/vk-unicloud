# 26、icon 图标选择

## 万能表单使用方式

```js
{ key: "icon", title: "图标", type: "icon" },
```

## API

### 公共属性

[点击查看『公共属性』](opens new window)

### 组件属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| multiple | 是否可多选 | Boolean | false | true |
| multipleLimit | 最大多选数量 | Number | - | - |
| filter | 图标过滤器（仅万能表单有效） | Function、String | - | - |

### filter

#### 字符串过滤模式

```js
// 只显示 vk- 开头的图标
{ key: "icon", title: "图标", type: "icon", filter: "vk-" },
```

#### 函数过滤模式

```js
// 只显示 vk-custom-icon 开头的图标
{
  key: "icon", title: "图标", type: "icon",
  filter: (name) => {
    return name.indexOf("vk-custom-icon") === 0 ? true : false;
  },
},
```

## 万能表格使用方式

```js
{ key: "icon", title: "图标", type: "icon" },
```

## template 使用方式

```html
<vk-data-input-icon v-model="icon" placeholder="请选择图标"></vk-data-input-icon>
```

## 如何扩展图标库？

[传送门 - 扩展图标库](opens new window)

### 图标列表

- vk-icon
- el-icon