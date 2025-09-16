### 实时通知系统

```javascript
// 实时通知功能
async function handleRealTimeNotifications() {
  const vk = require('vk-unicloud');
  
  // 发送个人通知
  async function sendPersonalNotification(userId, notification) {
    // 查找用户的WebSocket连接
    const userConnections = await vk.websocket.findConnectionsByUserId(userId);
    
    for (const connection of userConnections) {
      await vk.websocket.sendToClient(connection.client极速助手
        type: 'notification',
        id: generateId(),
        title: notification.title,
        content: notification.content,
        priority: notification.priority || 'normal',
        timestamp: Date.now()
      });
    }
    
    // 同时保存到数据库
    await saveNotificationToDB(userId, notification);
  }
  
  // 发送全局通知
  async function sendGlobalNotification(notification) {
    await vk.websocket.broadcastToAll({
      type: 'global_notification',
      id: generateId(),
      title: notification.title,
      content: notification.content,
      timestamp: Date.now()
    });
    
    // 记录日志
    await logGlobalNotification(notification);
  }
  
  // 标记通知为已读
  async function markNotificationAsRead(clientId, notificationId) {
    await vk.websocket.sendToClient(clientId, {
      type: 'notification_read',
      notificationId: notificationId,
      timestamp: Date.now()
    });
    
    // 更新数据库状态
    await updateNotificationStatus(notificationId, 'read');
  }
}
```

### 实时数据推送

```javascript
// 实时数据推送
async function handleRealTimeData() {
  const vk = require('vk-unicloud');
  
  // 股票价格实时推送
  async function pushStockPrices(stockData) {
    const subscribers = await vk.websocket.getRoomClients('stock-updates');
    
    for (const client of subscribers) {
      // 只推送用户关注的股票
      const userStocks = await getUserSubscribedStocks(client.userData.userId);
      const filteredData = stockData.filter(data => 
        userStocks.includes(data.symbol)
      );
      
      if (filteredData.length > 0) {
        await vk.websocket.sendToClient(client.clientId, {
          type: 'stock_update',
          data: filteredData,
          timestamp: Date.now()
        });
      }
    }
  }
  
  // 体育比赛实时比分
  async function pushSportsScores(gameData) {
    await vk.websocket.broadcastToRoom(`game-${gameData.gameId}`, {
      type: 'score_update',
      game: gameData,
      timestamp: Date.now()
    });
  }
  
  // 实时统计数据
  async function push极速助手
    // 只推送给管理员
    const adminConnections = await vk.websocket.findConnectionsByUserRole('admin');
    
    for (const connection of adminConnections) {
      await vk.websocket.sendToClient(connection.clientId, {
        type: 'stats_update',
        data: statsData,
        timestamp: Date.now()
      });
    }
  }
}
```

## 性能优化

### 连接池管理

```javascript
// WebSocket连接池优化
class WebSocketConnectionPool {
  constructor(maxConnections = 10000) {
    this.maxConnections = maxConnections;
    this.connections = new Map();
    this.roomIndex = new Map();
  }
  
  // 添加连接
  addConnection(clientId, connectionInfo) {
    if (this.connections.size >= this.maxConnections) {
      throw new Error('连接数已达上限');
    }
    
    this.connections.set(clientId, {
      ...connectionInfo,
      createdAt: Date.now(),
      lastActivity: Date.now()
    });
    
    console.log('连接已添加:', clientId);
  }
  
  // 移除连接
  removeConnection(clientId) {
    this.connections.delete(clientId);
    // 从所有房间中移除
    for (const [roomId, clients] of this.roomIndex) {
      if (clients.has(clientId)) {
        clients.delete(clientId);
      }
    }
    console.log('连接已移除:', clientId);
  }
  
  // 加入房间
  joinRoom(clientId, roomId) {
    if (!this.roomIndex.has(roomId)) {
      this.roomIndex.set(roomId, new Set());
    }
    this.roomIndex.get(roomId).add(clientId);
    console.log('客户端加入房间:', clientId, roomId);
  }
  
  // 离开房间
  leaveRoom(clientId, roomId) {
    if (this.roomIndex.has(roomId)) {
      this.roomIndex.get(roomId).delete(clientId);
      console.log('客户端离开房间:', clientId, roomId);
    }
  }
  
  // 获取房间客户端
  getRoomClients(roomId) {
    if (!this.roomIndex.has(roomId)) {
      return [];
    }
    return Array.from(this.roomIndex.get(roomId)).map(clientId => ({
      clientId,
      ...this.connections.get(clientId)
    }));
  }
  
  // 清理过期连接
  cleanupExpiredConnections(timeout = 300000) { // 5分钟
    const now = Date.now();
    for (const [clientId, connection] of this.connections) {
      if (now - connection.lastActivity > timeout) {
        this.removeConnection(clientId);
      }
    }
  }
}
```

