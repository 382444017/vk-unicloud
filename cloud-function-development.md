---
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