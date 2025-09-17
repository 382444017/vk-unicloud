# 3. 查询操作符详解

## 目录
- and、or、in、nin、neq用法
- 针对同一个字段的and和or
- 跨字段的and和or
- 多字段模糊搜索or
- in （包含其中）nin（都不包含）
- 大于小于
- 获取今日注册的用户列表
- 数组第一个字段是开始时间，第二个字段是结束时间，判断当前时间是否在范围内
- 控制只获取部分字段
- 排序
- 模糊查询
- 如判断字段是否存在
- 返回字段别名
- 查出表中字段a等于字段b的数据
- 如何查询数组字段内包含某个值的数据
- 分组count
- 用户表按注册日期分组统计
- 删除某个字段
- vk.baseDao.findById和vk.baseDao.findByWhereJson如何连表
- 如何更改字段名
- 字段如何自增

## and、or、in、nin、neq的用法

### 针对同一个字段的and和or

```js
// num >=0 and num <= 10
// 流式简写法
num : _.gte(0).lte(10)
// 流式完整写法
num : _.gte(0).and(_.lte(10))
// 前置写法
num : _.and(_.gte(0), _.lte(10))

// num <=0 or num >= 10
// 前置写法
num : _.or(_.lte(0), _.gte(10))
// 流式写法
num : _.lte(0).or(_.gte(10))

// num <=0 or (num > 10 and num <20)
// 流式写法(复式)
num : _.lte(0).or(_.gt(10).and(_.lt(20)))
```

### 跨字段的and和or

#### and

```js
// num >50 and name = 'test'
whereJson: _.and([
  {
    num: _.gt(50)
  },
  {
    name: 'test'
  }
])
```

#### or

```js
// num >50 or name = 'test'
whereJson: _.or([
  {
    num: _.gt(50)
  },
  {
    name: 'test'
  }
])
```

#### and嵌套or

```js
// num >50 and (name = 'test' or sex = 1)
whereJson: _.and([
  {
    num: _.gt(50)
  },
  _.or([
    {
      name: 'test'
    },
    {
      sex: 1
    }
  ])
])
```

#### or嵌套and

```js
// num >50 or (name = 'test' and sex = 1)
whereJson: _.or([
  {
    num: _.gt(50)
  },
  _.and([
    {
      name: 'test'
    },
    {
      sex: 1
    }
  ])
])
```

#### 多个嵌套

```js
// num >50 and (name = 'test' or sex = 1) and (name2 = 'test' or sex2 = 1)
whereJson: _.and([
  {
    num: _.gt(50)
  },
  _.or([
    {
      name: 'test'
    },
    {
      sex: 1
    }
  ]),
  _.or([
    {
      name2: 'test'
    },
    {
      sex2: 1
    }
  ])
])
```

#### 多层嵌套

```js
// num >50 and (num>50 and (name = 'test' or sex = 1)) and (name2 = 'test' or sex2 = 1)
whereJson: _.and([
  {
    num: _.gt(50)
  },
  _.and([
    {
      num: _.gt(50)
    },
    _.or([
      {
        name: 'test'
      },
      {
        sex: 1
      }
    ])
  ]),
  _.or([
    {
      name2: 'test'
    },
    {
      sex2: 1
    }
  ])
])
```

### 多字段模糊搜索or

一个搜索value对应多个字段模糊搜索

```js
let whereJson = {};
let andArr = [];
if (searchvalue) {
  // 查询包含searchvalue的数据
  try {
    let regExp = new RegExp(searchvalue);
    let orObj = _.or([
      {
        "username": regExp
      },
      {
        "nickname": regExp
      },
      {
        "mobile": regExp
      },
      {
        "_id": searchvalue
      }
    ]);
    andArr.push(orObj);
  } catch (err) {
    return { code: -1, msg: '请输入合法的查询内容' };
  }
}
if (andArr.length > 0) {
  whereJson = _.and(andArr);
}
```

### in （包含其中） nin（都不包含）

```js
// 等价于 _id = "1" or _id = "2" or _id =  "3"
_id : _.in(["1","2","3"])
// 等价于 _id != "1" and _id !== "2" and _id != "3"
_id : _.nin(["1","2","3"])
```

### neq (不等于）

```js
// num != 1
num : _.neq(1)
```

### 大于小于

#### gt(大于）

```js
// num > 0
num : _.gt(0)
```

#### gte(大于等于）

```js
// num >= 0
num : _.gte(0)
```

#### lt(小于）

```js
// num < 0
num : _.lt(0)
```

#### lte(小于等于）

```js
// num <= 0
num : _.lte(0)
```

#### 组合使用

