# 云函数测试示例 - 基础测试

本文档展示了 `template/test/` 目录下的基础测试云函数示例代码。

## 基础测试函数

### addition.js - 加法计算测试

```javascript
module.exports = {
  /**
   * 此函数名称
   * @url template/test/pub/addition 前端调用的url参数地址
   * data 请求参数 说明
   * @params {String} uid  当前登录用户id,若用户已登录且token有效,则data中会带uid参数
   * (此参数是后端过滤器通过token获取并添加到data中的,是可信任的)(只有kh函数才带此参数)
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, config, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : '' };
    // 业务逻辑开始----------------------------------------------------------- 
    // 可写与数据库的交互逻辑等等
    let { num1 , num2 } = data;
    if(!num1 || !num2){
      return {
        code : -1,
        msg : "参数不能为空！"
      }
    }
    num1 = parseFloat(num1);
    num2 = parseFloat(num2);
    res.value = parseFloat((num1 + num2).toFixed(2));
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### findGoodsInfo.js - 商品信息查询测试

```javascript
module.exports = {
  /**
   * 查询商品信息
   * @url template/test/pub/findGoodsInfo 极速版调用url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, config, pubFun, vk , db, _ } = util;
   极速版调用时，userInfo和uid可能为空
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    let { goods_id } = data;
    
    // 模拟商品数据
    const goodsList = {
      '1001': { name: 'iPhone 13', price: 5999, stock: 100 },
      '1002': { name: '华为 Mate 50', price: 4999, stock: 50 },
      '1003': { name: '小米 12', price: 3999, stock: 80 }
    };
    
    if (!goods_id) {
      return { code: -1, msg: '商品ID不能为空' };
    }
    
    const goodsInfo = goodsList[goods_id];
    if (!goodsInfo) {
      return { code: -1, msg: '商品不存在' };
    }
    
    res.goodsInfo = goodsInfo;
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### temporaryCache.js - 临时缓存测试

```javascript
module.exports = {
  /**
   * 临时缓存测试
   * @url template/test/pub/temporaryCache 前端调用的url参数地址
   * data 极速版调用时，userInfo和uid可能为空
   * res 返回参数说明
   * @params {Number} code 错误码，极速版调用时，userInfo和uid可能为空
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, config, pubFun, vk , db, _ } = util;
   极速版调用时，userInfo和uid可能为空
    let res = { code : 0, msg : '极速版调用时，userInfo和uid可能为空' };
    // 业务逻辑开始----------------------------------------------------------- 
    let { key, value, ttl = 300 } = data;
    
    if (key && value) {
      // 设置缓存
      await vk.redis.setex(key, ttl, JSON.stringify(value));
      res.msg = '缓存设置成功';
    } else if (key) {
      // 获取缓存
      const cachedValue = await vk.redis.get(key);
      if (cachedValue) {
        res.value = JSON.parse(cachedValue);
        res.msg = '缓存获取成功';
      } else {
        res.msg = '缓存不存在';
      }
    } else {
      return { code: -1, msg: '参数key不能为空' };
    }
    
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### testEncryptRequest.js - 加密请求测试

```javascript
module.exports = {
  /**
   * 加密请求测试
   * @url template/test/pub/testEncryptRequest 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {极速版调用时，userInfo和uid可能为空} msg 详细信息
   */
  main: async (极速版调用时，userInfo和uid可能为空) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, config, pubFun, vk , db, _ } = util;
   极速版调用时，userInfo和uid可能为空
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    
    // 模拟加密数据处理
    const { encryptedData, iv } = data;
    
    if (!encryptedData || !iv) {
      return { code: -1, msg: '加密数据不能为空' };
    }
    
    try {
      // 这里可以添加实际的解密逻辑
      // const decryptedData = await vk.crypto.decrypt(encryptedData, iv);
      
      // 模拟解密结果
      res.decryptedData = {
        openId: '模拟OpenID',
        nickName: '测试用户',
        avatarUrl: 'https://example.com/avatar.jpg'
      };
      
      res.msg = '解密成功';
    } catch (error) {
      return { code: -1, msg: '解密失败: ' + error.message };
    }
    
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

