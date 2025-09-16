# 云函数数据库API示例

本文档展示了 `template/db_api/` 目录下的数据库操作云函数示例代码。

## 数据库增删改查操作

### add.js - 添加单条数据

```javascript
module.exports = {
  /**
   * 添加单条数据
   * @url template/db_api/pub/add 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始-----------------------------------------------------------
    let startTime = Date.now();
    
    // 为了方便测试,这里固定在杭州市西湖区附近
    let longitude = 120.12792 + Math.floor(Math.random() * 500) / 10000;
    let latitude = 30.228932  + Math.floor(Math.random() * 500) / 10000;
    // 随机定位结束-----------------------------------------------------------
    res.id = await vk.baseDao.add({
      dbName:"vk-test",
      dataJson:{
        "money": Math.floor(Math.random() * 9 + 1),
        "user_id": "00"+Math.floor(Math.random() * 2 + 1),
        "location":new db.Geo.Point(longitude, latitude),
        "position":{
          "longitude":longitude,
          "latitude":latitude,
        },
      }
    });
    
    let endTime = Date.now();
    res.runTime = (endTime - startTime);
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### getList.js - 查询多条记录（分页）

```javascript
module.exports = {
  /**
   * 查询多条记录 分页
   * @url template/db_api/pub/getList 前端调用的url参数地址
   * data 请求参数 说明
   * @params {String} tableName  表名
   * @params {String} addTime   搜索开始时间
   * @params {String} endTime   搜索截止时间
   * @params {String} searchvalue 搜索指定内容
   * @params {Number} pageIndex  当前页码
   * @params {Number} pageSize   每页显示数量
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : '' };
    // 业务逻辑开始----------------------------------------------------------- 
    // 可写与数据库的交互逻辑等等
    let { pageIndex, pageSize, searchvalue, timeArr = [], addTimeLong, endTimeLong, sortRule } = data;
    let dbName = "vk-test";
    let fieldJson = {};
    let whereJson = {};
    let sortArr = [];
    // 排序规则开始-----------------------------------------------------------
    sortArr.push({
      "name":"_add_time",
      "type":"desc"
    });
    if(sortRule) sortArr = sortRule;
    // 排序规则结束-----------------------------------------------------------
    // 查询条件开始-----------------------------------------------------------
    if(searchvalue){
      // 查询searchvalue开头的数据
      try{
        // 如果查询需要用到正则，建议将正则用try catch 包起来。
        //whereJson["title"] = new RegExp('^'+searchvalue);
        whereJson["money"] = Number(searchvalue);
      }catch(err){
        return { code : -1, msg : '请输入合法的查询内容' };
      }
    }
    if(timeArr.length >=2 ){
      addTimeLong = timeArr[0];
      endTimeLong = timeArr[1];
    }
    if(addTimeLong && endTimeLong){
      whereJson["_add_time"] =  _.gte(addTimeLong).lte(endTimeLong);
    }else if(addTimeLong && !endTimeLong){
      whereJson["_add_time"] =  _.gte(addTimeLong);
    }else if(!addTimeLong && endTimeLong){
      whereJson["_add_time"] =  _.lte(endTimeLong);
    }
    // 这里可以写必须满足的查询条件，如whereJson["user_id"] = uid;代表只查自己的记录
    // whereJson["user_id"] = uid;
    // 查询条件结束-----------------------------------------------------------
    res = await vk.baseDao.select({
      dbName:dbName,
      getCount:true,
      pageIndex:pageIndex,
      pageSize:pageSize,
      fieldJson:fieldJson,
      whereJson:whereJson,
      sortArr:sortArr
    });
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### update.js - 修改数据

```javascript
module.exports = {
  /**
   * 修改数据
   * @url template/db_api/pub/update 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    let { _id = "___" } = data;
    res.num = await vk.baseDao.update({
      dbName:"vk-test",
      whereJson:{
        _id:_id,
      },
      dataJson:{
        money:1
      }
    });
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### delete.js - 删除数据

```javascript
module.exports = {
  /**
   * 删除数据
   * @url template/db_api/pub/delete 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    let { _id } = data;
    res.num = await vk.baseDao.delete({
      dbName:"vk-test",
      whereJson:{
        _id:_id
      }
    });
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

## 高级查询操作

### findById.js - 根据ID查询

```javascript
module.exports = {
  /**
   * 根据ID查询单条数据
   * @url template/db_api/pub/findById 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    let { _id } = data;
    res.data = await vk.baseDao.findById({
      dbName:"vk-test",
      id:_id
    });
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### findByWhereJson.js - 条件查询

```javascript
module.exports = {
  /**
   * 条件查询单条数据
   * @url template/db_api/pub/findByWhereJson 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    let { whereJson = {} } = data;
    res.data = await vk.baseDao.findByWhereJson({
      dbName:"vk-test",
      whereJson:whereJson
    });
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### count.js - 统计数量

```javascript
module.exports = {
  /**
   * 统计数量
   * @url template/db_api/pub/count 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    let { whereJson = {} } = data;
    res.count = await vk.baseDao.count({
      dbName:"vk-test",
      whereJson:whereJson
    });
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### sum.js - 求和计算

```javascript
module.exports = {
  /**
   * 求和计算
   * @url template/db_api/pub/sum 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    let { whereJson = {}, fieldName = "money" } = data;
    res.sum = await vk.baseDao.sum({
      dbName:"vk-test",
      whereJson:whereJson,
      fieldName:fieldName
    });
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### avg.js - 平均值计算

```javascript
module.exports = {
  /**
   * 平均值计算
   * @url template/db_api/pub/avg 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    let { whereJson = {}, fieldName = "money" } = data;
    res.avg = await vk.baseDao.avg({
      dbName:"vk-test",
      whereJson:whereJson,
      fieldName:fieldName
    });
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### min.js - 最小值计算

```javascript
module.exports = {
  /**
   * 最小值计算
   * @url template/db_api/pub/min 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    let { whereJson = {}, fieldName = "money" } = data;
    res.min = await vk.baseDao.min({
      dbName:"vk-test",
      whereJson:whereJson,
      fieldName:fieldName
    });
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### max.js - 最大值计算

```javascript
module.exports = {
  /**
   * 最大值计算
   * @url template/db_api/pub/max 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    let { whereJson = {}, fieldName = "money" } = data;
    res.max = await vk.baseDao.max({
      dbName:"vk-test",
      whereJson:whereJson,
      fieldName:fieldName
    });
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

## 高级功能

### geo.js - 地理位置查询

```javascript
module.exports = {
  /**
   * 地理位置查询
   * @url template/db_api/pub/geo 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    let { longitude, latitude, maxDistance = 1000 } = data;
    res.list = await vk.baseDao.select({
      dbName:"vk-test",
      whereJson:{
        location: _.geoNear({
          geometry: new db.Geo.Point(longitude, latitude),
          maxDistance: maxDistance, // 最大距离，单位米
          minDistance: 0 // 最小距离，单位米
        })
      },
      pageSize:100
    });
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### tree.js - 树形结构数据

```javascript
module.exports = {
  /**
   * 树形结构数据操作
   * @url template/db_api/pub/tree 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    // 树形结构数据操作示例
    res.treeData = await vk.baseDao.getTree({
      dbName:"tree-table",
      startId:"0", // 从哪个节点开始获取
      maxLevel:3 // 最大获取层级
    });
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### groupCount.js - 分组统计

```javascript
module.exports = {
  /**
   * 分组统计
   * @url template/db_api/pub/groupCount 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    let { groupField = "user_id" } = data;
    res.groupData = await vk.baseDao.groupCount({
      dbName:"vk-test",
      groupField:groupField,
      whereJson:{}
    });
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### sample.js - 随机抽样

```javascript
module.exports = {
  /**
   * 随机抽样
   * @url template/db_api/pub/sample 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    let { size = 10 } = data;
    res.sampleData = await vk.baseDao.sample({
      dbName:"vk-test",
      size:size,
      whereJson:{}
    });
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

## Promise.all.js - 并行操作

```javascript
module.exports = {
  /**
   * Promise.all 并行操作示例
   * @url template/db_api/pub/Promise.all 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    // 并行执行多个数据库操作
    let [countResult, sumResult, listResult] = await Promise.all([
      vk.baseDao.count({
        dbName:"vk-test",
        whereJson:{}
      }),
      vk.baseDao.sum({
        dbName:"vk-test",
        whereJson:{},
        fieldName:"money"
      }),
      vk.baseDao.select({
        dbName:"vk-test",
        pageSize:5,
        whereJson:{}
      })
    ]);
    
    res.count = countResult;
    res.sum = sumResult;
    res.list = listResult;
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

## 系统表操作 (sys目录)

### sys/add.js - 系统表添加

```javascript
module.exports = {
  /**
   * 系统表添加数据
   * @url template/db_api/sys/add 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    // 系统表操作需要管理员权限
    res.id = await vk.baseDao.add({
      dbName:"uni-id-users",
      dataJson:{
        username: "test_user",
        password: "123456",
        // 其他用户字段...
      }
    });
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### sys/getList.js - 系统表查询

```javascript
module.exports = {
  /**
   * 系统表查询数据
   * @url template/db_api/sys/getList 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { uniID, pubFun, vk , db, _ } = util;
    let { uid } = data;
    let res = { code : 0, msg : 'ok' };
    // 业务逻辑开始----------------------------------------------------------- 
    // 系统表操作需要管理员权限
    res = await vk.baseDao.select({
      dbName:"uni-id-users",
      pageIndex:1,
      pageSize:10,
      whereJson:{
        // 查询条件...
      }
    });
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

## 最佳实践

### 1. 错误处理

```javascript
// 使用 try-catch 处理数据库操作错误
try {
  res.data = await vk.baseDao.add({
    dbName: "my-table",
    dataJson: data
  });
} catch (error) {
  res.code = -1;
  res.msg = '数据库操作失败: ' + error.message;
}
```

### 2. 参数验证

```javascript
// 验证必填参数
if (!data.username || !data.password) {
  return { code: -1, msg: '用户名和密码不能为空' };
}

// 验证参数类型
if (typeof data.age !== 'number') {
  return { code: -1, msg: '年龄必须是数字' };
}
```

### 3. 性能优化

```javascript
// 使用字段投影减少数据传输
res.data = await vk.baseDao.select({
  dbName: "user-table",
  fieldJson: {
    username: true,
    avatar: true,
    _add_time: true
  },
  whereJson: {
    status: 1
  }
});

// 使用索引优化查询
// 确保经常查询的字段建立了数据库索引
```

### 4. 安全考虑

```javascript
// 防止SQL注入，使用参数化查询
// VK框架的baseDao已经做了防注入处理，直接使用whereJson即可

// 权限验证
if (!userInfo || userInfo.role !== 'admin') {
  return { code: -1, msg: '无权限操作' };
}

// 数据过滤，防止越权操作
whereJson.user_id = uid; // 只允许操作自己的数据
```

## 常见问题

### 1. 连接超时
- 检查数据库连接配置
- 优化查询语句，避免全表扫描

### 2. 内存溢出
- 分页查询大数据集
- 使用流式处理大量数据

### 3. 性能问题
- 为常用查询字段创建索引
- 避免在循环中执行数据库操作

### 4. 事务处理
```javascript
// 使用事务保证数据一致性
const transaction = await db.startTransaction();
try {
  // 执行多个数据库操作
  await transaction.collection('order').add(orderData);
  await transaction.collection('inventory').update(inventoryData);
  
  await transaction.commit();
  res.code = 0;
  res.msg = '操作成功';
} catch (error) {
  await transaction.rollback();
  res.code = -1;
  res.msg = '操作失败: ' + error.message;
}
```

通过以上示例，您可以掌握VK框架数据库API的使用方法，并应用到实际项目中。