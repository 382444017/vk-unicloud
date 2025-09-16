---
type: "manual"
description: "云开发最佳实践指南"
---

# 最佳实践

## 1. 错误处理策略
```javascript
module.exports = {
  main: async (event) => {
    let res = { code: 0, msg: '' };

    try {
      // 业务逻辑

    } catch (err) {
      console.error('云函数执行错误:', err);

      // 根据错误类型返回不同的错误码
      if (err.message.includes('权限')) {
        res.code = 403;
        res.msg = '权限不足';
      } else if (err.message.includes('参数')) {
        res.code = 400;
        res.msg = '参数错误';
      } else {
        res.code = 500;
        res.msg = '系统错误';
      }

      // 开发环境返回详细错误信息
      if (vk.isDev()) {
        res.error = err.stack;
      }
    }

    return res;
  }
}
```

## 2. 数据验证
```javascript
// 参数验证函数
function validateParams(data, rules) {
  for (let field in rules) {
    let rule = rules[field];
    let value = data[field];

    if (rule.required && !value) {
      throw new Error(`${rule.label || field}不能为空`);
    }

    if (value && rule.type === 'email' && !/\S+@\S+\.\S+/.test(value)) {
      throw new Error(`${rule.label || field}格式不正确`);
    }

    if (value && rule.minLength && value.length < rule.minLength) {
      throw new Error(`${rule.label || field}长度不能少于${rule.minLength}位`);
    }
  }
}

// 使用示例
module.exports = {
  main: async (event) => {
    let { data } = event;

    // 验证参数
    validateParams(data, {
      name: { required: true, label: '姓名' },
      email: { required: true, type: 'email', label: '邮箱' },
      password: { required: true, minLength: 极速开发, label: '密码' }
    });

    // 业务逻辑
    // ...
  }
}
```

## 3. 性能优化
```javascript
// 使用缓存提升性能
module.exports = {
  main: async (event) => {
    let { data } = event;
    let cacheKey = `user:${data.userId}`;

    // 先从缓存获取
    let userData = await cacheManage.get(cacheKey);

    if (!userData) {
      // 缓存不存在，从数据库查询
      userData = await vk.baseDao.findById({
        dbName: "uni-id-users",
        id: data.userId
      });

      // 存入缓存，缓存30分钟
      if (userData) {
        await cacheManage.set(cacheKey, userData, 1800);
      }
    }

    return { code: 0, data: userData };
  }
}
```

## 4. 日志记录
```javascript
// 统一日志记录
function logOperation(operation, data, result) {
  console.log(`[${new Date().toISOString()}] ${operation}`, {
    input: data,
    output: result,
    timestamp: Date.now()
  });
}

module.exports = {
  main: async (event) => {
    let { data, userInfo } = event;

    // 记录操作日志
    logOperation('用户登录', {
      uid: userInfo.uid,
      ip: event.clientIP
    }, { success: true });

    // 业务逻辑
    // ...
  }
}
```

## 5. 云端HTTP请求

```javascript
// 云端HTTP请求（必须使用await）
const response = await vk.request({
  url: 'https://api.example.com/data',
  method: 'POST',
  header: {
    'content-type': 'application/json',
    'authorization': 'Bearer token'
  },
  data: {
    key: 'value'
  },
  timeout: 10000
});

console.log('请求结果:', response);

// 请求文本格式数据
const textResponse = await vk.request({
  url: 'https://api.example.com/text',
  method: 'GET',
  dataType: 'text'
});

// 请求二进制数据
const binaryData = await vk.request({
  url: 'https://example.com/image.jpg',
  method: 'GET',
  dataType: 'default'
});

// 转换为base64
const base64 = "data:image/png;base64," + binaryData.toString('base64')

// 使用代理请求（仅阿里云）
const proxyResponse = await vk.request({
  url: 'https://api.example.com/data',
  useProxy: true,
  data: { key: 'value' }
});

// 加密通信
const encryptResponse = await vk.request({
  url: 'https://your-domain.com/http/router/api/test',
  encrypt: true,
  header: {
    'uni-id-token': userToken,
    'vk-appid': '__UNI__12345',
    'vk-platform': 'h5'
  },
  data: { a: 1, b: 2 }
});
```

## 6. 并发执行API

```javascript
// 批量并发执行（有数据源）
const batchRunRes = await vk.pubfn.batchRun({
  // 主执行函数
  main: async (item, index) => {
    // 模拟发送短信、邮件等操作
    await vk.pubfn.sleep(Math.random() * 1000)
    console.log(`处理第${index}项:`, item)
    return { code: 0, index, result: 'success' }
  },
  // 最大并发量
  concurrency: 50,
  // 数据源
  data: [
    { mobile: '13800138001', message: '消息1' },
    { mobile: '13800138002', message: '消息2' },
    { mobile: '13800138003', message: '消息3' }
  ]
})

// 批量并发执行（无数据源）
const batchRunRes = await vk.pubfn.batchRun({
  main: [
    // 并发执行多个不同的异步函数
    async () => {
      return await vk.daoCenter.userDao.count({ status: 1 })
    },
    async () => {
      return await vk.daoCenter.orderDao.count({ status: 'paid' })
    },
    async () => {
      return await vk.daoCenter.goodsDao.count({ is_on_sale: true })
    }
  ],
  concurrency: 10
})

console.log('并发执行结果:', batchRunRes.stack)
```

## 7. 云端工具API

```javascript
// 获取云函数请求ID
const requestId = vk.pubfn.getUniCloudRequestId()
console.log('请求ID:', requestId)

// 产生不重复随机数（异步）
const uniqueCode = await vk.pubfn.randomAsync(6, "23456789ABCDEFGHJKLMNPQRSTUVWXYZ", async (val) => {
  // 检查是否重复的自定义函数
  const count = await vk.baseDao.count({
    dbName: "uni-id-users",
    whereJson: {
      invite_code: val
    }
  })
  return count === 0 // 返回true表示不重复
}, 10) // 最大重试10次

console.log('生成的唯一邀请码:', uniqueCode)