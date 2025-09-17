# 2. 万能连表查询(selects)

## 目录
1. 1张主表多张副表

2. 1张主表多张副表，同时副表也有多张副表

3. 需要查询同时满足主表和副表的条件，即副表不满足条件，则主表记录也不获取

4. 连表查询时需要按照离给定点从近到远输出（地理位置经纬度）

5. 连表查询，主表外键是数组（id数组），查询出数组内的每个记录详情

6. 连表查询，副表外键是数组（只要数组内任意元素与主表外键匹配即可）

7. 连表查询，并获取满足条件的第一条记录，以对象形式返回

8. 连表查询，使用数组下标对应的值进行连表（如连表查推荐人信息）

9. 连表查询，通过副表字段排序

## vk.baseDao.selects（万能连表查询）
接口名

云开发数据库连表查询最大支持查询1000条数据，即pageSize最大值为1000

特别注意：此分页功能会随着 pageIndex 的值越大，效率越低（传统mysql也有此问题），pageIndex * pageSize 的值最好不要超过 300万（如每页显示10条，则建议最多让用户查看到第30万页）

vk-admin 的 万能表格 针对分页进行了优化，有多种方案可选 传送门

调用示例

```js
let res = await vk.baseDao.selects({
  dbName: "表名",
  getCount: false,
  pageIndex: 1,
  pageSize: 20,
  // 主表where条件
  whereJson: {
    
  },
  // 主表字段显示规则
  fieldJson: {},
  // 主表排序规则
  sortArr: [{ name:"_id", type:"desc" }],
  // 副表列表
  foreignDB: [
    {
      dbName: "副表表名",
      localKey: "主表外键名",
      foreignKey: "副表外键名",
      as: "副表as字段",
      limit: 1
    }
  ]
});
```

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 主表表名 |
| whereJson | Object | 否 | 主表 where 条件 |
| pageIndex | Number | 否 | 第几页 默认 1 |
| pageSize | Number | 否 | 每页显示数量 默认 10 |
| getOne | Boolean | 否 | 是否只返回第一条数据。默认 false |
| getMain | Boolean | 否 | 是否只返回rows数组。默认 false |
| getCount | Boolean | 否 | 是否返回满足条件的记录总数。默认 false 详情 |
| hasMore | Boolean | 否 | 是否返回精确的是否还有下一页。默认 false，若已设置 getCount 为 true，则无需设置此参数 详情 |
| groupJson | Object | 否 | 主表分组规则（副表不支持分组） |
| sortArr | Array | 否 | 主表排序规则 |
| foreignDB | Array | 否 | 连表规则 详情 |
| lastWhereJson | Object | 否 | 连表后的查询条件，有性能问题，慎用 详情 |
| lastSortArr | Array | 否 | 连表后的排序条件，有性能问题，慎用 详情 |
| addFields | Object | 否 | 添加自定义字段规则（用来添加虚拟字段，如增加一个通过某种计算得出的字段） |
| fieldJson | Object | 否 | 字段显示规则（用来控制只显示哪些字段或不显示哪些字段） |
| db | DB | 否 | 指定数据库实例 const db = uniCloud.database(); |
| debug | Boolean | 否 | 是否返回调试需要的参数，目前设置为true会返回数据库执行耗时 默认 false |

返回值

| 参数名 | 类型 | 说明 |
|--------|------|------|
| rows | Array | 数据列表，没有数据时返回空数组 |
| total | Number | 满足条件的记录总数（如果返回的getCount为false，则 total = (pageIndex - 1) * pageSize + rows.length） |
| hasMore | Boolean | 分页参数，true 还有下一页 false 没有下一页 详情 |
| pagination | Object | 当前分页参数 |
| getCount | Boolean | 是否有执行过getCount，true：有，false：无 |

pagination对象的属性

| 参数名 | 类型 | 说明 |
|--------|------|------|
| pageIndex | Number | 当前分页的页码 |
| pageSize | Number | 每页显示的大小 |

## vk.baseDao.getTableData的连表

