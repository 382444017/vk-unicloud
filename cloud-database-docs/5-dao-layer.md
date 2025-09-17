# 4. DAO层的作用及方法命名规范

## dao层的作用

1. 复用数据库API，减少重复代码，同时可以达到即使后面修改了数据库字段或数据库名称，也只需要修改少量 dao 层的代码即可。
2. dao 层是积木，而 service 层是利用这些积木来搭建你想要的乐园。
3. 理论上 dao 层的代码开发是可以脱离业务的，即使业务需求还不是很明确，只要数据库表名已确定，即可编写 dao 层。dao 层提供的是数据库原子性操作。

[点击安装自动生成Dao层基础代码插件](opens new window)

## 注意事项

- Dao层代码默认写在 router/dao/modules/ 目录 [查看自定义dao层目录](#自定义dao层目录)
- 文件名必须以 xxxDao.js 这样的格式命名(即必须Dao.js结尾)
- 最终通过 await vk.daoCenter.xxxDao.xxxx(); 形式进行调用
- 尽量只写跟数据库交互的代码，少写业务逻辑代码，业务逻辑应写在 service 层
- 如果新建dao层代码后运行提示dao不存在，则重新运行项目即可

## dao层与baseDao的区别

baseDao相当于 万能dao，他是最基础的零件，而 dao 层是利用零件组装不同形状和规则的积木，供 service 层使用。

## 命名规范（参考）

### 查单条记录: 
获取单条记录的方法用 find 或 get 作为前缀

### 查多条记录: 
获取多个对象的方法用 list 作为前缀

### 统计记录数量: 
获取统计行数的方法用 count 作为前缀

### 新增 : 
新增数据的方法用 add 或 save 作为前缀

### 删除 : 
删除数据的方法用 delete 或 remove 作为前缀

### 修改 : 
修改的方法用 update 作为前缀

### 前缀 + 条件类型（适用于1个dao只操作1张表的情况）

如：

- 根据 ID 获取一条信息 findById
- 根据 手机号 获取一条信息 findByMobile
- 根据 不定性条件 获取一条信息 findByWhereJson
- 根据 用户状态 获取多条信息 listByStatus
- 根据 不定性条件 获取多条信息 listByWhereJson
- 添加 add
- 删除，根据ID deleteById
- 修改，根据ID updateById
- 批量修改，根据自定义条件 updateByWhereJson
- 批量删除，根据自定义条件 deleteByWhereJson

### 前缀 + 表名 + 条件类型（适用于1个dao可能操作多张表的情况）

如：

- 根据 用户ID 获取用户信息 findUserById
- 根据 用户手机号 获取用户信息 findUserByMobile
- 根据 不定性条件 获取用户信息 findUserByWhereJson
- 根据 用户状态 获取用户列表 listUserByStatus
- 根据 不定性条件 获取用户列表 listUserByWhereJson
- 添加用户 addUser
- 删除用户，根据用户ID deleteUserById
- 修改用户，根据用户ID updateUserById
- 批量修改用户，根据自定义条件 updateUserByWhereJson
- 批量删除用户，根据自定义条件 deleteUserByWhereJson

## Dao文件代码生成工具

[点击安装自动生成Dao层基础代码插件](opens new window)

## 自定义dao层目录

如果你的项目除了 router 函数外，还有其他函数也需要用到 dao 层，此时 dao 层目录需要写在 公共模块 那，而不是写在 router/dao/modules/ 目录，具体操作如下：

1. 右键 cloudfunctions/common 目录，新建公共模块，输入名字 router-common
2. 将原本的在 router 内的 dao 目录剪切到刚新建的公共模块 router-common 下
3. 替换 router-common 根目录的 index.js 文件，文件内容如下：

```js
module.exports = {
  daoCenter: require('./dao/index.js'), // dao层入口文件
}
```

4. 修改 router/config.js 文件，文件内容如下：

```js
const routeCommon = require('router-common');
const requireFn = function(path) {
  return require(path);
}
const initConfig = {
  baseDir: __dirname, // 云函数根目录地址
  requireFn,
  daoCenter: routeCommon.daoCenter, // dao层入口文件
  customUtil: {
    
  }
};
module.exports = initConfig;
```

即 initConfig 增加了 daoCenter 配置

5. 完成

在其他函数引入 vk 的时候，需要这样写

```js
// 引入 vk-unicloud
const vkCloud = require('vk-unicloud');
// 引入 routeCommon 公共模块
const routeCommon = require('router-common');
// 通过 vkCloud.createInstance 创建 vk 实例
const vk = vkCloud.createInstance({
  baseDir: __dirname, // 云函数根目录地址
  requireFn: require,
  daoCenter: routeCommon.daoCenter, // dao层入口文件
}); 
```

注意：云函数至少需要添加的公共模块依赖有

- uni-config-center
- vk-unicloud
- router-common

操作完成后，就可以在任意云函数中直接调用 vk.daoCenter.xxxDao.xxx() API 了