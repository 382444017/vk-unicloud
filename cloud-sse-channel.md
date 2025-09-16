# SSE通道（Server-Sent Events）

## 介绍

SSE（Server-Sent Events）是一种服务器向客户端推送数据的技术，基于HTTP协议，使用简单，兼容性好。

## 基本用法

### 创建SSE通道

```javascript
'use strict';
exports.main = async (event, context) => {
  const vk = require('vk-unicloud');
  
  // 创建SSE通道
  const channel = await vk.sseChannel.create({
    channelId: 'user-notifications', // 通道ID
    timeout: 3600, // 超时时间（秒）
  });
  
  return {
    code: 0,
    data: channel
  };
};
```

### 向通道发送消息

```javascript
'use strict';
exports.main = async (event, context) => {
  const vk = require('vk-unicloud');
  
  // 向SSE通道发送消息
  await vk.sseChannel.send({
    channelId: 'user-notifications',
    data: {
      type: 'notification',
      message: '您有一条新消息',
      timestamp: Date.now()
    }
  });
  
  return {
    code: 0,
    msg: '消息发送成功'
  };
};
```

### 监听SSE通道

```javascript
// 前端代码
const eventSource = new EventSource('/http/router/sse/channel/user-notifications');

eventSource.onmessage = function(event) {
  const data = JSON.parse(event.data);
  console.log('收到消息:', data);
};

eventSource.onerror = function(error) {
  console.error('SSE连接错误:', error);
};
```

## 高级用法

### 带认证的SSE通道

```javascript
'use strict';
exports.main = async (event, context) => {
  const vk = require('vk-unicloud');
  
  // 验证用户权限
  const authResult = await vk.user.checkToken(event.uniIdToken);
  if (!authResult.code) {
    return { code: -1, msg: '未授权' };
  }
  
  // 创建带用户信息的SSE通道
  const channel = await vk.sseChannel.create({
    channelId: `user-${authResult.uid}-notifications`,
    timeout: 7200,
    metadata: {
      userId: authResult.uid,
      userInfo: authResult.userInfo
    }
  });
  
  return {
    code: 0,
    data: channel
  };
};
```

### 批量发送消息

```javascript
'use strict';
exports.main = async (event, context) => {
  const vk = require('vk-unicloud');
  
  // 批量向多个通道发送消息
  const results = await vk.sseChannel.batchSend([
    {
      channelId: 'channel-1',
      data: { message: '消息1' }
    },
    {
      channelId: 'channel-2', 
      data: { message: '消息2' }
    },
    {
      channelId: 'channel-3',
      data: { message: '消息3' }
    }
  ]);
  
  return {
    code: 0,
    data: results
  };
};
```

### 通道管理

```javascript
'use strict';
exports.main = async (event, context) => {
  const vk = require('vk-unicloud');
  
  // 获取通道信息
  const channelInfo = await vk.sseChannel.getInfo('user-notifications');
  
  // 获取所有活跃通道
  const activeChannels = await vk.sseChannel.listActive();
  
  // 关闭通道
  await vk.sseChannel.close('user-notifications');
  
  return {
    code: 0,
    data: {
      channelInfo,
      activeChannels
    }
  };
};
```

## 使用场景

### 实时通知系统

```javascript
// 发送通知消息
async function sendNotification(userId, message) {
  await vk.sseChannel.send({
    channelId: `user-${userId}-notifications`,
    data: {
      type: 'notification',
      message: message,
      timestamp: Date.now(),
      read: false
    }
  });
}

// 标记消息为已读
async function markAsRead(userId, messageId) {
  await vk.sseChannel.send({
    channelId: `user-${userId}-notifications`, 
    data: {
      type: 'mark-read',
      messageId: messageId,
      timestamp: Date.now()
    }
  });
}
```

### 实时数据更新

```javascript
// 股票价格更新
async function updateStockPrice(stockCode, price) {
  await vk.sseChannel.send({
    channelId: `stock-${stockCode}-updates`,
    data: {
      type: 'price-update',
      stockCode: stockCode,
      price: price,
      change: calculateChange(price),
      timestamp: Date.now()
    }
  });
}

// 体育比赛实时比分
async function updateGameScore(gameId, score) {
  await vk.sseChannel.send({
    channelId: `game-${gameId}-updates`,
    data: {
      type: 'score-update',
      gameId: gameId,
      score: score,
      timestamp: Date.now()
    }
  });
}
```

### 聊天应用

