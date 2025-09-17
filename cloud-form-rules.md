# 云端优雅的写表单验证

当表单参数不多时，这样写并无不雅的地方。

```javascript
if (!data.username) return { code:-1, msg:"用户名不能为空" }
if (!data.password) return { code:-1, msg:"密码不能为空" }
```

但是如果参数有10个以上呢？还这样写吗？

```javascript
if (!data.param1) return { code:-1, msg:"XXX不能为空" }
if (data.param2<=0) return { code:-1, msg:"XXX必须大于0" }
if (data.param3>100) return { code:-1, msg:"XXX必须小于100" }
if (!data.param4) return { code:-1, msg:"XXX不能为空" }
if (!data.param5) return { code:-1, msg:"XXX不能为空" }
if (!data.param6) return { code:-1, msg:"XXX不能为空" }
if (!data.param7) return { code:-1, msg:"XXX不能为空" }
if (!data.param8) return { code:-1, msg:"XXX不能为空" }
if (!data.param9) return { code:-1, msg:"XXX不能为空" }
...
```

上面的代码是不是很繁琐？

## 如何优化？

优化思路：

首先，前端一般都会写一下vue表单验证，毕竟前端的验证是没有网络请求，及时响应的

但是后端经验告诉我们，不可信任前端传过来的参数，因此后端也需要表单验证。

那么既然前端已经写好了，如果后端代码能直接复用前端代码是不是在一定程度上简化了后端工作量？

为此，VK框架云函数中实现了跟前端表单验证参数一模一样的功能函数 `vk.pubfn.formValidate`。

接下来重点介绍 `vk.pubfn.formValidate`

## 云函数

```javascript
'use strict';
module.exports = {
  /**
   * 此函数名称
   * @url user/pub/test1 前端调用的url参数地址
   * data 请求参数
   * @param {String} params1  参数1
   */
  main: async (event) => {
    let { data = {}, userInfo, util, originalParam } = event;
    let { customUtil, config, pubFun, vk, db, _ } = util;
    let { uid } = data;
    let res = { code: 0, msg: "" };
    // 业务逻辑开始-----------------------------------------------------------
    
    // 验证规则开始 -----------------------------------------------------------
    let rules = {
      username: [
        { required: true, validator: vk.pubfn.validator("username"), message: '用户名以字母开头，长度在3~32之间，只能包含字母、数字和下划线', trigger: ['blur','change'] }
      ],
      nickname: [
        { required: true, message: '昵称为必填字段', trigger: 'blur' },
        { min: 2, max: 20, message: '昵称长度在 2 到 20 个字符', trigger: 'blur' }
      ],
      password: [
        { validator:vk.pubfn.validator("password"), message: '密码长度在6~18之间，只能包含字母、数字和下划线', trigger: 'blur' }
      ],
      mobile: [
        { validator: vk.pubfn.validator("mobile"), message: '手机号格式错误', trigger: 'blur' }
      ]
    };
    // 验证规则结束 -----------------------------------------------------------
    // 开始进行验证
    let formRulesRes = vk.pubfn.formValidate({
      data: data,
      rules: rules
    });
    if (formRulesRes.code !== 0) {
      // 表单验证未通过
      return formRulesRes;
    }
    // 表单验证通过，下面写自己的业务逻辑

    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

同时为了让表单验证和业务逻辑代码独立，可以参考 `router/service/admin/system/role/sys/add.js` 此云函数内的写法。最终代码效果：

```javascript
const formRules = require("../util/formRules.js");
let formRulesRes = await formRules.add(event);
if (formRulesRes.code !== 0) {
  // 表单验证未通过
  return formRulesRes;
}
// 表单验证通过，下面写自己的业务逻辑
```

## 云对象

### 云对象-表单验证和业务逻辑代码写在一起的示例

```javascript
'use strict';
// 涉及的表名
const dbName = {
  //test: "vk-test", // 测试表
};

var db = uniCloud.database(); // 全局数据库引用
var _ = db.command; // 数据库操作符
var $ = _.aggregate; // 聚合查询操作符

