---
type: "manual"
description: "响应体格式规范和标准"
---

# 响应体规范

VK框架定义了标准的云函数响应体格式，确保前端能够正确处理返回结果。

## 基础响应格式

所有云函数的返回值都应该遵循以下基础格式：

```javascript
{
  code: 0,        // 状态码，0表示成功，非0表示失败
  msg: "操作成功", // 提示信息
  data: {}        // 业务数据（可选）
}
```

## 成功返回值

只有当 `code` 为 0 时，才代表操作成功，前端 `vk.callFunction` 会走 `success` 回调：

```javascript
// 基础成功返回
{
  code: 0,
  msg: "操作成功"
}

// 带数据的成功返回
{
  code: 0,
  msg: "获取成功",
  data: {
    userInfo: {
      _id: "user001",
      nickname: "张三",
      avatar: "https://example.com/avatar.jpg"
    }
  }
}

// 列表数据返回
{
  code: 0,
  msg: "获取成功",
  data: {
    rows: [
      { _极速开发: "1", title: "标题1" },
      { _id: "2", title: "标题2" }
    ],
    total: 100,
    pageIndex: 1,
    pageSize: 10,
    hasMore: true
  }
}
```

## 失败返回值

只要 `code` 不为 0，都属于失败，前端 `vk.callFunction` 会走 `fail` 回调，并自动执行 `vk.alert(msg)`：

```javascript
// 基础失败返回
{
  code: -1,
  msg: "操作失败"
}

// 业务逻辑失败
{
  code: 1001,
  msg: "兑换失败，积分不足"
}

// 参数验证失败
{
  code: 1002,
  msg: "参数错误：用户ID不能为空"
}

// 权限验证失败
{
  code: 1003,
  msg: "权限不足，无法执行此操作"
}

// 带详细错误信息
{
  code: 1004,
  msg: "数据验证失败",
  data: {
    errors: {
      username: "用户名长度不能少于3个字符",
      email: "邮箱格式不正确"
    }
  }
}
```

## 特殊响应格式

### 自动更新用户信息

当返回值包含 `needUpdateUserInfo: true` 和 `userInfo` 时，前端会自动更新Vuex中的用户信息缓存：

```javascript
{
  code: 0,
  msg: "积分兑换成功",
  needUpdateUserInfo: true,
  userInfo: {
    _id: "user001",
    nickname: "张三",
    score: 850,  // 更新后的积分
    level: 3     // 更新后的等级
  }
}
```

### 自动更新Token

当返回值包含 `vk_uni_token` 时，前端会自动更新本地存储的token：

```javascript
{
  code: 0,
  msg: "token刷新成功",
  vk_uni_token: {
    token: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    tokenExpired: 1681818627000  // token过期时间戳
  }
}
```

## 常用状态码规范

建议使用以下状态码规范：

```javascript
// 成功状态码
const SUCCESS_CODE = 0

// 通用错误码
const ERROR_CODES = {
  // 系统级错误 (1-99)
  SYSTEM_ERROR: 1,           // 系统错误
  NETWORK_ERROR: 2,          // 网络错误
  DATABASE_ERROR: 3,         // 数据库错误

  // 权限相关错误 (100-199)
  UNAUTHORIZED: 100,         // 未登录
  FORBIDDEN: 101,            // 权限不足
  TOKEN_EXPIRED: 102,        // token过期
  TOKEN_INVALID: 103,        // token无效

  // 参数相关错误 (200-299)
  PARAM_ERROR: 200,          // 参数错误
  PARAM_MISSING: 201,        // 参数缺失
  PARAM_INVALID: 202,        // 参数格式错误

  // 业务相关错误 (1000+)
  USER_NOT_FOUND: 1001,      // 用户不存在
  USER_ALREADY_EXISTS: 1002, // 用户已存在
  INSUFFICIENT_BALANCE: 1003, // 余额不足
  ORDER_NOT_FOUND: 1004,     // 订单不存在
  GOODS_OUT_OF_STOCK: 1005   // 商品库存不足
}