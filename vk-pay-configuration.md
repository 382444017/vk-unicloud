---
type: "manual"
---

# VK-Pay 万能支付插件 - 支付配置详解

## 🎯 微信支付配置

### 微信小程序支付配置

```javascript
"wxpay": {
  // 微信小程序支付
  "mp-weixin": {
    "appId": "",           // 小程序appid
    "secret": "",          // 小程序secret
    "mchId": "",           // 商户号
    "key": "",             // V2 API密钥
    "pfx": fs.readFileSync(__dirname + '/wxpay/apiclient_cert.p12'),
    "v3Key": "",           // V3 API密钥
    "appCertPath": path.join(__dirname, 'wxpay/apiclient_cert.pem'),
    "appPrivateKeyPath": path.join(__dirname, 'wxpay/apiclient_key.pem'),
    "wxpayPublicKeyPath": path.join(__dirname, 'wxpay/pub_key.pem'),
    "wxpayPublicKeyId": "", // V3公钥ID
    "version": 3           // 推荐使用V3版本
  }
}
```

### 微信APP支付配置

```javascript
"wxpay": {
  // 微信APP支付
  "app-plus": {
    "appId": "",           // 开放平台应用appid
    "secret": "",          // 开放平台应用secret
    "mchId": "",           // 商户号
    "key": "",             // V2 API密钥
    "pfx": fs.readFileSync(__dirname + '/wxpay/apiclient_cert.p12'),
    "v3Key": "",           // V3 API密钥
    "appCertPath": path.join(__dirname, 'wxpay/apiclient_cert.pem'),
    "appPrivateKeyPath": path.join(__dirname, 'wxpay/apiclient_key.pem'),
    "wxpayPublicKeyPath": path.join(__dirname, 'wxpay/pub_key.pem'),
    "wxpayPublicKeyId": "",
    "version": 3
  }
}
```

### 微信H5支付配置

```javascript
"wxpay": {
  // 微信H5外部浏览器支付
  "mweb": {
    "appId": "",           // 任意appid
    "secret": "",          // 对应secret
    "mchId": "",           // 商户号
    "key": "",             // V2 API密钥
    "sceneInfo": {         // 场景信息（必填）
      "h5_info": {
        "type": "Wap",
        "wap_url": "https://www.example.com",
        "wap_name": "网站名称"
      }
    },
    "version": 2           // H5支付推荐使用V2版本
  }
}
```

### 微信转账配置

```javascript
"wxpay": {
  // 微信转账
  "transfer": {
    "appId": "",           // 任意appid
    "mchId": "",           // 商户号
    "v3Key": "",           // V3 API密钥
    "appCertPath": path.join(__dirname, 'wxpay/apiclient_cert.pem'),
    "appPrivateKeyPath": path.join(__dirname, 'wxpay/apiclient_key.pem'),
    "wxpayPublicKeyPath": path.join(__dirname, 'wxpay/pub_key.pem'),
    "wxpayPublicKeyId": "",
    "version": 3           // 转账只支持V3
  }
}
```

## 💰 支付宝支付配置

### 支付宝小程序支付（密钥模式）

```javascript
"alipay": {
  // 支付宝小程序支付（密钥模式）
  "mp-alipay": {
    "appId": "",           // 支付宝小程序appid
    "privateKey": "",      // 应用私钥
    "alipayPublicKey": "", // 支付宝公钥
    "sandbox": false       // 是否沙箱环境
  }
}
```

### 支付宝APP支付（证书模式）

```javascript
"alipay": {
  // 支付宝APP支付（证书模式）
  "app-plus": {
    "appId": "",           // 开放平台应用appid
    "privateKey": "",      // 应用私钥
    "alipayPublicCertPath": path.join(__dirname, 'alipay/alipayCertPublicKey_RSA2.crt'),
    "alipayRootCertPath": path.join(__dirname, 'alipay/alipayRootCert.crt'),
    "appCertPath": path.join(__dirname, 'alipay/appCertPublicKey.crt'),
    "sandbox": false
  }
}
```

### 支付宝H5支付（当面付）

