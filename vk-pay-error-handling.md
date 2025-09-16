---
type: "manual"
---

# VK-Pay 万能支付插件 - 错误处理和最佳实践

## 🚨 错误处理

### 常见错误码

| 错误码 | 错误类型 | 说明 | 解决方案 |
|--------|---------|------|---------|
| 40001 | 参数错误 | 缺少必要参数或参数格式错误 | 检查参数完整性 |
| 40002 | 签名错误 | 签名验证失败 | 检查API密钥和签名算法 |
| 40003 | 证书错误 | 证书文件错误或路径不正确 | 确保证书文件正确配置 |
| 40004 | 网络错误 | 支付网关网络连接失败 | 检查网络连接，重试操作 |
| 40005 | 订单不存在 | 查询的订单不存在 | 检查订单号是否正确 |
| 40006 | 订单已支付 | 订单已经支付成功 | 无需重复支付 |
| 40007 | 余额不足 | 账户余额不足 | 提示用户充值 |
| 40008 | 频率限制 | API调用频率超限 | 降低调用频率 |
| 40009 | 权限不足 | 没有操作权限 | 检查API权限配置 |
| 40010 | 系统繁忙 | 支付系统繁忙 | 稍后重试 |

### 错误处理示例

```javascript
// 统一的错误处理函数
function handlePaymentError(error) {
  console.error('支付错误:', error);
  
  let errorCode = error.code || 'UNKNOWN_ERROR';
  let errorMessage = error.msg || error.message || '支付失败';
  
  switch (errorCode) {
    case '40001': // 参数错误
      return {
        code: 40001,
        msg: '参数错误，请检查输入',
        action: '检查参数完整性'
      };
      
    case '40002': // 签名错误
      return {
        code: 40002,
        msg: '签名验证失败',
        action: '检查API密钥配置'
      };
      
    case '40003': // 证书错误
      return {
        code: 40003,
        msg: '证书配置错误',
        action: '确保证书文件正确'
      };
      
    case '40004': // 网络错误
      return {
        code: 40004,
        msg: '网络连接失败，请重试',
        action: '检查网络连接'
      };
      
    case '40005': // 订单不存在
      return {
        code: 40005,
        msg: '订单不存在',
        action: '检查订单号'
      };
      
    case '40006': // 订单已支付
      return {
        code: 40006,
        msg: '订单已支付，无需重复操作',
        action: '查询订单状态'
      };
      
    case '40007': // 余额不足
      return {
        code: 40007,
        msg: '余额不足，请充值',
        action: '提示用户充值'
      };
      
    case '40008': // 频率限制
      return {
        code: 40008,
        msg: '操作过于频繁，请稍后重试',
        action: '降低调用频率'
      };
      
    case '40009': // 权限不足
      return {
        code: 40009,
        msg: '权限不足，无法操作',
        action: '检查API权限'
      };
      
    case '40010': // 系统繁忙
      return {
        code: 40010,
        msg: '系统繁忙，请稍后重试',
        action: '稍后重试'
      };
      
    default:
      return {
        code: 50000,
        msg: '系统错误，请联系客服',
        action: '联系技术支持'
      };
  }
}
```

### 前端错误处理

```javascript
// 前端支付错误处理
async function handlePayment() {
  try {
    const result = await vkPay.createPayment(paymentData);
    
    if (result.code === 0) {
      // 支付成功处理
      await handlePaymentSuccess(result);
    } else {
      // 支付失败处理
      const errorInfo = handlePaymentError(result);
      
      uni.showModal({
        title: '支付失败',
        content: errorInfo.msg,
        showCancel: false,
        confirmText: '知道了'
      });
      
      // 记录错误日志
      await recordPaymentError(errorInfo, paymentData);
    }
    
  } catch (error) {
    console.error('支付异常:', error);
    
    uni.showModal({
      title: '系统错误',
      content: '支付系统异常，请稍后重试',
      showCancel: false,
      confirmText: '确定'
    });
    
    // 记录异常日志
    await recordPaymentException(error, paymentData);
  }
}
```

## 📊 日志记录

### 支付日志记录

```javascript
// 支付日志记录函数
async function recordPaymentLog(logData) {
  const { type, status, out_trade_no, provider, amount, user_id, error } = logData;
  
  try {
    await vk.baseDao.add({
      dbName: "payment_logs",
      dataJson: {
        type,
        status,
        out_trade_no,
        provider,
        amount,
        user_id,
        error_info: error ? JSON.stringify(error) : null,
        ip_address: context.CLIENTIP,
        user_agent: context.USER_AGENT,
        create_date: new Date().getTime(),
        update_date: new Date().getTime()
      }
    });
  } catch (logError) {
    console.error('记录支付日志失败:', logError);
  }
}
```

### 错误日志记录

