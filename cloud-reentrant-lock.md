# 可重入锁

## 介绍

可重入锁是一种同步机制，允许同一个线程多次获取同一把锁，而不会造成死锁。

## 基本用法

### 获取锁

```javascript
'use strict';
exports.main = async (event, context) => {
  const vk = require('vk-unicloud');
  
  // 获取可重入锁
  const lock = await vk.reentrantLock.acquire('order-process-lock', {
    timeout: 30, // 锁超时时间（秒）
    maxRetries: 3 // 最大重试次数
  });
  
  try {
    // 执行需要加锁的业务逻辑
    console.log('获取锁成功，开始处理订单');
    await processOrder(event.orderId);
    
    return {
      code: 0,
      msg: '订单处理成功'
    };
  } finally {
    // 释放锁
    await lock.release();
    console.log('释放锁');
  }
};
```

### 尝试获取锁（非阻塞）

```javascript
'use strict';
exports.main = async (event, context) => {
  const vk = require('vk-unicloud');
  
  // 尝试获取锁（非阻塞方式）
  const lock = await vk.reentrantLock.tryAcquire('order-process-lock', {
    timeout: 30
  });
  
  if (!lock) {
    return {
      code: -1,
      msg: '系统繁忙，请稍后再试'
    };
  }
  
  try {
    // 执行需要加锁的业务逻辑
    await processOrder(event.orderId);
    
    return {
      code: 0,
      msg: '订单处理成功'
    };
  } finally {
    await lock.release();
  }
};
```

## 高级用法

### 嵌套锁

```javascript
'use strict';
exports.main = async (event, context) => {
  const vk = require('vk-unicloud');
  
  // 第一次获取锁
  const lock1 = await vk.reentrantLock.acquire('nested-lock');
  
  try {
    console.log('第一次获取锁成功');
    
    // 第二次获取同一把锁（可重入）
    const lock2 = await vk.reentrantLock.acquire('nested-lock');
    
    try {
      console.log('第二次获取锁成功（可重入）');
      
      // 执行业务逻辑
      await processData();
      
    } finally {
      await lock2.release();
      console.log('释放第二次锁');
    }
    
  } finally {
    await lock1.release();
    console.log('释放第一次锁');
  }
};
```

### 带条件的锁

```javascript
'use strict';
exports.main = async (event, context) => {
  const vk = require('vk-unicloud');
  
  // 获取带条件的锁
  const lock = await vk.reentrantLock.acquire('conditional-lock', {
    condition: async () => {
      // 检查业务条件是否满足
      const canProcess = await checkBusinessCondition();
      return canProcess;
    },
    checkInterval: 1000, // 检查间隔（毫秒）
    maxWait: 10000 // 最大等待时间（毫秒）
  });
  
  try {
    // 执行业务逻辑
    await processBusiness();
    
    return {
      code: 0,
      msg: '处理成功'
    };
  } finally {
    await lock.release();
  }
};
```

### 读写锁

```javascript
'use strict';
exports.main = async (event, context) => {
  const vk = require('vk-unicloud');
  
  if (event.type === 'read') {
    // 获取读锁
    const readLock = await vk.reentrantLock.acquireRead('data-lock');
    
    try {
      // 执行读操作
      const data = await readData();
      return {
        code: 0,
        data: data
      };
    } finally {
      await readLock.release();
    }
    
  } else if (event.type === 'write') {
    // 获取写锁
    const writeLock = await vk.reentrantLock.acquireWrite('data-lock');
    
    try {
      // 执行写操作
      await writeData(event.data);
      return {
        code: 0,
        msg: '写入成功'
      };
    } finally {
      await writeLock.release();
    }
  }
};
```

## 使用场景

### 订单处理防重复

```javascript
async function processOrderWithLock(orderId) {
  const lockKey = `order-process-${orderId}`;
  
  const lock = await vk.reentrantLock.acquire(lockKey, {
    timeout: 60,
    maxRetries: 5
  });
  
  try {
    // 检查订单是否已处理
    const order = await getOrder(orderId);
    if (order.status === 'processed') {
      throw new Error('订单已处理');
    }
    
    // 处理订单
    await updateOrderStatus(orderId, 'processing');
    await deductInventory(order.items);
    await createPaymentRecord(order);
    await updateOrderStatus(orderId, 'completed');
    
    return true;
  } catch (error) {
    await updateOrderStatus(orderId, 'failed');
    throw error;
  } finally {
    await lock.release();
  }
}
```

### 库存扣减防超卖

```javascript
async function deductInventoryWithLock(productId, quantity) {
  const lockKey = `inventory-${productId}`;
  
  const lock = await vk.reentrantLock.acquire(lockKey, {
    timeout: 30,
    maxRetries: 3
  });
  
  try {
    // 检查库存
    const inventory = await getInventory(productId);
    if (inventory.available < quantity) {
      throw new Error('库存不足');
    }
    
    // 扣减库存
    await updateInventory(productId, -quantity);
    
    return {
      code: 0,
      msg: '扣减成功'
    };
  } finally {
    await lock.release();
  }
}
```

