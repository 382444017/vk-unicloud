# VK Unicloud 常见问题解答

## 1. 如何切换正式环境和开发环境

### 创建2个服务空间

- 测试环境服务空间。（建议用阿里云按量计费空间）
- 正式环境服务空间。（套餐和按量都可以）

### 具体步骤:

1. 进入 https://unicloud.dcloud.net.cn/home 创建一个新的云服务空间
2. 右键 uniCloud目录 关联云服务空间 - 选择新建的云服务空间
3. 右键 uniCloud目录 云服务空间初始化向导（第一次关联测试空间需要这么做）
4. 重启项目

重启后就是测试环境，在测试环境开发测试完确保没有问题后，切回正式环境，再上传云函数router即可更新至正式环境。

### 如何切回正式环境？

右键 uniCloud目录 关联云服务空间 - 选择正式环境服务空间

## 2. 使用jquery、axios等工具访问云函数方式(云函数url化外部访问)

必须开启云函数的URL化，假如URL地址为：`https://xxx.bspapp.com/http/router`

开启URL化方法为：打开 router/package.json 文件，在 path 里填写 /http/router，最后重新上传云函数。

```json
"cloudfunction-config": {
  "concurrency": 1,
  "memorySize": 512,
  "path": "/http/router",
  "timeout": 60,
  "triggers": [],
  "runtime": "Nodejs12"
}
```

### 如何获得云函数URL化的域名地址？

进入uniCloud后台，在云函数的函数列表里找到router，点详情

注意：如果你直接在浏览器中访问url化地址，会触发下载请求，需要用 postman 等工具进行访问测试。

### axios请求示例

假设router的url化地址是 `https://xxx.com/http/router`

假设需要请求的云函数路由是 `template/test/pub/test`

则完整请求url是 `https://xxx.com/http/router/template/test/pub/test`

#### axios（不带token）

```javascript
axios.post('https://xxx.bspapp.com/http/router/test/pub/test', {
  a:1,
  b:"2"
})
.then((res) => {
  console.log("then", res);
})
.catch((err) => {
  console.log("catch", err);
});
```

#### axios（带token）

```javascript
axios({
  method: 'post',
  url: 'https://xxx.bspapp.com/http/router/test/pub/test',
  headers: {
    'content-type': 'application/json;charset=utf8',
    'uni-id-token': 'xxxxxxxxx', // 用户token
    'vk-appid': '__UNI__89927A9', // 你项目的dcloud_appid
    'vk-platform': 'mp-weixin', // 运行环境，如 h5、mp-weixin、app-plus 等
  },
  data: {
    a: 1,
    b: "2"
  }
})
.then((res) => {
  console.log("then", res);
})
.catch((err) => {
  console.log("catch", err);
});
```

### jquery ajax请求示例

假设router的url化地址是 `https://xxx.com/http/router`

假设需要请求的云函数路由是 `template/test/pub/test`

则完整请求url是 `https://xxx.com/http/router/template/test/pub/test`

#### jquery（无token示例）

```javascript
$.ajax({
  type: 'POST',
  url: "https://xxx.com/http/router/template/test/pub/test",
  data: JSON.stringify({
    a: 1,
    b: "2"
  }),
  success: (data) => {
    console.log("data", data);
  }
})
```

#### jquery（有token示例）

```javascript
$.ajax({
  type: 'POST',
  url: "https://xxx.com/http/router/template/test/pub/test",
  headers:{ 
    'content-type': 'application/json;charset=utf8',
    'uni-id-token': 'xxxxxxxxx', // 用户token
    'vk-appid': '__UNI__89927A9', // 你项目的dcloud_appid
    'vk-platform': 'mp-weixin', // 运行环境，如 h5、mp-weixin、app-plus 等
  },
  data: JSON.stringify({
    a:1,
    b:"2"
  }),
  success: (data) => {
    console.log("data", data);
  }
})
```

### uni.request示例

假设router的url化地址是 `https://xxx.com/http/router`

假设需要请求的云函数路由是 `template/test/pub/test`

则完整请求url是 `https://xxx.com/http/router/template/test/pub/test`

#### uni.request（不带token）

```javascript
uni.request({
  method: 'POST',
  url: "https://xxx.com/http/router/template/test/pub/test",
  data: {
    a: 1,
    b: "2"
  },
  success: (data) => {
    console.log("data", data);
  }
});
```

#### uni.request（带token）

```javascript
uni.request({
  method: 'POST',
  url: "https://xxx.com/http/router/template/test/pub/test",
  header: {
    'content-type': 'application/json;charset=utf8',
    'uni-id-token': 'xxxxxxxxx', // 用户token
    'vk-appid': '__UNI__89927A9', // 你项目的dcloud_appid
    'vk-platform': 'mp-weixin', // 运行环境，如 h5、mp-weixin、app-plus 等
  },
  data: {
    a: 1,
    b: "2"
  },
  success: (data) => {
    console.log("data", data);
  }
});
```

