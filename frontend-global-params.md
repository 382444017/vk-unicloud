---
type: "manual"
description: "全局请求参数配置"
---

# 全局请求参数

## 方式一：setCustomClientInfo

推荐使用此方式，参数与业务数据隔离：

```javascript
// 设置全局参数
vk.setCustomClientInfo({
  shop_id: "001",
  tenant_id: "tenant_001"
})

// 云函数中获取（云函数）
const customClientInfo = originalParam.context.customInfo
console.log('店铺ID:', customClientInfo.shop_id)

// 云对象中获取
const customClientInfo = this.getCustomClientInfo()
console.log('店铺ID:', customClientInfo.shop_id)
```

## 方式二：updateRequestGlobalParam

基于正则表达式的路由匹配：

```javascript
// 设置全局参数
vk.callFunctionUtil.updateRequestGlobalParam({
  "shop-manage": {
    regExp: "^shop/kh/", // 正则表达式匹配
    data: {
      shop_id: "001"
    }
  },
  "user-manage": {
    regExp: ["^user/kh/", "^member/kh/"], // 支持多个正则
    data: {
      tenant_id: "tenant_001"
    }
  }
})

// 手动指定使用某个配置
vk.callFunction({
  url: 'other/api/getData',
  globalParamName: "shop-manage", // 手动指定配置
  data: {
    keyword: "test"
  }
})
```

## 多配置管理

```javascript
// 动态切换店铺
const switchShop = (shopId) => {
  vk.setCustomClientInfo({
    shop_id: shopId
  })

  // 或者使用方式二
  vk.callFunctionUtil.updateRequestGlobalParam({
    "shop-manage": {
      regExp: "^shop/kh/",
      data: {
        shop_id: shopId
      }
    }
  })
}

// 清除全局参数
vk.setCustomClientInfo({})