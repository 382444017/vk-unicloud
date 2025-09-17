# 4. 数据库聚合查询

## 目录
1. 分组查询
2. 分组统计带if(查询每个班级中，数学成绩大于语文成绩的学生人数)
3. 利用分组查询实现以指定字段去重复查询
4. 用户表按注册日期分组统计
5. 查询返回树状结构
6. 树状结构参数详解
7. 聚合操作符说明

## 聚合查询概述

聚合查询是数据库中一种强大的查询方式，它允许我们对数据进行分组、统计、计算等操作。在VK框架中，聚合查询主要通过vk.baseDao.selects方法的groupJson参数和treeProps参数来实现。

聚合查询的主要应用场景包括：
- 数据统计和分析
- 分组计算
- 树状结构数据查询
- 复杂的数据处理和转换

## 分组查询

### 基本分组查询

以下代码作用是：用一条聚合查询语句，查询排行榜前10名用户消费金额和用户信息

```js
res = await vk.baseDao.selects({
  dbName: "你的消费记录表或订单表",
  pageIndex: 1,
  pageSize: 10,
  // 主表where条件
  whereJson: {

  },
  groupJson: {
    _id: "$user_id", // _id是分组id（_id:为固定写法，必填属性），这里指按user_id字段进行分组
    user_id: $.first("$user_id"), // 这里是为了把user_id原样输出
    payment_amount: $.sum("$payment_amount"), // sum求和支付金额
    count: $.sum(1), // count记录条数
  },
  sortArr: [{ name: "payment_amount",type: "desc" }], // 对分组后的结果进行排序
  // 副表列表
  foreignDB: [{
    dbName: "uni-id-users",
    localKey: "user_id",
    foreignKey: "_id",
    as: "userInfo",
    limit: 1
  }],
  // 最后的where，（分组后的筛选）主要用于对分组后的结果再进行筛选 如：筛选金额大于1000才能上榜（这里的lastWhereJson在数据量大的情况下是有性能问题的，（建议主表的where条件中先进行筛选，如只查本季度数据，只要主表过滤完后数据量不大，则没有性能问题。）
  lastWhereJson:{
    payment_amount:_.gt(1000)
  }
});
```

### 分组统计带if

以下代码作用是：以班级分组，统计本期考试每个班级学生成绩中，数学成绩大于语文成绩的人数和物理成绩大于化学成绩的人数

```js
res = await vk.baseDao.selects({
  dbName: "学生学科成绩表",
  pageIndex: 1,
  pageSize: 1000,
  // 主表where条件
  whereJson: {
    no:"20211201", // 本期考试编号
  },
  groupJson: {
    _id: "$班级id字段", // 以 班级id 作为分组字段
    // count1是 数学成绩大于语文成绩的人数
    count1: $.sum($.cond({
      if: $.gte(['$数学成绩字段', '$语文成绩字段']),
      then: 1,
      else: 0,
    })),
    // count2是 物理成绩大于化学成绩的人数
    count2: $.sum($.cond({
      if: $.lt(['$物理成绩字段', '$化学成绩字段']),
      then: 1,
      else: 0,
    })),
  },
  // 数学成绩大于语文成绩的人数越多,越显示在前面.
  sortArr: [{ "name": "count1", "type": "desc" }]
});
```

### 利用分组查询实现以指定字段去重复查询

注意：分组后，显示其他字段需要通过$.first("$key1"),形式

first代表分组后，显示该组第一条数据的该字段值。

```js
res = await vk.baseDao.selects({
  dbName: "表名",
  pageIndex: 1,
  pageSize: 10,
  // 主表where条件（分组前的筛选）
  whereJson: {
    
  },
  groupJson: {
    _id: "$key1", // _id是分组id（_id:为固定写法，必填属性） $ 后面接字段名，如user_id字段进行分组
    key1: $.first("$key1"), // $ 后面接字段名，如把user_id原样输出
    key2: $.first("$key2"), // $ 后面接字段名，如把user_id原样输出
    key3: $.first("$key3"), // $ 后面接字段名，如把user_id原样输出
    key4: $.first("$key4"), // $ 后面接字段名，如把user_id原样输出
    count: $.sum(1), // 代表每组各有多少条记录总量
  },
  sortArr: [{ name: "count",type: "desc" }], // 对分组后的结果进行排序
  // 最后的where，（分组后的筛选）主要用于对分组后的结果再进行筛选 如：筛选金额大于1000才能上榜（这里的lastWhereJson在数据量大的情况下是有性能问题的，（建议主表的where条件中先进行筛选，如只查本季度数据，只要主表过滤完后数据量不大，则没有性能问题。）
  // lastWhereJson:{
  //   count:_.gte(10)
  // }
});
```

还可以多字段组合去重复