```javascript
// 发送聊天消息
async function sendChatMessage(roomId, message, sender) {
  await vk.sseChannel.send({
    channelId: `chat-room-${roomId}`,
    data: {
      type: 'message',
      roomId: roomId,
      message: message,
      sender: sender,
      timestamp: Date.now()
    }
  });
}

// 用户加入/离开聊天室
async function updateChatRoomPresence(roomId, user, action) {
  await vk.sseChannel.send({
    channelId: `chat-room-${roomId}`,
    data: {
      type: 'presence',
      roomId: roomId,
      user: user,
      action: action, // 'join' or 'leave'
      timestamp: Date.now()
    }
  });
}
```

## 性能优化

### 消息批量处理

```javascript
// 批量处理消息发送
async function batchSendNotifications(notifications) {
  const sendTasks = notifications.map(notification => ({
    channelId: `user-${notification.userId}-notifications`,
    data: {
      type: 'notification',
      message: notification.message,
      timestamp: Date.now()
    }
  }));
  
  await vk.sseChannel.batchSend(sendTasks);
}
```

### 通道清理

```javascript
// 定期清理过期通道
async function cleanupExpiredChannels() {
  const expiredChannels = await vk.sseChannel.listExpired();
  
  for (const channel of expiredChannels) {
    await vk.sseChannel.close(channel.channelId);
  }
  
  console.log(`清理了 ${expiredChannels.length} 个过期通道`);
}
```

### 连接监控

```javascript
// 监控SSE连接状态
async function monitorSSEConnections() {
  const stats = await vk.sseChannel.getStats();
  
  console.log('SSE连接统计:');
  console.log('- 活跃通道数:', stats.activeChannels);
  console.log('- 总消息数:', stats.totalMessages);
  console.log('- 平均消息大小:', stats.avgMessageSize);
  console.log('- 最大并发连接数:', stats.maxConcurrent);
}
```

## 错误处理

### 连接错误处理

```javascript
// 前端错误处理
const eventSource = new EventSource('/sse/channel/user-notifications');

eventSource.onerror = function(error) {
  if (eventSource.readyState === EventSource.CLOSED) {
    console.log('SSE连接已关闭');
    // 尝试重新连接
    setTimeout(() => {
      initSSEConnection();
    }, 5000);
  } else {
    console.error('SSE连接错误:', error);
  }
};
```

### 服务端错误处理

```javascript
// 服务端错误处理
async function sendSSEMessageSafely(channelId, data) {
  try {
    await vk.sseChannel.send({
      channelId: channelId,
      data: data
    });
    return true;
  } catch (error) {
    console.error('发送SSE消息失败:', error);
    
    // 通道不存在，尝试创建新通道
    if (error.code === 'CHANNEL_NOT_FOUND') {
      try {
        await vk.sseChannel.create({
          channelId: channelId,
          timeout: 3600
        });
        // 重新发送消息
        await vk.sseChannel.send({
          channelId: channelId,
          data: data
        });
        return true;
      } catch (retryError) {
        console.error('重试发送SSE消息失败:', retryError);
        return false;
      }
    }
    
    return false;
  }
}
```

## 安全考虑

### 通道权限验证

```javascript
// 验证通道访问权限
async function validateChannelAccess(channelId, userId) {
  const channelInfo = await vk.sseChannel.getInfo(channelId);
  
  // 检查通道元数据中的用户ID是否匹配
  if (channelInfo.metadata && channelInfo.metadata.userId !== userId) {
    throw new Error('无权访问此通道');
  }
  
  return true;
}
```

### 消息内容过滤

```javascript
// 过滤敏感信息
function filterSensitiveContent(message) {
  const sensitivePatterns = [
    /password/gi,
    /token/gi,
    /secret/gi,
    /credit.card/gi
  ];
  
  let filteredMessage = message;
  
  sensitivePatterns.forEach(pattern => {
    filteredMessage = filteredMessage.replace(pattern, '***');
  });
  
  return filteredMessage;
}
```

### 频率限制

```javascript
// 限制消息发送频率
const rateLimit = new Map();

async function checkRateLimit(channelId) {
  const now = Date.now();
  const lastSendTime = rateLimit.get(channelId) || 0;
  
  if (now - lastSendTime < 1000) { // 1秒内只能发送1条消息
    throw new Error('发送频率过高');
  }
  
  rateLimit.set(channelId, now);
  return true;
}
```

## 最佳实践

1. **使用合适的超时时间**：根据业务需求设置合理的通道超时时间
2. **实施频率限制**：防止滥用和DDoS攻击
3. **验证消息内容**：确保发送的消息格式正确且不包含敏感信息
4. **监控连接状态**：定期检查通道健康状况
5. **优雅的错误处理**：提供有意义的错误消息和重试机制
6. **使用HTTPS**：在生产环境中始终使用HTTPS确保通信安全
7. **考虑兼容性**：为不支持SSE的客户端提供降级方案