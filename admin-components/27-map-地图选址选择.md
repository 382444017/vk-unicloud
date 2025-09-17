# 28、map 地图选址选择

## 万能表单使用方式

```js
{ key: "position", title: "地图位置", type: "map", width: 600, height: 300, defaultLocation: { latitude: 30.224781, longitude: 120.12438 } },
```

## 注意

H5需要设置 manifest.json - H5配置 - 腾讯地图和高德地图二选一 - 填写key

key的申请地址：[腾讯地图](opens new window) [高德](opens new window)

## API

### 公共属性

[点击查看『公共属性』](opens new window)

### 组件属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| width | 当有值时，地图显示的宽度，单位px | Number | 600 | - |
| height | 当有值时，地图显示的高度，单位px | Number | 300 | - |
| defaultLocation | 当无值的情况下，打开地图时默认显示的位置 | Object | - | - |
| mode | 模式 edit 编辑模式 preview 预览模式 | String | - | - |

### defaultLocation

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| latitude | 纬度 | Number | - | - |
| longitude | 经度 | Number | - | - |

### onChange 使用示例

```js
{
  key: "position", title: "地图位置", type: "map",
  width: 600,
  height: 300,
  onChange: (val, formData, column, index) => {
    /**
     * val 双向绑定的表单值
     * formData 双向绑定的整个表单数据源
     */
    console.log(1, val, formData, column, index);
  }
},
```

**推荐使用 watch 代替 onChange** [传送门 - watch](opens new window)

## 万能表格使用方式

```js
{ key: "position", title: "地图位置", type: "map", width: 400 },
```

## template 使用方式

```html
<vk-data-input-map v-model="value" placeholder="请选择地图" :width="600" :height="300"></vk-data-input-map>
```

## 组件双向绑定的值格式

建议直接将整个数据存数据库，方便组件的编辑和显示。

### 示例

```js
{
  "name": "西湖风景名胜区",
  "address": "龙井路1号",
  "latitude": 30.224781,
  "longitude": 120.12438,
  "formatted_address": "浙江省杭州市西湖区龙井路1号",
  "province": {
    "code": "330000",
    "name": "浙江省"
  },
  "city": {
    "code": "330100",
    "name": "杭州市"
  },
  "area": {
    "code": "330106",
    "name": "西湖区"
  }
}
```

### 参数说明

| 参数 | 说明 | 类型 |
|------|------|------|
| name | 地图上的名称，如：西湖风景名胜区 | String |
| address | 街道地址，如：龙井路1号 | String |
| latitude | 纬度，如：30.224781 | Number |
| longitude | 经度，如：120.12438 | Number |
| formatted_address | 完整地址，如：浙江省杭州市西湖区龙井路1号 | String |
| province | 省份 | Object {"code": "330000","name": "浙江省"} |
| city | 地级市 | Object {"code": "330100","name": "杭州市"} |
| area | 县级市 | Object {"code": "330106","name": "西湖区"} |