# 3、money 金额输入

## 万能表单使用方式

```javascript
{ key: "balance", title: "金额类型", type: "money" }
```

## API

### 公共属性
点击查看[公共属性](../0-公共属性.md)

### 组件属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| max | 最大输入 | Number | - | - |
| rightText | 右侧文字后缀，如元 | String | - | - |
| precision | 精度（小数点位数） | Number | 2 | - |
| readonly | 原生属性，是否只读 | boolean | false | true |

## 注意

如果需要大于2位数的小数点，建议直接用number组件，因为money组件在数据库中是以 100=1元 的形式储存（在js中，整数求和不会有误差，小数求和有误差），如果小数点超过2位，则100=1元无意义了

## 万能表格使用方式

```javascript
{ key:"balance" , title:"金额类型" , type:"money" , width:100 }
```

## template 使用方式

```html
<vk-data-input-money v-model="form1.balance" placeholder="请输入金额" width="300px"></vk-data-input-money>
```