```javascript
// 错误日志记录
async function recordPaymentError(errorInfo, paymentData) {
  await vk.baseDao.add({
    dbName: "payment_errors",
    dataJson: {
      error_code: errorInfo.code,
      error_message: errorInfo.msg,
      error_action: errorInfo.action,
      payment_data: JSON.stringify(paymentData),
      stack_trace: errorInfo.stack || '',
      create_date: new Date().getTime()
    }
  });
}
```

## 🛡️ 安全最佳实践

### 1. 生产环境安全配置

```javascript
// 生产环境安全配置
module.exports = {
  // 删除测试接口
  "removeTestEndpoints": true,
  
  // 启用HTTPS
  "requireHTTPS": true,
  
  // 设置合理的超时时间
  "timeout": 10000,
  
  // 启用请求频率限制
  "rateLimit": {
    "windowMs": 60000,
    "max": 100
  },
  
  // 启用IP白名单
  "ipWhitelist": [
    "192.168.1.0/24",
    "10.0.0.0/8"
  ],
  
  // 启用请求签名验证
  "requireSignature": true
};
```

### 2. 敏感数据保护

```javascript
// 敏感数据处理
function sanitizePaymentData(paymentData) {
  const sanitized = { ...paymentData };
  
  // 移除敏感信息
  delete sanitized.card_number;
  delete sanitized.cvv;
  delete sanitized.expiry_date;
  delete sanitized.private_key;
  
  // 加密敏感数据
  if (sanitized.user_info) {
    sanitized.user_info = encryptData(sanitized.user_info);
  }
  
  return sanitized;
}

// 数据加密函数
function encryptData(data) {
  const crypto = require('crypto');
  const algorithm = 'aes-256-gcm';
  const key = process.env.ENCRYPTION_KEY;
  
  const iv = crypto.randomBytes(16);
  const cipher = crypto.createCipheriv(algorithm, key, iv);
  
  let encrypted = cipher.update(JSON.stringify(data), 'utf8', 'hex');
  encrypted += cipher.final('hex');
  
  const authTag = cipher.getAuthTag();
  
  return {
    iv: iv.toString('hex'),
    encryptedData: encrypted,
    authTag: authTag.toString('hex')
  };
}
```

### 3. 输入验证

```javascript
// 支付参数验证
function validatePaymentParams(params) {
  const errors = [];
  
  // 验证订单号
  if (!params.out_trade_no || params.out_trade_no.length < 6) {
    errors.push('订单号格式不正确');
  }
  
  // 验证金额
  if (!params.total_fee || params.total_fee <= 0) {
    errors.push('支付金额必须大于0');
  }
  
  // 验证金额范围
  if (params.total_fee > 1000000) { // 最大1万元
    errors.push('支付金额超出限制');
  }
  
  // 验证订单标题
  if (!params.subject || params.subject.length < 2) {
    errors.push('订单标题不能为空');
  }
  
  // 验证订单类型
  const validTypes = ['recharge', 'goods', 'vip', 'custom'];
  if (!validTypes.includes(params.type)) {
    errors.push('无效的订单类型');
  }
  
  return errors;
}
```

## ⚡ 性能优化

### 1. 数据库优化

```javascript
// 支付表索引优化
async function setupPaymentIndexes() {
  // 创建订单号索引
  await vk.baseDao.createIndex({
    dbName: "payment_orders",
    field: "out_trade_no",
    unique: true
  });
  
  // 创建用户ID索引
  await vk.baseDao.createIndex({
    dbName: "payment_orders",
    field: "user_id"
  });
  
  // 创建状态索引
  await vk.baseDao.createIndex({
    dbName: "payment_orders",
    field: "status"
  });
  
  // 创建时间索引
  await vk.baseDao.createIndex({
    dbName: "payment_orders",
    field: "create_date"
  });
}
```

### 2. 缓存优化

```javascript
// 支付结果缓存
async function getCachedPaymentResult(out_trade_no) {
  const cacheKey = `payment_result:${out_trade_no}`;
  
  // 尝试从缓存获取
  const cachedResult = await vk.cache.get(cacheKey);
  if (cachedResult) {
    return cachedResult;
  }
  
  // 从数据库查询
  const result = await vk.baseDao.find({
    dbName: "payment_orders",
    where: { out_trade_no }
  });
  
  if (result) {
    // 缓存结果（5分钟）
    await vk.cache.set(cacheKey, result, 300);
  }
  
  return result;
}
```

### 3. 连接池优化

```javascript
// 数据库连接池配置
module.exports = {
  database: {
    poolSize: 10,           // 连接池大小
    acquireTimeout: 30000,  // 获取连接超时时间
    idleTimeout: 60000,    // 空闲连接超时时间
    reapInterval: 1000     // 回收间隔
  },
  
  // Redis连接池配置
  redis: {
    poolSize: 20,
    idleTimeout: 30000,
    reapInterval: 1000
  }
};
```