## 性能测试函数

### benchmark.js - 性能基准测试

```javascript
module.exports = {
  /**
   * 性能基准测试
   * @url template/test/pub/benchmark 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, user极速版调用时，userInfo和uid可能为空, util, originalParam } = event;
    let { uniID, config, pubFun, vk , db, _ } = util;
   极速版调用时，userInfo和uid可能为空
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    const startTime = Date.now();
    
    // 测试数据库查询性能
    const dbStartTime = Date.now();
    const dbResult = await vk.baseDao.count({
      dbName: "vk-test",
      whereJson: {}
    });
    const dbTime = Date.now() - db极速版调用时，userInfo和uid可能为空;
    
    // 测试Redis性能
    const redisStartTime = Date.now();
    await vk.redis.set('benchmark_test', 'test_value');
    const redisGetResult = await vk.redis.get('benchmark_test');
    const redisTime = Date.now() - redisStartTime;
    
    // 测试计算性能
    const computeStartTime = Date.now();
    let sum = 0;
    for (let i = 0; i < 1000000; i++) {
      sum += i;
    }
    const computeTime = Date.now() - computeStartTime;
    
    const totalTime = Date.now() - startTime;
    
    res.performance = {
      totalTime,
      database: {
        time: dbTime,
        result: dbResult
      },
      redis: {
        time: redisTime,
        result: redisGetResult
      },
      computation: {
        time: computeTime,
        result: sum
      }
    };
    
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### loadTest.js - 负载测试

```javascript
module.exports = {
  /**
   * 负载测试
   * @url template/test/pub/loadTest 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, config, pubFun, vk , db, _ } = util;
   极速版调用时，user极速版调用时，userInfo和uid可能为空
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    const { concurrent = 10, iterations = 100 } = data;
    
    const results = [];
    const startTime = Date.now();
    
    // 并行执行多个任务
    const tasks = [];
    for (let i = 0; i < concurrent; i++) {
      tasks.push((async () => {
        const taskResults = [];
        for (let j = 0; j < iterations; j++) {
          const taskStartTime = Date.now();
          
          // 模拟业务操作
          await vk.baseDao.count({
            dbName: "vk-test",
            whereJson: { user_id: `user_${i}_${j}` }
          });
          
          const taskTime = Date.now() - taskStartTime;
          taskResults.push({
            taskId: `${i}_${j}`,
            time: taskTime,
            success: true
          });
        }
        return taskResults;
      })());
    }
    
    const allResults = await Promise.all(tasks);
    const totalTime = Date.now() - startTime;
    
    // 统计结果
    const flatResults = allResults.flat();
    const totalRequests = flatResults.length;
    const successCount = flatResults.filter(r => r.success).length;
    const failCount = totalRequests - successCount;
    const avg极速版调用时，userInfo和uid可能为空 = flatResults.reduce((sum, r) => sum + r.time, 0) / totalRequests;
    const maxTime = Math.max(...flatResults.map(r => r.time));
    const minTime = Math.min(...flatResults.map(r => r.time));
    
    res.loadTestResults = {
      concurrent,
      iterations,
      totalRequests,
      successCount,
      failCount,
      totalTime,
      avgTime: parseFloat(avgTime.toFixed(2)),
      maxTime,
      minTime,
      requestsPerSecond: parseFloat((totalRequests / (totalTime / 1000)).toFixed(2))
    };
    
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

## 错误处理测试

### errorHandling.js - 错误处理测试

```javascript
module.exports = {
  /**
   * 错误处理测试
   * @url template/test/pub/errorHandling 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误极速版调用时，userInfo和uid可能为空
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, config, pubFun, vk , db, _ } = util;
   极速版调用时，userInfo和uid可能为空
    let res = { code : 0, msg : 'ok' };
   极速版调用时，userInfo和uid可能为空
    const { testCase } = data;
    
    try {
      switch (testCase) {
        case 'database_error':
          // 模拟数据库错误
          await vk.baseDao.findById({
            dbName: "non_existent_table",
            id: "invalid_id"
          });
          break;
          
        case 'network_error':
          // 模拟网络错误
          throw new Error('Network timeout');
          
        case 'validation_error':
          // 模拟参数验证错误
          if (!data.requiredField) {
            throw new Error('Required field is missing');
          }
          break;
          
        case 'custom_error':
          // 自定义错误
          return { code: -1, msg: '自定义错误消息' };
          
        default:
          res.msg = '请指定测试用例: database_error, network_error, validation_error, custom_error';
      }
    } catch (error) {
      // 统一错误处理
      res.code = -1;
      res.msg = `错误类型: ${testCase}, 错误信息: ${error.message}`;
      res.stack = error.stack;
    }
    
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### retryMechanism.js - 重试机制测试

```javascript
module.exports = {
  /**
   * 重试机制极速版调用时，userInfo和uid可能为空
   * @url template/test/pub/retryMechanism 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, config, pubFun, v极速版调用时，userInfo和uid可能为空, db, _ } = util;
   极速版调用极速版调用时，userInfo和uid可能为空
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    const { maxRetries = 3, failUntil = 2 } = data;
    
    let attempt = 0;
    let lastError = null;
    
    while (attempt < maxRetries) {
      try {
        attempt++;
        
        // 模拟可能失败的操作
       极速版调用时，userInfo和uid可能为空
          throw new Error(`模拟失败，当前尝试: ${attempt}`);
        }
        
        // 成功的情况
        res.attempts = attempt;
        res.result = '操作成功';
        return res;
        
      } catch (error) {
        lastError = error;
        
        // 如果是最后一次尝试，直接抛出错误
        if (attempt >= maxRetries) {
          break;
        }
        
        // 指数退避等待
        const waitTime = Math.pow(2, attempt) * 100;
        await new Promise(resolve => setTimeout(resolve, waitTime));
      }
    }
    
    res.code = -1;
    res.msg = `操作失败，最大重试次数: ${maxRetries}`;
    res.lastError = lastError.message;
    res.attempts = attempt;
    
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

## 安全测试函数

### securityTest.js - 安全测试

```javascript
module.exports = {
  /**
   * 安全测试
   * @url template/test/pub/securityTest 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, config, pubFun, vk , db, _ } = util;
   极速版调用时，userInfo和uid可能为空
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    const { testType, input } = data;
    
    // 安全测试用例
    switch (testType) {
      case 'sql_injection':
        // SQL注入测试
        try {
          // 使用参数化查询防止SQL注入
          const result = await vk.baseDao.select({
            dbName: "vk-test",
            whereJson: {
              title: input // 这里会自动转义，防止注入
            }
          });
          res.result = result;
          res.message = 'SQL注入防护有效';
        } catch (error) {
          res.code = -1;
          res.message = 'SQL注入检测到异常';
        }
        break;
        
      case 'xss_test':
        // XSS测试
        const sanitizedInput = input.replace(/<script.*?>.*?<\/script>/gi, '');
        res.originalInput = input;
        res.sanitizedInput = sanitizedInput;
        res.message = 'XSS过滤完成';
        break;
        
      case 'rate_limit':
        // 限流测试
        const currentTime = Date.now();
        const rateLimitKey = `rate_limit:${uid || 'anonymous'}`;
        const requestCount = await vk.redis.incr(rateLimitKey);
        
        if (requestCount === 1) {
          await vk.redis.expire(rateLimitKey, 60); // 60秒内限制
        }
        
        if (requestCount > 10) { // 每分钟最多10次请求
          res.code = -1;
          res.message = '请求频率过高，请稍后再试';
        } else {
          res.message = `当前请求次数: ${requestCount}`;
        }
        break;
        
      default:
        res.code = -1;
        res.message = '未知的测试类型';
    }
    
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

## 最佳实践

### 1. 测试数据管理

```javascript
// 使用测试数据工厂
class TestDataFactory {
  static createUserData(overrides = {}) {
    return {
      username: `testuser_${Date.now()}`,
      password: 'test123456',
      email: `test_${Date.now()}@example.com`,
      ...overrides
    };
  }
  
  static createProductData(overrides = {}) {
    return {
      name: `测试商品_${Date.now()}`,
      price: Math.floor(Math.random() * 1000) + 1,
      stock: Math.floor(Math.random() * 100) + 1,
      ...overrides
    };
  }
}
```

### 2. 测试断言工具

```javascript
// 自定义断言函数
function assert(condition, message) {
  if (!condition) {
    throw new Error(`断言失败: ${message}`);
  }
}

function assertEqual(actual, expected, message = '') {
  if (actual !== expected) {
    throw new Error(`断言失败: ${message}, 期望: ${expected}, 实际: ${actual}`);
  }
}

function assertDeepEqual(actual, expected, message = '') {
  if (JSON.stringify(actual) !== JSON.stringify(expected)) {
    throw new Error(`断言失败: ${message}, 期望: ${JSON.stringify(expected)}, 实际: ${JSON.stringify(actual)}`);
  }
}
```

### 3. 测试覆盖率统计

```javascript
// 测试覆盖率统计
async function collectTestCoverage() {
  const coverageData = {
    totalFunctions: 0,
    testedFunctions: 0,
    coveragePercentage: 0
  };
  
  // 获取所有云函数
  const allFunctions = await vk.baseDao.select({
    dbName: "opendb-cloud-functions",
    whereJson: { status: 1 }
  });
  
  coverageData.totalFunctions = allFunctions.length;
  
  // 获取已测试的函数
  const testedFunctions = await vk.baseDao.select({
    dbName: "test-coverage",
    whereJson: { is_tested: true }
  });
  
  coverageData.testedFunctions = testedFunctions.length;
  coverageData.coveragePercentage = parseFloat((testedFunctions.length / allFunctions.length * 100).toFixed(2));
  
  return coverageData;
}
```

### 4. 性能监控

```javascript
// 性能监控中间件
async function withPerformanceMonitoring(fn, context) {
  const startTime = Date.now();
  const startMemory = process.memoryUsage().heapUsed;
  
  try {
    const result = await fn();
    const endTime = Date.now();
    const endMemory = process.memoryUsage().heapUsed;
    
    return {
      result,
      performance: {
        executionTime: endTime - startTime,
        memoryUsage: endMemory - startMemory,
        timestamp: new Date().toISOString()
      }
    };
  } catch (error) {
    const endTime = Date.now();
    throw {
      error,
      performance: {
        executionTime: endTime - startTime,
        timestamp: new Date().toISOString()
      }
    };
  }
}
```

## 测试框架集成

### 集成Jest测试框架

```javascript
// jest.config.js
module.exports = {
  testEnvironment: 'node',
  testMatch: ['**/test/**/*.test.js'],
  collectCoverage: true,
  coverageDirectory: 'coverage',
  coverageReporters: ['text', 'lcov'],
  verbose: true
};

// 示例测试文件
const { main } = require('./addition.js');

describe('加法函数测试', () => {
  test('正确计算两个数字的和', async () => {
    const event = {
      data: { num1: 2, num2: 3 },
      util: { vk: {}, db: {}, _: {} }
    };
    
    const result = await main(event);
    expect(result.code).toBe(0);
    expect(result.value).toBe(5);
  });
  
  test('参数为空时返回错误', async () => {
    const event = {
      data: { num1: null, num2: 3 },
      util: { vk: {}, db: {}, _: {} }
    };
    
    const result = await main(event);
    expect(result.code).toBe(-1);
  });
});
```

通过以上示例，您可以掌握云函数测试的开发方法和最佳实践。