---
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