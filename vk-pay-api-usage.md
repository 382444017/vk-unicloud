---
type: "manual"
---

# VK-Pay 万能支付插件 - 支付API使用

## 🎯 核心API使用

### 创建支付订单

```javascript
const vkPay = require("vk-uni-pay");

module.exports = {
  main: async (event, context) => {
    let res = await vkPay.createPayment({
      context,                    // 客户端环境（必填）
      provider: "wxpay",          // 支付供应商
      data: {
        openid: "用户openid",     // 小程序支付必填
        out_trade_no: "订单号",   // 商户订单号（必填）
        total_fee: 100,           // 订单金额（分）
        subject: "订单标题",      // 订单标题
        type: "recharge",         // 订单类型
        custom: {                 // 自定义回调数据
          user_id: "用户ID",
          recharge_balance: 100
        },
        other: {                  // 其他参数
          // 微信、支付宝官方参数
        }
      }
    });
    
    return res;
  }
};
```

### 支付供应商类型

- `wxpay` - 微信支付官方
- `alipay` - 支付宝支付官方  
- `appleiap` - iOS内购支付
- `vkspay` - VksPay个人支付
- `wxpay-virtual` - 微信小程序虚拟支付
- `douyin` - 抖音支付
- `huawei` - 华为支付

### 查询支付状态

```javascript
const vkPay = require("vk-uni-pay");

let res = await vkPay.queryPayment({
  provider: "wxpay",
  out_trade_no: "商户订单号"
});

// 返回结果示例
{
  code: 0,
  msg: "success",
  data: {
    trade_state: "SUCCESS", // 支付状态
    total_fee: 100,         // 订单金额
    time_end: "20240101120000" // 支付完成时间
  }
}
```

### 申请退款

```javascript
const vkPay = require("vk-uni-pay");

let res = await vkPay.refund({
  provider: "wxpay",
  out_trade_no: "原订单号",
  out_refund_no: "退款单号", 
  total_fee: 100,        // 原订单金额
  refund_fee: 50,        // 退款金额
  refund_desc: "退款原因"
});

// 返回结果示例
{
  code: 0,
  msg: "success",
  data: {
    refund_id: "微信退款单号",
    refund_fee: 50
  }
}
```

### 转账到用户

```javascript
const vkPay = require("vk-uni-pay");

// 微信转账
let res = await vkPay.transfer({
  provider: "wxpay",
  out_trade_no: "转账单号",
  openid: "用户openid",
  re_user_name: "用户姓名",
  money: 100,            // 转账金额（分）
  desc: "转账说明"
});

// 支付宝转账
let res = await vkPay.transfer({
  provider: "alipay", 
  out_trade_no: "转账单号",
  payee_account: "支付宝账号",
  payee_real_name: "真实姓名",
  money: 100,            // 转账金额（分）
  desc: "转账说明"
});
```

## 📊 订单类型说明

### 充值订单

```javascript
// 创建充值订单
let res = await vkPay.createPayment({
  context,
  provider: "wxpay",
  data: {
    out_trade_no: "RECHARGE_" + Date.now(),
    total_fee: 10000, // 100元
    subject: "账户充值",
    type: "recharge",
    custom: {
      user_id: "user_001",
      recharge_balance: 10000
    }
  }
});
```

### 商品订单

```javascript
// 创建商品订单
let res = await vkPay.createPayment({
  context,
  provider: "alipay",
  data: {
    out_trade_no: "GOODS_" + Date.now(),
    total_fee: 19900, // 199元
    subject: "商品名称",
    type: "goods",
    custom: {
      user_id: "user_001",
      goods_id: "goods_001",
      quantity: 1
    }
  }
});
```

### 会员订单

```javascript
// 创建会员订单
let res = await vkPay.createPayment({
  context,
  provider: "wxpay",
  data: {
    out_trade_no: "VIP_" + Date.now(),
    total_fee: 29900, // 299元
    subject: "VIP会员",
    type: "vip",
    custom: {
      user_id: "user_001",
      vip_type: "year",
      vip_days: 365
    }
  }
});
```

### 自定义订单

```javascript
// 创建自定义订单
let res = await vkPay.createPayment({
  context,
  provider: "alipay",
  data: {
    out_trade_no: "CUSTOM_" + Date.now(),
    total_fee: 5000, // 50元
    subject: "自定义服务",
    type: "custom",
    custom: {
      user_id: "user_001",
      service_type: "consulting",
      duration: 60
    }
  }
});
```

## 🔄 订单状态管理

### 查询订单列表

```javascript
// 通过数据库查询订单
const orderList = await vk.baseDao.select({
  dbName: "vk-pay-orders",
  where: {
    user_id: "user_001",
    status: "SUCCESS"
  },
  orderBy: "create_date desc",
  pageIndex: 1,
  pageSize: 10
});
```

### 订单状态流转

```javascript
// 订单状态处理逻辑
async function handleOrderStatus(order) {
  switch (order.status) {
    case "NOTPAY": // 未支付
      console.log("订单未支付");
      break;
    case "SUCCESS": // 支付成功
      await processSuccessfulOrder(order);
      break;
    case "REFUND": // 已退款
      console.log("订单已退款");
      break;
    case "CLOSED": // 已关闭
      console.log("订单已关闭");
      break;
    default:
      console.log("未知订单状态");
  }
}
```

## 🎯 平台特定参数

### 微信支付特定参数

```javascript
let res = await vkPay.createPayment({
  context,
  provider: "wxpay",
  data: {
    out_trade_no: "ORDER_" + Date.now(),
    total_fee: 100,
    subject: "测试订单",
    type: "goods",
    other: {
      // 微信特定参数
      detail: JSON.stringify({
        goods_detail: [
          {
            goods_id: "123",
            goods_name: "测试商品",
            quantity: 1,
            price: 100
          }
        ]
      }),
      attach: "附加数据"
    }
  }
});
```

### 支付宝支付特定参数

```javascript
let res = await vkPay.createPayment({
  context,
  provider: "alipay",
  data: {
    out_trade_no: "ORDER_" + Date.now(),
    total_fee: 100,
    subject: "测试订单",
    type: "goods",
    other: {
      // 支付宝特定参数
      timeout_express: "30m", // 超时时间
      goods_type: "0",        // 商品类型
      promo_params: {}        // 营销参数
    }
  }
});
```

## 📱 客户端集成

### 获取支付参数

```javascript
// 前端调用获取支付参数
async function getPaymentParams(orderData) {
  try {
    const res = await uniCloud.callFunction({
      name: 'vk-pay',
      data: {
        url: 'pay/createPayment',
        data: orderData
      }
    });
    
    if (res.result.code === 0) {
      return res.result.data;
    } else {
      throw new Error(res.result.msg);
    }
  } catch (error) {
    console.error('获取支付参数失败:', error);
    throw error;
  }
}
```

### 处理支付结果

```javascript
// 前端处理支付结果
function handlePaymentResult(result) {
  if (result.code === 0) {
    // 支付成功
    uni.showToast({
      title: '支付成功',
      icon: 'success'
    });
    
    // 跳转到成功页面
    uni.navigateTo({
      url: '/pages/payment/success'
    });
  } else {
    // 支付失败
    uni.showToast({
      title: result.msg || '支付失败',
      icon: 'none'
    });
  }
}
```

## 🔗 相关文档

- [插件介绍和安装](vk-pay-introduction.md)
- [支付配置详解](vk-pay-configuration.md)
- [回调处理和前端组件](vk-pay-callback-frontend.md)
- [多商户和特殊支付](vk-pay-advanced.md)
- [错误处理和最佳实践](vk-pay-error-handling.md)