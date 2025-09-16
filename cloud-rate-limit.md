# 速率限制

## 介绍

速率限制用于控制API的访问频率，防止滥用和DDoS攻击。

## 基本用法

### 简单频率限制

```javascript
'use strict';
exports.main = async (event, context) => {
  const vk = require('vk-unicloud');
  
  // 检查API调用频率
  const limitResult = await vk.rateLimit.check('api-call', {
    key: event.ip, // 使用IP作为限制键
    limit: 100,    // 每分钟最多100次
    period: 60     // 时间窗口（秒）
  });
  
  if (limitResult.remaining === 0) {
    return {
      code: -1,
      msg: '请求过于频繁，请稍后再试',
      retryAfter: limitResult.resetAfter
    };
  }
  
  // 执行正常的业务逻辑
  const result = await processRequest(event);
  
  return {
    code: 0,
    data: result,
    rateLimit: {
      remaining: limitResult.remaining - 1,
      reset: limitResult.resetTime
    }
  };
};
```

### 用户级频率限制

```javascript
'use strict';
exports.main = async (event, context) => {
  const vk = require('vk-unicloud');
  
  // 获取用户ID（需要先验证token）
  const authResult = await vk.user.checkToken(event.uniIdToken);
  if (!authResult.code) {
    return { code: -1, msg: '未授权' };
  }
  
  // 用户级频率限制
  const limitResult = await vk.rateLimit.check('user-api', {
    key: authResult.uid, // 使用用户ID作为限制键
    limit: 1000,         // 每小时最多1000次
    period: 3600         // 时间窗口（秒）
  });
  
  if (limitResult.remaining === 0) {
    return {
      code: -1,
      msg: '今日API调用次数已用完',
      retryAfter: limitResult.resetAfter
    };
  }
  
  // 处理请求
  const result = await handleUserRequest(event, authResult.uid);
  
  return {
    code: 0,
    data: result,
    rateLimit: {
      remaining: limitResult.remaining - 1,
      reset: limitResult.resetTime
    }
  };
};
```

## 高级用法

### 滑动窗口限制

```javascript
'use strict';
exports.main = async (event, context) => {
  const vk = require('vk-unicloud');
  
  // 滑动窗口频率限制
  const limitResult = await vk.rateLimit.slidingWindow('sliding-api', {
    key: event.ip,
    limit: 100,     // 每分钟最多100次
    window: 60      // 时间窗口（秒）
  });
  
  if (!limitResult.allowed) {
    return {
      code: -1,
      msg: '请求过于频繁',
      retryAfter: limitResult.retryAfter
    };
  }
  
  return {
    code: 0,
    data: await processRequest(event)
  };
};
```

### 令牌桶算法

```javascript
'use strict';
exports.main = async (event, context) => {
  const vk = require('vk-unicloud');
  
  // 令牌桶频率限制
  const bucketResult = await vk.rateLimit.tokenBucket('token-api', {
    key: event.ip,
    capacity: 100,  // 桶容量
    refillRate: 10   // 每秒补充10个令牌
  });
  
  if (!bucketResult.allowed) {
    return {
      code: -1,
      msg: '系统繁忙，请稍后再试',
      retryAfter: bucketResult.retryAfter
    };
  }
  
  return {
    code: 0,
    data: await processRequest(event)
  };
};
```

### 分布式速率限制

```javascript
'use strict';
exports.main = async (event, context) => {
  const vk = require('vk-unicloud');
  
  // 分布式频率限制（适用于多实例部署）
  const limitResult = await vk.rateLimit.distributed('distributed-api', {
    key: event.ip,
    limit: 1000,
    period: 3600,
    redis: {
      host: 'redis-host',
      port: 6379,
      password: 'redis-password'
    }
  });
  
  if (limitResult.remaining === 0) {
    return {
      code: -1,
      msg: '请求过于频繁',
      retryAfter: limitResult.resetAfter
    };
  }
  
  return {
    code: 0,
    data: await processRequest(event)
  };
};
```

## 使用场景

### API接口限流

```javascript
// API接口频率限制中间件
async function apiRateLimitMiddleware(event, context) {
  const vk = require('vk-unicloud');
  
  // 根据API路径设置不同的限制
  const apiConfigs = {
    '/api/login': { limit: 10, period: 60 },    // 登录接口：每分钟10次
    '/api/register': { limit: 5, period: 3600 }, // 注册接口：每小时5次
    '/api/data': { limit: 100, period: 60 },    // 数据接口：每分钟100次
    '/api/upload': { limit: 20, period: 3600 }  // 上传接口：每小时20次
  };
  
  const apiPath = event.path;
  const config = apiConfigs[apiPath] || { limit: 100, period: 60 };
  
  const limitResult = await vk.rateLimit.check(`api-${apiPath}`, {
    key: event.ip,
    limit: config.limit,
    period: config.period
  });
  
  if (limitResult.remaining === 0) {
    throw new Error(`请求过于频繁，请${limitResult.resetAfter}秒后再试`);
  }
  
  return limitResult;
}
```

### 短信验证码防刷