注意：vk.baseDao.getTableData 和 vk.baseDao.selects 的连表用法完全一致，也支持 foreignDB 属性。在 vk-admin 项目中比较常用 详细介绍

getTableData连表示例：

```js
res = await vk.baseDao.getTableData({
  dbName: "主表表名",
  data,
  // 副表列表
  foreignDB:[
    {
      dbName:"副表表名",
      localKey:"主表外键名",
      foreignKey:"副表外键名",
      as:"副表as字段",
      limit:1
    }
  ]
});
```

## foreignDB（连表规则）

foreignDB是一个数组，数组内每一个对象代表一个连表规则

代码示例

```js
foreignDB:[
  {
    dbName:"副表表名",
    localKey:"主表外键名",
    foreignKey:"副表外键名",
    as:"副表as字段",
    limit:1, // limit=1则以对象形式返回
  },
  {
    dbName:"副表表名",
    localKey:"主表外键名",
    foreignKey:"副表外键名",
    as:"副表as字段",
    limit:10, // limit>1则以数组形式返回
  },
  {
    dbName:"副表表名",
    localKey:"主表外键名",
    foreignKey:"副表外键名",
    as:"副表as字段",
    limit:1,
    // foreignDB能再嵌套foreignDB，等于副表的副表（因云数据库限制，最多可以嵌套15层）
    foreignDB:[
      {
        dbName:"副表表名",
        localKey:"主表外键名",
        foreignKey:"副表外键名",
        as:"副表as字段",
        limit:1,
      }
    ]
  }
]
```

foreignDB（参数说明）

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 副表表名 |
| localKey | String | 是 | 主表外键名 |
| foreignKey | String | 是 | 副表外键名 |
| as | String | 是 | 副表连表结果的别名 |
| whereJson | Object | 否 | 副表 where 条件 |
| sortArr | Array | 否 | 副表排序规则 |
| limit | Number | 否 | 副表限制取多少条数据，当limit = 1时，以对象形式返回，否则以数组形式返回 |
| foreignDB | Array | 否 | 副表连表规则 |
| addFields | Object | 否 | 副表添加自定义字段规则 |
| fieldJson | Object | 否 | 副表字段显示规则 |

## getCount

vk.baseDao.selects 默认 getCount 为 false

vk.baseDao.getTableData 默认 getCount 为 true

设置为 true 后，会同时查询满足条件的总记录条数，并返回真实的 total，若为 false，则 total = rows.length

特别注意：

1. 设置为 true 会多一次 count 请求，因此，如果仅为了获取 rows，则应该设置 false
2. 带 whereJson 条件的 count 请求，其中 whereJson 能匹配的记录数越多，性能越差，甚至会超时报错
3. lastWhereJson 如果和 getCount: true 一起使用，性能较差，甚至会超时报错

因此大表（数据很多的表）在进行带条件的 count 请求时，除了设置必要的索引外，还应该限制查询条件范围，比如只能查本月、本季度、本年（确保满足条件的数据不会太多），而如果 count 请求不带查询条件，则没有性能问题。

友情提示：

设置 debug: true 可以返回 count 和 rows 查询的数据库耗时（单位ms）

注意：测试耗时必须连接云端云函数，并多测试几次，取个稳定值（本地云函数由于是外网访问数据库，很慢，不准）

### rows 的耗时

| 耗时 | 说明 |
|------|------|
| [0,100ms) | 正常 |
| [100ms,300ms) | 检查索引，优化查询条件 |
| 300ms以上 | 检查索引，优化查询条件，必要时修改业务实现逻辑 |

### count 的耗时

| 耗时 | 说明 |
|------|------|
| [0,100ms) | 正常 |
| [100ms,300ms) | 可选优化 |
| [300ms,1000ms) | 检查索引，优化查询条件 |
| 1000ms以上 | 检查索引，优化查询条件，必要时修改业务实现逻辑 |

## hasMore

查询结果会同时返回 hasMore，代表是否还有下一页