```js
// num >=0 and num <= 10
num : _.gte(0).lte(10) 
```

### 如何获取今日注册的用户列表

```js
let { todayStart, todayEnd } = vk.pubfn.getCommonTime();
let selectRes = await vk.baseDao.select({
  dbName: "uni-id-users",
  pageIndex: 1,
  pageSize: 20,
  whereJson: {
    register_date: _.gte(todayStart).lte(todayEnd)  
  }
});
```

### 数组第一个字段是开始时间-第二个字段是结束时间-判断当前时间是否在范围内

```js
let time = Date.now();
let selectRes = await vk.baseDao.select({
  dbName: "xxxx表",
  pageIndex: 1,
  pageSize: 20,
  whereJson: {
    "arr.0": _.lte(time),
    "arr.1": _.gte(time)
  }
});
```

### 如何控制只获取部分字段

#### 只取 username 和 nickname

```js
let selectRes = await vk.baseDao.select({
  dbName: "uni-id-users",
  pageIndex: 1,
  pageSize: 20,
  fieldJson: {
    username: true, 
    nickname: true
  }
});
```

#### 除了 token 和 password 其他都取

```js
let selectRes = await vk.baseDao.select({
  dbName: "uni-id-users",
  pageIndex: 1,
  pageSize: 20,
  fieldJson: {
    token: false, 
    password: false 
  }
});
```

### 排序

#### 升序

```js
// 按注册时间升序
let selectRes = await vk.baseDao.select({
  dbName: "uni-id-users",
  pageIndex: 1,
  pageSize: 20,
  sortArr: [{ "name":"register_date", "type":"asc" }]
});
```

#### 降序

```js
// 按注册时间降序
let selectRes = await vk.baseDao.select({
  dbName: "uni-id-users",
  pageIndex: 1,
  pageSize: 20,
  sortArr: [{ "name":"register_date", "type":"desc" }]
});
```

#### 多个排序条件

```js
// 按注册时间降序，时间相同者按_id 降序
let selectRes = await vk.baseDao.select({
  dbName: "uni-id-users",
  pageIndex: 1,
  pageSize: 20,
  sortArr: [
    { "name":"register_date", "type":"desc" },
    { "name":"_id", "type":"desc" }
  ]
});
```

### 模糊查询

#### 以xxxx开头

```js
let selectRes = await vk.baseDao.select({
  dbName: "uni-id-users",
  pageIndex: 1,
  pageSize: 20,
  whereJson: {
    nickname: new RegExp('^'+searchvalue)
  }
});
```

#### 以xxxx结尾

```js
let selectRes = await vk.baseDao.select({
  dbName: "uni-id-users",
  pageIndex: 1,
  pageSize: 20,
  whereJson: {
    nickname: new RegExp(searchvalue+'$')
  }
});
```

#### 包含xxxx

```js
let selectRes = await vk.baseDao.select({
  dbName: "uni-id-users",
  pageIndex: 1,
  pageSize: 20,
  whereJson: {
    nickname: new RegExp(searchvalue)
  }
});
```

#### 包含 xxxx 或 yyyy

```js
let selectRes = await vk.baseDao.select({
  dbName: "uni-id-users",
  pageIndex: 1,
  pageSize: 20,
  whereJson: {
    nickname: new RegExp(`xxxx|yyyy`)
  }
});
```

### 如何判断字段是否存在

```js
let selectRes = await vk.baseDao.select({
  dbName: "uni-id-users",
  pageIndex: 1,
  pageSize: 20,
  whereJson: {
    nickname: _.exists(true), // true：存在 false：不存在
  }
});
```

### 返回字段别名

如数据库中是_id，但想返回给前端是user_id

注意：select不支持字段别名，需要selects

```js
let selectRes = await vk.baseDao.selects({
  dbName: "uni-id-users",
  pageIndex: 1,
  pageSize: 20,
  fieldJson: {
    user_id: "$_id",
    nickname: true
  },
  whereJson: {
    
  }
});
```

### 查出表中字段a等于字段b的数据

```js
let selectRes = await vk.baseDao.select({
  dbName: "表名",
  pageIndex: 1,
  pageSize: 20,
  whereJson: _.expr($.and([
    $.eq(['$a', '$b']), // $.eq 等于 $.gt 大于 $.gte 大于等于 lt小于 lte 小于等于
  ]))
});
```

### 如何查询数组字段内包含某个值的数据

直接 ids: id 即可，如

```js
let selectRes = await vk.baseDao.select({
  dbName: "表名",
  pageIndex: 1,
  pageSize: 20,
  whereJson: {
    role: roleId, // role 在数据库中是数组形式字段，如["roleid1","roleid2","roleid3"]  而 roleId = "roleid1" 代表查询数组内有包含roleid1的数据
  }
});
```