### 常见问题

注意：部分接口若报如下错误

则需要在请求头多传2个参数 vk-appid 和 vk-platform

其中 vk-appid 是 manifest.json 内的 dcloud_appid vk-platform 是当前环境，如：h5 mp-weixin app-plus 等

以 axios 示例

```javascript
axios({
  method: 'post',
  url: 'https://xxx.bspapp.com/http/router/test/pub/test',
  headers: {
    'content-type': 'application/json;charset=utf8',
    'uni-id-token': 'xxxxxxxxx', // 用户token
    'vk-appid': '__UNI__89927A9', // 你项目的dcloud_appid
    'vk-platform': 'mp-weixin', // 运行环境，如 h5、mp-weixin、app-plus 等
    'vk-locale': 'zh-Hans', // 默认语言
  },
  data: {
    a: 1,
    b: "2"
  }
})
.then((res) => {
  console.log("then", res);
})
.catch((err) => {
  console.log("catch", err);
});
```

## 3. 框架更新步骤

目前升级框架方式：

### 方式一: 升级整个框架（包含模块）

HBX编译器版本需 3.1.6或更高

右键项目根目录下的 package.json 文件，再点击从【插件市场更新】

注意：

使用方式一更新框架是最方便的，但是如果你改动了框架内置的部分代码，你改动过的代码可能会被还原，因此如果你改动过框架内置代码，合并的时候，可以先看下改动的文件。

如：
1. app.config.js
2. App.vue
3. main.js
4. pages.json
等等

升级前一定要进行文件对比！

### 方式二: 只升级模块

右键 uni_modules/vk-unicloud/ 目录，再点击从【插件市场更新】

### 注意事项

- 更新后，需要在 common/vk-unicloud 右键上传公共模块才会生效
- 若本次更新还涉及router内的函数更新，还需要再上传router函数
- 若是本地调试模式，则需要重启本地服务才会生效。
- 使用方式一更新时，你改动过的代码可能会被还原，因此如果你改动过框架内置代码，合并的时候，需要仔细对比差异。
- 使用方式二更新框架不会造成你修改过的框架文件被覆盖，但是如果本次更新需要改动框架内一些文件，则需要你手动更改。

## 4. 关于用户角色权限

### 介绍

目前框架内置了sys过滤器（云函数层的权限过滤器，检测用户是否有此业务云函数的执行权限）

- 用户表：`uni-id-users`
- 角色表：`uni-id-roles`
- 权限表：`uni-id-permissions`

由于云数据库的特殊性：
- 用户角色中间表为用户表中的`role`字段(用户角色列表，由`role_id`组成的数组)
- 角色权限中间表为角色表中的`permission`字段(角色拥有的权限列表，由`permission_id`组成的数组)

### 云函数权限检测逻辑

这里只是介绍下，框架已内置，无需写对应的代码

1. 用户访问云函数 `user/sys/updateUserInfo`
   则 `url = "user/sys/updateUserInfo"`
   则云函数权限检测就变成了用户是否有执行 `user/sys/updateUserInfo` 云函数的权限。
2. 根据 `token` 获取到用户 `permission`
3. 根据权限表的权限规则（如完整匹配、通配符匹配、正则匹配）校验用户拥有的 `permission` 是否有任意一个权限匹配到了此 `url`
4. 若能匹配，代表用户有此云函数执行权限，放行，若不能匹配，代表用户无此云函数执行权限，需要拦截。

以上为用户角色权限校验的大致逻辑（因为非常灵活配置，可以适用于所有系统：admin后台，ERP软件，APP、小程序等）

但是：如果你只想要在小程序客户端（非admin端）增加一个权限控制，且角色不多，如只有3个角色（商家，用户，骑手），且权限规则基本固定，则我们可以使用更简便高效的方法。

商家特殊权限：查看自己店铺订单，发货等
骑手特殊权限：接单，查看自己接到的订单等
用户特殊权限：无

1. 编写自定义过滤器  在目录 `middleware/modules/` 新建2个过滤器 
   分别为：`shopFilter`、`riderFilter`
2. 在 `shopFilter` 的 `regExp`属性写 `^client/shop/manage/(.*)` 代表所有`client/shop/manage/`开头的云函数都会先执行`shopFilter`过滤器
3. 在 `shopFilter` 的 `main` 属性写自己的检测逻辑，如判断 `userInfo.role` 包含 `shopManage` 这个角色即放行，否则拦截
4. 把商家才能进行的特殊权限的云函数写在 `client/shop/manage/` 目录下即可

### shopFilter 示例代码