若请求参数 hasMore 不传，默认情况下，返回值 hasMore 的计算方式是 hasMore = rows.length >= pageSize ? true : false

在上面这种计算方式下，会出现返回值 hasMore 为 true，但请求下一页其实已经没有数据的情况出现

为了能够返回精确的 hasMore，需要在查询时，多传一个请求参数 hasMore: true

当请求参数 hasMore 设置为 true 时，框架会返回精确的 hasMore，其原理是通过多查一条数据实现的，即本次如果要查10条数据，则框架实际查了11条数据（接口返回的数据rows还是10条），如果存在11条数据，代表一定还有下一页，否则代表没有下一页了

若请求参数 getCount 已经设置为 true 时，会忽略参数 hasMore，因为此时的返回值 total 是满足查询条件的总数量（数据库多执行了一次count请求），通过 最大页数 = Math.ceil(total / pageSize) 已经可以求出最大页数，所以此时返回值 hasMore 的计算方式是 hasMore = pageIndex < Math.ceil(total / pageSize) ? true : false

## lastWhereJson

主要用于对连表或分组后的结果再进行筛选，但 lastWhereJson 在数据量大的情况下是有性能问题的，建议先在主表的 where 条件中进行筛选，如只查本季度数据

劣势：在数据量大的情况下是有性能问题，特别是 getCount:true 的情况下，lastWhereJson 有严重的性能问题

场景示例：分组查询后再筛选

## lastSortArr

主要用于连表后，再通过副表字段进行排序，但 lastSortArr 在数据量大的情况下是有性能问题的，建议先在主表的 where 条件中进行筛选，如只查本季度数据

劣势：在数据量大的情况下是有性能问题

场景示例：连表查询，通过副表字段排序

## addFields和fieldJson的组合使用

若觉得副表的层级过深，则可以通过（addFields和fieldJson的组合使用）将副表的字段拆分到顶层显示，具体代码如下

