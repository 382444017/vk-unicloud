# 1. 数据库基础操作

## 增

### vk.baseDao.add（单条记录增加）
接口名

vk.baseDao.add

调用示例

```js
let id = await vk.baseDao.add({
  dbName: "vk-test", // 表名
  dataJson: { // 需要新增的数据 json格式
    money: Math.floor(Math.random() * 9 + 1)
  }
});
```

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 表名 |
| dataJson | Object | 是 | 需要添加的数据 |
| cancelAddTime | Boolean | 否 | 取消自动生成 _add_time 和 _add_time_str 字段 |
| cancelAddTimeStr | Boolean | 否 | 取消自动生成 _add_time_str 字段 |
| db | DB | 否 | 指定数据库实例 const db = uniCloud.database(); |

返回值

返回值为添加数据的_id，添加失败，则返回null

### vk.baseDao.adds（批量增加）
接口名

vk.baseDao.adds

调用示例

```js
let ids = await vk.baseDao.adds({
  dbName: "vk-test",// 表名
  dataJson: [
    { money: Math.floor(Math.random() * 9 + 1) },
    { money: Math.floor(Math.random() * 9 + 1) }
  ]
});
```

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 表名 |
| dataJson | Array | 是 | 需要批量添加的数据 |
| cancelAddTime | Boolean | 否 | 取消自动生成 _add_time 和 _add_time_str 字段 |
| cancelAddTimeStr | Boolean | 否 | 取消自动生成 _add_time_str 字段 |
| needReturnIds | Boolean | 否 | 是否需要返回ids数组数据，默认dataJson的长度 ≤10万 则true，＞10万 则false，false时返回的ids是空数组 |
| db | DB | 否 | 指定数据库实例 const db = uniCloud.database(); |

返回值

返回值为添加数据的_id数组，添加失败，则返回null

若 needReturnIds 为false，则返回的是空数组，设置为false可以减少内存占用，提升性能
若 needReturnIds 为true，则返回的所有添加数据的_id
注意: add 和 adds 默认会自动加上 _add_time字段，该字段表示该条记录的添加时间

可以通过参数 cancelAddTime:true 来取消 _add_time 字段的添加，如下

```js
let id = await vk.baseDao.add({
  dbName: "vk-test",
  cancelAddTime: true, // 通过设置 cancelAddTime:true 可以取消 _add_time 字段的添加
  dataJson: { 
    money: Math.floor(Math.random() * 9 + 1)
  }
});
```

也可以通过配置 /common/uni-config-center/vk-unicloud/index.js 内的 vk.db.unicloud.cancelAddTime = true 来全局取消

提示：正常情况下，没有必要特意取消该字段，该字段记录了本条记录的实际添加时间，且该字段可以用于按时间排序（默认 vk.baseDao.getTableData 的排序规则就是按 _add_time 降序。）

## 删

### vk.baseDao.del（批量删除）
接口名

vk.baseDao.del

批量删除 对应的传统sql语句: delete from vk-test where money = 1

调用示例

```js
// 返回被删除的记录条数
await vk.baseDao.del({
  dbName: "vk-test",
  whereJson: {
    money: 1
  }
});
```

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 表名 |
| whereJson | Object | 是 | where 条件 |
| db | DB | 否 | 指定数据库实例 const db = uniCloud.database(); |

返回值

返回值是删除的记录数量

如果你想要删除此表全部数据，可以这样写

```js
await vk.baseDao.del({
  dbName: "vk-test",
  whereJson: {
    _id: _.exists(true),
  }
});
```

### vk.baseDao.deleteById（根据ID删除数据 ）
接口名

vk.baseDao.deleteById

根据ID删除数据 对应的传统sql语句: delete from vk-test where _id = 1

调用示例

```js
// 返回被删除的记录条数
await vk.baseDao.deleteById({
  dbName: "vk-test",
  id: 1
});
```

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 表名 |
| id | String | 是 | 记录的_id |
| db | DB | 否 | 指定数据库实例 const db = uniCloud.database(); |

返回值

返回值是删除的记录数量

## 改

### vk.baseDao.update（批量修改）
接口名

vk.baseDao.update

批量修改 对应的传统sql语句: update vk-test set money = money-100 where _id="5f3a14823d11c6000106ff5c" and money >= 100

调用示例