## 🔍 监控和告警

### 1. 支付成功率监控

```javascript
// 支付成功率统计
async function getPaymentSuccessRate(startDate, endDate) {
  const totalPayments = await vk.baseDao.count({
    dbName: "payment_orders",
    where: {
      create_date: {
        $gte: startDate,
        $lte: endDate
      }
    }
  });
  
  const successfulPayments = await vk.baseDao.count({
    dbName: "payment_orders",
    where: {
      create_date: {
        $gte: startDate,
        $lte: endDate
      },
      status: "SUCCESS"
    }
  });
  
  const successRate = totalPayments > 0 ? (successfulPayments / totalPayments) * 100 : 0;
  
  return {
    total: totalPayments,
    success: successfulPayments,
    rate: successRate.toFixed(2)
  };
}
```

### 2. 异常监控告警

```javascript
// 异常监控告警
async function monitorPaymentExceptions() {
  // 检查最近5分钟的错误率
  const fiveMinutesAgo = Date.now() - 5 * 60 * 1000;
  
  const totalRequests = await vk.baseDao.count({
    dbName: "payment_logs",
    where: {
      create_date: { $gte: fiveMinutesAgo }
    }
  });
  
  const errorRequests = await vk.baseDao.count({
    dbName: "payment_logs",
    where: {
      create_date: { $gte: fiveMinutesAgo },
      status: "ERROR"
    }
  });
  
  const errorRate = totalRequests > 0 ? (errorRequests / totalRequests) * 100 : 0;
  
  // 如果错误率超过10%，发送告警
  if (errorRate > 10) {
    await sendAlert({
      title: '支付系统异常告警',
      message: `支付错误率异常：${errorRate.toFixed(2)}%`,
      level: 'critical'
    });
  }
  
  return { errorRate: errorRate.toFixed(2) };
}
```

### 3. 性能监控

```javascript
// 支付性能监控
async function monitorPaymentPerformance() {
  const oneHourAgo = Date.now() - 60 * 60 * 1000;
  
  const performanceStats = await vk.baseDao.aggregate({
    dbName: "payment_logs",
    pipeline: [
      {
        $match: {
          create_date: { $gte: oneHourAgo },
          status: "SUCCESS"
        }
      },
      {
        $group: {
          _id: "$provider",
          count: { $sum: 1 },
          avgResponseTime: { $avg: "$response_time" },
          maxResponseTime: { $max: "$response_time" },
          minResponseTime: { $min: "$response_time" }
        }
      }
    ]
  });
  
  return performanceStats;
}
```

## 🚀 部署最佳实践

### 1. 多环境配置

```javascript
// 环境配置管理
const envConfigs = {
  development: {
    // 开发环境配置
    wxpay: {
      sandbox: true,
      debug: true
    },
    alipay: {
      sandbox: true
    }
  },
  
  testing: {
    // 测试环境配置
    wxpay: {
      sandbox: true,
      debug: false
    },
    alipay: {
      sandbox: true
    }
  },
  
  production: {
    // 生产环境配置
    wxpay: {
      sandbox: false,
      debug: false
    },
    alipay: {
      sandbox: false
    }
  }
};

const currentEnv = process.env.NODE_ENV || 'development';
module.exports = envConfigs[currentEnv];
```

### 2. 灰度发布策略

```javascript
// 灰度发布控制
function canUseNewPaymentFeature(userId) {
  // 基于用户ID的灰度发布
  const hash = hashCode(userId);
  return hash % 100 < 10; // 10%的用户启用新功能
}

function hashCode(str) {
  let hash = 0;
  for (let i = 0; i < str.length; i++) {
    hash = ((hash << 5) - hash) + str.charCodeAt(i);
    hash |= 0; // Convert to 32bit integer
  }
  return Math.abs(hash);
}
```

### 3. 回滚策略

```javascript
// 快速回滚机制
async function rollbackPaymentIfNeeded(out_trade_no) {
  // 检查支付状态
  const paymentStatus = await getPaymentStatus(out_trade_no);
  
  if (paymentStatus === 'PROCESSING') {
    // 如果支付处理中，尝试取消
    const cancelResult = await cancelPayment(out_trade_no);
    
    if (cancelResult.success) {
      // 记录回滚日志
      await recordRollbackLog(out_trade_no, 'payment_cancelled');
    } else {
      // 需要人工干预
      await escalateToManualReview(out_trade_no);
    }
  }
}
```

## 🔗 相关文档

- [插件介绍和安装](vk-pay-introduction.md)
- [支付配置详解](vk-pay-configuration.md)
- [支付API使用](vk-pay-api-usage.md)
- [回调处理和前端组件](vk-pay-callback-frontend.md)
- [多商户和特殊支付](vk-pay-advanced.md)