```javascript
/**
 * 店铺权限过滤器
 */

module.exports = [
  {
    id: "shopManage",
    regExp: "^client/shop/manage/(.*)",
    description: "店铺操作接口需要检测用户是否有权限",
    index: 210,// 此处必须>200 因为检测用户是否登录的过滤器的index是200
    mode:"onActionExecuting", // 可选 onActionExecuting onActionExecuted
    enable:true, // 通过设置enable=false可以关闭该中间件
    main: async function(event) {
      let { util, filterResponse } = event;
      let { vk , db, _ } = util;
      let { userInfo = {} } = filterResponse;
      let { role = [] } = userInfo;
      let res = { code : 0, msg : 'ok' };
      if(role.indexOf("shopManage") === -1){
        return { code : -1, msg : '无权限' };
      }
      return res;
    }
  }
]
```

riderFilter 同理

## 5. 只安装 vk-unicloud-router 核心库

注意需要用到npm：

1. 若你的电脑没有安装 Node.js，则无法使用 npm 命令。
2. Node.js 安装包及源码下载地址为：https://nodejs.org/en/download/
3. Node.js 安装教程：https://www.runoob.com/nodejs/nodejs-install-setup.html

### 正式安装

#### 前端（页面端）安装步骤

自己的项目导入核心库 点击前往(opens new window)

导入项目后

main.js 引入 vk-unicloud 库

```javascript
// main.js
import vk from './uni_modules/vk-unicloud';
Vue.use(vk);
```

#### 带vuex功能

store目录需要从完整框架项目中复制 点击前往(opens new window)

```javascript
// main.js
import store from './store'
const app = new Vue({
  store,
  ...App
});
```

从完整的框架项目中复制以下文件到你的新项目 点击前往(opens new window)

1. `项目根目录/common/`（整个目录）
2. `项目根目录/store/`（整个目录）
3. `项目根目录/app.config.js`

#### 完整 main.js 示例（带vuex功能）

```javascript
import Vue from 'vue'
import App from './App'
import store from './store'
import config from '@/app.config.js'

// 引入 vk框架前端
import vk from './uni_modules/vk-unicloud';
Vue.use(vk);

// 初始化 vk框架
Vue.prototype.vk.init({
  Vue,               // Vue实例
  config,      // 全局配置
});

Vue.config.productionTip = false

App.mpType = 'app'

const app = new Vue({
  store,
  ...App
});
app.$mount();
```

#### 后端（云函数端）安装步骤

如果你不使用云函数，则此步骤可以忽略。

1. 先在插件市场导入一个完整的框架项目（主要用于方便复制一些框架必须的代码文件） 点击前往(opens new window)

2. 新建一个uniapp项目（启用云开发）（也可以直接导入老项目）

3. 新建的项目导入核心库（如果已经导入了，则无需重复导入） 点击前往(opens new window)

