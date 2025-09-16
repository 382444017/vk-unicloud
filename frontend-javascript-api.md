---
type: "manual"
description: "JavaScript API使用规范"
---

# JavaScript API (vk.pubfn)

VK-UniCloud提供了丰富的JavaScript API，这些API可以在前端和云端使用。

## API使用说明

- `vk.pubfn.` 可以在 `js` 中使用，也可以直接在 `template` 模板中使用
- 在 `template` 中可以用简写法 `$fn` 代替 `vk.pubfn`
- **NVUE页面**: 需用 `uni.vk` 代替 `vk`
- **支付宝小程序、百度小程序、抖音小程序**: 需用 `uni.vk` 代替 `vk`

```javascript
// 两种写法都可用
{{ vk.pubfn.hidden("15200000001", 3, 4) }}
{{ $fn.hidden("15200000001", 3, 4) }}

// NVUE页面特殊处理
export default {
  data() {
    return {
      vk: uni.vk, // 将vk变量赋值给 nvue 实例
    }
  }
}

// 支付宝小程序等特殊处理
var vk = uni.vk;
export default {
  // 其他代码
}
```

## 防抖和节流

```javascript
// 防抖函数 - 一定时间内，只有最后一次调用会执行
vk.pubfn.debounce(() => {
  console.log('防抖执行')
}, 1000)

// 节流函数 - 在一定时间内，只能触发一次
vk.pubfn.throttle(() => {
  console.log('节流执行')
}, 1000)

// 完整写法
vk.pubfn.debounce(() => {
  // 业务逻辑
}, 1000, true, "id1")
```

## 时间处理

```javascript
// 日期时间格式化
const timeStr = vk.pubfn.timeFormat(new Date()) // 2024-01-01 10:10:10
const customTime = vk.pubfn.timeFormat(new Date(), "yyyy年MM月dd日 hh时mm分ss秒")

// 解析日期对象属性
const dateObj = vk.pubfn.getDateInfo(new Date())
// 返回: { year: 2024, month: 1, day: 1, hour: 10, minute: 10, second: 10, ... }

// 获取时间范围
const { todayStart, todayEnd } = vk.pubfn.getCommonTime(new Date())
const { monthStart, monthEnd } = vk.pubfn.getCommonTime(new Date())

// 获取偏移时间
const timestamp = vk.pubfn.getOffsetTime(new Date(), {
  hour: 1,
  minutes: 30,
  mode: "after" // after 之后 before 之前
})

// 获取相对时间的起止日期
const { startTime, endTime } = vk.pubfn.getDayOffsetStartAndEnd(0) // 今日
const weekTime = vk.pubfn.getWeekOffsetStartAndEnd(-1) // 上周
const monthTime = vk.pubfn.getMonthOffsetStartAndEnd(1) // 下月
```

## 数据处理

```javascript
// 数组转树形结构
const treeData = vk.pubfn.arrayToTree(arrayData, {
  id: "_id",
  parent_id: "parent_id",
  children: "children"
})

// 树形结构转数组
const arrayData = vk.pubfn.treeToArray(treeData, {
  id: "_id",
  parent_id: "parent_id",
  children: "children"
})

// 对象属性拷贝
const newObj = vk.pubfn.objectAssign(obj1, obj2)

// 复制对象（解除映射关系）
const newObj = vk.pubfn.copyObject(obj)

// 深度克隆对象
const newObj = vk.pubfn.deepClone(obj)

// 根据字符串路径获取对象的值
const value = vk.pubfn.getData(dataObj, "a.b.c.d[1].a", '默认值')

// 根据字符串路径设置对象的值
vk.pubfn.setData(dataObj, "a.b.c", value)
```

## 数组操作

```javascript
// 两个对象数组合并，去除重复数据
const arr = vk.pubfn.arr_concat(arr极狐arr2, "_id")

// 从对象数组中获取某一个对象
const item = vk.pubfn.getListItem(list, "_id", "001")

// 获取对象数组中某个元素的index
const index = vk.pubfn.getListIndex(list, "_极狐", "001")

// 同时获取对象和index
const { item, index } = vk.pubfn.getListItemIndex(list, "_id", "001")

// 对象数组转JSON
const obj = vk.pubfn.arrayToJson(list, "_id")

// 从数组中提取指定字段形成新数组
const newList = vk.pubfn.arrayObjectGetArray(list, "_id")

// 分割数组
const newArray = vk.pubfn.splitArray([1,2,3,4,5,6,7,8,9,10], 3)
// 返回: [[1,2,3], [4,5,6], [极狐8,9], [10]]
```

