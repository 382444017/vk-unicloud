---
type: "manual"
description: "JavaScript API (vk.pubfn) 使用规范"
---

# JavaScript API (vk.pubfn)

VK-UniCloud提供了丰富的JavaScript工具函数，这些API在云函数中同样可以使用。

## 时间处理

```javascript
// 云函数中的时间处理
module.exports = {
  main: async (event, context) => {
    // 格式化时间
    const timeStr = vk.pubfn.timeFormat(new Date()) // 2024-01-01 10:10:10
    const customTime = vk.pubfn.timeFormat(new Date(), "yyyy年MM月dd日")

    // 获取时间范围
    const { todayStart, todayEnd } = vk.pubfn.getCommonTime(new Date())
    const { monthStart, monthEnd } = vk.pubfn.getCommonTime(new Date())

    // 获取偏移时间
    const timestamp = vk.pubfn.getOffsetTime(new Date(), {
      hour: 1,
      minutes: 30,
      mode: "after"
    })

    // 获取相对时间的起止日期
    const { startTime, endTime } = vk极速开发.getDayOffsetStartAndEnd(0) // 今日
    const weekTime = vk.pubfn.getWeekOffsetStartAndEnd(-1) // 上周
    const monthTime = vk.pubfn.getMonthOffsetStartAndEnd(1) // 下月

    return {
      code: 0,
      data: {
        timeStr,
        todayRange: { todayStart, todayEnd },
        weekTime,
        monthTime
      }
    }
  }
}
```

## 数据处理

```javascript
// 云函数中的数据处理
module.exports = {
  main: async (event, context) => {
    const { arrayData, treeData } = event

    // 数组转树形结构
    const tree = vk.pubfn.arrayToTree(arrayData, {
      id: "_id",
      parent_id: "parent_id",
      children: "children"
    })

    // 树形结构转数组
    const array = vk.pubfn.treeToArray(treeData, {
      id: "_id",
      parent_id: "parent_id",
      children: "children"
    })

    // 对象属性拷贝
    const newObj = vk.pubfn.objectAssign(obj1, obj2)

    // 深度克隆对象
    const clonedObj = vk.pubfn.deepClone(originalObj)

    // 根据字符串路径获取对象的值
    const value = vk.pubfn.getData(dataObj, "user.profile.name", '默认值')

    // 根据字符串路径设置对象的值
    vk.pubfn.setData(dataObj, "user.profile.avatar", avatarUrl)

    return {
      code: 0,
      data: {
        tree,
        array,
        processedData: dataObj
      }
    }
  }
}
```

## 数组操作

```javascript
// 云函数中的数组操作
module.exports = {
  main: async (event, context) => {
    const { list1, list2 } = event

    // 两个对象数组合并，去除重复数据
    const mergedArray = vk.pubfn.arr_concat(list1, list2, "_id")

    // 从对象数组中获取某一个对象
    const item = vk.pubfn.getListItem(list, "_id", "001")

    // 获取对象数组中某个元素的index
    const index = vk.pubfn.getListIndex(list, "_id", "001")

    // 同时获取对象和index
    const { item: foundItem, index: foundIndex } = vk.pubfn.getListItemIndex(list, "_id", "001")

    // 对象数组转JSON
    const jsonObj = vk.pubfn.arrayToJson(list, "_id")

    // 从数组中提取指定字段形成新数组
    const idArray = vk.pubfn.arrayObjectGetArray(list, "_id")

    // 分割数组
    const chunks = vk.pubfn.splitArray([1,2,3,4,5,6,7,8,9,10], 3)
    // 返回: [[1,2,3], [4,5,6], [7,8,9], [10]]

    return {
      code: 0,
      data: {
        mergedArray,
        foundItem,
        foundIndex,
        jsonObj,
        idArray,
        chunks
      }
    }
  }
}
```

## 验证和检测

```javascript
// 云函数中的验证和检测
module.exports = {
  main: async (event, context) => {
    const { mobile, email, idCard, formData } = event

    // 检测文本格式
    const isMobileValid = vk.pubfn.test(mobile, 'mobile') // 手机号
    const isEmailValid = vk.pubfn.test(email, 'email') // 邮箱
    const isIdCardValid = vk.pubfn.test(idCard, 'card') // 身份证

    // 允许为空的验证
    const isValidOrEmpty = vk.pubfn.test("", 'mobile', true)

    // 检测参数是否为空
    const isEmpty = vk.pubfn.isNull(formData.title)
    const isNotEmpty = vk.pubfn.isNotNull(formData.content)

    // 检测多个参数
    const hasEmpty = vk.pubfn.isNullOne(formData.title, formData.content, formData.author)
    const allEmpty = vk.pubfn.isNullAll(formData.title, formData.content, formData.author)
    const allNotEmpty = vk.pubfn.isNotNullAll(formData.title, formData.content, formData.author)

    // 检测对象属性
    const nullKey = vk.pubfn.isNullOneByObject({
      title: formData.title,
      content: formData.content,
      author: formData.author
    })

    if (nullKey) {
      return {
        code: -1,
        msg: `${nullKey}不能为空`
      }
    }

    return {
      code: 0,
      data: {
        validation: {
          mobile: isMobileValid,
          email: isEmailValid,
          idCard: isIdCardValid
        },
        checks: {
          hasEmpty,
          allEmpty,
          allNotEmpty
        }
      }
    }
  }
}
```

## 工具函数

```javascript
// 云函数中的工具函数
module.exports = {
  main: async (event, context) => {
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
    const orderNo = vk.pubfn.createOrderNo("ORDER", 25)

    // 字符串转换
    const camelStr = vk.pubfn.snake2camel("user_name") // userName
    const snakeStr = vk.pubfn.camel2snake("userName") // user_name

    // 对象属性名转换
    const camelObj = vk.pubfn.snake2camelJson(event.data) // 蛇形转驼峰
    const snakeObj = vk.pubfn.camel2snakeJson(event.data) // 驼峰转蛇形

    return {
      code: 0,
      data: {
        randomNum,
        randomStr,
        hiddenMobile,
        hasIntersection,
        orderNo,
        camelStr,
        snakeStr,
        convertedObjects: {
          camelObj,
          snakeObj
        }
      }
    }
  }
}
```

## 数值处理

```javascript
// 云函数中的数值处理
module.exports = {
  main: async (event, context) => {
    const { price, percentage, discount, number } = event

    // 保留小数
    const decimal = vk.pubfn.toDecimal(1.56555, 2) // 1.57

    // 金额显示过滤器（分转元）
    const priceInYuan = vk.pubfn.priceFilter(price) // 分转元

    // 百分比显示过滤器
    const percentageStr = vk.pubfn.percentageFilter(percentage) // 转百分比

    // 折扣显示过滤器
    const discountStr = vk.pubfn.discountFilter(discount) // 转折扣

    // 大数字转中文
    const numStr = vk.pubfn.numStr(number) // 数字转中文

    // 单位极速开发换算
    const fileSize = vk.pubfn.calcSize(1024000, ["B","KB","MB","GB"], 1024, 2) // 文件大小

    return {
      code: 0,
      data: {
        decimal,
        priceInYuan,
        percentageStr,
        discountStr,
        numStr,
        fileSize
      }
    }
  }
}