``js
res = await vk.baseDao.selects({
  dbName: "表名",
  getCount: false,
  pageIndex: 1,
  pageSize: 20,
  // 主表where条件
  whereJson: {
    
  },
  addFields: { 
    a: "$副表as字段.a", // 以$为前缀，将副表字段的a字段赋值给主表的a字段（不会改变数据库实际数据，只是改变本次显示，如果主表已有a字段，则会被副表的覆盖）
    b: "$副表as字段.b", // 以$为前缀，将副表字段的b字段赋值给主表的b字段（不会改变数据库实际数据，只是改变本次显示，如果主表已有b字段，则会被副表的覆盖）
  },
  fieldJson: {
    副表as字段: false, // 不显示副表的字段
  },
  // 主表排序规则
  sortArr: [{ name:"_id", type:"desc" }],
  // 副表列表
  foreignDB: [
    {
      dbName: "副表表名",
      localKey: "主表外键名",
      foreignKey: "副表外键名",
      as: "副表as字段",
      limit: 1
    }
  ]
});
```

## 场景示例

### 场景1：1张主表多张副表

主表：uni-id-users （用户表）

副表：order （用户订单表）

副表：vip （会员卡表）

以下代码作用是：用一条聚合查询语句，查询前10个用户的信息，并查询他们的最新10个订单记录表，再查询他们的VIP信息

```js
res = await vk.baseDao.selects({
  dbName: "uni-id-users",// 主表名
  getCount: false, // 是否需要同时查询满足条件的记录总数量，默认false
  getMain:false,// 是否只返回rows数据，默认false
  pageIndex: 1, // 查询第几页
  pageSize: 10, // 每页多少条数据
  // 主表where条件
  whereJson: {

  },
  // 主表字段显示规则
  fieldJson: {
    token: false,
    password: false,
  },
  // 主表排序规则
  sortArr: [{ "name": "_id","type": "desc" }],
  // 副表列表
  foreignDB: [
    {
      dbName: "order", // 副表名
      localKey:"_id", // 主表外键字段名
      foreignKey: "user_id", // 副表外键字段名
      as: "orderList",
      limit: 10, // 当limit = 1时，以对象形式返回，否则以数组形式返回
      // 副表where条件
      whereJson: {},
      // 副表字段显示规则
      fieldJson: {},
      // 副表排序规则
      sortArr: [{ "name": "time","type": "desc" }],
    },
    {
      dbName: "vip", // 副表名
      localKey:"_id", // 主表外键字段名
      foreignKey: "user_id", // 副表外键字段名
      as: "vipInfo",
      limit: 1, // 当limit = 1时，以对象形式返回，否则以数组形式返回
    }
  ]
});
```

### 场景2：1张主表多张副表，同时副表也有多张副表

主表：opendb-mall-comments （评论表）

副表：uni-id-users （用户表）

副表的副表：opendb-mall-orders（用户订单表）

以下代码作用是：用一条聚合查询语句，查询前10个评论信息，并查询评论的发布者信息，再查询他们各自最新的3个订单信息

```js
res = await vk.baseDao.selects({
  dbName:"opendb-mall-comments",
  pageIndex: 1,
  pageSize: 10,
  // 副表列表
  foreignDB:[
    {
      dbName:"uni-id-users",
      localKey:"user_id",
      foreignKey:"_id",
      as:"userInfo",
      limit:1,
      foreignDB:[
        {
          dbName:"opendb-mall-orders",
          localKey:"_id",
          foreignKey:"user_id",
          as:"orderList",
          limit:3,
          sortArr:[{ "name":"_add_time", "type":"desc" }],
        },
      ]
    }
  ]
});
```

### 场景3：需要查询同时满足主表和副表的条件，即副表不满足条件，则主表记录也不获取

主表：opendb-mall-comments （评论表）

副表：uni-id-users （用户表）

以下代码作用是：用一条聚合查询语句，查询前10条女性用户的评论信息

```js
res = await vk.baseDao.selects({
  dbName:"opendb-mall-comments",
  pageIndex: 1,
  pageSize: 10,
  // 副表列表
  foreignDB:[
    {
      dbName:"uni-id-users",
      localKey:"user_id",
      foreignKey:"_id",
      as:"userInfo",
      limit:1
    }
  ],
  lastWhereJson:{
    "userInfo.gender" : 2
  }
});
```

### 场景4：连表查询时需要按照离给定点从近到远输出（地理位置经纬度）

主表：vk-test （地理位置测试表）

副表：uni-id-users （用户表）

以下代码作用是：用一条聚合查询语句，查询前10条离我最近的记录

```js
res = await vk.baseDao.selects({
  dbName: "vk-test",
  getCount: true,
  pageIndex: 1,
  pageSize: 10,
  // 主表where条件
  whereJson: {
    location: _.geoNear({
      geometry: new db.Geo.Point(120.12792, 30.228932),  // 点的地理位置
      maxDistance: 4000,         // 选填，最大距离，米为单位
      minDistance: 0,            // 选填，最小距离，米为单位
      distanceMultiplier: 1,     // 返回时在距离上乘以该数字 1代表米 100 代表厘米 0.001 代表千米
      distanceField: "distance", // 输出的每个记录中 distance 即是与给定点的距离
    })
  },
  // 副表列表
  foreignDB: [{
    dbName: "uni-id-users",
    localKey: "user_id",
    foreignKey: "_id",
    as: "userInfo",
    limit: 1
  }]
});
```

提示:使用地理位置经纬度查询前，数据表内需要事先准备好地理位置经纬度数据，例如：

```js
  let longitude = 120.12792 
  let latitude = 30.228932
  res.id = await vk.baseDao.add({
    dbName:"vk-test",
    dataJson:{
      "location":new db.Geo.Point(longitude, latitude) // 使用new db.Geo.Point(longitude，latitude)方法写入
    }
  });
