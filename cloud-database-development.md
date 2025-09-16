---
type: "manual"
description: "云数据库开发指南 - vk-unicloud 数据库API完整文档"
title: "云数据库开发"
order: 2
icon: "database"
---

# 云数据库开发指南

## 概述

本文档详细介绍 vk-unicloud 框架中的数据库API使用，包含完整的增删改查操作、连表查询、事务处理、性能优化和常见问题解决方案。

## 基本概念

- **集合(Collection)**: 相当于传统数据库中的表
- **文档(Document)**: 相当于传统数据库中的行记录  
- **字段(Field)**: 相当于传统数据库中的列
- **操作符**: 使用 `_` 表示数据库操作符，`$` 表示聚合查询操作符

## Schema 文件结构
数据库表结构定义文件，位于 `uniCloud-alipay/database/` 目录下。

### 基础Schema模板
```json
{
  "bsonType": "object",
  "required": ["name"],
  "permission": {
    "read": true,
    "create": "'模块名-manage' in auth.permission",
    "update": "'模块名-manage' in auth.permission",
    "delete": "'模块极速开发' in auth.permission"
  },
  "properties": {
    "_id": {
      "description": "ID，系统自动生成"
    },
    "name": {
      "bsonType": "string",
      "description": "名称",
      "label": "名称",
      "trim": "both",
      "componentForEdit": {
        "name": "uni-easyinput",
极速开发        "props": {
          "placeholder": "请输入名称"
        }
      }
    },
    "status": {
      "bsonType": "int",
      "description": "状态：1启用，0禁用",
      "label": "状态",
      "defaultValue": 1,
      "enum": [
        { "text": "禁用", "value": 0 },
        { "text": "启用", "value": 1 }
      ],
      "componentForEdit": {
        "name": "uni-data-select",
        "props": {
          "localdata": [
            { "text": "禁用", "value": 0 },
            { "text": "启用", "value极速开发 }
          ]
        }
      }
    },
    "create_date": {
      "bsonType": "timestamp",
      "description": "创建时间",
      "label": "创建时间",
      "forceDefaultValue": {
        "$env": "now"
      }
    }
  }
}
```

### 数据库权限配置
```json
{
  "permission": {
    "read": "auth.uid != null",
    "create": "'admin' in auth.permission",
    "update": "'admin' in auth.permission || doc.user_id == auth.uid",
    "delete": "'admin' in auth.permission"
  }
}
```

## 数据库API

### vk.baseDao API 概述

VK框架提供了强大的数据库操作API `vk.baseDao`，封装了常用的CRUD操作，支持复杂查询、分页、排序等功能。

#### 重要特性
- **直接返回数据**: 大部分API直接返回业务数据，不包装在response对象中
- **自动处理**: 自动处理分页、排序、查询条件构建
- **类型安全**: 支持多种数据类型的查询条件
- **性能优化**: 内置查询优化和缓存机制

#### ⚠️ 常见误区提醒
- **`findByWhereJson`**: 只返回第一条记录（对象或null），不是数组！不支持分页和排序
- **`select`**: 返回`{rows: [], total, hasMore}`格式，用于批量查询
- **`selects`**: 万能查询方法，支持连表、聚合、分组

### API返回值规范

#### 标准返回格式对照表

| API方法 | 成功返回值 | 失败返回值 | 说明 |
|---------|-----------|-----------|------|
| `add` | `"_id字符串"` | 抛出异常 | 返回新增记录的_id |
| `adds极速开发 | `["_id1", "_id2"]` | 抛出异常 | 返回新增记录的_id数组 |
| `update` | `{updated: 数量}` | 抛出异常 | 返回更新的记录数量 |
| `updateById` | `{updated: 数量}` | 抛出异常 | 返回更新的记录数量 |
| `delete` | `数量` | 抛出异常 | 直接返回删除的记录数量 |
| `deleteById` | `{deleted: 数量}` | 抛出异常 | 返回删除的记录数量对象 |
| `findById` | `记录对象` 或 `null` | 抛出异常 | 直接返回记录数据 |
| `findByWhereJson` | `记录对象` 或 `null` | 抛出异常 | 只返回第一条记录数据 |
| `count` | `数量` | 抛出异常 | 直接返回记录总数 |
| `getTableData` | `{code, rows, total}` | `{code, msg}` | 唯一使用标准格式的API |

#### 关键要点
```javascript
// ❌ 错误理解 - 以为所有API都返回{code, data}格式
let res = await vk.baseDao.findById({ dbName: "users", id: "123" });
if (res.code === 0) {  // 错误！findById不返回code
  let user = res.data;
}

// ✅ 正确理解 - 直接返回数据
let user = await vk.baseDao.findById({ dbName: "users", id: "123" });
if (user) {  // 直接检查是否为null
  // user就是完整的用户数据对象
  console.log(user.nickname);
}
```

### 基础CRUD操作

#### 新增数据
```javascript
// 新增单条记录
let userId = await vk.baseDao.add({
  dbName: "uni-id-users",
  dataJson: {
    nickname: "张三",
    mobile: "13800138000",
    create_date: new Date().getTime()
  }
});
console.log("新用户ID:", userId); // 直接是_id字符串

