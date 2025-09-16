---
type: "manual"
description: "VK-UniCloud 框架概述和项目结构规范"
---

# VK-UniCloud 框架概述

## 框架概述

VK-UniCloud 是一个基于 UniCloud 的快速开发框架，包含客户端框架和管理端框架两部分：

- **客户端框架**: vk-unicloud-router - 云函数路由模式开发框架
- **管理端框架**: vk-unicloud-admin - 基于 Element UI 的后台管理系统

### 核心特性

#### 1. 云函数路由模式
- 使用纯原生代码实现路由模式，兼容性强
- 减少云函数数量，一个云函数实现多个功能
- 支持云函数和云对象两种模式
- 美化云函数请求日志，便于调试

#### 2. 内置丰富API
- 集成 uni-id 用户系统
- 提供 vk.baseDao 数据库API
- 内置微信、支付宝、百度等平台服务端API
- 支持短信、邮件、支付等功能

#### 3. 权限管理
  - 全局过滤器，过滤非法请求
  - 完善的用户角色权限系统
  - 支持权限精确到每个云函数

#### 4. 开发工具
- 万能表格组件（管理端）
- 万能表单组件（管理端）
- 代码生成器
- 调试工具

## 项目结构

### 云端目录结构
```
uniCloud-alipay/
├── cloudfunctions/           # 云函数目录
│   ├── common/              # 公共模块
│   │   ├── uni-config-center/  # 配置中心
│   │   ├── uni-id/            # 用户身份管理
│   │   └── vk-unicloud/       # VK框架核心
│   └── router/              # 路由云函数
│       ├── service/         # 业务逻辑
│       │   ├── admin/       # 管理端业务
│       │   ├── client/      # 客户端业务
│       │   └── user/        # 用户中心
│       └── index.js         # 入口文件
├── database/                # 数据库Schema
│   ├── uni-id-users.schema.json
│   └── custom-table.schema.json
└── cloudobjects/           # 云对象目录（可选）
```

### 云函数目录权限规范
```
service/
├── admin/                   # 管理端业务逻辑
│   └── [模块]/
│       ├── sys/            # 需要特定权限验证
│       ├── kh/             # 需要登录 + allow_login_background
│       └── pub/            # 公开访问
├── client/                  # 客户端业务逻辑
│   └── [模块]/
│       ├── kh/             # 需要登录
│       └── pub/            # 公开访问
└── user/                    # 用户中心服务
    ├── sys/                # 管理员权限
    ├── kh/                 # 用户登录权限
    └── pub/                # 公开访问
```
</content>
</write_to_file>

<write_to_file id="mf0jdbngeazub20wnv1j6t4yoysc8oml">
<path>.augment/rules/cloud-function-development.md</path>
<content>---
type: "manual"
description: "云函数开发规范和模板"
---

# 云函数开发规范

## 标准云函数模板

**注意：** 云函数只有 `main` 方法，不支持 `before`、`after` 等方法（这些是云对象的特性）。

```javascript
'use strict';
/**
 * 云函数模板
 * @url 模块名/接口名 前端调用的url参数地址
 * @description 接口描述
 * @param {String} param1 参数1说明
 * @param {Object} param2 参数2说明
 */
module.exports = {
  /**
   * 主函数
   * @param {Object} event 前端传来的参数
   */
  main: async (event) => {
    let { data = {}, userInfo, util, filterResponse, originalParam } = event;
    let { customUtil, uniID, config, pubFun, vk, db, _, $ } = util;
    let { uid } = data; // 注意：uid 从 data 中获取，不是从 userInfo 中
    let res = { code: 0, msg: '' };

    // 业务逻辑开始-----------------------------------------------------------

    // 参数获取
    let { param1, param2 } = data;

    // 参数验证
    if (!param1) {
      return { code: -1, msg: 'param1不能为空' };
    }

    try {
      // 数据库操作
      let dbRes = await vk.baseDao.select({
        dbName: "table-name",
        whereJson: { _id: param1 }
      });

      // 返回结果
      res.data = dbRes.rows;

    } catch (err) {
      res.code = -1;
      res.msg = err.message;
    }

    // 业务逻辑结束-----------------------------------------------------------

    return res;
  }
}
```