```javascript
// 短信验证码频率限制
async function sendSmsWithRateLimit(phoneNumber, templateId) {
  const vk = require('vk-unicloud');
  
  // 手机号级别限制
  const phoneLimit = await vk.rateLimit.check(`sms-phone-${phoneNumber}`, {
    limit: 5,    // 每个手机号每天最多5条
    period: 86400 // 24小时
  });
  
  if (phoneLimit.remaining === 0) {
    throw new Error('今日短信发送次数已用完');
  }
  
  // IP级别限制
  const ipLimit = await vk.rateLimit.check('sms-ip', {
    key: getClientIP(),
    limit: 100,  // 每个IP每天最多100条
    period: 86400
  });
  
  if (ipLimit.remaining === 0) {
    throw new Error('IP发送短信次数超限');
  }
  
  // 模板级别限制
  const templateLimit = await vk.rateLimit.check(`sms-template-${templateId}`, {
    limit: 1000, // 每个模板每天最多1000条
    period: 86400
  });
  
  if (templateLimit.remaining === 0) {
    throw new Error('该模板短信发送次数超限');
  }
  
  // 发送短信
  const result = await sendSms(phoneNumber, templateId);
  
  return {
    ...result,
    rateLimit: {
      phoneRemaining: phoneLimit.remaining - 1,
      ipRemaining: ipLimit.remaining - 1,
      templateRemaining: templateLimit.remaining - 1
    }
  };
}
```

### 爬虫访问控制

```javascript
// 爬虫访问频率控制
async function handleCrawlerRequest(event) {
  const vk = require('vk-unicloud');
  
  const userAgent = event.headers['user-agent'] || '';
  const isCrawler = detectCrawler(userAgent);
  
  if (isCrawler) {
    // 对爬虫实施更严格的限制
    const crawlLimit = await vk.rateLimit.check('crawler', {
      key: event.ip,
      limit: 10,     // 爬虫每分钟最多10次
      period: 60
    });
    
    if (crawlLimit.remaining === 0) {
      return {
        code: 429,
        msg: 'Crawler rate limit exceeded',
        retryAfter: crawlLimit.resetAfter
      };
    }
    
    // 额外的延迟，降低爬虫速度
    await delay(1000);
  }
  
  return await processRequest(event);
}
```

## 性能优化

### 内存缓存优化

```javascript
// 使用内存缓存优化频率限制
const memoryCache = new Map();

async function optimizedRateLimit(key, limit, period) {
  const cacheKey = `rate-limit-${key}`;
  const now = Date.now();
  
  // 检查内存缓存
  if (memoryCache.has(cacheKey)) {
    const cached = memoryCache.get(cacheKey);
    if (now - cached.timestamp < period * 1000) {
      if (cached.count >= limit) {
        return {
          allowed: false,
          retryAfter: period - Math.floor((now - cached.timestamp) / 1000)
        };
      }
      cached.count++;
      return { allowed: true, remaining: limit - cached.count };
    }
  }
  
  // 内存缓存过期或不存在，使用持久化存储
  const result = await vk.rateLimit.check(key, { limit, period });
  
  // 更新内存缓存
  memoryCache.set(cacheKey, {
    count: limit - result.remaining,
    timestamp: now
  });
  
  return {
    allowed: result.remaining > 0,
    remaining: result.remaining
  };
}
```

### 批量处理优化

```javascript
// 批量处理频率限制检查
async function batchRateLimit(checks) {
  const vk = require('vk-unicloud');
  
  const results = {};
  const promises = [];
  
  for (const [key, config] of Object.entries(checks)) {
    promises.push(
      vk.rateLimit.check(key, config)
        .then(result => { results[key] = result; })
        .catch(error => { results[key] = { error }; })
    );
  }
  
  await Promise.all(promises);
  return results;
}

// 使用示例
const limitResults = await batchRateLimit({
  'api-login': { key: event.ip, limit: 10, period: 60 },
  'api-data': { key: event.ip, limit: 100, period: 60 },
  'sms-phone': { key: event.phone, limit: 5, period: 86400 }
});
```

## 错误处理

### 优雅的限流响应

```javascript
// 统一的限流错误处理
function createRateLimitResponse(limitResult) {
  const headers = {
    'X-RateLimit-Limit': limitResult.limit,
    'X-RateLimit-Remaining': limitResult.remaining,
    'X-RateLimit-Reset': limitResult.resetTime,
    'Retry-After': limitResult.resetAfter
  };
  
  return {
    code: 429,
    msg: 'Too Many Requests',
    headers: headers,
    data: {
      error: 'rate_limit_exceeded',
      message: '请求过于频繁，请稍后再试',
      retry_after: limitResult.resetAfter
    }
  };
}
```

### 限流异常监控

```javascript
// 监控限流异常
async function monitorRateLimitExceptions() {
  const exceptions = await vk.rateLimit.getExceptionStats();
  
  console.log('限流异常统计:');
  console.log('- 总限流次数:', exceptions.totalBlocks);
  console.log('- 最近1小时限流次数:', exceptions.lastHourBlocks);
  console.log('- 最常被限流的API:', exceptions.topApis);
  console.log('- 最常被限流的IP:', exceptions.topIps);
  
  // 检测异常模式
  if (exceptions.lastHourBlocks > 1000) {
    console.warn('检测到可能的DDoS攻击');
    await alertSecurityTeam();
  }
}
```

## 最佳实践

1. **分层限流**：实施IP级别、用户级别、API级别的多层限流
2. **动态调整**：根据系统负载动态调整限流阈值
3. **优雅降级**：限流时提供友好的错误信息和重试建议
4. **监控告警**：监控限流情况，及时发现异常模式
5. **黑白名单**：结合IP黑白名单机制
6. **分布式协调**：在分布式环境中确保限流一致性
7. **测试验证**：定期测试限流功能确保正常工作
8. **文档透明**：向用户公开限流策略，提高透明度