```

### 场景5：连表查询，主表外键是数组（id数组），查询出数组内的每个记录详情

主表：uni-id-users （用户表）

副表：uni-id-roles （角色表）

以下代码作用是：用一条聚合查询语句，查询前10个用户信息（同时带出角色列表roleList）

```js
res = await vk.baseDao.selects({
  dbName: "uni-id-users",
  getCount: true,
  pageIndex: 1,
  pageSize: 10,
  // 主表where条件
  whereJson: {

  },
  // 副表列表
  foreignDB: [{
    dbName: "uni-id-roles",
    localKey: "role",
    localKeyType: "array",
    foreignKey: "role_id",
    as: "roleList",
    limit: 1000
  }]
});
```

### 场景6：连表查询，副表外键是数组（只要数组内任意元素与主表外键匹配即可）

主表：uni-id-roles （角色表）

副表：uni-id-users （用户表）

以下代码作用是：用一条聚合查询语句，查询前10个角色信息（同时带出拥有该角色的用户列表）

```js
res = await vk.baseDao.selects({
  dbName:"uni-id-roles",
  getCount:false,
  pageIndex:1,
  pageSize:10,
  // 主表where条件
  whereJson:{

  },
  // 副表列表
  foreignDB:[
    {
      dbName:"uni-id-users",
      localKey:"role_id",
      foreignKey:"role",
      foreignKeyType:"array",
      as:"userInfo",
      limit: 1000
    }
  ]
});
```

### 场景7：分组查询

主表：你的消费记录表或订单表

副表：uni-id-users （用户表）

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

### 场景8：分组统计带if

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

### 场景9：连表查询，并获取满足条件的第一条记录，以对象形式返回

```js
let info = await vk.baseDao.selects({
  dbName: "用户表",
  getOne: true,
  getMain: true,
  // 主表where条件
  whereJson: {
    _id: "001"
  },
  foreignDB: [
    {
      dbName: "vip", // 副表名
      localKey:"vip_id", // 主表外键字段名
      foreignKey: "user_id", // 副表外键字段名
      as: "vipInfo",
      limit: 1, // 当limit = 1时，以对象形式返回，否则以数组形式返回
    }
  ]
});
```

参数解析

getOne:true 代表只获取满足条件的第一条数据，并以对象形式返回。
getMain:true 代表返回的数据不包含code:0
即原本selects返回的数据格式是这样的。

```json
{
  "code": 0,
  "msg": "查询成功",
  "total": 1,
  "hasMore": false,
  "rows": [{"_id":"001","name":"xxx"}]
}
```

getOne:true 后 返回的数据格式是这样的（rows从数组变成了对象）

```json
{
  "code": 0,
  "msg": "查询成功",
  "total": 1,
  "hasMore": false,
  "rows": {"_id":"001","name":"xxx"}
}
```

getMain:true 后 返回的数据格式是这样的（直接返回rows的值）

```json
[{"_id":"001","name":"xxx"}]
```

getMain:true + getOne:true 后 返回的数据格式是这样的（等于findByWhereJson的效果，但具有连表功能）

```json
{"_id":"001","name":"xxx"}
```

### 场景10：利用分组查询实现以指定字段去重复查询

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

### 场景11：使用数组下标对应的值进行连表

核心

第一个

```js
localKey: $.arrayElemAt(['$inviter_uid', 0]),
```

最后一个

```js
localKey: $.arrayElemAt(['$inviter_uid', -1]),
```

注意

```js
$ = db.command.aggregate
```

下方的代码效果是查询并连表带出用户的第一级分享人信息

```js
res = await vk.baseDao.selects({
  dbName: "uni-id-users",
  getCount: false,
  pageIndex: 1,
  pageSize: 5,
  fieldJson:{ token: false, password: false },
  // 副表列表
  foreignDB: [{
    dbName: "uni-id-users",
    localKey: $.arrayElemAt(['$inviter_uid', 0]),
    foreignKey: "_id",
    as: "inviterUserInfo",
    limit: 1,
    fieldJson:{ token: false, password: false },
  }]
});
```

下方的代码效果是查询并连表带出用户的最后一级分享人信息

```js
res = await vk.baseDao.selects({
  dbName: "uni-id-users",
  getCount: false,
  pageIndex: 1,
  pageSize: 5,
  fieldJson:{ token: false, password: false },
  // 副表列表
  foreignDB: [{
    dbName: "uni-id-users",
    localKey: $.arrayElemAt(['$inviter_uid', -1]),
    foreignKey: "_id",
    as: "lastInviterUserInfo",
    limit: 1,
    fieldJson:{ token: false, password: false },
  }]
});
```

下方的代码效果是查询并连表带出用户的直推用户列表

```js
res = await vk.baseDao.selects({
  dbName: "uni-id-users",
  pageIndex: 1,
  pageSize: 1000,
  whereJson:{
    inviter_uid:  _.exists(false),
  },
  fieldJson:{ token: false, password: false },
  // 副表列表
  foreignDB: [{
    dbName: "uni-id-users",
    localKey: "_id",
    foreignKey: $.arrayElemAt(['$inviter_uid', 0]),
    as: "shareUserList",
    limit: 1000,
    fieldJson:{ token: false, password: false },
  }]
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

### 场景12：通过副表字段排序

主表：opendb-mall-comments （评论表）

副表：uni-id-users （用户表）

以下代码作用是：用一条聚合查询语句，查询前10条用户的评论信息，且优先展示新用户

```js
res = await vk.baseDao.selects({
  dbName:"opendb-mall-comments",
  pageIndex: 1,
  pageSize: 10,
  // 副表列表
  foreignDB:[
    {
      dbName:"uni-id-users",
      localKey:"user_id",
      foreignKey:"_id",
      as:"userInfo",
      limit:1
    }
  ],
  lastSortArr: [{ name: "userInfo._add_time",type: "desc" }],
});
```

### 场景13：用户表按注册日期分组统计

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

注意：文档中出现的 $ 在云函数若不可用，则可写成 _.$
以下是 _ 和 $ 变量实际代表的含义

```js
var db = uniCloud.database(); // 全局数据库引用
var _ = db.command; // 数据库操作符
var $ = _.aggregate; // 聚合查询操作符
```

## 连表返回单条记录

主表：uni-id-users （用户表）
副表：order （用户订单表）
副表：vip （会员卡表）
以下代码作用是：用一条聚合查询语句，查询前用户ID为001的用户信息，并查询他们的最新10个订单记录表，再查询他们的VIP信息
通过 getOne:true 设置只返回第一条数据，且是对象形式返回

通过 getMain:true 直接返回数据库查询到的数据（不带code,rows等参数）

```js
let info = await vk.baseDao.selects({
  dbName: "uni-id-users",
  getOne:true, // 只返回第一条数据
  getMain:true, // 直接返回数据库查询到的数据（不带code,rows等参数）
  // 主表where条件
  whereJson: {
    _id: "001"
  },
  // 主表字段显示规则
  fieldJson: {
    token: false,
    password: false,
  },
  // 副表列表
  foreignDB: [
    {
      dbName: "order",
      localKey:"_id",
      foreignKey: "user_id",
      as: "orderList",
      limit: 10,
      // 副表where条件
      whereJson: {},
      // 副表字段显示规则
      fieldJson: {},
      // 副表排序规则
      sortArr: [{ "name": "time","type": "desc" }],
    },
    {
      dbName: "vip",
      localKey:"_id",
      foreignKey: "user_id",
      as: "vipInfo",
      limit: 1, // 当limit = 1时，以对象形式返回，否则以数组形式返回
    }
  ]
});
```

## 查询返回树状结构

### 代码示例

#### 树状结构

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

#### 树状结构+or查询条件

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

#### 树状结构+and+or查询条件

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

#### 树状结构+连表

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

注意：文档中出现的 $ 在云函数若不可用，则可写成 _.$
以下是 _ 和 $ 变量实际代表的含义

```js
const db = uniCloud.database(); // 全局数据库引用
const _ = db.command; // 数据库操作符
const $ = _.aggregate; // 聚合查询操作符
```