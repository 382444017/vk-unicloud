# 固定出口IP

## 支付宝云空间

直接把下面的ip都加进去即可。

**固定出口IP列表**

```
47.97.38.108
112.124.10.115
```

注意：上面的IP是云端运行时的IP，若是本地运行云函数，则需要把自己电脑的外网IP加进去

## 腾讯云空间

## 阿里云空间

**阿里云必须使用`uniCloud.httpProxyForEip`发送请求才会固定出口ip**，其原理是通过代理请求获得固定出口IP的能力。IP为轮转不固定，因此三方服务要求使用白名单时开发者需要将代理服务器可能的IP均加入到白名单中，见下方代理服务器列表。此外对于代理的域名有限制，当前仅持 `weixin.qq.com` 泛域名。若开发者有其他域名代理需求，发送邮件到service@dcloud.io申请，邮件模板参考： [申请解除限制邮件模板](https://doc.dcloud.net.cn/uniCloud/price.html#apply-email-template)。

**代理服务器IP列表**

```
47.92.132.2
47.92.152.34
47.92.87.58
47.92.207.183
8.142.185.204
```

如需在获取微信公众号access_token场景使用，请将上述ip配置到 `微信公众平台 -> 基本配置 -> IP白名单` 内，相关链接： [微信公众平台](https://mp.weixin.qq.com/)

### 阿里云发送Get请求

**用法**

```javascript
uniCloud.httpProxyForEip.get(url: String, params?: Object)
```

**示例**

```javascript
await uniCloud.httpProxyForEip.get(
  'https://api.weixin.qq.com/cgi-bin/token',
  {
    grant_type: 'client_credential',
    appid: 'xxxx',
    secret: 'xxxx'
  }
)
```

### 阿里云发送POST请求携带表单数据

注意，此接口以 `application/x-www-form-urlencoded` 格式发送数据而不是 `multipart/form-data`

**用法**

```javascript
uniCloud.httpProxyForEip.postForm(url: String, data?: Object, headers?: Object)
```

**示例**

```javascript
uniCloud.httpProxyForEip.postForm(
  'https://www.example.com/search',
  {
    q: 'nodejs',
    cat: '1001'
  }
)
```

### 阿里云发送POST请求携带JSON数据

以 `application/json` 格式post数据

**用法**

```javascript
uniCloud.httpProxyForEip.postJson(url: String, json?: Object, headers?: Object)
```

**示例**

```javascript
uniCloud.httpProxyForEip.postJson(
  'https://www.example.com/search',
  {
    q: 'nodejs',
    cat: '1001'
  }
)
```

### 阿里云POST通用数据

**用法**

```javascript
uniCloud.httpProxyForEip.post(url: String, text?: String, headers?: Object)
```

**示例**

```javascript
uniCloud.httpProxyForEip.post(
  'https://www.example.com/search',
  'abcdefg',
  {
    "Content-Type": "text/plain"
  }
)
```

**注意**

- 不支持发送multipart格式的内容
- 代理请求超时时间为5秒
- 上述接口支持本地运行