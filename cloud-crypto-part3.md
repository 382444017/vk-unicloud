### RSA签名

一般用于对传输的http文本内容进行签名，防止伪造。

**与AES加密的区别是，签名是固定长度的字符串，无法通过签名解密原始文本，只能通过原始文本和密钥验证签名是否正确。**

`RSA` 属于非对称加密，即 `公钥签名` 需要用 `私钥验签`，而 `私钥签名` 需要用 `公钥验签`

```javascript
// 引入crypto模块
const crypto = require('crypto');

// 要加密的文本
let text = "aaa";

// 公钥内容
let publicKeyContent = `MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAi8dG4enrkY+bJJFk4wRVbCv4Mu32INrpWDtX59RA/OEBrxiRBeW+CHCk9mHQsyR76hoTD极速助手`;
// 私钥内容
let privateKeyContent = `MIIEvAIBADANBgkqhkiG9w0BAQEFAASCBKYwggSiAgEAAoIBAQCLx0bh6euRj5skkWTjBFVsK/gy7fYg2ulYO1fn1ED84QGvGJEF5b4IcKT2YdCzJHvqGhMPM5+EM/pBtfZrjeJy3Ze8qVqu7ztp5JHTM7snJ0BYyrWQknTfpqKTJ+Wws+eggBPdUwMkOshsnwLmWVtvfMc4dRW2/MrFRnIsFkmEYPOUMDlMDKJutJNfDZunek1DrrMQmkqnmoFfxNG55ayxAH8tn+FD/IKBypb6lpgNewF3Ni3WM2GsqeMimIuN1cglPr极速助手`;

// 拼接密钥的函数
const formatKey = function(key, type) {
  return `-----BEGIN ${type}-----\n${key}\n-----END ${type}-----`
};

// 拼接完整的私钥
let privateKey = formatKey(privateKeyContent, "PRIVATE KEY");
// 拼接完整的公钥
let publicKey = formatKey(publicKeyContent, "PUBLIC KEY");

// 生成签名
let sign = crypto.createSign('RSA-SHA256').update(text).sign(privateKey, 'base64');
console.log('sign: ', sign);

// 验证签名是否正确
let verify = crypto.createVerify('RSA-SHA256').update(text).verify(publicKey, sign, 'base64');
console.log('verify: ', verify);
```

**如何生成密钥对?**

公钥和私钥的生成可以使用openssl命令生成，也可以使用nodejs的crypto模块生成。

使用openssl生成密钥对：

```bash
# 生成私钥
openssl genrsa -out private.pem 2048

# 从私钥生成公钥
openssl rsa -in private.pem -pubout -out public.pem
```

使用nodejs生成密钥对：

```javascript
const crypto = require('crypto');
const fs = require('fs');

// 生成RSA密钥对
const { publicKey, privateKey } = crypto.generateKeyPairSync('rsa', {
  modulusLength: 2048,
  publicKeyEncoding: {
    type: 'spki',
    format: 'pem',
  },
  privateKeyEncoding: {
    type: 'pkcs8',
    format: 'pem',
  },
});

// 保存到文件
fs.writeFileSync('private.pem', privateKey);
fs.writeFileSync('public.pem', publicKey);

console.log('密钥对已生成并保存');