var cloudObject = {
  isCloudObject: true, // 标记为云对象模式
  /**
   * 模板函数
   * @url client/muban.test 前端调用的url参数地址
   */
  test: async function(data) {
    let { uid } = this.getClientInfo(); // 获取客户端信息
    let res = { code: 0, msg: '' };
    // 业务逻辑开始-----------------------------------------------------------
    
    // 验证规则开始 -----------------------------------------------------------
    let rules = {
      username: [
        { required: true, validator: vk.pubfn.validator("username"), message: '用户名以字母开头，长度在3~32之间，只能包含字母、数字和下划线', trigger: 'blur' }
      ],
      nickname: [
        { required: true, message: '昵称为必填字段', trigger: 'blur' },
        { min: 2, max: 20, message: '昵称长度在 2 到 20 个字符', trigger: 'blur' }
      ],
      password: [
        { validator:vk.pubfn.validator("password"), message: '密码长度在6~18之间，只能包含字母、数字和下划线', trigger: 'blur' }
      ],
      mobile: [
        { validator: vk.pubfn.validator("mobile"), message: '手机号格式错误', trigger: 'blur' }
      ]
    };
    // 验证规则结束 -----------------------------------------------------------
    // 开始进行验证
    let formRulesRes = vk.pubfn.formValidate({
      data: data,
      rules: rules
    });
    if (formRulesRes.code !== 0) {
      // 表单验证未通过
      return formRulesRes;
    }
    // 表单验证通过，下面写自己的业务逻辑
    
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
};

module.exports = cloudObject;
```

### 云对象-表单验证和业务逻辑代码独立版示例

```javascript
'use strict';
// 涉及的表名
const dbName = {
  //test: "vk-test", // 测试表
};

var db = uniCloud.database(); // 全局数据库引用
var _ = db.command; // 数据库操作符
var $ = _.aggregate; // 聚合查询操作符

var cloudObject = {
  isCloudObject: true, // 标记为云对象模式
  /**
   * 模板函数
   * @url client/muban.test 前端调用的url参数地址
   */
  test: async function(data) {
    let { uid } = this.getClientInfo(); // 获取客户端信息
    let res = { code: 0, msg: '' };
    // 业务逻辑开始-----------------------------------------------------------
    const formRules = require("./util/formRules.js"); // 基于该云对象文件所在路径的相对路径
    let formRulesRes = await formRules.add(data);
    if (formRulesRes.code !== 0) {
      // 表单验证未通过
      return formRulesRes;
    }
    // 表单验证通过，下面写自己的业务逻辑
    
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
};

module.exports = cloudObject;
```

### ./util/formRules.js

```javascript
'use strict';
/**
 * 表单验证
 */
class Util {
  constructor() {}
  /**
   * 添加
   */
  async add(data={}) {
    let res = { code: 0, msg: '' };
    // 验证规则开始 -----------------------------------------------------------
    let rules = {
      username: [
        { required: true, validator: vk.pubfn.validator("username"),
          message: '用户名以字母开头，长度在3~32之间，只能包含字母、数字和下划线', trigger: ['blur','change'] }
      ],
      nickname: [
        { required: true, message: '昵称为必填字段', trigger: 'blur' },
        { min: 2, max: 20, message: '昵称长度在 2 到 20 个字符', trigger: 'blur' }
      ],
      password: [
        { validator:vk.pubfn.validator("password"), message: '密码长度在6~18之间，只能包含字母、数字和下划线', trigger: 'blur' }
      ],
      mobile: [
        { validator: vk.pubfn.validator("mobile"), message: '手机号格式错误', trigger: 'blur' }
      ],
      email: [
        { validator: vk.pubfn.validator("email"), message: '邮箱格式错误', trigger: 'blur' }
      ],
    };
    // 验证规则结束 -----------------------------------------------------------

    // 开始进行验证
    res = vk.pubfn.formValidate({
      data: data,
      rules: rules
    });
    // 返回验证结果
    return res;
  }
}
module.exports = new Util
```

### 云对象-利用_before实现表单验证

```javascript
'use strict';
var vk; // 全局vk实例
// 涉及的表名
const dbName = {
  //test: "vk-test", // 测试表
};

var db = uniCloud.database(); // 全局数据库引用
var _ = db.command; // 数据库操作符
var $ = _.aggregate; // 聚合查询操作符

var cloudObject = {
  isCloudObject: true, // 标记为云对象模式
  /**
   * 请求前处理，主要用于调用方法之前进行预处理，一般用于拦截器、统一的身份验证、参数校验、定义全局对象等。
   * 文档地址：https://vkdoc.fsq.pub/client/uniCloud/cloudfunctions/cloudObject.html#before-预处理
   */
  _before: async function() {
    vk = this.vk; // 将vk定义为全局对象
    // 引入表单验证
    const formRules = require("./util/formRules.js"); // 基于该云对象文件所在路径的相对路径
    // 获取当前运行的函数
    const methodName = this.getMethodName();
    // 如果该函数有表单验证的方法，则进行验证
    if (typeof formRules[methodName] === "function") {
      // 进行表单验证
      let formRulesRes = await formRules[methodName].call(this);
      if (formRulesRes.code !== 0) {
        // 表单验证未通过
        return formRulesRes;
      }
    }
  },
  /**
   * 模板函数
   * @url client/muban.add 前端调用的url参数地址
   */
  test: async function(data) {
    let { uid } = this.getClientInfo(); // 获取客户端信息
    let res = { code: 0, msg: '' };
   
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  },
  /**
   * 模板函数
   * @url client/muban.add 前端调用的url参数地址
   */
  add: async function(data) {
    let { uid } = this.getClientInfo(); // 获取客户端信息
    let res = { code: 0, msg: '' };
   
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
};

module.exports = cloudObject;
```

### ./util/formRules.js

```javascript
'use strict';
/**
 * 表单验证
 */
class Util {
  constructor() {}
  /**
   * 验证test函数
   */
  async test() {
    const data = this.getParams();
    let res = { code: 0, msg: '' };
    // 验证规则开始 -----------------------------------------------------------
    let rules = {
      username: [{
        required: true,
        validator: vk.pubfn.validator("username"),
        message: '用户名以字母开头，长度在3~32之间，只能包含字母、数字和下划线',
        trigger: ['blur','change']
      }],
      nickname: [
        { required: true, message: '昵称为必填字段', trigger: ['blur','change'] },
        { min: 2, max: 20, message: '昵称长度在 2 到 20 个字符', trigger: ['blur','change'] }
      ]
    };
    // 验证规则结束 -----------------------------------------------------------

    // 开始进行验证
    res = vk.pubfn.formValidate({
      data: data,
      rules: rules
    });
    // 返回验证结果
    return res;
  },
  /**
   * 验证add函数
   */
  async add() {
    const data = this.getParams();
    let res = { code: 0, msg: '' };
    // 验证规则开始 -----------------------------------------------------------
    let rules = {
      nickname: [
        { required: true, message: '昵称为必填字段', trigger: 'blur' },
        { min: 2, max: 20, message: '昵称长度在 2 到 20 个字符', trigger: 'blur' }
      ]
    };
    // 验证规则结束 -----------------------------------------------------------
  
    // 开始进行验证
    res = vk.pubfn.formValidate({
      data: data,
      rules: rules
    });
    // 返回验证结果
    return res;
  }
}
module.exports = new Util
```

## rules详解

rules跟前端vue表单验证写法是完全一样的

以下是常用的几个表单验证示例。

注意：trigger 属性不写也可以，trigger只在前端生效。（不过一般都是先把前端验证写完，云函数直接复制前端的验证）

```javascript
{
  user_id: [
    // 必填
    { required: true, message: "用户ID不能为空", trigger: ['blur','change'] }
  ],
  nickname: [
    { required: true, message: '昵称为必填字段', trigger: 'blur' },
    { min: 2, max: 20, message: '昵称长度在 2 到 20 个字符', trigger: 'blur' }
  ],
  money: [
    // 必填
    { required: true, message: "金额不能为空", trigger: ['blur','change'] },
    // 必须是数字
    { type: "number", message: "金额必须是数字", trigger: ['blur','change'] }
  ],
   mobile: [
    // 必填
    { required:true,  message: '手机号不能为空', trigger: 'blur' },
    // 必须是手机号格式
    { validator: vk.pubfn.validator("mobile"),  message: '手机号格式错误', trigger: 'blur' }
  ],
  card: [
    // 身份证
    { validator: vk.pubfn.validator("card"),  message: '身份证格式错误', trigger: 'blur' }
  ],
  QQ: [
    // qq
    { validator: vk.pubfn.validator("QQ"),  message: 'QQ号格式错误', trigger: 'blur' }
  ],
  URL: [
    // URL
    { validator: vk.pubfn.validator("URL"),  message: 'URL格式错误', trigger: 'blur' }
  ],
  IP: [
    // IP
    { validator: vk.pubfn.validator("IP"),  message: 'IP格式错误', trigger: 'blur' }
  ],
  english: [
    // 英文
    { validator: vk.pubfn.validator("english"),  message: '只能输入英文', trigger: 'blur' }
  ],
  englishnumber: [
    // 只能是英文或数字
    { validator: vk.pubfn.validator("english+number"),  message: '只能输入英文或数字', trigger: 'blur' }
  ],
  englishnumber2: [
    // 只能是英文、数字、下划线
    { validator: vk.pubfn.validator("english+number+_"),  message: '只能输入英文、数字、下划线', trigger: 'blur' }
  ],
  chinese: [
    // 中文
    { validator: vk.pubfn.validator("chinese"),  message: '只能输入中文', trigger: 'blur' }
  ],
  lower: [
    // 小写字母
    { validator: vk.pubfn.validator("lower"),  message: '只能输入小写字母', trigger: 'blur' }
  ],
  upper: [
    // 大写字母
    { validator: vk.pubfn.validator("upper"),  message: '只能输入大写字母', trigger: 'blur' }
  ],
  HTML: [
    // HTML
    { validator: vk.pubfn.validator("HTML"),  message: 'html格式错误', trigger: 'blur' }
  ],
  pwd2: [
    // 自定义
    { validator: (rule, value, callback)=>{
      if (value === '') {
        callback(new Error('请再次输入密码'));
      } else if (value !== data.pwd) { // 需要特別注意，如果用到跟其他变量做对比，前端写法是 this.form1.data.pwd 而云端写法是 data.pwd
        callback(new Error('两次输入密码不一致!'));
      } else {
        callback();
      }
    }, trigger: ['blur','change'] }
  ]
}
```