# 云函数开放平台API示例

本文档展示了 `template/openapi/` 目录下的开放平台API云函数示例代码。

## 微信开放平台API

### msgSecCheck.js - 文本内容安全检测

```javascript
'use strict';
module.exports = {
  /**
   * 文本违规检测示例
   * @url template/openapi/weixin/pub/msgSecCheck 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util } = event;
    let { config, vk, db, _ } = util;
    let { uid } = data;
    let res = { code: 0, msg: 'ok' };
    // 业务逻辑开始-----------------------------------------------------------
    let {
      content,
      version,
      scene,
      openid,
      title,
      nickname,
      signature,
    } = data;
    // 实际业务中，content可能是评论的内容等
    let msgSecCheckRes = await vk.openapi.weixin.security.msgSecCheck({
      content,
      version,
      scene,
      openid,
      title,
      nickname,
      signature,
    });
    if (msgSecCheckRes.code !== 0) {
      // 未通过检测
      return msgSecCheckRes;
    }
    res.result = msgSecCheckRes;
    // 若通过检测，则继续执行自己的业务
    // ...
    // ...

    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### imgSecCheck.js - 图片内容安全检测

```javascript
'use strict';
module.exports = {
  /**
   * 图片违规检测示例
   * @url template/openapi/weixin/pub/imgSecCheck 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util } = event;
    let { config, vk, db, _ } = util;
    let { uid } = data;
    let res = { code: 0, msg: 'ok' };
    // 业务逻辑开始-----------------------------------------------------------
    let { imageUrl, openid, scene } = data;
    
    // 图片内容安全检测
    let imgSecCheckRes = await vk.openapi.weixin.security.imgSecCheck({
      imageUrl,
      openid,
      scene
    });
    
    if (imgSecCheckRes.code !== 0) {
      // 图片未通过检测
      return imgSecCheckRes;
    }
    
    res.result = imgSecCheckRes;
    // 若通过检测，则继续执行自己的业务
    // 例如保存图片信息到数据库等
    
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### sendTemplateMessage.js - 发送模板消息

```javascript
'use strict';
module.exports = {
  /**
   * 发送模板消息示例
   * @url template/openapi/weixin/pub/sendTemplateMessage 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util } = event;
    let { config, vk, db, _ } = util;
    let { uid } = data;
    let res = { code: 0, msg: 'ok' };
    // 业务逻辑开始-----------------------------------------------------------
    let {
      openid,
      templateId,
      formId,
      page,
      data: templateData,
      emphasisKeyword
    } = data;
    
    // 发送模板消息
    let sendResult = await vk.openapi.weixin.message.sendTemplateMessage({
      openid,
      templateId,
      formId,
      page,
      data: templateData,
      emphasisKeyword
    });
    
    if (sendResult.code !== 0) {
      return sendResult;
    }
    
    res.result = sendResult;
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### getTemplateList.js - 获取模板列表

```javascript
'use strict';
module.exports = {
  /**
   * 获取模板列表示例
   * @url template/openapi/weixin/pub/getTemplateList 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util } = event;
    let { config, vk, db, _ } = util;
    let { uid } = data;
    let res = { code: 0, msg: 'ok' };
    // 业务逻辑开始-----------------------------------------------------------
    
    // 获取模板列表
    let templateList = await vk.openapi.weixin.message.getTemplateList();
    
    res.list = templateList;
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

## 支付宝开放平台API

### code2Session.js - 支付宝登录凭证校验

```javascript
'use strict';
module.exports = {
  /**
   * 支付宝登录凭证校验
   * @url template/openapi/alipay/pub/code2Session 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util } = event;
    let { config, vk, db, _ } = util;
    let { uid } = data;
    let res = { code: 0, msg: 'ok' };
    // 业务逻辑开始-----------------------------------------------------------
    let { code } = data;
    
    // 支付宝登录凭证校验
    let sessionInfo = await vk.openapi.alipay.auth.code2Session({
      code
    });
    
    if (sessionInfo.code !== 0) {
      return sessionInfo;
    }
    
    res.sessionInfo = sessionInfo;
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### getMiniCode.js - 获取支付宝小程序码

```javascript
'use strict';
module.exports = {
  /**
   * 获取支付宝小程序码
   * @url template/openapi/alipay/pub/getMiniCode 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util } = event;
    let { config, vk, db, _ } = util;
    let { uid } = data;
    let res = { code: 0, msg: 'ok' };
    // 业务逻辑开始-----------------------------------------------------------
    let {
      path,
      width = 430,
      color = { r: 0, g: 0, b: 0 },
      isHyaline = false
    } = data;
    
    // 获取支付宝小程序码
    let qrCode = await vk.openapi.alipay.app.getMiniCode({
      path,
      width,
      color,
      isHyaline
    });
    
    res.qrCode = qrCode;
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

## 抖音开放平台API

### code2Session.js - 抖音登录凭证校验

```javascript
'use strict';
module.exports = {
  /**
   * 抖音登录凭证校验
   * @url template/openapi/douyin/pub/code2Session 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util } = event;
    let { config, vk, db, _ } = util;
    let { uid } = data;
    let res = { code: 0, msg: 'ok' };
    // 业务逻辑开始-----------------------------------------------------------
    let { code, anonymousCode } = data;
    
    // 抖音登录凭证校验
    let sessionInfo = await vk.openapi.douyin.auth.code2Session({
      code,
      anonymousCode
    });
    
    if (sessionInfo.code !== 0) {
      return sessionInfo;
    }
    
    res.sessionInfo = sessionInfo;
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### imgSecCheck.js - 抖音图片内容安全检测

```javascript
'use strict';
module.exports = {
  /**
   * 抖音图片违规检测
   * @url template/openapi/douyin/pub/imgSecCheck 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util } = event;
    let { config, vk, db, _ } = util;
    let { uid } = data;
    let res = { code: 0, msg: 'ok' };
    // 业务逻辑开始-----------------------------------------------------------
    let { imageUrl, openid } = data;
    
    // 抖音图片内容安全检测
    let imgSecCheckRes = await vk.openapi.douyin.security.imgSecCheck({
      imageUrl,
      openid
    });
    
    if (imgSecCheckRes.code !== 0) {
      return imgSecCheckRes;
    }
    
    res.result = imgSecCheckRes;
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### msgSecCheck.js - 抖音文本内容安全检测

```javascript
'use strict';
module.exports = {
  /**
   * 抖音文本违规检测
   * @url template/openapi/douyin/pub/msgSecCheck 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util } = event;
    let { config, vk, db, _ } = util;
    let { uid } = data;
    let res = { code: 0, msg: 'ok' };
    // 业务逻辑开始-----------------------------------------------------------
    let { content, openid } = data;
    
    // 抖音文本内容安全检测
    let msgSecCheckRes = await vk.openapi.douyin.security.msgSecCheck({
      content,
      openid
    });
    
    if (msgSecCheckRes.code !== 0) {
      return msgSecCheckRes;
    }
    
    res.result = msgSecCheckRes;
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

## QQ开放平台API

### code2Session.js - QQ登录凭证校验

```javascript
'use strict';
module.exports = {
  /**
   * QQ登录凭证校验
   * @url template/openapi/qq/pub/code2Session 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util } = event;
    let { config, vk, db, _ } = util;
    let { uid } = data;
    let res = { code: 0, msg: 'ok' };
    // 业务逻辑开始-----------------------------------------------------------
    let { code } = data;
    
    // QQ登录凭证校验
    let sessionInfo = await vk.openapi.qq.auth.code2Session({
      code
    });
    
    if (sessionInfo.code !== 0) {
      return sessionInfo;
    }
    
    res.sessionInfo = sessionInfo;
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### imgSecCheck.js - QQ图片内容安全检测

```javascript
'use strict';
module.exports = {
  /**
   * QQ图片违规检测
   * @url template/openapi/qq/pub/imgSecCheck 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util } = event;
    let { config, vk, db, _ } = util;
    let { uid } = data;
    let res = { code: 0, msg: 'ok' };
    // 业务逻辑开始-----------------------------------------------------------
    let { imageUrl, openid } = data;
    
    // QQ图片内容安全检测
    let imgSecCheckRes = await vk.openapi.qq.security.imgSecCheck({
      imageUrl,
      openid
    });
    
    if (imgSecCheckRes.code !== 0) {
      return imgSecCheckRes;
    }
    
    res.result = imgSecCheckRes;
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

### msgSecCheck.js - QQ文本内容安全检测

```javascript
'use strict';
module.exports = {
  /**
   * QQ文本违规检测
   * @url template/openapi/qq/pub/msgSecCheck 前端调用的url参数地址
   * data 请求参数 说明
   * res 返回参数说明
   * @params {Number} code 错误码，0表示成功
   * @params {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util } = event;
    let { config, vk, db, _ } = util;
    let { uid } = data;
    let res = { code: 0, msg: 'ok' };
    // 业务逻辑开始-----------------------------------------------------------
    let { content, openid } = data;
    
    // QQ文本内容安全检测
    let msgSecCheckRes = await vk.openapi.qq.security.msgSecCheck({
      content,
      openid
    });
    
    if (msgSecCheckRes.code !== 0) {
      return msgSecCheckRes;
    }
    
    res.result = msgSecCheckRes;
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}
```

## 最佳实践

### 1. 错误处理与重试机制

```javascript
// 开放平台API调用错误处理
async function callOpenAPIWithRetry(apiFunction, params, maxRetries = 3) {
  let retries = 0;
  while (retries < maxRetries) {
    try {
      const result = await apiFunction(params);
      if (result.code === 0) {
        return result;
      }
      
      // 如果是网络错误或限流错误，进行重试
      if (result.code === -1 || result.code === 45009) {
        retries++;
        await new Promise(resolve => setTimeout(resolve, 1000 * retries)); // 指数退避
        continue;
      }
      
      return result;
    } catch (error) {
      retries++;
      if (retries >= maxRetries) {
        throw error;
      }
      await new Promise(resolve => setTimeout(resolve, 1000 * retries));
    }
  }
}
```

### 2. 参数验证与格式化

```javascript
// 验证开放平台API参数
function validateOpenAPIParams(params, requiredFields) {
  for (const field of requiredFields) {
    if (!params[field]) {
      return { code: -1, msg: `参数 ${field} 不能为空` };
    }
  }
  return null;
}

// 格式化返回数据
function formatOpenAPIResponse(data, options = {}) {
  const { success = true, message = 'success' } = options;
  return {
    code: success ? 0 : -1,
    msg: message,
    data: data
  };
}
```

### 3. 缓存策略

```javascript
// 使用Redis缓存开放平台访问令牌
async function getCachedAccessToken(platform, appId) {
  const cacheKey = `access_token:${platform}:${appId}`;
  const cachedToken = await vk.redis.get(cacheKey);
  
  if (cachedToken) {
    return cachedToken;
  }
  
  // 从开放平台获取新的访问令牌
  const newToken = await vk.openapi[platform].auth.getAccessToken();
  
  // 缓存令牌，设置过期时间（比实际过期时间稍短）
  await vk.redis.setex(cacheKey, newToken.expires_in - 60, newToken.access_token);
  
  return newToken.access_token;
}
```

### 4. 日志记录与监控

```javascript
// 记录开放平台API调用日志
async function logOpenAPICall(platform, apiName, params, result, responseTime) {
  await vk.baseDao.add({
    dbName: 'openapi_logs',
    dataJson: {
      platform,
      api_name: apiName,
      params: JSON.stringify(params),
      result_code: result.code,
      result_msg: result.msg,
      response_time: responseTime,
      create_time: Date.now()
    }
  });
}
```

## 常见问题解决方案

### 1. 访问令牌过期
- 实现自动刷新令牌机制
- 使用缓存减少令牌获取次数

### 2. API调用频率限制
- 实现请求队列和限流控制
- 使用指数退避算法进行重试

### 3. 网络超时处理
- 设置合理的超时时间
- 实现重试机制

### 4. 数据安全
- 对敏感数据进行加密处理
- 验证请求来源防止恶意调用

### 5. 多平台兼容性
```javascript
// 多平台统一接口封装
class OpenAPIService {
  constructor(platform) {
    this.platform = platform;
  }
  
  async code2Session(code) {
    switch (this.platform) {
      case 'weixin':
        return vk.openapi.weixin.auth.code2Session({ code });
      case 'alipay':
        return vk.openapi.alipay.auth.code2Session({ code });
      case 'douyin':
        return vk.openapi.douyin.auth.code2Session({ code });
      case 'qq':
        return vk.openapi.qq.auth.code2Session({ code });
      default:
        throw new Error('不支持的平台');
    }
  }
  
  // 其他统一方法...
}
```

通过以上示例和最佳实践，您可以高效地使用和管理开放平台API。