### 万能表格数据接口模板
```javascript
/**
 * 万能表格数据接口
 * @url admin/模块名/sys/getList
 * @description 获取列表数据
 */
module.exports = {
  main: async (event) => {
    let { data = {}, userInfo, util } = event;
    let { vk, db } = util;
    let res = { code: 0, msg: '' };

    // 获取分页参数
    let { pageIndex = 1, pageSize = 20, sortRule = [] } = data;
    
    // 获取搜索条件
    let { keyword, status, category_id } = data;
    
    // 构建查询条件
    let whereJson = {};
    
    if (keyword) {
      whereJson.name = new RegExp(keyword, 'i'); // 模糊搜索
    }
    
    if (typeof status !== 'undefined') {
      whereJson.status = status;
    }
    
    if (category_id) {
      whereJson.category_id = category_id;
    }

    try {
      // 查询数据
      let dbRes = await vk.baseDao.getTableData({
        dbName: "table-name",
        pageIndex,
        pageSize,
        whereJson,
        sortRule,
        // 关联查询示例
        foreignDB: [
          {
            dbName: "category",
            localKey: "category_id",
            foreignKey: "_id",
            as: "categoryInfo",
            limit: 1
          }
        ]
      });

      res.rows = dbRes.rows;
      res.total = dbRes.total;
      res.hasMore = dbRes.hasMore;

    } catch (err) {
      res.code = -1;
      res.msg = err.message;
    }

    return res;
  }
}
```

### 增删改查模板
```javascript
/**
 * 新增数据
 * @url admin/极速开发/sys/add
 */
const add = {
  main: async (event) => {
    let { data = {}, userInfo, util } = event;
    let { vk } = util;
    let { uid } = userInfo;
    let res = { code: 0, msg: '' };

    // 参数验证
    let { name, description } = data;
    if (!name) {
      return { code: -1, msg: '名称不能为空' };
    }

    try {
      // 添加创建信息
      data.create_date = new Date().getTime();
      data.create_by = uid;

      // 插入数据
      let addRes = await vk.baseDao.add({
        dbName: "table-name",
        dataJson: data
      });

      res.data = addRes;
      res.msg = '添加成功';

    }极速开发 (err) {
      res.code = -1;
      res.msg = err.message;
    }

    return res;
  }
}

/**
 * 更新数据
 * @url admin/模块名/sys/update
 */
const update = {
  main: async (event) => {
    let { data = {}, userInfo, util } = event;
    let { vk } = util;
    let { uid } = userInfo;
    let res = { code: 0,极速开发 '' };

    let { _id } = data;
    if (!_id) {
      return { code: -1, msg: 'ID不能为空' };
    }

    try {
      // 添加更新信息
      data.update_date = new Date().getTime();
      data.update_by = uid;

      // 更新数据
      let updateRes = await vk.baseDao.update({
        dbName: "table-name",
        whereJson: { _id },
        dataJson: data
      });

      res.data = updateRes;
      res.msg = '更新成功';

    } catch (err) {
      res.code = -1;
      res.msg = err.message;
    }

    return res;
  }
}

/**
 * 删除数据
 * @url admin/模块名/sys/delete
 */
const deleteData = {
  main: async (event) => {
    let { data = {}, util } = event;
    let { vk } = util;
    let res = { code: 0, msg: '' };

    let { _id } = data;
    if (!_id) {
      return { code: -1, msg: 'ID不能为空' };
    }

    try {
      // 支持批量删除
      let whereJson = {};
      if (Array.isArray(_极速开发)) {
        whereJson._id = vk.db.command.in(_id);
      } else {
        whereJson._id = _id;
      }

      // 删除数据
      let deleteRes = await vk.baseDao.delete({
        dbName: "table-name",
        whereJson
      });

      res.data = deleteRes;
      res.msg = '删除成功';

    } catch (err) {
      res.code = -1;
      res.msg = err.message;
    }

    return res;
  }
}
```

