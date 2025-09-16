---
type: "manual"
---

# VK-Pay 万能支付插件 - 介绍和安装配置

## 📦 插件介绍

VK-Pay (vk-uni-pay) 是基于 UniCloud 量身定制的万能支付插件，兼容任何 UniCloud 框架。

### 核心特性
- **多平台支持**: 支持微信支付、支付宝支付、苹果内购、抖音支付、华为支付等
- **多场景覆盖**: 小程序支付、APP支付、H5支付、PC扫码支付、公众号支付
- **高度集成**: 与VK-UniCloud框架深度集成，开箱即用
- **安全可靠**: 支持V2和V3版本，完整的证书验证机制
- **灵活配置**: 支持多商户模式、个人支付、虚拟支付等

### 支持的支付方式

| 支付方式 | 支持情况 | 说明 | 开通要求 |
|---------|---------|------|---------|
| 微信小程序支付 | ✅ | 在微信小程序中支付 | 微信小程序支付 |
| 支付宝小程序支付 | ✅ | 在支付宝小程序中支付 | 支付宝JSAPI支付 |
| APP-微信支付 | ✅ | 在APP中唤起微信客户端支付 | 微信APP支付 |
| APP-支付宝支付 | ✅ | 在APP中唤起支付宝客户端支付 | 支付宝APP支付 |
| H5手机-微信支付 | ✅ | 在H5浏览器中唤起微信客户端支付 | 微信Native支付 |
| H5手机-支付宝支付 | ✅ | 在H5浏览器中唤起支付宝客户端支付 | 支付宝当面付 |
| PC扫码支付-微信 | ✅ | PC浏览器显示二维码，微信扫码支付 | 微信Native支付 |
| PC扫码支付-支付宝 | ✅ | PC浏览器显示二维码，支付宝扫码支付 | 支付宝当面付 |
| 公众号H5-微信支付 | ✅ | 在微信公众号H5页面中支付 | 微信JSAPI支付 |
| 转账到支付宝余额 | ✅ | 给用户提现到支付宝（秒到） | 转账到支付宝账户 |
| 转账到微信零钱 | ✅ | 给用户提现到微信（秒到） | 商家转账 |
| IOS内购支付 | ✅ | Apple App Store应用内购买 | 苹果应用内购 |
| 个人支付(VksPay) | ✅ | 无需营业执照的个人支付接口 | VksPay个人支付 |
| 微信小程序虚拟支付 | ✅ | 短剧类目微信小程序虚拟支付 | 微信小程序虚拟支付 |
| 抖音小程序支付 | ✅ | 在抖音小程序中支付 | 抖音支付 |
| 付款码支付 | ✅ | 商家扫用户付款码支付 | 支付宝当面付、微信付款码支付 |
| 华为支付 | ✅ | 鸿蒙元服务内华为支付 | 华为支付 |

## 🚀 快速安装

### 1. 下载安装步骤

1. 从插件市场购买并安装 `vk-uni-pay` 插件
2. 在云函数中引入公共模块依赖：`uni-config-center`、`vk-uni-pay`
3. 配置支付参数文件：`uniCloud/cloudfunctions/common/uni-config-center/uni-pay/config.js`
4. 复制示例项目中的云函数代码到 `uniCloud/cloudfunctions/vk-pay/service/`
5. 上传公共模块 `vk-uni-pay` 和云函数 `vk-pay`

### 2. 配置uni-id小程序参数

```json
// cloudfunctions/common/uni-config-center/uni-id/config.json
{
  "mp-weixin": {
    "oauth": {
      "weixin": {
        "appid": "微信小程序appid",
        "appsecret": "微信小程序secret"
      }
    }
  },
  "mp-alipay": {
    "oauth": {
      "alipay": {
        "appid": "支付宝小程序appid", 
        "privateKey": "支付宝小程序privateKey"
      }
    }
  }
}
```

### 3. 安全提醒

**生产环境必须删除以下测试文件：**
- `vk-pay/service/pay/refund.js` - 退款接口
- `vk-pay/service/pay/transfer.js` - 转账接口

退款和转账功能应在业务云函数中实现，并严格验证权限。

## ⚙️ 基础配置

### 配置结构

```javascript
// cloudfunctions/common/uni-config-center/uni-pay/config.js
const fs = require('fs');
const path = require('path');

module.exports = {
  // 统一支付回调地址
  "notifyUrl": {
    "服务空间SpaceID": "URL化完整地址"
  },
  
  // 回调通信密钥（建议64位以上）
  "notifyKey": "5fb2cd73c7b53918728417c50762e6d45fb2cd73c7b53918728417c50762e6d4",
  
  // 自动删除过期订单（天数，0表示不删除）
  "autoDeleteExpiredOrders": 0,
  
  // 支付宝APP支付转H5支付
  "alipayAppPayToH5Pay": false,
  
  // 用户白名单（测试时使用）
  // "userWhitelist": [],
  
  // 微信支付配置
  "wxpay": {
    // 各平台配置...
  },
  
  // 支付宝支付配置
  "alipay": {
    // 各平台配置...
  }
};
```

## 🔗 相关文档

- [支付配置详解](vk-pay-configuration.md)
- [支付API使用](vk-pay-api-usage.md)
- [回调处理和前端组件](vk-pay-callback-frontend.md)
- [多商户和特殊支付](vk-pay-advanced.md)
- [错误处理和最佳实践](vk-pay-error-handling.md)