### 消息压缩

```javascript
// WebSocket消息压缩
class MessageCompressor {
  constructor() {
    this.compressionEnabled = true;
    this.minSizeForCompression = 1024; // 1KB以上才压缩
  }
  
  // 压缩消息
  compressMessage(message) {
    if (!this.compressionEnabled || message.length < this.minSizeForCompression) {
      return message;
    }
    
    try {
      // 使用gzip压缩
      const compressed = gzipSync(Buffer.from(message));
      return compressed.toString('base64');
    } catch (error) {
      console.warn('消息压缩失败:', error);
      return message;
    }
  }
  
  // 解压消息
  decompressMessage(compressedMessage) {
    if (typeof compressedMessage !== 'string' || !compressedMessage.startsWith('gzip:')) {
      return compressedMessage;
    }
    
    try {
      const compressedData = Buffer.from(compressedMessage.substring(5), 'base64');
      const decompressed = gunzipSync(compressedData);
      return decompressed.toString('utf8');
    } catch (error) {
      console.warn('消息解压失败:', error);
      return compressedMessage;
    }
  }
  
  // 包装消息
  wrapMessage(data, options = {}) {
    const message = {
      type: options.type || 'data',
      data: data,
      timestamp: Date.now(),
      compressed: false
    };
    
    const jsonStr = JSON.stringify(message);
    
    if (this.compressionEnabled && jsonStr.length >= this.minSizeForCompression) {
      const compressed = this.compressMessage(jsonStr);
      return {
        type: 'compressed',
        data: compressed,
        timestamp: Date.now()
      };
    }
    
    return message;
  }
}
```

### 批量消息处理

```javascript
// 批量消息处理
class BatchMessageProcessor {
  constructor(batchSize = 50, batchTimeout = 100) {
    this.batchSize = batchSize;
    this.batchTimeout = batchTimeout;
    this.batchQueue = new Map();
    this.timers = new Map();
  }
  
  // 添加消息到批处理
  addToBatch(roomId, message) {
    if (!this.batchQueue.has(roomId)) {
      this.batchQueue.set(roomId, []);
    }
    
    const queue = this.batchQueue.get(roomId);
    queue.push(message);
    
    // 如果达到批量大小，立即处理
    if (queue.length >= this.batchSize) {
      this.processBatch(roomId);
      return;
    }
    
    // 设置/重置定时器
    if (this.timers.has(roomId)) {
      clearTimeout(this.timers.get(roomId));
    }
    
    this.timers.set(roomId, setTimeout(() => {
      this.processBatch(roomId);
    }, this.batchTimeout));
  }
  
  // 处理批量消息
  async processBatch(roomId) {
    if (!this.batchQueue.has(roomId)) {
      return;
    }
    
    const messages = this.batchQueue.get(roomId);
    if (messages.length === 0) {
      return;
    }
    
    // 清除队列和定时器
    this.batchQueue.set(roomId, []);
    if (this.timers.has(roomId)) {
      clearTimeout(this.timers.get(roomId));
      this.timers.delete(roomId);
    }
    
    // 批量发送消息
    try {
      await vk.websocket.broadcastToRoom(roomId, {
        type: 'batch_messages',
        messages: messages,
        count: messages.length,
        timestamp: Date.now()
      });
    } catch (error) {
      console.error('批量消息发送失败:', error);
      // 重新加入队列
      this.batchQueue.get(roomId).push(...messages);
    }
  }
  
  // 强制处理所有批量
  async flushAll() {
    for (const roomId of this.batchQueue.keys()) {
      await this.processBatch(roomId);
    }
  }
}
```

## 错误处理

### 连接错误处理

