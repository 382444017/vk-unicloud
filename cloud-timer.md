# 定时器（定时任务）

## 单次执行时长小于180秒的定时任务

以实现1小时自动请求一次指定的云函数为例：

1. 编写 `client/timedTask/pub/autoCancelOrder` 的云函数，写定时任务的业务逻辑。（没有则新建）
2. 右键 `cloudfunctions` 目录 新建云函数，取名为 `z_timer1` （名字任意，这里以 `z_timer1` 为定时器1号的名称）
3. 复制定时器模板代码到 `z_timer1` 的 `index.js`

```javascript
'use strict';
exports.main = async (event, context) => {
  /**
   * 定时器1号 - xxxxx
   */
  let res = { code: 0, msg: "" };
  res.callFunctionResult = await uniCloud.callFunction({
    name: "router",
    data: {
      // 需要执行的云函数路径
      $url: "client/timedTask/pub/autoCancelOrder",
      // 请求参数，这里加个key可以有效防止云函数被直接访问，云函数中加判断条件，如果key不是666666，则不运行。
      data: {
        key: "666666"
      }
    }
  });
  return res;
};
```

4. 编写 `z_timer1/package.json` 的代码

```json
{
  "name": "z_timer1",
  "main": "index.js",
  "version": "1.0.0",
  "description": "",
  "cloudfunction-config": {
    "concurrency": 1,
    "memorySize": 512,
    "path": "",
    "timeout": 600,
    "triggers": [{
      "config": "0 0 * * * * *",
      "name": "z_timer1",
      "type": "timer"
    }],
    "runtime": "Nodejs12"
  }
}
```

5. 右键 `z_timer1` 上传部署
6. 完成

## 单次执行时长大于180秒的定时任务

因为通过 `await uniCloud.callFunction` 方式调用其他云函数时，云函数最长执行180秒，故当单次执行时长大于180秒时，不能用 `await uniCloud.callFunction` 的方式，只能将逻辑全部写在定时任务里。

具体操作步骤如下：

1. 右键 `cloudfunctions` 目录 新建云函数，取名为 `z_timer1` （名字任意，这里以 `z_timer1` 为定时器1号的名称）
2. 复制定时器模板代码到 `z_timer1` 的 `index.js`

```javascript
'use strict';
// 引入 vk-unicloud
const vkCloud = require('vk-unicloud');
// 通过 vkCloud.createInstance 创建 vk 实例
const vk = vkCloud.createInstance({
  baseDir: __dirname,
  requireFn: require
});
exports.main = async (event, context) => {
  /**
   * 定时器1号 - xxxxxx
   */
  let res = { code: 0, msg: "" };
  
  // 这里直接写你的定时任务逻辑
  
  // 可以直接调用baseDao的API了
  let userList = await vk.baseDao.select({
    dbName: "uni-id-users",
    getMain: true,
    pageIndex: 1,
    pageSize: 1000,
    whereJson: {
    },
    fieldJson: {},
    sortArr: [{ "name": "_id", "type": "desc" }],
  });
  
  return res;
};
```

3. 编写 `z_timer1/package.json` 的代码

```json
{
  "name": "z_timer1",
  "main": "index.js",
  "version": "1.0.0",
  "description": "",
  "dependencies": {
    "uni-config-center": "file:../../../uni_modules/uni-config-center/uniCloud/cloudfunctions/common/uni-config-center",
    "uni-id": "file:../../../uni_modules/uni-id/uniCloud/cloudfunctions/common/uni-id",
    "vk-unicloud": "file:../../../uni_modules/vk-unicloud/uniCloud/cloudfunctions/common/vk-unicloud"
  },
  "cloudfunction-config": {
    "concurrency": 1,
    "memorySize": 512,
    "path": "",
    "timeout": 7200,
    "triggers": [{
      "config": "0 0 * * * * *",
      "name": "z_timer1",
      "type": "timer"
    }],
    "runtime": "Nodejs12"
  }
}
```

4. 右键 `z_timer1` 上传部署
5. 完成

## Cron 表达式

Cron 表达式有七个必需字段（阿里云是6个），按空格分隔。

| 位数 | 说明 | 值 |
|------|------|-----|
| 1 | 秒 | 0-59 的整数 |
| 2 | 分钟 | 0-59 的整数 |
| 3 | 小时 | 0-23 的整数 |
| 4 | 日 | 1-31 的整数（需要考虑月的天数） |
| 5 | 月 | 1-12 的整数 |
| 6 | 星期 | 0-6 的整数 或 SUN,MON,TUE,WED,THU,FRI,SAT；其中 0 指周日，1 指周一，依次类推，6 指周六 |
| 7 | 年 | 1970~2099 的整数 （阿里云没有这个，但是仍需将第七位设置为*） |

## 通配符

| 通配符 | 说明 |
|--------|------|
| , (逗号) | 代表取用逗号隔开的字符的并集。例如：在"小时"字段中 1,2,3 表示1点、2点和3点 |
| - (破折号) | 包含指定范围的所有值。例如：在"日"字段中，1-15 包含指定月份的 1 号到 15 号 |
| * (星号) | 表示所有值。在"小时"字段中，* 表示每个小时 |
| / (正斜杠) | 指定增量。在"分钟"字段中，输入 1/10 以指定从第一分钟开始的每隔十分钟重复。例如，第 11 分钟、第 21 分钟和第 31 分钟，依此类推 |

## 注意事项

在 Cron 表达式中的"日"和"星期"字段同时指定值时，两者为"或"关系，即两者的条件分别均生效。
触发器规则的时区为 UTC+8

## 示例

下面展示了一些 Cron 表达式和相关含义的示例：

| 示例 | 说明 | 云厂商兼容性 |
|------|------|-------------|
| * * * * * * * | 每1秒触发一次（阿里云不支持） | 腾讯云、支付宝云 |
| */5 * * * * * * | 每5秒触发一次（阿里云不支持） | 腾讯云、支付宝云 |
| 0 * * * * * * | 每1分钟触发一次（每分的0秒触发） | all |
| 10 * * * * * * | 每1分钟触发一次（每分的第10秒触发） | all |
| 0 */10 * * * * * | 每10分钟触发一次 | all |
| 0 1/10 * * * * * | 每10分钟触发一次（如：08:11:00、08:21:00、08:31:00，依此类推 | all |
| 0 0 * * * * * | 每1小时触发一次（整点触发） | all |
| 0 10 * * * * * | 每1小时触发一次（每小时的10分触发，如08:10:00、09:10:00） | all |
| 0 0 */2 * * * * | 每2小时触发一次（整点触发） | all |
| 0 0 18 * * * * | 每天的下午6点触发一次（整点触发，18:00:00） | all |
| 0 0 10,14,16 * * * * | 每天上午10点，下午2点，4点触发（整点触发，10:00:00、14:00:00、16:00:00） | all |
| 0 */30 9-17 * * * * | 每天上午9点到下午5点内每半小时触发 | all |
| 0 0 2 1 * * * | 每月的1日的凌晨2点触发 | all |
| 0 15 10 * * 1-5 * | 周一到周五每天上午10:15触发 | 腾讯云、阿里云 |
| 0 0 12 * * 3 * | 每个星期三中午12点触发 | 腾讯云、阿里云 |

兼容性总结：
- 阿里云不支持秒级触发（最低是1分钟）
- 支付宝云不支持星期参数