### 分布式任务调度

```javascript
async function scheduleDistributedTask(taskId) {
  const lockKey = `task-schedule-${taskId}`;
  
  const lock = await vk.reentrantLock.acquire(lockKey, {
    timeout: 300,
    maxRetries: 10
  });
  
  try {
    // 检查任务状态
    const task = await getTask(taskId);
    if (task.status !== 'pending') {
      throw new Error('任务已处理');
    }
    
    // 执行任务
    await updateTaskStatus(taskId, 'running');
    const result = await executeTask(task);
    await updateTaskStatus(taskId, 'completed', result);
    
    return result;
  } catch (error) {
    await updateTaskStatus(taskId, 'failed', error.message);
    throw error;
  } finally {
    await lock.release();
  }
}
```

## 性能优化

### 锁超时设置

```javascript
// 根据业务场景设置合适的超时时间
const lockConfigs = {
  // 短时操作：秒级超时
  quickOperation: {
    timeout: 10,
    maxRetries: 2
  },
  
  // 中等操作：分钟级超时  
  mediumOperation: {
    timeout: 60,
    maxRetries: 5
  },
  
  // 长时操作：小时级超时
  longOperation: {
    timeout: 3600,
    maxRetries: 10
  }
};
```

### 锁粒度控制

```javascript
// 细粒度锁：按资源ID加锁
async function processResource(resourceId) {
  const lockKey = `resource-${resourceId}`;
  const lock = await vk.reentrantLock.acquire(lockKey);
  // ...
}

// 粗粒度锁：按业务类型加锁  
async function processBusiness(businessType) {
  const lockKey = `business-${businessType}`;
  const lock = await vk.reentrantLock.acquire(lockKey);
  // ...
}
```

### 锁监控

```javascript
// 监控锁使用情况
async function monitorLocks() {
  const lockStats = await vk.reentrantLock.getStats();
  
  console.log('锁统计信息:');
  console.log('- 活跃锁数量:', lockStats.activeLocks);
  console.log('- 等待获取锁的线程数:', lockStats.waitingThreads);
  console.log('- 锁获取成功率:', lockStats.successRate);
  console.log('- 平均等待时间:', lockStats.avgWaitTime);
  
  // 检测死锁
  const deadlocks = await vk.reentrantLock.detectDeadlocks();
  if (deadlocks.length > 0) {
    console.warn('检测到死锁:', deadlocks);
    await handleDeadlocks(deadlocks);
  }
}
```

## 错误处理

### 锁获取失败处理

```javascript
async function safeAcquireLock(lockKey, config) {
  try {
    const lock = await vk.reentrantLock.acquire(lockKey, config);
    return lock;
  } catch (error) {
    if (error.code === 'LOCK_TIMEOUT') {
      console.warn('获取锁超时:', lockKey);
      throw new Error('系统繁忙，请稍后再试');
    } else if (error.code === 'MAX_RETRIES_EXCEEDED') {
      console.warn('获取锁重试次数超限:', lockKey);
      throw new Error('操作过于频繁，请稍后再试');
    } else {
      console.error('获取锁未知错误:', error);
      throw new Error('系统错误，请稍后再试');
    }
  }
}
```

### 锁释放保证

```javascript
async function withLock(lockKey, operation, config = {}) {
  const lock = await vk.reentrantLock.acquire(lockKey, config);
  
  try {
    return await operation();
  } finally {
    try {
      await lock.release();
    } catch (releaseError) {
      console.error('释放锁失败:', releaseError);
      // 记录日志但不要抛出异常，避免掩盖业务异常
    }
  }
}

// 使用示例
await withLock('order-lock', async () => {
  await processOrder();
}, { timeout: 30 });
```

### 锁状态恢复

```javascript
async function recoverLockState(lockKey) {
  try {
    // 检查锁状态
    const lockInfo = await vk.reentrantLock.getInfo(lockKey);
    
    if (lockInfo && lockInfo.isLocked) {
      // 锁可能处于异常状态，尝试强制释放
      const currentTime = Date.now();
      const lockTimestamp = lockInfo.lockedAt;
      
      // 如果锁持有时间超过超时时间的2倍，认为异常
      if (currentTime - lockTimestamp > lockInfo.timeout * 2 * 1000) {
        console.warn('检测到可能异常的锁，强制释放:', lockKey);
        await vk.reentrantLock.forceRelease(lockKey);
        return true;
      }
    }
    
    return false;
  } catch (error) {
    console.error('恢复锁状态失败:', error);
    return false;
  }
}
```

## 最佳实践

1. **尽量缩短锁持有时间**：减少锁的竞争
2. **使用合适的锁粒度**：平衡并发性和安全性
3. **设置合理的超时时间**：避免死锁和长时间等待
4. **始终在finally中释放锁**：确保锁一定会被释放
5. **监控锁使用情况**：及时发现性能问题
6. **实现锁的重试机制**：提高系统健壮性
7. **考虑分布式环境**：确保锁在分布式系统中正常工作
8. **测试并发场景**：确保锁在各种并发情况下都能正常工作