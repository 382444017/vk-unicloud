# 5. 事务使用

## 示例

两个账户之间进行转账的简易示例

```js
let { data = {}, userInfo, util, originalParam } = event;
let { customUtil, config, pubFun, vk, db, _ } = util;
let { uid } = data;
let res = { code: 0, msg: '' };
// 业务逻辑开始-----------------------------------------------------------

// 开启事务
const transaction = await vk.baseDao.startTransaction();
try {
  let dbName = "uni-id-users";
  let money = 100;
  // 给用户001减100余额
  let update1Res = await vk.baseDao.updateById({
    db: transaction, // 本次数据库语句带上事务对象（重要，必须带上）
    dbName,
    id: "001",
    dataJson: {
      account_balance: _.inc(money * -1)
    },
  });
  // 给用户2加100余额
  let update2Res = await vk.baseDao.updateById({
    db: transaction, // 本次数据库语句带上事务对象（重要，必须带上）
    dbName,
    id: "002",
    dataJson: {
      account_balance: _.inc(money)
    },
  });
  const endRes = await vk.baseDao.findById({
    db: transaction, // 本次数据库语句带上事务对象（重要，必须带上）
    dbName,
    id: "001"
  });
  if (endRes.account_balance < 0) {
    // 事务回滚
    await transaction.rollback();
    return {
      code: -1,
      msg: "用户001账户余额不足",
      aaaAccount: endRes.account_balance
    }
  } else {
    // 提交事务
    await transaction.commit();
    console.log(`transaction succeeded`);
    return {
      code: 0,
      msg: "转账成功",
      aaaAccount: endRes.account_balance
    }
  }
} catch (err) {
  // 事务回滚
  await transaction.rollback();
  console.error(`transaction error`, err)
  return {
    code: -1,
    msg: "数据库写入异常,事务已回滚",
    err: {
      message: err.message,
      stack: err.stack
    }
  }
}
// 业务逻辑结束-----------------------------------------------------------
return res;
```

## 简易模板

```js
// 开启事务
const transaction = await vk.baseDao.startTransaction();
try {
  // 这里写数据库语句

  // 提交事务
  await transaction.commit();
  console.log(`transaction succeeded`);
} catch (err) {
  // 事务回滚
  return await vk.baseDao.rollbackTransaction({
    db: transaction,
    err
  });
}
```

请注意以下内容：

1. 事务自带锁，因此需要注意事务操作时会对行进行锁定。
2. 事务开始后到提交或回滚之间的时间不能超过10秒，否则会报错。
3. 如果事务异常后没有执行vk.baseDao.rollbackTransaction进行回滚，虽然数据依然不会被改变，但这会导致这些行记录被锁定，大概会锁1分钟左右。
4. 带有事务的 vk.baseDao.add 方法不会执行自动建表操作。当在事务中操作不存在的表时，会直接抛出异常，必须确保表在事务开始前已存在。
5. 暂只有以下API支持事务
   - vk.baseDao.add
   - vk.baseDao.findById
   - vk.baseDao.updateById
   - vk.baseDao.deleteById
   - vk.baseDao.updateAndReturn
   - vk.baseDao.setById

提示：若使用扩展数据库，[则全部数据库API都能支持事务](opens new window)，包含批量新增、批量修改、批量删除都能支持事务。

## 事务隔离级别：

- 读：ReadConcern.SNAPSHOT
- 写：WriteConcern.MAJORITY