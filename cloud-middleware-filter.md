# 云开发中间件过滤器

## 什么是过滤器?

过滤器（middleware，又名中间件）可以在业务云函数执行之前（或之后），统一拦截，进行过滤后再放行。

完整示例代码在目录 `router/middleware/modules/` 目录下

## 我用自定义过滤器可以做什么?

### 自定义过滤器使用场景1 - 电商多店店铺管理系统后台

1. 使用自定义过滤器拦截当前登录用户
2. 检查该用户是否有权限操作该店铺
3. 如有权限，则同时将店铺信息直接回传给业务云函数
4. 在业务云函数的内置变量 `filterResponse` 中可直接获得当前操作的店铺的信息

## 框架内置的过滤器

无需配置，已自动生效，编写同ID的过滤器可以覆盖内置过滤器（层级index比内置的小1即可）

| 过滤器ID | 正则规则 | 层级 | 类型 | 说明 |
|---------|---------|------|------|------|
| pub | /pub/ | 100 | onActionExecuting | 所有人都可以访问，不进行过滤 |
| kh | /kh/ | 200 | onActionExecuting | 用户token过滤器，检测用户是否已登录 |
| sys | /sys/ | 300 | onActionExecuting | 云函数层的权限过滤器，检测用户是否有此业务云函数的执行权限 |

## 中间件参数说明

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|-----|-----|------|-------|-------|
| id | 中间件ID，全局必须唯一，相同中间件ID会被覆盖 | String | - | - |
| regExp | 中间件的正则匹配规则（支持数组） | String Array | 无 | - |
| description | 中间件的描述，仅方便自己查看 | String | - | - |
| index | 中间件的执行顺序，值越小越先执行 | Number | - | - |
| mode | 中间件的模式 | String | onActionExecuting | 见下方mode参数说明 |
| enable | 是否启动该中间件 | Boolean | false | true |
| main | 执行的函数 | async function(event, serviceRes) | - | - |
| returnMode | 返回值模式，仅mode为onActionExecuted时生效 | Number | 0 | 1 |

## regExp参数说明

regExp 的值如果是字符串，代表只有一个匹配条件，如果是数组，代表多个匹配条件

如果是数组的情况下，只需要数组内任意一条规则匹配到就会运行该中间件

### 常用正则示例

#### 匹配所有
```javascript
regExp: "(.*)",
```

#### 匹配 xxx/kh 开头的
```javascript
regExp: "^xxx/kh",
```

#### 匹配 xxx 结尾的
```javascript
regExp: "xxx$",
```

#### 精确匹配 xxx/kh/getList
```javascript
regExp: "^xxx/kh/getList$",
```

#### 多个精确匹配 xxx/kh/getList 和 xxx/kh/add
```javascript
regExp: [
  "^xxx/kh/getList$",
  "^xxx/kh/add$",
]
```

## mode参数说明

| 类型 | 说明 |
|-----|-----|
| onActionExecuting | 在action执行前执行 |
| onActionExecuted | 在action执行后执行 |
| onActionIntercepted | 在action被其他中间件拦截后执行 |
| onActionError | 在action执行异常时执行 |

## returnMode参数说明

返回值模式，仅mode为onActionExecuted时生效

| 值 | 说明 |
|---|-----|
| 0 | 使用Object.assign合并 |
| 1 | 完全替换 |

### 详细说明

当设置 returnMode: 0 时，返回值会使用Object.assign合并

如函数返回值：
```javascript
{
  "code": 0,
  "a": 1
}
```

在中间件返回值：
```javascript
{
  "code": -1,
  "msg": "失败"
}
```

则最终返回值：
```javascript
{
  "code": -1,
  "msg": "失败",
  "a": 1
}
```

当设置 returnMode: 1 时，返回值会被中间件返回值完全替换

如函数返回值：
```javascript
{
  "code": 0,
  "a": 1
}
```

在中间件返回值：
```javascript
{
  "code": -1,
  "msg": "失败"
}
```

则最终返回值：
```javascript
{
  "code": -1,
  "msg": "失败"
}
```

## 自定义过滤器代码示例

### 单店版

