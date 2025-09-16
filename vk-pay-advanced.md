---
type: "manual"
---

# VK-Pay 万能支付插件 - 多商户和特殊支付

## 🏪 多商户模式

### 数据库配置表结构

```javascript
// vk-pay-config 表结构
{
  "_id": "merchant_001", // 商户ID
  "name": "商户A",        // 商户名称
  "status": "active",     // 状态：active/inactive
  "wxpay": {
    "mp-weixin": {
      "appId": "商户A的小程序appid",
      "secret": "商户A的小程序secret",
      "mchId": "商户A的微信商户号",
      "key": "V2 API密钥",
      "v3Key": "V3 API密钥",
      "appCertPath": "path/to/merchant_a/cert.pem",
      "appPrivateKeyPath": "path/to/merchant_a/key.pem",
      "wxpayPublicKeyPath": "path/to/merchant_a/pub_key.pem",
      "version": 3
    }
  },
  "alipay": {
    "mp-alipay": {
      "appId": "商户A的支付宝appid",
      "privateKey": "商户A的支付宝私钥",
      "alipayPublicKey": "商户A的支付宝公钥",
      "sandbox": false
    }
  },
  "create_date": 1640995200000, // 创建时间
  "update_date": 1640995200000  // 更新时间
}
```

### 多商户支付调用

```javascript
// 指定商户ID创建支付
const vkPay = require("vk-uni-pay");

module.exports = {
  main: async (event, context) => {
    let res = await vkPay.createPayment({
      context,
      provider: "wxpay",
      data: {
        pid: "merchant_001",    // 指定商户ID
        out_trade_no: "ORDER_" + Date.now(),
        total_fee: 100,
        subject: "商品名称",
        type: "goods",
        custom: {
          user_id: "user_001",
          goods_id: "goods_001"
        }
      }
    });
    
    return res;
  }
};
```

### 商户配置管理

```javascript
// 商户配置管理云函数
module.exports = {
  // 添加商户配置
  async addMerchantConfig(data = {}) {
    const { merchant_id, name, wxpay_config, alipay_config } = data;
    
    await vk.baseDao.add({
      dbName: "vk-pay-config",
      dataJson: {
        _id: merchant_id,
        name,
        wxpay: wxpay_config,
        alipay: alipay_config,
        status: "active",
        create_date: new Date().getTime(),
        update_date: new Date().getTime()
      }
    });
    
    return { code: 0, msg: "商户配置添加成功" };
  },
  
  // 更新商户配置
  async updateMerchantConfig(data = {}) {
    const { merchant_id, wxpay_config, alipay_config } = data;
    
    await vk.baseDao.updateById({
      dbName: "vk-pay-config",
      id: merchant_id,
      dataJson: {
        wxpay: wxpay_config,
        alipay: alipay_config,
        update_date: new Date().getTime()
      }
    });
    
    return { code: 0, msg: "商户配置更新成功" };
  },
  
  // 获取商户配置
  async getMerchantConfig(merchant_id) {
    const config = await vk.baseDao.findById({
      dbName: "vk-pay-config",
      id: merchant_id
    });
    
    if (!config || config.status !== "active") {
      return { code: 400, msg: "商户配置不存在或已停用" };
    }
    
    return { code: 0, data: config };
  }
};
```

## 🍎 iOS内购支付

### 内购配置

```javascript
// config.js 中配置iOS内购
"appleiap": {
  "app-plus": {
    "password": "App专用共享密钥",
    "timeout": 10000,
    "receiptExpiresIn": 86400,
    "sandbox": true    // 测试环境为true，正式环境为false
  }
}
```

### 内购支付调用

```javascript
// 创建iOS内购支付
const vkPay = require("vk-uni-pay");

let res = await vkPay.createPayment({
  context,
  provider: "appleiap",
  data: {
    out_trade_no: "ORDER_" + Date.now(),
    total_fee: 100,
    subject: "内购商品",
    type: "goods",
    productid: "com.example.product1",  // App Store商品ID
    custom: {
      user_id: "user_001",
      product_type: "premium"
    }
  }
});
```

### 内购验证处理