```js
// 返回被修改的记录条数
let num = await vk.baseDao.update({
  dbName: "vk-test", // 表名
  whereJson: { // 条件
    _id: "5f3a14823d11c6000106ff5c",
    money: _.gte(100)
  },
  dataJson: { // 需要修改的数据
    money: _.inc(-100)
  }
});
```

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 表名 |
| whereJson | Object | 是 | where 条件 |
| dataJson | Object | 是 | 需要修改的数据 |
| db | DB | 否 | 指定数据库实例 const db = uniCloud.database(); |

返回值

返回值是受影响的行数

如果你想要修改此表全部数据，可以这样写

```js
let num = await vk.baseDao.update({
  dbName: "vk-test", // 表名
  whereJson: { // 条件
    _id: _.exists(true),
  },
  dataJson:{ // 需要修改的数据
    money: _.inc(-1)
  }
});
```

### vk.baseDao.updateById（根据ID修改数据）
接口名

vk.baseDao.updateById

调用示例

```js
let newInfo = await vk.baseDao.updateById({
  dbName: "vk-test",
  id: _id,
  dataJson: {
    money: 1
  },
  getUpdateData: true, // 去掉getUpdateData，则不会返回修改后的数据对象
});
```

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 表名 |
| id | String | 是 | 记录的_id |
| dataJson | Object | 是 | 需要修改的数据 |
| getUpdateData | Boolean | 否 | 是否需要返回修改后的数据 |
| db | DB | 否 | 指定数据库实例 const db = uniCloud.database(); |

返回值

默认返回值是受影响的行数，如果getUpdateData为true，则返回修改后的数据对象

### vk.baseDao.updateAndReturn（更新并返回更新后的数据，原子操作）
接口名

vk.baseDao.updateAndReturn

优势：此为原子操作，并不是先判断是否存在，再进行修改或新增。只计一次写操作。（原子操作）（支持事务）

调用示例

注意：whereJson条件不管写什么，只会修改满足条件的第一条记录，且不支持排序

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
```

如上代码运行后，newInfo.money 的值就会自增1，且不会受到并发影响

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 表名 |
| whereJson | Object | 是 | where 条件 |
| dataJson | Object | 是 | 需要修改的数据 |
| db | DB | 否 | 指定数据库实例 const db = uniCloud.database(); |

返回值

默认返回值是修改后的数据对象

vk.baseDao.updateAndReturn 和 vk.baseDao.updateById + getUpdateData:true 的区别？

1. vk.baseDao.updateAndReturn 是原子操作，而后者不是原子操作（后者是先修改再查询）
2. vk.baseDao.updateAndReturn 可以实现什么功能？

   - 实现id自增
   - 实现阅读数自增（同时返回自增后的商品或文章详细信息等）
   - 实现跟数值有关的自增和自减（同时需要实时获取自增或自减后的值）

### vk.baseDao.setById（根据ID判断存在则替换，不存在则添加）
优势：此为原子操作，并不是先判断是否存在，再进行替换或新增。只计一次写操作。（原子操作）（支持事务）

注意：若id存在，则会用传的数据进行替换，即未写在 dataJson 内的字段会被删除

VK框架核心库版本需 >= 2.15.1

接口名

vk.baseDao.setById

调用示例

```js
let setRes = await vk.baseDao.setById({
  dbName: "vk-test",
  dataJson: {
    _id: "666",
    money: 1
  }
});
```

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 表名 |
| dataJson | Object | 是 | 需要替换或新增的完整数据（必须包含_id） |
| id | String | 否 | 如果传了id参数，则会与dataJson中的_id判断是否一致，不一致会报错 |
| db | DB | 否 | 指定数据库实例 const db = uniCloud.database(); |

返回值

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| type | String | 是 | add 添加 update 修改（替换） |
| id | String | 是 | 若返回的type=add，则会额外返回当前新增的记录的id |

## 查

### vk.baseDao.findById（根据id获取单条记录）
接口名

vk.baseDao.findById

根据id获取单条记录 对应的传统sql语句: select * from vk-test where _id = "5f3a125b3d11c6000106d338"

调用示例

```js
let info = await vk.baseDao.findById({
  dbName: "vk-test",
  id: "5f3a125b3d11c6000106d338"
});
```

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 表名 |
| id | String | 是 | 记录的_id |
| fieldJson | Object | 否 | 字段显示规则（用来控制只显示哪些字段或不显示哪些字段） |
| db | DB | 否 | 指定数据库实例 const db = uniCloud.database(); |

返回值

返回值是该条记录数据

### vk.baseDao.findByWhereJson（根据条件获取单条记录）
接口名

vk.baseDao.findByWhereJson

根据条件获取单条记录 对应的传统sql语句: select * from vk-test where _id = "5f3a125b3d11c6000106d338"

调用示例

```js
let info = await vk.baseDao.findByWhereJson({
  dbName: "vk-test",
  whereJson: {
    _id: "5f3a125b3d11c6000106d338",
  }
});
```

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 表名 |
| whereJson | Object | 是 | where 条件 |
| fieldJson | Object | 否 | 字段显示规则（用来控制只显示哪些字段或不显示哪些字段） |
| sortArr | Array | 否 | 排序规则 |
| db | DB | 否 | 指定数据库实例 const db = uniCloud.database(); |

返回值

返回值是该条记录数据（只返回第一条数据的内容）

### vk.baseDao.select（查多条记录，具有分页功能）
接口名

vk.baseDao.select

查多条记录（具有分页功能） 对应的传统sql语句: `select * from vk-test where money>=0 limit 1,20