### 分组count

#### 最终返回的本月共有多少用户登录（去重后）。

```js
let { monthStart, monthEnd } = vk.pubfn.getCommonTime(new Date());
let selectRes = await vk.baseDao.count({
  dbName: "登录日志表",
  // where条件
  whereJson: {
    _add_time: _.gte(monthStart).lte(monthEnd),
  },
  groupJson: {
    _id: "$user_id", // _id是分组id， $ 后面接字段名
  },
});
```

#### 最终返回的是每个日期登录的用户数量分别有多少个。

```js
let selectRes = await vk.baseDao.selects({
  dbName: "登录日志表",
  pageIndex: 1,
  pageSize: 10,
  // where条件
  whereJson: {
    
  },
  groupJson: {
    _id: "$date_str", // _id是分组id， $ 后面接字段名，如按date_str字段进行分组(date_str字段是2021-08-19这样的字符串)
    count: $.addToSet("$user_id"), // $ 后面接字段名
  },
  sortArr: [{ name: "_id",type: "desc" }], // 对分组后的结果进行排序
  addFields: {
    count: $.size("$count")
  }
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

### 删除某个字段

```js
await vk.baseDao.update({
  dbName: "vk-test", // 表名
  whereJson: { // 条件
    _id: "5f3a14823d11c6000106ff5c"
  },
  dataJson: { 
    money: _.remove(), // 代表删除money字段
  }
});
```

### findById如何连表

findById 和 findByWhereJson 不支持连表，可以用 selects + getOne + getMain 代替

完整代码

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
      localKey: "vip_id", // 主表外键字段名
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

### 如何更改字段名

rename 是字段重命名操作符。如果需要对嵌套深层的字段做重命名，需要用点路径表示法。不能对嵌套在数组里的对象的字段进行重命名。

重命名后，原字段名在数据库中就不存在了，取而代之的是新字段名（字段的值不会变）

#### 示例 1：重命名顶层字段

以下是将 vk-test 表的 money 字段改成 money2 字段示例

```js
await vk.baseDao.update({
  dbName: "vk-test",
  dataJson: {
    money: _.rename('money2')
  }
});
```

#### 示例 2：重命名嵌套字段

```js
await vk.baseDao.update({
  dbName: "vk-test",
  dataJson: {
    'someObject.someField': _.rename('someObject.renamedField')
  }
});
```

#### 示例 3：重命名多个字段

```js
await vk.baseDao.update({
  dbName: "vk-test",
  dataJson: {
     money: _.rename('money2'),
    'someObject.someField': _.rename('someObject.renamedField')
  }
});
```

特别注意：如果数据库数据很多，会报超时，但不用管，数据库依然在执行，速度大概是 5万条记录/秒，因此你可以用 总数据量/50000 计算出大概需要耗时多少。

### 字段如何自增

通过 _.inc(1) 来实现字段自增，同时再通过 updateAndReturn 返回自增后的值

```js
let newInfo = await vk.baseDao.updateAndReturn({
  dbName: "vk-test",
  whereJson: {
    _id: _id
  },
  dataJson: {
    money: _.inc(1)
  }
});
console.log("自增后的值：", newInfo.money);
```

### 如何实现自增id

需要先创建一个存储当前表id的自增值，比如表名叫：id-inc

创建表后，立即新增一条数据，值如下，下面的值不要改，就这样创建。

```json
{
  "_id": "001"
}
```

然后假设你现在需要实现自增id的表名为 my-orders，则在执行 vk.baseDao.add 前，你需要先执行 vk.baseDao.updateAndReturn，代码如下

```js
let newInfo = await vk.baseDao.updateAndReturn({
  dbName: "id-inc",
  whereJson: {
    _id: "001"
  },
  dataJson: {
    "my-orders": _.inc(1)
  }
});
let newId = newInfo['my-orders'];

await vk.baseDao.add({
  dbName: "my-orders",
  dataJson: { 
    id: newId, // 注意：这里最好用id，不要用_id，这样_id依然是数据库默认的，然后多了一个叫id的自增id字段，这样兼容性最高
    // ...其他字段的值
    // a: 1,
    // b: "2"
  }
});
```

注意：文档中出现的 $ 在云函数若不可用，则可写成 _.$
以下是 _ 和 $ 变量实际代表的含义

```js
var db = uniCloud.database(); // 全局数据库引用
var _ = db.command; // 数据库操作符
var $ = _.aggregate; // 聚合查询操作符
```