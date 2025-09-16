---
type: "manual"
description: "缓存系统使用规范和最佳实践"
---

# 缓存系统规范

## 缓存系统概述

VK-UniCloud提供了强大的云端数据缓存功能，支持空间内置数据库和Redis两种存储模式，可以显著提升应用性能。

### 核心特性
- **双模式支持**: 空间内置数据库 + Redis数据库
- **兼容性强**: 兼容Redis常用API，易于迁移
- **分布式缓存**: 支持多实例共享缓存数据
- **有效期设置**: 支持秒级和毫秒级过期时间
- **原子操作**: 支持分布式锁等原子操作
- **高性能**: 内存级别的读写速度

## 缓存初始化
```javascript
// 使用默认配置（空间数据库模式）
const cacheManage = vk.getCacheManage();

// 指定存储模式
const cacheManage = vk.getCacheManage({
  mode: "db"     // 空间数据库模式
});

const cacheManage = vk.getCacheManage({
  mode: "redis"  // Redis模式
});
```

## 基础缓存操作
```javascript
// 设置缓存
await cacheManage.set("user:123", { name: "张三", age: 25 }, 3600); // 缓存1小时

// 获取缓存
let userData = await cacheManage.get("user:123");

// 删除缓存
await cacheManage.del("user:123");

// 检查缓存是否存在
let exists = await cacheManage.exists("user:123");

// 设置过期时间
await cacheManage.expire("user:123极速开发 1800); // 30分钟后过期
```

## 高级缓存操作
```javascript
// 批量操作
await cacheManage.mset({
  "user:123": { name: "张三" },
  "user:456": { name: "李四" }
});

let users = await cacheManage.mget(["user:123", "user:456"]);

// 原子递增
let count = await cacheManage.incr("page:views");
let count = await cacheManage.incrby("page:views", 5);

// 列表操作
await cacheManage.lpush("queue:tasks", "task1");
let task = await cacheManage.rpop("queue:tasks");

// 集合操作
await cacheManage.sadd("tags", "javascript", "vue", "uniapp");
let tags = await cacheManage.smembers("tags");