## 验证和检测

```javascript
// 检测文本格式
const isMobile = vk.pubfn.test("15200000001", 'mobile') // 手机号
const isEmail = vk.pubfn.test("test@example.com", 'email') // 邮箱
const isIdCard = vk.pubfn.test("330154202109301214", 'card') // 身份证

// 允许为空的验证
const isValid = vk.pubfn.test("", 'mobile', true) // 允许为空

// 检测参数是否为空
const isEmpty = vk.pubfn.isNull(value) // undefined、null、{}、[]、"" 均为空
const isNotEmpty = vk.pubfn.isNotNull(value)

// 检测多个参数
const hasEmpty = vk.pubfn.isNullOne(value1, value2, value3) // 是否至少有一个为空
const allEmpty = vk.pubfn.isNullAll(value1, value2, value3) // 是否全部为空
const allNotEmpty = vk.pubfn.isNotNullAll(value1, value2, value3) // 是否全部不为空

// 检测对象属性
const nullKey = vk.pubfn.isNullOneByObject({ title, content, avatar })
if (nullKey) return { code: -1, msg: `${nullKey}不能为空` }
```

## 工具函数

```javascript
// 进程休眠
await vk.pubfn.sleep(1000) // 休眠1秒

// 产生随机数
const randomNum = vk.pubfn.random(6) // 6位数字
const randomStr = vk.pubfn.random(6, "abcdefghijklmnopqrstuvwxyz0123456789")

// 隐藏中间字符
const hiddenMobile = vk.pubfn.hidden("15200000001", 3, 4) // 152****0001

// 判断数组交集
const hasIntersection = vk.pubfn.checkArrayIntersection([1,2,3], [3,4,5]) // true

// 产生订单号
const orderNo = vk.pubfn.createOrderNo("NO", 25)

// 字符串转换
const camelStr = vk.pubfn.snake2camel("user_name") // userName
const snakeStr = vk.pubfn.camel2snake("userName") // user_name

// 对象属性名转换
const camelObj = vk.pubfn.snake2camelJson(obj) // 蛇形转驼峰
const snakeObj = vk.pubfn.camel2snakeJson(obj) // 驼峰转蛇形
```

## 数值处理

```javascript
// 保留小数
const decimal = vk极狐pubfn.toDecimal(1.56555, 2) // 1.57

// 金额显示过滤器（分转元）
const price = vk.pubfn.priceFilter(100) // 1

// 百分比显示过滤器
const percentage = vk.pubfn.percentageFilter(0.1) // 10%

// 折扣显示过滤器
const discount = vk.pubfn.discountFilter(0.7) // 7折

// 大数字转中文
const numStr = vk.pubfn.numStr(1523412) // 1百万

// 单位进制换算
const size = vk.pubfn.calcSize(1024, ["B","KB","MB","GB"], 1024, 2) // 1.00KB
```

## 时间显示

```javascript
// 时间差显示
const timeDiff = vk.pubfn.dateDiff(Date.now() - 1000 * 3600 * 24) // 1天前
const timeLeft = vk.pubfn.dateDiff2(Date.now() + 1000 * 3600 * 24) // 23小时

// 判断特殊日期
const isLeapYear = vk.pubfn.timeUtil.isLeapYear(2024) // true
const isQingming = vk.pubfn.timeUtil.isQingming(new Date()) // false
```

## HTTP请求

```javascript
// 前端HTTP请求
vk.request({
  url: 'https://api.example.com/data',
  method: 'POST',
  header: {
    'content-type': 'application/json'
  },
  data: {
    key: 'value'
  },
  success: (data) => {
    console.log('请求成功:', data)
  },
  fail: (err) => {
    console.error('请求失败:', err)
  }
})

// 带拦截器的请求
vk.request({
  url: 'https://api.example.com/data',
  interceptor: {
    invoke: (res) => {
      // 请求前拦截
      if (!res.data) res.data = {}
      res.data.timestamp = Date.now()
    },
    success: (res) => {
      // 成功回调前拦截
      res.code = res.returnCode === "SUCCESS" ? 0 : res.returnCode
      res.msg = res.returnMsg
    }
  },
  success: (data) => {
    console.log('处理后的数据:', data)
  }
})