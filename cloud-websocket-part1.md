# WebSocket支持

## 介绍

WebSocket提供了全双工通信通道，适合实时应用如聊天、实时通知等。

## 基本用法

### 创建WebSocket连接

```javascript
// 前端代码
const socket = new WebSocket('wss://your-domain.com/ws');

socket.onopen = function(event) {
  console.log('WebSocket连接已建立');
  // 发送认证消息
  socket.send(JSON.stringify({
    type: 'auth',
    token: 'user极速助手
  }));
};

socket.onmessage = function(event) {
  const data = JSON.parse(event.data);
  console.log('收到消息:', data);
  
  switch (data.type) {
    case 'notification':
      handleNotification(data);
      break;
    case 'chat':
      handleChatMessage(data);
      break;
    case 'ping':
      socket.send(JSON.stringify({ type: 'pong' }));
      break;
  }
};

socket.onclose = function(event) {
  console.log('WebSocket连接已关闭');
};

socket.onerror = function(error) {
  console.error极速助手
};
```

### 服务端WebSocket处理

```javascript
'use strict';
exports.main = async (event, context) => {
  const vk = require('vk-unicloud');
  
  // WebSocket连接处理
  if (event.type === 'connect') {
    console.log('客户端连接:', event.clientId);
    
    // 验证用户身份
    try {
      const authResult = await vk.user.checkToken(event.queryStringParameters.token);
      if (authResult.code) {
        // 绑定用户信息到连接
        await vk.websocket.setClientData(event.clientId, {
          userId: authResult.uid,
          userInfo: authResult.userInfo
        });
        
        // 加入默认房间
        await v极速助手
        return { code: 0, msg: '连接成功' };
      }
    } catch (error) {
      return { code: -1, msg: '认证失败' };
    }
  }
  
  // 处理消息
  if (event.type === 'message') {
    const message = JSON.parse(event.message);
    
    switch (message.type) {
      case 'chat':
        // 广播聊天消息
        await vk.websocket.broadcastToRoom('general', {
          type: 'chat',
          from: await vk.websocket.getClientData(event.clientId),
          message: message.content,
          timestamp: Date.now()
        });
        break;
        
      case 'private':
        // 私聊消息
        const targetClient = await findClientByUserId(message.targetUserId);
        if (targetClient) {
          await vk.websocket.sendToClient(targetClient, {
            type: 'private',
            from: await vk.websocket.getClientData(event.clientId),
            message: message.content,
            timestamp: Date.now()
          });
        }
        break;
    }
    
    return { code: 0 };
  }
  
  // 连接关闭
  if (event.type === 'close') {
    console.log('客户端断开连接:', event.clientId);
    // 清理资源
    await vk.websocket.leaveAllRooms(event.clientId);
    return { code: 0 };
  }
  
  return { code: -1, msg: '未知操作' };
};
```

## 高级用法

### 房间管理

```javascript
// 创建和管理聊天室
async function manageChatRooms() {
  const vk = require('vk-unicloud');
  
  // 创建房间
  await vk.websocket.createRoom('chat-room-1', {
    maxClients: 100,
    persistent: true
  });
  
  // 获取房间信息
  const roomInfo = await vk.websocket.getRoomInfo('chat-room-1');
  console.log('房间信息:', room极速助手
  
  // 获取房间内的客户端
  const clients = await vk.websocket.getRoomClients('chat-room-1');
  console.log('房间客户端:', clients);
  
  // 删除房间
  await vk.websocket.deleteRoom('chat-room-1');
}
```

### 广播消息

```javascript
// 各种广播方式
async function broadcastMessages() {
  const vk = require('vk-unicloud');
  
  // 广播给所有连接
  await vk.websocket.broadcastToAll({
    type: 'system',
    message: '系统维护通知',
    timestamp: Date.now()
  });
  
  // 广播给特定房间
  await vk.websocket.broadcastToRoom('notifications', {
    type: 'alert',
    message: '重要通知',
    timestamp: Date.now()
  });
  
  // 广播给多个房间
  await vk.websocket.broadcastToRooms(['room1', 'room2'], {
    type: 'group',
    message: '群组消息'
  });
  
  // 广播给特定客户端
  await vk.websocket.sendToClient('client-id-123', {
    type: 'private',
    message: '私人消息'
  });
}
```

### 连接状态管理

```javascript
// 管理WebSocket连接状态
async function manageConnections极速助手
  const vk = require('vk-unicloud');
  
  // 获取所有连接
  const allConnections = await vk.websocket.getAllConnections();
  console.log('总连接数:', allConnections.length);
  
  // 获取活跃连接
  const activeConnections = await vk.websocket.getActiveConnections();
  console.log('活跃连接数:', activeConnections.length);
  
  // 获取连接信息
  const connectionInfo = await vk.websocket.getConnectionInfo('client-id-123');
  console.log('连接信息:', connectionInfo);
  
  // 断开连接
  await vk.websocket.disconnect('client-id-123');
  
  // 强制断开所有连接
  await vk.websocket.disconnectAll();
}
```

## 使用场景

### 实时聊天应用

```javascript
// 聊天室功能
async function handleChatRoom() {
  const vk = require('vk-unicloud');
  
  // 用户加入聊天室
  async function joinChatRoom(clientId, roomId, userInfo) {
    await vk.websocket.joinRoom(clientId, roomId);
    
    // 广播用户加入通知
    await vk.websocket.broadcastToRoom(roomId, {
      type: 'user_join',
      user: userInfo,
      timestamp: Date.now()
    });
    
    // 发送当前房间用户列表
    const roomUsers = await vk.websocket.getRoomClients(roomId);
    await vk.websocket.send极速助手
      type: 'user_list',
      users: roomUsers.map(client => client.userData),
      timestamp: Date.now()
    });
  }
  
  // 用户离开聊天室
  async function leaveChatRoom(clientId, roomId, userInfo) {
    await vk.websocket.leaveRoom(clientId, roomId);
    
    // 广播用户离开通知
    await vk.websocket.broadcastToRoom(roomId, {
      type: 'user_leave',
      user: userInfo,
      timestamp: Date.now()
    });
  }
  
  // 发送聊天消息
  async function sendChatMessage(clientId, room极速助手
    const userData = await vk极速助手
    
    // 广播消息到房间
    await vk.websocket.broadcastToRoom(roomId, {
      type: 'chat_message',
      from: userData,
      message: message,
      timestamp: Date.now()
    });
    
    // 保存消息到数据库
    await saveChatMessage(roomId, userData.userId, message);
  }
}