```javascript
"alipay": {
  // 支付宝H5支付（当面付）
  "h5": {
    "appId": "",           // 开放平台应用appid
    "privateKey": "",      // 应用私钥
    "alipayPublicCertPath": path.join(__dirname, 'alipay/alipayCertPublicKey_RSA2.crt'),
    "alipayRootCertPath": path.join(__dirname, 'alipay/alipayRootCert.crt'),
    "appCertPath": path.join(__dirname, 'alipay/appCertPublicKey.crt'),
    "sandbox": false
  }
}
```

### 支付宝转账配置

```javascript
"alipay": {
  // 支付宝转账
  "transfer": {
    "appId": "",           // 开放平台应用appid
    "privateKey": "",      // 应用私钥
    "alipayPublicCertPath": path.join(__dirname, 'alipay/alipayCertPublicKey_RSA2.crt'),
    "alipayRootCertPath": path.join(__dirname, 'alipay/alipayRootCert.crt'),
    "appCertPath": path.join(__dirname, 'alipay/appCertPublicKey.crt'),
    "sandbox": false
  }
}
```

## 🍎 iOS内购支付配置

```javascript
"appleiap": {
  "app-plus": {
    "password": "App专用共享密钥",
    "timeout": 10000,
    "receiptExpiresIn": 86400,
    "sandbox": true    // 测试环境为true，正式环境为false
  }
}
```

## 👤 个人支付配置

### VksPay个人支付配置

```javascript
"vkspay": {
  "mchId": "商户号",
  "key": "商户密钥"
}
```

### 微信小程序虚拟支付配置

```javascript
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

## 📋 配置注意事项

### 1. 证书文件管理

证书文件应放置在以下目录结构：
```
uniCloud/
└── cloudfunctions/
    └── common/
        └── uni-config-center/
            └── uni-pay/
                ├── config.js
                ├── wxpay/
                │   ├── apiclient_cert.p12
                │   ├── apiclient_cert.pem
                │   ├── apiclient_key.pem
                │   └── pub_key.pem
                └── alipay/
                    ├── alipayCertPublicKey_RSA2.crt
                    ├── alipayRootCert.crt
                    └── appCertPublicKey.crt
```

### 2. 环境配置

```javascript
// 根据环境动态配置
const isDevelopment = process.env.NODE_ENV === 'development';

module.exports = {
  "wxpay": {
    "mp-weixin": {
      "sandbox": isDevelopment, // 开发环境使用沙箱
      // 其他配置...
    }
  },
  "alipay": {
    "mp-alipay": {
      "sandbox": isDevelopment, // 开发环境使用沙箱
      // 其他配置...
    }
  }
};
```

### 3. 多环境配置

```javascript
// 多环境配置示例
const configs = {
  development: {
    // 开发环境配置
    wxpay: { /* 开发环境微信配置 */ },
    alipay: { /* 开发环境支付宝配置 */ }
  },
  production: {
    // 生产环境配置
    wxpay: { /* 生产环境微信配置 */ },
    alipay: { /* 生产环境支付宝配置 */ }
  }
};

const env = process.env.UNI_CLOUD_ENV || 'development';
module.exports = configs[env];
```

## 🔐 安全配置

### 回调安全配置

```javascript
module.exports = {
  // 回调通信密钥（建议64位以上随机字符串）
  "notifyKey": "5fb2cd73c7b53918728417c50762e6d45fb2cd73c7b53918728417c50762e6d4",
  
  // 统一支付回调地址
  "notifyUrl": {
    "space-dev-id": "https://fc-mp-space-dev-id.next.bspapp.com/http/vk-pay",
    "space-prod-id": "https://fc-mp-space-prod-id.next.bspapp.com/http/vk-pay"
  },
  
  // 用户白名单（测试环境使用）
  "userWhitelist": isDevelopment ? ["test_user_id_1", "test_user_id_2"] : []
};
```

## 🔗 相关文档

- [插件介绍和安装](vk-pay-introduction.md)
- [支付API使用](vk-pay-api-usage.md)
- [回调处理和前端组件](vk-pay-callback-frontend.md)
- [多商户和特殊支付](vk-pay-advanced.md)
- [错误处理和最佳实践](vk-pay-error-handling.md)