```js
res = await vk.baseDao.selects({
  dbName: "表名",
  pageIndex: 1,
  pageSize: 10,
  // 主表where条件（分组前的筛选）
  whereJson: {
    
  },
  groupJson: {
    _id: {
      key1:"$key1",
      key2:"$key2",
    }, // _id是分组id（_id:为固定写法，必填属性）， $ 后面接字段名，如user_id字段进行分组
    key1: $.first("$key1"), // $ 后面接字段名，如把user_id原样输出
    key2: $.first("$key2"), // $ 后面接字段名，如把user_id原样输出
    key3: $.first("$key3"), // $ 后面接字段名，如把user_id原样输出
    key4: $.first("$key4"), // $ 后面接字段名，如把user_id原样输出
    count: $.sum(1), // 代表每组各有多少条记录总量
  },
  sortArr: [{ name: "count",type: "desc" }], // 对分组后的结果进行排序
  // 最后的where，（分组后的筛选）主要用于对分组后的结果再进行筛选 如：筛选金额大于1000才能上榜（这里的lastWhereJson在数据量大的情况下是有性能问题的，（建议主表的where条件中先进行筛选，如只查本季度数据，只要主表过滤完后数据量不大，则没有性能问题。）
  // lastWhereJson:{
  //   count:_.gte(10)
  // }
});
```

### 用户表按注册日期分组统计

以下语句是统计本年度每月注册用户的数量

```js
let { yearStart, yearEnd } = vk.pubfn.getCommonTime();
res = await vk.baseDao.selects({
  dbName: "uni-id-users",
  pageIndex: 1,
  pageSize: 1000,
  whereJson: {
    register_date : _.gte(yearStart).lte(yearEnd), // 只查询本年，不加此条件则查全表
  },
  groupJson: {
    // _id是分组id， 将$register_date转为date，然后将date转为需要分组的date格式，直接分组
    _id: $.dateToString({
      date: $.add([new Date(0), "$register_date"]),
      format: "%Y-%m",
      timezone: "+08:00", // +08:00 代表北京时间（东八区）
      onNull: null, // 可选。当 <日期表达式> 返回空或者不存在的时候，会返回此表达式指明的值。
    }),
    count: $.sum(1),
  },
  sortArr: [{ "name": "_id", "type": "desc" }]
});
```

下面是格式说明符的详细说明：

常用

| 说明符 | 描述 | 合法值 |
|--------|------|--------|
| %Y | 年份（4位数，0填充） | 0000 - 9999 |
| %m | 月份（2位数，0填充） | 01 - 12 |
| %d | 月份的日期（2位数，0填充） | 01 - 31 |
| %H | 小时（2位数，0填充，24小时制） | 00 - 23 |
| %M | 分钟（2位数，0填充） | 00 - 59 |
| %S | 秒（2位数，0填充） | 00 - 60 |
| %L | 毫秒（3位数，0填充） | 000 - 999 |
| %w | 星期几 | 1 - 7 |
| %j | 一年中的一天（3位数，0填充） | 001 - 366 |
| %U | 一年中的一周（2位数，0填充） | 00 - 53 |

不常用

| 说明符 | 描述 | 合法值 |
|--------|------|--------|
| %Z | 以分钟为单位，与 UTC 的时区偏移量 | +/-mmm |
| %z | 与 UTC 的时区偏移量 | +/-[hh][mm] |
| %G | ISO 8601 格式的年份 | 0000 - 9999 |
| %u | ISO 8601 格式的星期几 | 1 - 7 |
| %V | ISO 8601 格式的一年中的一周 | 1 - 53 |
| %% | 百分号作为字符 | % |

## 查询返回树状结构

### 树状结构

以下语句效果是：查询已启用的菜单，并自动将子菜单合并到父菜单的children字段下

```js
res = await vk.baseDao.selects({
  dbName: "opendb-admin-menus",
  pageIndex: 1,
  pageSize: 500,
  whereJson:{
    enable: true,
    parent_id: _.in([null, ""]),
    menu_id: _.exists(true)
  },
  sortArr: [{ name: "sort", type: "asc" }], // 主节点的排序规则
  // 树状结构参数
  treeProps: {
    id: "menu_id",          // 唯一标识字段，默认为 _id
    parent_id: "parent_id", // 父级标识字段，默认为 parent_id
    children: "children",   // 自定义返回的下级字段名，默认为 children
    level: 3,               // 查询返回的树的最大层级。超过设定层级的节点不会返回。默认10级，最大15，最小1
    limit: 500,             // 每一级最大返回的数据。
    sortArr: [{ name: "sort", type: "asc" }], // 所有子节点的排序规则
    whereJson: {
      enable: true
    }
  }
});
```

### 树状结构+or查询条件

注意：

treeProps 内的 whereJson 若需要用到 or 和 and 则

_.or 需写成 $.or

_.and 需写成 $.and

同时不支持流式语法，只支持如下写法。