### 错误处理和日志
```javascript
module.exports = {
  main: async (event) => {
    let { data = {}, userInfo, util } = event;
    let { vk } = util;
    let res = { code: 0, msg: '' };

    try {
      // 记录请求日志
      console.log('请求参数:', data);
      console.log('用户信息:', userInfo);

      // 业务逻辑
      // ...

      // 记录成功日志
      console.log('执行成功:', res);

    } catch (err) {
      // 记录错误日志
      console.error('执行失败:', err);
      
      res.code = -1;
      res.msg = err.message || '系统错误';
      
      // 开发环境显示详细错误
      if (vk.isDev()) {
        res.error = err.stack;
      }
    }

    return res;
  }
}
```
</content>
</write_to_file>

<write_to_file id="mf0jdrirk6h5pbhjiweuhdybdbewimkm">
<path>.augment/rules/cloud-object-development.md</path>
<content>---
type: "manual"
description: "云对象开发规范和模板"
---

# 云对象开发规范

云对象是云函数的集合，即N个函数写在同一个xx.js文件里。在VK框架中，可以做到云对象和云函数同时存在。

## 云对象基本模板

```javascript
'use strict';
var vk; // 全局vk实例
// 涉及的表名
const dbName = {
  //test: "vk-test", // 测试表
};

var db = uniCloud.database(); // 全局数据库引用
var _ = db.command; // 数据库操作符
var $ = _.aggregate; // 聚合查询操作符

var cloudObject = {
  isCloudObject: true, // 标记为极速开发模式
  /**
   * 请求前处理，主要用于调用方法之前进行预处理，一般用于拦截器、统一的身份验证、参数校验、定义全局对象等。
   */
  _before: async function() {
    vk = this.vk; // 将vk定义为全局对象
    // let { customUtil, uniID, config, pubFun } = this.getUtil(); // 获取工具包
  },
  /**
   * 请求后处理，主要用于处理本次调用方法的返回结果或者抛出的错误
   */
  _after: async function(options) {
    let { err, res } = options;
    if (err) {
      return; // 如果方法抛出错误，直接return;不处理
    }
    return res;
  },
  /**
   * 模板函数
   * @url client/user.getInfo 前端调用的url参数地址
   */
  get极速开发: async function(data) {
    let { uid } = this.getClientInfo(); // 获取客户端信息
    let userInfo = await this.getUserInfo(); // 获取当前登录的用户极速开发
    let res = { code: 0, msg: '' };
    // 业务逻辑开始-----------------------------------------------------------
    console.log("请求参数", data);
    res.userInfo = userInfo; // 返回前端当前登录的用户信息

    // 业务逻辑结束-----------------------------------------------------------
    return res;
  },
  /**
   * 模板函数
   * @url client/user.getList 前端调用的url参数地址
   */
  getList: async function(data) {
    let { uid, filterResponse, originalParam } = this.getClientInfo(); // 获取客户端信息
    let res = { code: 0, msg: '' };
    // 业务逻辑开始-----------------------------------------------------------
    console.log("请求参数", data);

    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
};

module.exports = cloudObject;
```

## 云对象内置API

### this.getClientInfo（获取客户端信息）

```javascript
module.exports = {
  add: function() {
    const clientInfo = this.getClientInfo();
    let { uid } = this.getClientInfo(); // 解构赋值
  }
}
```

**返回值包含：**
- `uid`: 当前登录用户ID（可信任）
- `os`: 客户端系统
- `appId`: 客户端DCloud AppId
- `clientIP`: 客户端IP
- `platform`: 客户端平台（h5、mp-weixin等）