```javascript
/**
 * 店铺权限过滤器示例
 */
module.exports = [
  {
    id: "shopManage",
    regExp: "^client/shop/manage/(.*)",
    description: "店铺操作接口需要检测用户是否有权限",
    index: 310,// 此处必须>300 因为检测用户是否登录的过滤器的index是200（sys是300，因此为了能通用，建议填大于300的值）（越小越先执行）
    mode:"onActionExecuting", 
    enable:true, // 通过设置enable=false可以关闭该中间件
    main: async function(event) {
      let { util, filterResponse } = event;
      let { vk , db, _ } = util;
      let { uid, userInfo = {} } = filterResponse;
      if (vk.pubfn.isNull(userInfo) && uid) {
        // 如果userInfo为空，则通过uid获取用户信息（kh类型的云对象默认userInfo为空，需要手动获取）
        userInfo = await vk.daoCenter.userDao.findById(uid);
        filterResponse.userInfo = userInfo; // 将用户信息重新赋值给filterResponse.userInfo，方便云对象内执行this.getUserInfo();时无需再查库
      }
      let { role = [] } = userInfo;
      let res = { code : 0, msg : 'ok' };
      // 用户没有shopManage角色则拦截。（拦截后后面的云函数将不会运行，达到了简单的权限控制效果）
      if(role.indexOf("shopManage") === -1){
        return { code : -1, msg : '无权限' };
      }
      return res;
    }
  }
]
```

### 多店版（多商家版本）

即用户A只能操作自己的店铺A，不可以操作店铺B

```javascript
/**
 * 多店版（多商家版本）店铺权限过滤器示例，此仅为示例，实际需要修改成与你系统逻辑相符合才行。
 */
module.exports = [
  {
    id: "shopManage",
    // 这里代表的是哪些云函数需要进行判断权限
    regExp: [
      "^client/business/(.*)kh/",
      "^client/business/(.*)sys/"
    ],
    description: "需要判断用户是否有权限操作此店铺",
    index: 310,// 此处必须>300 因为检测用户是否登录的过滤器的index是200（sys是300，因此为了能通用，建议填大于300的值）（越小越先执行）
    mode:"onActionExecuting", 
    enable:true, // 通过设置enable=false可以关闭该中间件
    main: async function(event) {
      let { data = {}, util, filterResponse } = event;
      let { vk , db, _ } = util;
      let { uid, userInfo = {} } = filterResponse;
      if (vk.pubfn.isNull(userInfo) && uid) {
        // 如果userInfo为空，则通过uid获取用户信息（kh类型的云对象默认userInfo为空，需要手动获取）
        userInfo = await vk.daoCenter.userDao.findById(uid);
        filterResponse.userInfo = userInfo; // 将用户信息重新赋值给filterResponse.userInfo，方便云对象内执行this.getUserInfo();时无需再查库
      }
      let { shop_ids = [] } = userInfo;
      // 此 shop_id 为用户前端传过来的，因此我们需要在此进行判断，此用户是否有权限可操作这个该店铺
      let { shop_id } = data;
      let res = { code : 0, msg : 'ok' };
      if (vk.pubfn.isNull(shop_id)) {
        return { code : -1, msg : `店铺id不能为空` };
      }
      if (shop_ids.indexOf(shop_id) === -1) {
        return { code : -1, msg : `无权限操作店铺【${shop_id}】` };
      }
      return res;
    }
  }
]
```

## 常见问题

过滤器可以有很多种用途，权限判断只是其中一种用途，还可以：

1. 权限校验
2. 统一对对外的接口进行加密、解密
3. 写日志
4. 对请求参数进行过滤处理
5. 对返回给前端的数据进行过滤处理
6. 等等

### 如何在业务函数获得过滤器传给业务函数的数据？

```javascript
'use strict';
module.exports = {
  main: async(event) => {
    // filterResponse 为过滤器传给业务函数的数据
    let { filterResponse } = event;
    /**
     * 自定义过滤器检测了用户token已通过
     * 且通过用户token获得了用户信息和用户当前登录的店铺信息,并传给业务函数
     */
    let res = {
      code: 0,
      msg: '',
      shopInfo : filterResponse.shopInfo,
      userInfo : filterResponse.userInfo 
    };
    return res;
  }
}
```

### 如何在onActionExecuted类型的过滤器中获取云函数返回的数据?

```javascript
/**
 * 自定义过滤器 - 后置
 */
module.exports = [
  {
    id: "xxxx2",
    regExp: "^xxx2/kh", // 正则匹配规则，这个是以^xxx2/kh/开头的云函数会被拦截
    description: "这里是你过滤器2号的描述",
    index: 310,// 此处必须>300 因为检测用户是否登录的过滤器的index是200（sys是300，因此为了能通用，建议填大于300的值）（越小越先执行）
    mode:"onActionExecuted",
    main: async function(event, serviceRes) {
      let { data = {}, util } = event;
      let { vk , db, _ } = util;
      // 这里的 serviceRes 即云函数将要返回给前端的数据
      serviceRes.msg = "被过滤器修改后的值";
      return serviceRes;
    }
  }
]