```js
let selectsRes = await vk.baseDao.selects({
  dbName: "opendb-admin-menus",
  pageIndex: 1,
  pageSize: 500,
  whereJson:{
    enable: true,
    parent_id: _.in([null, ""]),
    menu_id: _.exists(true)
  },
  sortArr: [{ name: "sort", type: "asc" }], // 主节点的排序规则
  // 树状结构参数
  treeProps: {
    id: "menu_id",          // 唯一标识字段，默认为 _id
    parent_id: "parent_id", // 父级标识字段，默认为 parent_id
    children: "children",   // 自定义返回的下级字段名，默认为 children
    level: 3,               // 查询返回的树的最大层级。超过设定层级的节点不会返回。默认10级，最大15，最小1
    limit: 500,             // 每一级最大返回的数据。
    sortArr: [{ name: "sort", type: "asc" }], // 所有子节点的排序规则
    whereJson: $.or([
      {
        menu_id: _.eq("sys-user-manage")
      },
      {
        menu_id: _.exists(false)
      }
    ])
  }
});
```

### 树状结构+and+or查询条件

注意：

treeProps 内的 whereJson 若需要用到 or 和 and 则

_.or 需写成 $.or

_.and 需写成 $.and

同时不支持流式语法，只支持如下写法。

```js
let selectsRes = await vk.baseDao.selects({
  dbName: "opendb-admin-menus",
  pageIndex: 1,
  pageSize: 500,
  whereJson:{
    enable: true,
    parent_id: _.in([null, ""]),
    menu_id: _.exists(true)
  },
  sortArr: [{ name: "sort", type: "asc" }], // 主节点的排序规则
  // 树状结构参数
  treeProps: {
    id: "menu_id",          // 唯一标识字段，默认为 _id
    parent_id: "parent_id", // 父级标识字段，默认为 parent_id
    children: "children",   // 自定义返回的下级字段名，默认为 children
    level: 3,               // 查询返回的树的最大层级。超过设定层级的节点不会返回。默认10级，最大15，最小1
    limit: 500,             // 每一级最大返回的数据。
    sortArr: [{ name: "sort", type: "asc" }], // 所有子节点的排序规则
    whereJson: $.and([
      {
        menu_id: _.eq("sys-user-manage")
      },
      $.or([
        {
          menu_id: _.eq("sys-user-manage2")
        },
        {
          menu_id: _.exists(true)
        }
      ])
    ])
  }
});
```

### 树状结构+连表

连表时，foreignDB 属性只需写在主表下，无需写在 treeProps 内。（子表会继承主表的 foreignDB 属性)

```js
res = await vk.baseDao.selects({
  dbName: "opendb-admin-menus",
  pageIndex: 1,
  pageSize: 500,
  whereJson:{
    enable: true,
    parent_id: _.in([null, ""]),
    menu_id: _.exists(true)
  },
  sortArr: [{ name: "sort", type: "asc" }], // 主节点的排序规则
  // 树状结构参数
  treeProps: {
    id: "menu_id",          // 唯一标识字段，默认为 _id
    parent_id: "parent_id", // 父级标识字段，默认为 parent_id
    children: "children",   // 自定义返回的下级字段名，默认为 children
    level: 3,               // 查询返回的树的最大层级。超过设定层级的节点不会返回。默认10级，最大15，最小1
    limit: 500,             // 每一级最大返回的数据。
    sortArr: [{ name: "sort", type: "asc" }], // 所有子节点的排序规则
    whereJson: {
      enable: true
    }
  },
  // 副表列表
  foreignDB: [
    {
      dbName: "副表1表名",
      localKey: "主表外键名",
      foreignKey: "副表1外键名",
      as: "副表1as字段",
      limit: 1
    },
    {
      dbName: "副表2表名",
      localKey: "主表外键名",
      foreignKey: "副表2外键名",
      as: "副表2as字段",
      limit: 1
    }
  ]
});
```

下方的代码效果是查询用户列表，并自动带出用户推广的用户列表（组成树状结构，支持带出多层）

```js
res = await vk.baseDao.selects({
  dbName: "uni-id-users",
  pageIndex: 1,
  pageSize: 1000,
  whereJson:{
    inviter_uid:  _.exists(false),
  },
  // 树状结构参数
  treeProps: {
    id: "_id",
    parent_id: $.arrayElemAt(['$inviter_uid', 0]),
    children: "children",
    level: 2,
    limit: 1000,
    whereJson: {
      
    }
  }
});
```

## 聚合操作符说明

注意：文档中出现的 $ 在云函数若不可用，则可写成 _.$
以下是 _ 和 $ 变量实际代表的含义

```js
const db = uniCloud.database(); // 全局数据库引用
const _ = db.command; // 数据库操作符
const $ = _.aggregate; // 聚合查询操作符
```

常用的聚合操作符包括：

- $.sum() - 求和
- $.avg() - 平均值
- $.min() - 最小值
- $.max() - 最大值
- $.first() - 第一个值
- $.last() - 最后一个值
- $.push() - 将值推入数组
- $.addToSet() - 添加到集合
- $.cond() - 条件判断
- $.gte() - 大于等于
- $.lte() - 小于等于
- $.gt() - 大于
- $.lt() - 小于
- $.eq() - 等于
- $.dateToString() - 日期格式化