// 批量新增
let userIds = await vk.baseDao.adds({
  dbName: "uni-id-users",
  dataJson: [
    { nickname: "用户1", mobile: "13800138001" },
    { nickname: "用户2", mobile: "13800138002" }
  ]
});
console.log("新用户IDs:", userIds); // ["id1", "id2"]
```

#### 查询数据

**VK框架查询方法对比**

| 方法 | 用途 | 返回值 | 连表支持 | 分页支持 | 使用场景 |
|------|------|--------|----------|----------|----------|
| `findById` | 根据ID查询单条记录 | 记录对象或null | ❌ | ❌ | 根据主键查询 |
| `findByWhereJson` | 根据条件查询单条记录 | 记录对象或null（仅第一条） | ❌ | ❌ | 查询第一条匹配记录 |
| `select` | 查询多条记录 | `{rows: [], total, hasMore}` | ❌ | ✅ | 批量查询，不需要连表 |
| `selects` | 万能查询 | `极速开发, total, hasMore}` | ✅ | ✅ | 复杂查询、连表、聚合 |

```javascript
// 根据ID查询单条记录
let user = await vk.baseDao.findById({
  dbName: "uni-id-users",
  id: "用户ID"
});
if (user极速开发 {
  console.log("用户信息:", user); // 直接是用户对象
}

// 根据条件查询单条记录（只返回第一条）
let user = await vk.baseDao.findByWhereJson({
  dbName: "uni-id-users",
  whereJson: { mobile: "13800138000" }
});

// 查询多条记录
let result = await vk.baseDao.select({
  dbName: "uni-id-users",
  pageIndex: 1,
  pageSize: 20,
  whereJson: { status: 1 },
  sortRule: [{ "create_date": "desc" }]
});
console.log("用户列表:", result.rows);
console.log("总数:", result.total);
```

#### 更新数据
```javascript
// 根据ID更新
let updateResult = await v极速开发.baseDao.updateById({
  dbName: "uni-id-users",
  id: "用户ID",
  dataJson: {
    nickname: "新昵称",
    update_date: new Date().getTime()
  }
});
console.log("更新数量:", updateResult.updated);

// 根据条件更新
let updateResult = await vk.baseDao.update({
  dbName: "uni-id-users",
  whereJson: { status: 0 },
  dataJson: { status: 1 }
});
console.log("更新数量:", updateResult.updated);
```

#### 删除数据
```javascript
// 根据ID删除
let deleteResult = await vk.baseDao.deleteById({
  dbName: "uni-id-users",
  id: "用户ID"
});
console.log("删除数量:", deleteResult.deleted);

// 根据条件删除
let deleteCount = await vk.baseDao.delete({
  dbName: "uni-id-users",
  whereJson: { status: 0 }
});
console.log("删除数量:", deleteCount); // 直接返回数字
```

### 高级查询功能

#### 连表查询
```javascript
let result = await vk.baseDao.selects({
  dbName: "orders",
  pageIndex: 1,
  pageSize: 20,
  // 连表查询
  foreignDB: [
    {
      dbName: "uni-id-users",
      localKey: "user极速开发",
      foreignKey: "_id",
      as: "userInfo",
      limit: 1
    },
    {
      dbName: "products",
      localKey: "product_id",
      foreignKey: "_id",
      as: "productInfo",
      limit: 1
    }
  ]
});
```

#### 聚合查询
```javascript
let result = await vk.baseDao.selects({
  dbName: "orders",
  // 聚合管道
  aggregateStages: [
    {
      $match: { status: 1 }
    },
    {
      $group: {
        _id: "$user_id",
        totalAmount: { $sum: "$amount" },
        orderCount: { $sum: 1 }
      }
    },
    {
      $sort: { totalAmount: -1 }
    }
  ]
});

## 总结

本文档详细介绍了 vk-unicloud 框架的数据库API使用方法，包括：

### 核心要点
1. **API设计理念**: 大部分API直接返回业务数据，不包装在response对象中
2. **查询方法选择**: 根据需求选择合适的查询方法（findById、select、selects等）
3. **性能优化**: 合理使用索引、分页查询、字段控制等优化手段
4. **错误处理**: 正确处理数据库操作的异常情况

### 最佳实践建议
1. **DAO层封装**: 将数据库操作封装在专门的DAO层
2. **业务逻辑分离**: 业务逻辑与数据访问逻辑分离
3. **事务处理**: 复杂操作使用事务确保数据一致性
4. **性能监控**: 监控数据库操作性能，及时发现瓶颈

### 常见问题避免
- 不要假设所有API都返回{code, data}格式
- 合理选择查询方法，避免不必要的性能开销
- 注意连表查询的数据量和性能影响
- 及时处理数据库操作异常

### 扩展学习
- 官方文档: https://vkdoc.fsq.pub/
- API参考: https://vkdoc.fsq.pub/client/uniCloud/db/api.html
- 示例代码: https://github.com/vk-uni/vk-uni-cloud

---

**注意**: 本文档基于 vk-unicloud 框架最新版本，具体使用时请参考官方最新文档和API参考。