```javascript
// 内购验证回调处理
module.exports = {
  main: async (event) => {
    let { data, userInfo, util } = event;
    let { vk } = util;

    let { out_trade_no, user_id, product_type } = data;

    // 验证内购收据
    const verifyRes = await vkPay.verifyAppleIapReceipt({
      receipt_data: data.receipt_data
    });

    if (!verifyRes.valid) {
      return { code: 400, msg: "内购验证失败" };
    }

    // 处理内购商品
    switch (product_type) {
      case "premium":
        await handlePremiumPurchase(user_id);
        break;
      case "coins":
        await handleCoinsPurchase(user_id, data.quantity);
        break;
      default:
        return { code: 400, msg: "未知商品类型" };
    }

    return { code: 0, msg: "内购处理成功" };
  }
};
```

## 👤 个人支付（VksPay）

### VksPay配置

```javascript
// config.js 中配置VksPay
"vkspay": {
  "mchId": "商户号",
  "key": "商户密钥"
}
```

### VksPay支付调用

```javascript
// 创建VksPay支付
const vkPay = require("vk-uni-pay");

let res = await vkPay.createPayment({
  context,
  provider: "vkspay",
  data: {
    out_trade_no: "ORDER_" + Date.now(),
    total_fee: 100,
    subject: "商品名称",
    type: "goods",
    return_url: "https://example.com/return",  // 同步回调地址
    custom: {
      user_id: "user_001",
      goods_id: "goods_001"
    }
  }
});
```

## 🎮 微信小程序虚拟支付

### 虚拟支付配置

```javascript
// config.js 中配置虚拟支付
"wxpay-virtual": {
  "mp-weixin": {
    "appId": "小程序appid",
    "secret": "小程序secret",
    "mchId": "商户号",
    "offerId": "支付应用ID",
    "appKey": "现网AppKey",
    "sandboxAppKey": "沙箱AppKey",
    "rate": 100,              // 1元=100代币
    "token": "消息推送Token",
    "encodingAESKey": "消息加密密钥",
    "sandbox": false
  }
}
```

### 虚拟支付调用

```javascript
// 创建虚拟支付
const vkPay = require("vk-uni-pay");

let res = await vkPay.createPayment({
  context,
  provider: "wxpay-virtual",
  data: {
    out_trade_no: "ORDER_" + Date.now(),
    total_fee: 100,
    subject: "虚拟商品",
    type: "virtual",
    custom: {
      user_id: "user_001",
      virtual_goods_id: "vg_001",
      quantity: 1
    }
  }
});
```

## 📱 抖音支付

### 抖音支付配置

```javascript
// config.js 中配置抖音支付
"douyin": {
  "mp-toutiao": {
    "appId": "抖音小程序appid",
    "secret": "抖音小程序secret",
    "mchId": "商户号",
    "key": "API密钥",
    "sandbox": false
  }
}
```

### 抖音支付调用

```javascript
// 创建抖音支付
const vkPay = require("vk-uni-pay");

let res = await vkPay.createPayment({
  context,
  provider: "douyin",
  data: {
    out_trade_no: "ORDER_" + Date.now(),
    total_frade_no: "ORDER_" + Date.now(),
    total_fee: 100,
    subject: "商品名称",
    type: "goods",
    custom: {
      user_id: "user_001",
      goods_id: "goods_001"
    }
  }
});
```

## 📱 华为支付

### 华为支付配置

```javascript
// config.js 中配置华为支付
"huawei": {
  "app-plus": {
    "appId": "华为应用appid",
    "secret": "华为应用secret",
    "mchId": "商户号",
    "key": "API密钥",
    "sandbox": false
  }
}
```

### 华为支付调用

```javascript
// 创建华为支付
const vkPay = require("vk-uni-pay");

let res = await vkPay.createPayment({
  context,
  provider: "huawei",
  data: {
    out_trade_no: "ORDER_" + Date.now(),
    total_fee: 100,
    subject: "商品名称",
    type: "goods",
    custom: {
      user_id: "user_001",
      goods_id: "goods_001"
    }
  }
});
```

## 💳 付款码支付

### 付款码支付配置

```javascript
// 使用现有的微信/支付宝配置即可支持付款码支付
// 无需额外配置
```

### 付款码支付调用