云开发数据库最大支持查询1000条数据，而此API可以达到1万条数据，通过设置 pageSize: 10000，最大极限要看你返回的数据量大小。（建议控制在1万以内）

特别注意：此分页功能会随着 pageIndex 的值越大，效率越低（传统mysql也有此问题），pageIndex * pageSize 的值最好不要超过 300万（如每页显示10条，则建议最多让用户查看到第30万页）

vk-admin 的 万能表格 针对分页进行了优化，有多种方案可选 传送门

调用示例

```js
let res = await vk.baseDao.select({
  dbName: "vk-test", // 表名
  getCount: false, // 是否需要同时查询满足条件的记录总数量，默认false
  getMain: false, // 是否只返回rows数据，默认false
  pageIndex: 1, // 当前第几页
  pageSize: 20, // 每页条数
  whereJson: { // 条件
    money: _.gte(0)  // 金额大于0
  },
  // 代表只显示_id和money字段
  fieldJson: {
    _id: true,
    money: true, 
  },
  // 按_id降序 asc 升序 desc 降序 
  sortArr: [
    { name: "_id", type: "desc" }
  ],
});
```

pageSize介绍

由于云厂商限制云数据库最大每次只能查1000条数据，因此VK框架针对select的pageSize进行了特殊处理，可以"突破"云厂商限制，达到1万条以上
联表查询（selects）时，依然为1000条限制
若pageSize设置为-1，则默认查全部数据（会一直查询到云函数超时或内存爆满为止）
根据第3条介绍，故建议pageSize的值尽量在10万以内，1万以内更佳
当pageSize为-1或大于1000时，若未设置排序条件，或排序条件只有_id，可以大大提升性能（会使用游标查询法遍历数据，而不是skip+limit方式）

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 表名 |
| getCount | Boolean | 否 | 是否返回满足条件的记录总数。默认 false 详情 |
| hasMore | Boolean | 否 | 是否返回精确的是否还有下一页。默认 false，若已设置 getCount 为 true，则无需设置此参数 详情 |
| getMain | Boolean | 否 | 是否只返回rows数组。默认 false |
| getOne | Boolean | 否 | 是否只返回第一条数据。默认 false |
| pageIndex | Number | 否 | 第几页 默认 1 |
| pageSize | Number | 否 | 每页显示数量 默认 10 |
| whereJson | Object | 否 | where 条件 |
| fieldJson | Object | 否 | 字段显示规则（用来控制只显示哪些字段或不显示哪些字段）（见上方调用示例） |
| sortArr | Array | 否 | 排序规则（见上方调用示例） |
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

### vk.baseDao.count（获取记录总条数）
接口名

vk.baseDao.count

获取记录总条数 对应的传统sql语句: select count(*) from vk-test

调用示例

不带条件count

```js
let num = await vk.baseDao.count({
  dbName: "vk-test",// 表名
});
```

带条件count

```js
let num = await vk.baseDao.count({
  dbName: "vk-test",// 表名
  whereJson: { // 条件
    
  }
});
```

注意：数据量很大的情况下，带条件运算count全表的性能会很差，尽量使用其他方式替代。比如：