4. 从完整的框架项目中复制以下文件到你的新项目

   1. `项目根目录/uni_modules/uni-config-center/` （整个目录）
   2. `项目根目录/uniCloud/cloudfunctions/router/` （整个目录）
   3. `项目根目录/uniCloud/cloudfunctions/database/db_init.json`(这里注意先备份下你之前的db_init.json）
   4. `项目根目录/common/`（整个目录）
   5. `项目根目录/store/`（整个目录）
   6. `项目根目录/app.config.js`

5. 修改配置中心的配置
   - uni-id 配置：uniCloud/cloudfunctions/common/uni-config-center/uni-id/config.json
   - vk-unicloud 配置：uniCloud/cloudfunctions/common/uni-config-center/vk-unicloud/index.js

6. 上传公共模块
   修改完配置文件后上传 `uniCloud/cloudfunctions/common` 目录下的所有公共模块
   1. 右键 common 下的 uni-config-center目录，点击上传公共模块
   2. 右键 common 下的 uni-id 目录，点击上传公共模块
   3. 右键 common 下的 uni-pay 目录，点击上传公共模块
   4. 右键 common 下的 vk-unicloud 目录，点击上传公共模块

7. router安装公共模块
   右键 router 云函数，点击【管理公共模块或扩展库】，全选下方的模块。

8. 上传云函数 router（右键router目录，上传部署）

9. 初始化云数据库 database/db_init.json（右键db_init.json文件，初始化云数据库）

## 6. 设置页面只在开发模式下打包

你可能会有这样的需求

在开发时，有一些测试页面，而这些测试页面不想在正式上线时被用户访问到。

以设置 pages_template 目录内的所有页面仅在开发模式下生效为例。

### 步骤

1. 在项目根目录新建文件 pages-dev.json

```json
{
  "subPackages": [{
    "root": "pages_template",
    "pages": [{
      "path": "db-test/db-test",
      "style": {
        "navigationBarTitleText": "数据库调用示例"
      }
    },
    {
      "path": "db-test/list/list",
      "style": {
        "navigationBarTitleText": "列表渲染 - 分页加载"
      }
    },
    {
      "path": "uni-id/index/index",
      "style": {
        "navigationBarTitleText": "云函数调用示例-uni-id"
      }
    },
    {
      "path": "uni-id/password/password",
      "style": {}
    },
    {
      "path": "uni-id/mobile/mobile",
      "style": {}
    },
    {
      "path": "uni-id/univerify/univerify",
      "style": {}
    },
    {
      "path": "uni-id/weixin/weixin",
      "style": {}
    },
    {
      "path": "uni-id/alipay/alipay",
      "style": {}
    },
    {
      "path": "uni-id/util/util",
      "style": {}
    },
    {
      "path": "uni-id/email/email",
      "style": {}
    },
    {
      "path": "uni-id/login/index/index",
      "style": {
        "navigationBarTitleText": "登录"
      }
    },
    {
      "path": "uni-id/login/register/register",
      "style": {
        "navigationBarTitleText": "注册"
      }
    },
    {
      "path": "uni-id/login/forget/forget",
      "style": {
        "navigationBarTitleText": "找回密码"
      }
    },
    {
      "path": "vk-vuex/vk-vuex",
      "style": {
        "navigationBarTitleText": "vuex演示示例"
      }
    },
    {
      "path": "openapi/weixin/weixin",
      "style": {}
    },
    {
      "path": "openapi/weixin/msgSecCheck/msgSecCheck",
      "style": {}
    },
    {
      "path": "openapi/weixin/imgSecCheck/imgSecCheck",
      "style": {}
    },
    {
      "path": "openapi/weixin/sendMessage/sendMessage",
      "style": {}
    },
    {
      "path": "openapi/baidu/baidu",
      "style": {}
    }]
  }]
}
```

2. 删除 pages.json 内原有的 pages_template 分包
   若删除后需要保留 "subPackages": [],

3. 项目根目录新建文件 pages.js

```javascript
const debug = process.env.NODE_ENV !== 'production';
var devPages;
try {
  // 只在开发环境中才会被HBX打包的页面
  devPages = require('./pages-dev.json');
} catch (err) {
  console.error("检测到pages-dev.json文件异常，请检查（json文件内不可以有注释，每个{}是否全部对配，是否多写了逗号,）");
  console.error(err);
  console.error("检测到pages-dev.json文件异常，请检查（json文件内不可以有注释，每个{}是否全部对配，是否多写了逗号,）");
  throw Error(err)
}
module.exports = function(pagesJson) {
  try {
    let newDevPages = JSON.parse(JSON.stringify(devPages));
    if (debug && newDevPages) {
      for (let keyName in newDevPages) {
        let item = newDevPages[keyName];
        if (Object.prototype.toString.call(item) === "[object Array]") {
          pagesJson[keyName].push(...item);
        } else if (Object.prototype.toString.call(item) === '[object Object]') {
          pagesJson[keyName] = Object.assign(pagesJson[keyName], item);
        }
      }
    }
  } catch (err) {
    console.error("pages.js运行异常，请检查");
    console.error(err);
    console.error("pages.js运行异常，请检查");
    throw Error(err)
  }
  return pagesJson;
}
```

4. 重新编译（完成）

此时你可以尝试点击hbx上方菜单 发行 微信-小程序

此时是发行（正式）模式，可以看到 pages_template 目录并没有被打包到小程序源码中。

而点击hbx上方菜单 运行 运行到小程序模拟器

此时是调式（开发）模式，可以看到 pages_template 目录被打包到小程序源码中。

## 7. 高并发抢红包示例（非事务版本）

```javascript
'use strict';
module.exports = {
  /**
   * 高并发抢红包示例
   * @url db_api/sys/concurrent 前端调用的url参数地址
   */
  main: async (event) => {
    let { data = {}, userInfo, util, filterResponse, originalParam } = event;
    let { customUtil, config, pubFun, vk, db, _ } = util;
    let { uid } = data;
    let res = { code: 0, msg: "" };
    // 业务逻辑开始-----------------------------------------------------------
    let money = 1; // 每次抢红包的金额
  
    res.num = await vk.baseDao.update({
      dbName:"vk-test",         // 红包测试表
      whereJson:{
        _id:_id,                // 红包id,
        money: _.gte(money),    // 判断当前红包金额必须>=要扣除的金额
        receive_uid: _.nin([uid]), // 只有我没领过才可以领
      },
      dataJson:{
        money: _.inc(money*-1),  // 减当前红包金额
        receive_uid: _.push(uid), // 将当前登录用户的id写入该红包，防止重复领取
      }
    });
    // 利用update + where 形成数据库乐观锁达到控制高并发下不会扣成负数。
    if(res.num <= 0){
      return { code: -1, msg: "未抢到红包，您已抢过或红包已抢完!" };
    }
    // 给用户加余额
    await vk.baseDao.update({
      dbName:"vk-test-balance",  // 用户余额测试表
      whereJson:{
        user_id : uid
      },
      dataJson:{
        balance: _.inc(money)    // 加抢到的当前红包金额
      }
    });
    res.msg = `恭喜,抢到了${money}元红包`;

    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

## 8. config升级uni-config-center模块

为了响应官方uni-config-center 模块的推动，现框架的配置中心升级为uni-config-center 模式。此次升级也是为后续升级做铺垫。

注意：本次更新目录结构改动较大，可能你漏一步后运行会报错，这时别慌，来群包解决：22466457

### 快速升级教程（优势：简单）

1. 右键项目根目录的package.json，点击从插件市场更新，即可一键升级 (但需要注意文件的替换，切记仔细对比，不要直接点合并)
2. 上传uni-config-center公共模块
3. 在cloudfunctions/router/目录执行npm i
4. 上传router云函数
5. 完成。（可以尝试运行项目看是否有报错，有问题可以进群解决：22466457，如没有问题，则可以删除原先的config模块了）

### 手动升级教程

1. 右键uni_modules目录，点击从插件市场更新所有插件
2. cloudfunctions/common/uni-config-center/ 的根目录新建目录vk-unicloud
3. cloudfunctions/common/uni-config-center/vk-unicloud/ 目录新建文件index.js（注意文件名是index.js，不是config.js）代码如下（请重新配置下配置信息）

```javascript
const uniIdConfig = require('../uni-id/config.json');
module.exports = {
  "uni":uniIdConfig,
  "vk":{
    "system":{
      // 若serviceShutdow:true，则所有云函数无法访问（适用于需要临时关闭后端服务的情况，如迁移数据）
      // 注意：本地调试时，需要重新启动本地服务才能生效。
      "serviceShutdown":false,
      "serviceShutdownDescription":"系统维护中，预计2小时恢复!"
    },
    "service": {
      // 邮箱发送服务
      "email": {
        // qq邮箱参数配置
        "qq": {
          "host": "smtp.qq.com",
          "port": 465,
          "secure": true,
          "auth": {
            "user": "你的邮箱@qq.com",
            "pass": "邮箱授权码"
          }
        }
      },
      // 日志服务
      "log":{
        // 用户登录日志
        "login":{
          "status":true  // 是否开启用户登录日志
        }
      },
      // 短信服务
      "sms": {
        // 阿里云短信服务
        "aliyun": {
          "enable": false,         // 是否使用阿里云短信代替unicloud短信发送短信验证码
          "accessKeyId": "",       // 短信密钥key
          "accessKeySecret": "",   // 短信密钥secret
          "signName": "",          // 默认签名
          "templateCode": {
            "verifyCode": ""       // 验证码短信模板 - 配合uni-id需要
          }
        }
      },
      // 开放平台api
      "openapi":{
        // 百度开放平台 (主要用于身份证识别,营业执照识别等API)
        // API Key申请地址：https://cloud.baidu.com/doc/OCR/s/rk3h7xzck 点击右上角注册
        "baidu":{
          "appid" : "",       // 对应的API Key
          "appsecret" : ""    // 对应的Secret Key
        }
      }
    },
    "db":{
      "unicloud":{
        "maxLimit" : 500,   // 最大limit限制(目前腾讯云最大1000,阿里云最大500)
        "cancelAddTime" : false,// 取消vk.baseDao.add 时自动生成_add_time和_add_time_str
      }
    },
    // 其他小程序的密钥 当需要多个小程序绑定同一服务空间,并调用小程序服务端API时需要填写 暂只支持微信小程序
    "oauth":{
      // 微信小程序
      "weixin":{
        // 密钥列表
        "list":[
          {
            "appid" : "",
            "appsecret" : ""
          },
          {
            "appid" : "",
            "appsecret" : ""
          }
        ]
      }
    }
  }
};
```

4. 重新配置下cloudfunctions/common/uni-config-center/uni-id/config.json 的uni-id配置（此文件内不可写注释）

5. 修改cloudfunctions/router/config.js 文件内的代码

替换前：

```javascript
const createConfig  = require('uni-config-center'); // 获取全局配置信息
const config = createConfig({pluginId: 'vk-unicloud'}).requireFile('index.js');
const uniID = require('uni-id');                 // uni-id 公共模块
uniID.init(config["uni"]);                       // 初始化uni-id
const uniPay = require('uni-pay');               // uni-pay 公共模块
const db = uniCloud.database();                  // 获取数据库实例
const pubFun = require('./util/pubFunction');    // 自定义公共函数
const urlrewrite = require('./util/urlrewrite'); // url重写（内部转发）
// 自定义过滤器(中间件)
const middlewareService = require('./middleware/index');
// 数据库操作中心
const daoCenter = require('./dao/index');
// 加密解密工具包
const crypto = require('crypto');
const requireFn = function(path){
  return require(path);
}
const initConfig = {
  baseDir : __dirname, // 云函数根目录地址
  requireFn,
  config,
  uniID,
  uniPay,
  db,
  pubFun,
  urlrewrite,
  middlewareService,
  daoCenter,
  crypto
  // customUtil :{
  //   mynpm1:mynpm1
  // }
};
module.exports = initConfig;
```

替换后：

```javascript
const requireFn = function(path) {
  return require(path);
}
const initConfig = {
  baseDir: __dirname, // 云函数根目录地址
  requireFn,
  customUtil :{
    // 你自己的工具包，写这里后即可听过customUtil.mynpm1调用
    // mynpm1:mynpm1
  }
};
module.exports = initConfig;
```

6. 删除cloudfunctions/router/package.json文件内的以下代码

```json
"config": "file:../common/config",
```

7. 删除cloudfunctions/router/package-lock.json 文件和 cloudfunctions/router/node_modules 目录
8. 上传uni-config-center公共模块
9. 在cloudfunctions/router/目录执行npm i
10. 上传router云函数
11. 完成。

## 9. 请求多服务空间

### 前端请求多服务空间

#### 方式一

##### 请求支付宝云空间

```javascript
const myCloud = uniCloud.init({
  provider: 'alipay', // 支付宝云
  spaceId: 'env-xxxxxx', // 服务空间id 从 https://unicloud.dcloud.net.cn/home 获取 对应SpaceId参数
  spaceAppId: "", // 支付宝云专属参数 从 https://unicloud.dcloud.net.cn/home 获取 对应SpaceAppId参数
  accessKey: "", // 支付宝云专属参数 从 https://unicloud.dcloud.net.cn/home 获取 对应AK参数 
  secretKey: "", // 支付宝云专属参数 从 https://unicloud.dcloud.net.cn/home 获取 对应SK参数
  // endpoint: "" // 选填 api网关地址，需要海外加速时使用，仅阿里云和支付宝云支持此参数
});
vk.callFunction({
  url: 'template/db_api/pub/count',
  title: '请求中...',
  uniCloud: myCloud,
  success: (data) => {
    console.log(data);
  }
});
```

##### 请求阿里云空间

```javascript
const myCloud = uniCloud.init({
  provider: 'aliyun', // 阿里云
  spaceId: 'mp-xxxxxx', // 服务空间id 从 https://unicloud.dcloud.net.cn/home 获取 对应SpaceId参数
  clientSecret: '', // 阿里云专属参数 从 https://unicloud.dcloud.net.cn/home 获取 对应ClientSecret参数
  // endpoint: "", // 选填 api网关地址，需要海外加速时使用，仅阿里云和支付宝云支持此参数
});
vk.callFunction({
  url: 'template/db_api/pub/count',
  title: '请求中...',
  uniCloud: myCloud,
  success: (data) => {
    console.log(data);
  }
});
```

##### 请求腾讯云空间

```javascript
const myCloud = uniCloud.init({
  provider: 'tencent', // 腾讯云
  spaceId: 'tcb-xxxxxx', // 服务空间id 从 https://unicloud.dcloud.net.cn/home 获取 对应SpaceId参数
});
vk.callFunction({
  url: 'template/db_api/pub/count',
  title: '请求中...',
  uniCloud: myCloud,
  success: (data) => {
    console.log(data);
  }
});
```

#### 方式二

方式二需要 vk-unicloud 核心库版本 >= 2.8.1

在 app.config.js 中新增配置 uniCloud 属性，如下

```javascript
{
  "uniCloud": {
    // 自定义默认环境（一般无需配置，除非你知道这么配置带来的意义）
    "envs": {
      // 当有设置 default 的环境时，不传env，会自动用此环境调用云函数。
      "default":{
        "provider": "", // 通用参数 支付宝云：alipay 阿里云：aliyun 腾讯云：tencent 
        "spaceId": "", // 通用参数 从 https://unicloud.dcloud.net.cn/home 获取 对应SpaceId参数
        "clientSecret": "", // 阿里云专属参数 从 https://unicloud.dcloud.net.cn/home 获取 对应ClientSecret参数
        "spaceAppId": "", // 支付宝云专属参数 从 https://unicloud.dcloud.net.cn/home 获取 对应SpaceAppId参数
        "accessKey": "", // 支付宝云专属参数 从 https://unicloud.dcloud.net.cn/home 获取 对应AK参数 
        "secretKey": "", // 支付宝云专属参数 从 https://unicloud.dcloud.net.cn/home 获取 对应SK参数
        "endpoint": "" // 选填 api网关地址，需要海外加速时使用，仅阿里云和支付宝云支持此参数
      },
      // a环境
      "a":{
        "provider": "", // 通用参数 支付宝云：alipay 阿里云：aliyun 腾讯云：tencent
        "spaceId": "", // 通用参数 从 https://unicloud.dcloud.net.cn/home 获取 对应SpaceId参数
        "clientSecret": "", // 阿里云专属参数 从 https://unicloud.dcloud.net.cn/home 获取 对应ClientSecret参数
        "spaceAppId": "", // 支付宝云专属参数 从 https://unicloud.dcloud.net.cn/home 获取 对应SpaceAppId参数
        "accessKey": "", // 支付宝云专属参数 从 https://unicloud.dcloud.net.cn/home 获取 对应AK参数 
        "secretKey": "", // 支付宝云专属参数 从 https://unicloud.dcloud.net.cn/home 获取 对应SK参数
        "endpoint": "" // 选填 api网关地址，需要海外加速时使用，仅阿里云和支付宝云支持此参数
      },
      // b环境
      "b":{
        "provider": "", // 通用参数 支付宝云：alipay 阿里云：aliyun 腾讯云：tencent
        "spaceId": "", // 通用参数 从 https://unicloud.dcloud.net.cn/home 获取 对应SpaceId参数
        "clientSecret": "", // 阿里云专属参数 从 https://unicloud.dcloud.net.cn/home 获取 对应ClientSecret参数
        "spaceAppId": "", // 支付宝云专属参数 从 https://unicloud.dcloud.net.cn/home 获取 对应SpaceAppId参数
        "accessKey": "", // 支付宝云专属参数 从 https://unicloud.dcloud.net.cn/home 获取 对应AK参数 
        "secretKey": "", // 支付宝云专属参数 从 https://unicloud.dcloud.net.cn/home 获取 对应SK参数
        "endpoint": "" // 选填 api网关地址，需要海外加速时使用，仅阿里云和支付宝云支持此参数
      }
    }
  }
}
```

##### 前端调用

使用 default 环境调用云函数（如果没有配置 default 环境，则使用项目绑定的云环境调用云函数）

```javascript
vk.callFunction({
  url: '',
  title: '请求中...',
  data: {

  },
  success: (data) => {

  }
});
```

使用 a 环境调用云函数

```javascript
vk.callFunction({
  url: '',
  title: '请求中...',
  env: 'a', // 使用a环境调用云函数
  data: {

  },
  success: (data) => {

  }
});
```

使用 b 环境调用云函数

```javascript
// 使用 b 环境调用云函数
vk.callFunction({
  url: '',
  title: '请求中...',
  env: 'b', // 使用b环境调用云函数
  data: {

  },
  success: (data) => {

  }
});
```

当有设置 default 的环境时，不传env，会自动用此环境调用云函数。

#### vk.callFunction 参数说明：

点击查看(opens new window)

#### uniCloud.init 参数说明

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| provider | String | 是 | - | alipay、aliyun、tencent |
| spaceId | String | 是 | - | 服务空间ID，注意是服务空间ID，不是服务空间名称 |
| clientSecret | String | 是 | - | 仅阿里云支持，可以在uniCloud控制台 (opens new window)服务空间列表中查看 |
| accessKey | String | 是 | - | 仅支付宝云支持, 可以在uniCloud控制台 (opens new window)服务空间详情中查看 |
| secretKey | String | 是 | - | 仅支付宝云支持, 可以在uniCloud控制台 (opens new window)服务空间详情中查看 |
| spaceAppId | String | 是 | - | 仅支付宝云支持, 可以在uniCloud控制台 (opens new window)服务空间详情中查看 |
| endpoint | String | 否 | 阿里云：https://api.next.bspapp.com 支付宝云：https://{spaceId}.api-hz.cloudbasefunction.cn | 服务空间地址，仅阿里云与支付宝云支持。 |

### 云函数端请求多服务空间

#### 方式一

云函数请求另外一个服务空间的数据库

目前仅限腾讯云支持

```javascript
const myDB = uniCloud.database({
  provider: 'tencent', // 目前只支持tencent
  spaceId: 'xxxx-yyy'
})
res = await vk.baseDao.select({
  db: myDB,
  dbName: "vk-test",
  pageIndex: 1,
  pageSize: 100,
  whereJson: {

  }
});
```

#### uniCloud.database 参数说明

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| provider | String | 是 | - | 目前只能是 tencent |
| spaceId | String | 是 | - | 服务空间ID，注意是服务空间ID，不是服务空间名称 |

#### 方式二

云函数请求另外一个服务空间的云函数

目前仅限腾讯云支持

```javascript
const myCloud = uniCloud.init({
  provider: 'tencent', // 目前只支持tencent
  spaceId: 'xxxx-yyy'
})
//通过 myCloud 实例调用API
myCloud.callFunction({
  name: "router", // 大云函数名称
  data: {
    $url: "云函数路由的路径",
    data: {
      // 请求参数
    }
  }
});
```

## 10. 小程序体验版和正式版无法请求云函数

### 解决办法

将以下域名加入小程序域名白名单

#### request合法域名

- 支付宝云：`https://{spaceId}.api-hz.cloudbasefunction.cn;` 这里的{spaceId}替换成你支付宝云的空间id
- 阿里云：`https://api.next.bspapp.com;`
- 腾讯云（2个都要加）：
  - `https://tcb-api.tencentcloudapi.com;`
  - `https://{spaceId}.ap-shanghai.tcb-api.tencentcloudapi.com;` 这里的{spaceId}替换成你腾讯云的空间id

#### uploadFile合法域名

- 支付宝云：`https://u.object.cloudrun.cloudbaseapp.cn;`
- 阿里云：前往unicloud控制台查看
- 腾讯云：`https://cos.ap-shanghai.myqcloud.com;`

#### download合法域名

- 支付宝云：`https://{spaceId}.normal.cloudstatic.cn;` 这里的{spaceId}替换成你支付宝云的空间id
- 阿里云：前往unicloud控制台查看
- 腾讯云：需要从云存储下载文件的时候才需要配置，不同服务空间域名不同，可以在web控制台查看文件详情里面看到url域名

### 步骤：

进入小程序后台 https://mp.weixin.qq.com 点击前往(opens new window)

点击 开发管理 - 开发设置 - 服务器域名 点击 修改

在 request 合法域名中添加域名。

在 uploadFile 合法域名中添加域名。

在 download 合法域名中添加域名。

### 添加域名后还是无法请求?

可能是缓存问题，删除微信APP里对应的小程序（清空缓存），微信开发者工具点下清空全部缓存，并重启微信开发者工具（点重新打开项目），最后重新上传体验版。

如果上面的清空缓存还是不行，则点击微信开发者工具右上角【详情】，把不校验合法域名前的勾去掉，尝试在微信开发者工具里访问云函数，此时会提示还有哪个域名没有加入白名单。然后把提示的域名加入白名单

重复步骤1和步骤2，直到在步骤2时没有提示需要加白名单的域名时才算成功。

### 特别注意

如果步骤2上提示的域名是一个ip 127.0.0.1，则代表你点的HBX的【运行】菜单上传的体验版，你应该点HBX上方菜单【发行】-【微信小程序】的方式上传体验版。

即【运行】菜单打包出来的代码就只能本地调试用，体验版和正式版用不了，只有【发行】菜单打包出来的代码才能发体验版和正式版。

### 微信小程序体验版和正式版无法获取昵称和头像等API的调用?

需要在你的隐私协议里明确声明需要获取昵称和头像等其他API的使用

## 11. 云函数请求报跨域错误

### 解决办法：

将你用到的域名全部加入到unicloud web控制台的跨域白名单

如:

- 自己的域名1
- 自己的域名2

同时顺便把localhost也加入了

- localhost:8080
- localhost:8081
- localhost:8082
- localhost:8083
- localhost:3000
- localhost:3001
- localhost:3002
- localhost:3003

现在可以直接加 * 加入白名单了（代表对任何域名跨域）

### 步骤：

1. 进入unicloud web控制台 https://unicloud.dcloud.net.cn/domain
2. 点击跨域配置 - 新增域名
3. 输入域名

## 12. 微信小程序配置教程

1. 打开文件 manifest.json，点击【微信小程序配置】，配置 appid （在微信小程序官方的后台获取）
2. 打开文件 cloudfunctions/common/uni-config-center/uni-id/config.json(没有则新建)（注意这里是config.json)

配置 mp-weixin 内的 appid 和 appsecret（在微信小程序官方的后台获取）

```json
"mp-weixin": {
  "oauth" : {
    "weixin" : {
      "appid" : "weixin appid",
      "appsecret" : "weixin appsecret"
    }
  }
},
```

3. 将以下域名加入小程序 request 合法域名（为了省事，可以两个都加了）

- 支付宝云：`https://{spaceId}.api-hz.cloudbasefunction.cn;` 这里的{spaceId}替换成你支付宝云的空间id
- 阿里云：`https://api.next.bspapp.com;`
- 腾讯云（2个都要加）：
  - `https://tcb-api.tencentcloudapi.com;`
  - `https://{spaceId}.ap-shanghai.tcb-api.tencentcloudapi.com;` 这里的{spaceId}替换成你腾讯云的空间id

### 步骤：

1. 进入小程序后台 https://mp.weixin.qq.com/wxamp/devprofile/get_profile 点击前往(opens new window)
2. 点击 开发管理 - 开发设置 - 服务器域名 点击 修改
3. 在 request 合法域名中添加域名。
4. 配置完后，记得上传 公共模块 和 云函数 (如果第一次上传，需要上传全部的 公共模块 和 云函数)