### this.getUserInfo（获取用户信息）

```javascript
module.exports = {
  getUserData: async function() {
    let userInfo = await this.getUserInfo();
    console.log('用户信息:', userInfo);
    return userInfo;
  }
}
```

### this.getUtil（获取工具包）

```javascript
module.exports = {
  _before: async function() {
    let { customUtil, uniID, config, pubFun, v极速开发, db, _, $ } = this.getUtil();
    // 使用工具包
  }
}
```

## 云对象权限控制

```javascript
var cloudObject = {
  // 权限配置
  _permission: {
    // 需要登录的方法
    'getUserInfo': ['login'],
    // 需要特定权限的方法
    'deleteUser': ['admin'],
    // 公开方法（无需权限）
    'getPublicData': []
  },

  _before: async function() {
    // 权限验证逻辑
    let methodName = this.getMethodName();
    let permissions = this._permission[methodName];

    if (permissions && permissions.includes('login')) {
      let { uid } = this.getClientInfo();
      if (!uid) {
        throw new Error('请先登录');
      }
    }
  }
};
```
</content>
</write_to_file>

<write_to_file id="mf0jdrirk6h5pux001ij263rm1o89xbf2">
<path>.augment/rules/cloud-database-development.md</path>
<content>---
type: "manual"
description: "数据库开发规范和Schema定义"
---

# 数据库开发规范

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
```
</content>
</write_to_file>

<write_to_file id="mf0jdrirk6h5pux001ij263rm1o89xbf3">
<path>.augment/rules/cloud-cache-system.md</path>
<content>---
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
```
</content>
</write_to_file>

<write_to_file id="mf0jdrirk6h5pux001ij263rm1o89xbf4">
<path>.augment/rules/cloud-permission-system.md</path>
<极速开发>---
type: "manual"
description: "权限系统架构和使用规范"
---

# 权限系统规范

## 权限系统架构

VK-UniCloud采用基于角色的权限控制(RBAC)模型，通过用户、角色、权限三层关系实现灵活的权限管理。

### 核心概念
- **用户(User)**: 系统的使用者
- **角色(Role)**: 权限的集合，如管理员、编辑者、查看者
- **权限(Permission)**: 具体的操作权限，如用户管理、订单查看
- **菜单(Menu)**: 前端页面访问权限

### 数据表关系
```
uni-id-users (用户表)
├── role (角色字段，数组类型)
└── permission (权限字段，数组类型)

uni-id-roles (角色表)
├── role_id (角色ID)
├── role_name (角色名称)
└── permission (权限列表，数组类型)

uni-id-permissions (权限极速开发)
├── permission_id (权限ID)
├── permission_name (权限名称)
└── comment (权限说明)
```

## 权限验证

### 云函数权限验证
```javascript
module.exports = {
  main: async (event) => {
    let { data = {}, userInfo, util } = event;
    let { vk } = util;
    let { uid } = userInfo;

    // 检查登录状态
    if (!uid) {
      return { code: 401, msg: '请先登录' };
    }

    // 检查权限
    let hasPermission = await vk.userCenter.hasPermission({
      uid,
      permission: 'user-manage'
    });

    if (!hasPermission) {
      return { code: 403, msg: '没有操作权限' };
    }

    // 业务逻辑
    // ...
  }
}
```

### 角色权限检查
```javascript
// 检查用户是否有特定角色
let hasRole = await vk.userCenter.hasRole({
  uid,
  role: 'admin'
});

// 检查用户是否有多个权限中的任意一个
let hasAnyPermission = await vk.userCenter.hasAnyPermission({
  uid,
  permissions: ['user-view', 'user-manage']
});

// 检查用户是否有所有指定权限
let hasAllPermissions = await vk.userCenter.hasAllPermissions({
  uid,
  permissions: ['user-view', 'user-edit']
});