```javascript
// 创建付款码支付
const vkPay = require("vk-uni-pay");

let res = await vkPay.createPayment({
  context,
  provider: "wxpay", // 或 "alipay"
  data: {
    out_trade_no: "ORDER_" + Date.now(),
    total_fee: 100,
    subject: "商品名称",
    type: "barcode",
    auth_code: "134633259402361560", // 用户付款码
    custom: {
      user_id: "user_001",
      goods_id: "goods_001"
    }
  }
});
```

## 🔄 分账功能

### 分账配置

```javascript
// 分账配置示例
async function setupProfitSharing(orderData) {
  const { total_fee, profit_sharing_rules } = orderData;
  
  // 计算分账金额
  const sharingAmounts = profit_sharing_rules.map(rule => {
    return {
      receiver: rule.receiver_id,
      amount: Math.floor(total_fee * rule.percentage),
      description: rule.description
    };
  });
  
  // 创建分账订单
  const res = await vkPay.createPayment({
    context,
    provider: "wxpay",
    data: {
      ...orderData,
      profit_sharing: true,
      profit_sharing_receivers: sharingAmounts
    }
  });
  
  return res;
}
```

### 分账回调处理

```javascript
// 分账成功回调处理
module.exports = {
  main: async (event) => {
    let { data, userInfo, util } = event;
    let { vk } = util;

    let { out_trade_no, profit_sharing_receivers } = data;

    // 处理分账结果
    for (let receiver of profit_sharing_receivers) {
      await vk.baseDao.add({
        dbName: "profit_sharing_logs",
        dataJson: {
          out_trade_no,
          receiver_id: receiver.receiver,
          amount: receiver.amount,
          status: "success",
          create_date: new Date().getTime()
        }
      });
    }

    return { code: 0, msg: "分账处理成功" };
  }
};
```

## 🌐 跨境支付

### 跨境支付配置

```javascript
// 跨境支付配置示例
"wxpay": {
  "cross-border": {
    "appId": "跨境应用appid",
    "mchId": "跨境商户号",
    "sub_mch_id": "子商户号",
    "key": "API密钥",
    "currency": "USD", // 结算货币
    "rate": 6.5,       // 汇率
    "sandbox": false
  }
}
```

### 跨境支付调用

```javascript
// 创建跨境支付
const vkPay = require("vk-uni-pay");

let res = await vkPay.createPayment({
  context,
  provider: "wxpay",
  data: {
    out_trade_no: "ORDER_" + Date.now(),
    total_fee: 1000, // 美元金额*100
    fee_type: "USD",  // 货币类型
    subject: "International Product",
    type: "cross-border",
    custom: {
      user_id: "user_001",
      product_id: "product_001",
      original_amount: 15.99, // 原始金额
      original_currency: "USD"
    }
  }
});
```

## 🔧 高级配置管理

### 动态配置加载

```javascript
// 动态加载支付配置
async function getDynamicPaymentConfig(provider, platform, merchant_id = null) {
  if (merchant_id) {
    // 加载商户特定配置
    const merchantConfig = await vk.baseDao.findById({
      dbName: "vk-pay-config",
      id: merchant_id
    });
    
    if (merchantConfig && merchantConfig[provider] && merchantConfig[provider][platform]) {
      return merchantConfig[provider][platform];
    }
  }
  
  // 加载默认配置
  const uniPayConfig = require('uni-config-center')({
    pluginId: 'uni-pay'
  }).config();
  
  return uniPayConfig[provider] && uniPayConfig[provider][platform];
}
```

### 配置热更新

```javascript
// 配置热更新监听
function setupConfigHotReload() {
  // 监听配置表变化
  vk.baseDao.watch({
    dbName: "vk-pay-config",
    callback: (change) => {
      console.log('支付配置已更新:', change);
      // 重新加载配置
      reloadPaymentConfigs();
    }
  });
}

// 重新加载配置
function reloadPaymentConfigs() {
  // 清空配置缓存
  vkPay.clearConfigCache();
  
  // 重新初始化支付模块
  vkPay.init();
}
```

## 🔗 相关文档

- [插件介绍和安装](vk-pay-introduction.md)
- [支付配置详解](vk-pay-configuration.md)
- [支付API使用](vk-pay-api-usage.md)
- [回调处理和前端组件](vk-pay-callback-frontend.md)
- [错误处理和最佳实践](vk-pay-error-handling.md)