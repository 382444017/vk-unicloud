# 云函数测试示例 - 高级测试

本文档展示了 `template/test/` 目录下的高级测试云函数示例代码。

## 高级测试函数

### mockData.js - 模拟数据生成

```javascript
module.exports = {
  /**
   * 模拟数据生成
   * @url template/test/pub/mockData 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, config, pubFun, vk , db, _ } = util;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    const { dataType, count = 10 } = data;
    
    const mockGenerators = {
      users: () => ({
        username: `user_${Math.random().toString(36).substr(2, 8)}`,
        email: `test_${Date.now()}@example.com`,
        avatar: 'https://example.com/avatar.png',
        created_date: new Date()
      }),
      
      products: () => ({
        name: `商品_${Math.random().toString(36).substr(2, 6)}`,
        price: parseFloat((Math.random() * 1000).toFixed(2)),
        stock: Math.floor(Math.random() * 100),
        category: ['电子', '服装', '食品'][Math.floor(Math.random() * 3)],
        description: '这是一个测试商品描述'
      }),
      
      orders: () => ({
        order_no: `ORDER_${Date.now()}_${Math.random().toString(36).substr(2, 4)}`,
        total_amount: parseFloat((Math.random() * 1000).toFixed(2)),
        status: ['pending', 'paid', 'shipped', 'completed'][Math.floor(Math.random() * 4)],
        created_date: new Date(Date.now() - Math.random() * 30 * 24 * 60 * 60 * 1000)
      })
    };
    
    if (!dataType || !mockGenerators[dataType]) {
      return { code: -1, msg: '不支持的数据类型，支持: users, products, orders' };
    }
    
    const mockData = [];
    for (let i = 0; i < count; i++) {
      mockData.push(mockGenerators[dataType]());
    }
    
    res.mockData = mockData;
    res.count = mockData.length;
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### integrationTest.js - 集成测试

```javascript
module.exports = {
  /**
   * 集成测试
   * @url template/test/pub/integrationTest 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, config, pubFun, vk , db, _ } = util;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    const { testScenario } = data;
    
    const testScenarios = {
      userRegistration: async () => {
        // 模拟用户注册流程
        const userData = {
          username: `testuser_${Date.now()}`,
          password: 'Test123!',
          email: `test_${Date.now()}@example.com`
        };
        
        // 调用用户注册服务
        const registerResult = await vk.callFunction({
          url: 'client/user/pub/register',
          data: userData
        });
        
        if (registerResult.code !== 0) {
          throw new Error(`用户注册失败: ${registerResult.msg}`);
        }
        
        // 验证用户数据
        const userInfo = await vk.baseDao.findById({
          dbName: 'uni-id-users',
          id: registerResult.uid
        });
        
        return {
          success: true,
          userId: registerResult.uid,
          userInfo: userInfo
        };
      },
      
      orderProcess: async () => {
        // 模拟订单处理流程
        const product = await vk.baseDao.add({
          dbName: 'products',
          dataJson: {
            name: '测试商品',
            price: 99.99,
            stock: 10
          }
        });
        
        const orderResult = await vk.callFunction({
          url: 'client/order/pub/create',
          data: {
            product_id: product._id,
            quantity: 1,
            total_amount: 99.99
          }
        });
        
        return {
          success: orderResult.code === 0,
          order: orderResult.order
        };
      }
    };
    
    if (!testScenario || !testScenarios[testScenario]) {
      return { code: -1, msg: '不支持的测试场景' };
    }
    
    try {
      const testResult = await testScenarios[testScenario]();
      res.testResult = testResult;
    } catch (error) {
      res.code = -1;
      res.msg = `集成测试失败: ${error.message}`;
    }
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

## 测试工具函数

### testUtils.js - 测试工具集

```javascript
// 测试工具函数库
class TestUtils {
  // 生成随机测试数据
  static generateRandomData(type, count = 1) {
    const generators = {
      string: () => Math.random().toString(36).substr(2, 8),
      number: () => Math.floor(Math.random() * 1000),
      boolean: () => Math.random() > 0.5,
      date: () => new Date(Date.now() - Math.random() * 365 * 24 * 60 * 60 * 1000),
      email: () => `test_${Date.now()}@example.com`,
      phone: () => `1${Math.random().toString().substr(2, 9)}`
    };
    
    if (count === 1) {
      return generators[type] ? generators[type]() : null;
    }
    
    const results = [];
    for (let i = 0; i < count; i++) {
      results.push(generators[type] ? generators[type]() : null);
    }
    return results;
  }
  
  // 性能测试工具
  static async measurePerformance(fn, iterations = 100) {
    const results = [];
    
    for (let i = 0; i < iterations; i++) {
      const startTime = Date.now();
      const startMemory = process.memoryUsage().heapUsed;
      
      await fn();
      
      const endTime = Date.now();
      const endMemory = process.memoryUsage().heapUsed;
      
      results.push({
        executionTime: endTime - startTime,
        memoryUsage: endMemory - startMemory
      });
    }
    
    return {
      avgExecutionTime: results.reduce((sum, r) => sum + r.executionTime, 0) / iterations,
      avgMemoryUsage: results.reduce((sum, r) => sum + r.memoryUsage, 0) / iterations,
      maxExecutionTime: Math.max(...results.map(r => r.executionTime)),
      minExecutionTime: Math.min(...results.map(r => r.executionTime))
    };
  }
  
  // 断言工具
  static assert(condition, message) {
    if (!condition) {
      throw new Error(`断言失败: ${message}`);
    }
  }
  
  static async assertThrowsAsync(fn, errorMessage) {
    try {
      await fn();
      throw new Error('期望函数抛出异常，但实际没有');
    } catch (error) {
      if (errorMessage && !error.message.includes(errorMessage)) {
        throw new Error(`期望错误信息包含: "${errorMessage}", 实际: "${error.message}"`);
      }
    }
  }
}

module.exports = {
  /**
   * 测试工具集
   * @url template/test/pub/testUtils 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, config, pubFun, vk , db, _ } = util;
    let res = { code : 0, msg : '测试工具集' };
    // 业务逻辑开始----------------------------------------------------------- 
    const { tool, params } = data;
    
    try {
      switch (tool) {
        case 'generateData':
          res.result = TestUtils.generateRandomData(params.type, params.count);
          break;
          
        case 'measurePerformance':
          const testFn = eval(params.function); // 注意：生产环境避免使用eval
          res.result = await TestUtils.measurePerformance(testFn, params.iterations);
          break;
          
        default:
          res.code = -1;
          res.msg = '不支持的测试工具';
      }
    } catch (error) {
      res.code = -1;
      res.msg = `工具执行错误: ${error.message}`;
    }
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
};
```

通过以上高级测试示例，您可以构建更复杂和全面的测试体系。