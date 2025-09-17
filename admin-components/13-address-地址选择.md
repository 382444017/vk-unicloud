# 13、address 地址选择（省市区选择）

## 万能表单使用方式

```js
{ key: "address", title: "address类型", type: "address" },
```

## API

### 公共属性

[点击查看『公共属性』](opens new window)

### 组件属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| level | 层级（最大3级，省市区） | Number | 3 | 1 、 2 |
| clearable | 是否可以清空选项 | Boolean | true | false |

## 万能表格使用方式

```js
{ key: "address", title: "地址", type: "address", width: 120 },
```

## template 使用方式

```html
<vk-data-input-address v-model="address" placeholder="请选择省市区" :level="3"></vk-data-input-address>
```

## 常见问题

### 如何获取省市区的数据?

#### 获取省数据

```js
vk.addressUtil.getProvinces();
```

#### 获取市数据

```js
vk.addressUtil.getCitys();
```

#### 获取区数据

```js
vk.addressUtil.getAreas();
```

#### 获取省市区三级联动数据

```js
vk.addressUtil.getPcaCode();
```

#### 获取省市二级联动数据

```js
vk.addressUtil.getPcCode();
```

### 省市区如何多选?

内置address组件不支持多选，如果你需要多选，可以通过级联组件，并结合上面提供的省市区数据源自己实现。