1. 不加条件时count全表不存在性能问题
2. 新增一个字段专门用来存放总数。（如文章评论数量，可以不count评论记录表，而是每次评论时，该文章的评论数量字段+1，通过 _.inc(1) 自增1实现）
3. 增加缩小数据范围的必要条件，比如只查某一个月的数据，此时由于本身满足条件的数据变少了，因此性能极大提升。

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 表名 |
| whereJson | Object | 否 | where 条件 |
| foreignDB | Array | 否 | 连表规则 |
| groupJson | Object | 否 | 分组规则 |
| lastWhereJson | Object | 否 | 连表后的where条件 |
| db | DB | 否 | 指定数据库实例 const db = uniCloud.database(); |

返回值

返回值是满足条件的记录总数

扩展示例

判断用户名是否存在

```js
// 判断用户名是否存在
let num = await vk.baseDao.count({
  dbName: "uni-id-users",// 表名
  whereJson: { // 条件
    username: "admin"
  }
});
if(num>0){
  // 用户名存在
}
```

在设置了昵称唯一时，判断此昵称是否允许修改

```js
// 判断用户名是否存在
let uid = "当前要修改的用户的_id";
let num = await vk.baseDao.count({
  dbName: "uni-id-users",// 表名
  whereJson: { // 条件
    _id: _.neq(uid), // 这里排除掉自己
    nickname: "我要修改的昵称"
  }
});
if(num>0){
  // 用户名存在
}
```

### vk.baseDao.sum（求和）
接口名

vk.baseDao.sum

sum求和 对应的传统sql语句: select sum(money) from vk-test

调用示例

```js
let sum = await vk.baseDao.sum({
  dbName: "vk-test", // 表名
  fieldName: "money", // 需要求和的字段名
  whereJson: { // 条件
    
  }
});
```

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 表名 |
| fieldName | String | 是 | 求和的字段名 |
| whereJson | Object | 否 | where 条件 |
| db | DB | 否 | 指定数据库实例 const db = uniCloud.database(); |

返回值

返回值是求和的值（只能针对数值类型的字段求和，且求和的所有记录中，该字段不允许有字符串）

### vk.baseDao.max（取最大值）
接口名

vk.baseDao.max

对应的传统sql语句: select max(money) from vk-test

调用示例

```js
let max = await vk.baseDao.max({
  dbName: "vk-test", // 表名
  fieldName: "money", // 需要取最大值的字段名
  whereJson: { // 条件
    
  }
});
```

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 表名 |
| fieldName | String | 是 | 求最大值的字段名 |
| whereJson | Object | 否 | where 条件 |
| db | DB | 否 | 指定数据库实例 const db = uniCloud.database(); |

返回值

返回值是最大值

### vk.baseDao.min（取最小值）
接口名

vk.baseDao.min

对应的传统sql语句: select min(money) from vk-test

调用示例

```js
let min = await vk.baseDao.min({
  dbName: "vk-test", // 表名
  fieldName: "money", // 需要取最小值的字段名
  whereJson: { // 条件
    
  }
});
```

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 表名 |
| fieldName | String | 是 | 求最小值的字段名 |
| whereJson | Object | 否 | where 条件 |
| db | DB | 否 | 指定数据库实例 const db = uniCloud.database(); |

返回值

返回值是最小值

### vk.baseDao.avg（取平均值）
接口名

vk.baseDao.avg

对应的传统sql语句: select avg(money) from vk-test

调用示例

```js
let avg = await vk.baseDao.avg({
  dbName: "vk-test", // 表名
  fieldName: "money", // 需要取平均值的字段名
  whereJson: { // 条件
    
  }
});
```

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 表名 |
| fieldName | String | 是 | 求平均值的字段名 |
| whereJson | Object | 否 | where 条件 |
| db | DB | 否 | 指定数据库实例 const db = uniCloud.database(); |

返回值

返回值是平均值

### vk.baseDao.sample（随机取N条数据）
接口名

vk.baseDao.sample

随机取N条数据

调用示例

注意：不支持连表查询

```js
let list = await vk.baseDao.sample({
  dbName: "vk-test", // 表名
  size: 1, // 随机条数
  whereJson: { // 条件
    
  }
});
```

请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| dbName | String | 是 | 表名 |
| size | Number | 是 | 随机获取的记录数量 |
| whereJson | Object | 否 | where 条件 |
| fieldJson | Object | 否 | 字段显示规则（用来控制只显示哪些字段或不显示哪些字段） |
| db | DB | 否 | 指定数据库实例 const db = uniCloud.database(); |

返回值

返回值是平均值