```javascript
// WebSocket连接错误处理
class WebSocketErrorHandler {
  constructor() {
    this.retryAttempts = new Map();
    this.maxRetries = 3;
    this.retryDelay = 1000;
  }
  
  // 处理连接错误
  async handleConnectionError(clientId, error) {
    const attempts = this.retryAttempts.get(clientId) || 0;
    
    if (attempts >= this.maxRetries) {
      console.error('连接重试次数超限:', clientId, error);
      this.retryAttempts.delete(clientId);
      throw error;
    }
    
    this.retryAttempts.set(clientId, attempts + 1);
    
    console.warn('连接错误，尝试重连:', clientId, `尝试 ${attempts + 1}/${this.maxRetries}`);
    
    // 延迟重试
    await new Promise(resolve => setTimeout(resolve, this.retryDelay * Math.pow(2, attempts)));
    
    try {
      // 尝试重新连接
      await vk.websocket.reconnect(clientId);
      this.retryAttempts.delete(clientId);
      console.log('重连成功:', clientId);
      return true;
    } catch (retryError) {
      console.error('重连失败:', clientId, retryError);
      return false;
    }
  }
  
  // 处理消息发送错误
  async handleSendError(clientId, message, error) {
    console.error('消息发送失败:', clientId, error);
    
    // 如果是连接问题，尝试重连
    if (error.code === 'CONNECTION_CLOSED' || error.code === 'CONNECTION_ERROR') {
      const reconnected = await this.handleConnectionError(clientId, error);
      if (reconnected) {
        // 重连成功后重新发送消息
        try {
          await vk.websocket.sendToClient(clientId, message);
          return true;
        } catch (retryError) {
          console.error('重发消息失败:', clientId, retryError);
        }
      }
    }
    
    return false;
  }
  
  // 清理重试记录
  cleanupRetryRecords(timeout = 3600000) { // 1小时
    const now = Date.now();
    for (const [clientId, timestamp] of this.retryAttempts) {
      if (now - timestamp > timeout) {
        this.retryAttempts.delete(clientId);
      }
    }
  }
}
```

### 消息确认机制

```javascript
// 消息确认机制
class MessageAcknowledgment {
  constructor(timeout = 30000) { // 30秒超时
    this.pendingAcks = new Map();
    this.timeout = timeout;
  }
  
  // 发送需要确认的消息
  async sendWithAck(clientId, message, ackTimeout = this.timeout) {
    const messageId = generateMessageId();
    const ackPromise = new Promise((resolve, reject) => {
      // 设置超时
      const timeoutId = setTimeout(() => {
        this.pendingAcks.delete(messageId);
        reject(new Error('消息确认超时'));
      }, ackTimeout);
      
      this.pendingAcks.set(messageId, {
        resolve,
        reject,
        timeoutId,
        clientId,
        message,
        sentAt: Date.now()
      });
    });
    
    // 发送消息
    await vk.websocket.sendToClient(clientId, {
      ...message,
      _ack: true,
      _messageId: messageId
    });
    
    return ackPromise;
  }
  
  // 处理确认消息
  handleAck(messageId) {
    const pending = this.pendingAcks.get(messageId);
    if (pending) {
      clearTimeout(pending.timeoutId);
      pending.resolve({ messageId, acknowledged: true });
      this.pendingAcks.delete(messageId);
    }
  }
  
  // 清理过期确认
  cleanupExpiredAcks() {
    const now = Date.now();
    for (const [messageId, pending] of this.pendingAcks) {
      if (now - pending.sentAt > this.timeout) {
        clearTimeout(pending.timeoutId);
        pending.reject(new Error('消息确认超时'));
        this.pendingAcks.delete(messageId);
      }
    }
  }
  
  // 批量发送带确认的消息
  async batchSendWithAck(clientIds, message, ackTimeout = this.timeout) {
    const results = {};
    const promises = [];
    
    for (const clientId of clientIds) {
      promises.push(
        this.sendWithAck(clientId, message, ackTimeout)
          .then(() => { results[clientId] = { success: true }; })
          .catch(error => { results[clientId] = { success: false, error: error.message }; })
      );
    }
    
    await Promise.all(promises);
    return results;
  }
}
```

## 最佳实践

1. **实施连接认证**：确保只有授权用户可以建立WebSocket连接
2. **使用房间机制**：合理组织客户端，提高广播效率
3. **实施消息压缩**：对大消息进行压缩，减少带宽消耗
4. **批量处理消息**：对高频消息进行批量处理，提高性能
5. **实现错误重试**：对网络错误实施自动重试机制
6. **监控连接状态**：实时监控连接数和消息流量
7. **限制连接数**：防止单个用户建立过多连接
8. **定期清理资源**：清理过期连接和未确认的消息
9. **实施速率限制**：防止消息洪水攻击
10. **日志记录**：记录重要操作和错误信息