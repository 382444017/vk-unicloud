# 21、time 时间选择

## 万能表单使用方式

```js
{ key: "time1", title: "time类型1", type: "time" },
{
  key: "time2", title: "time类型2", type: "time",
  valueFormat: "HH:mm",
  pickerOptions: {
    format: "HH:mm"
  }
},
{
  key: "time3", title: "time类型3", type: "time",
  custom: true,
  pickerOptions: {
    start: '08:00',
    step: '01:00',
    end: '24:00'
  }
},
{ key: "timeArr1", title: "time类型范围1", type: "time", isRange: true },
{
  key: "timeArr2", title: "time类型范围2", type: "time",
  isRange: true,
  valueFormat: "HH:mm",
  pickerOptions: {
    selectableRange: '18:30:00 - 20:30:00',
    format: "HH:mm"
  }
},
```

## API

### 公共属性

[点击查看『公共属性』](opens new window)

### 组件属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| align | 对其方式 | String | left | left, center, right |
| format | 显示在输入框中的格式 | String | yyyy-MM-dd HH:mm:ss | - |
| startPlaceholder | 范围选择时开始日期的占位内容 | String | - | - |
| endPlaceholder | 范围选择时结束日期的占位内容 | String | - | - |
| rangeSeparator | 选择范围时的分隔符 | String | '-' | - |
| pickerOptions | 当前时间日期选择器特有的选项参考下表 | Object | {} | - |

### pickerOptions 详情说明

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| selectableRange | 可选时间段，例如'18:30:00 - 20:30:00'或者传入数组['09:30:00 - 12:00:00', '14:30:00 - 18:30:00'] | string / array | - | - |
| format | 设置禁用状态，参数为当前日期，要求返回 Boolean | string | 'HH:mm:ss' | 小时：HH，分：mm，秒：ss，AM/PM A |

[点击查看更多element 官方API](opens new window)

## 万能表格使用方式

```js
{ key: "time", title: "添加时间", type: "text", width: 160 },
```

## template 使用方式

```html
<vk-data-input-time
  v-model="value1"
  :picker-options="{
    start: '08:30',
    step: '00:15',
    end: '18:30'
  }"
  placeholder="选择时间"
></vk-data-input-time>
```

[点击查看更多element 官方API](opens new window)