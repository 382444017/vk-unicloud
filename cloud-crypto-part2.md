## AES加解密（对称加密）

AES属于对称加密方法，高级加密标准(Advanced Encryption Standard，AES)

即加密和解密都用同一个密钥

### aes-256-ecb算法

```javascript
const crypto = require('crypto');

// 密钥
const key = "5d44a032652974c3e53644945a95b126"; // AES-256位密钥

// 待加密的文本
const text = '我是待加密的信息';
console.log('加密前的原文:', text);

// 加密
const cipher = crypto.createCipheriv('aes-256-ecb', key, '');
let encrypted = cipher.update(text, 'utf8', 'base64');
encrypted += cipher.final('base64');
console.log('加密后的密文:', encrypted);

// 解密
const decipher = crypto.createDecipheriv('aes-256-ecb', key, '');
let decrypted = decipher.update(encrypted, 'base64', 'utf8');
decrypted += decipher.final('utf8');
console.log('解密后的明文:', decrypted);
```

### aes-256-cbc算法（带偏移量）

```javascript
const crypto = require('crypto');

// 密钥和偏移量（IV）
const key = "5d44a032652974c3e53644945a95b126"; // AES-256位密钥，必须32位
const iv = "652974c3e5364494"; // 初始化向量（IV）必须16位

// 待加密的文本
const text = '我是待加密的信息';
console.log('加密前的原文:', text);

// 加密
const cipher = crypto.createCipheriv('aes-256-cbc', key, iv);
let encrypted = cipher.update(text, 'utf8', 'base64');
encrypted += cipher.final('base64');
console.log('加密后的密文:', encrypted);

// 解密
const decipher = crypto.createDecipheriv('aes-256-cbc', key, iv);
let decrypted = decipher.update(encrypted, 'base64', 'utf8');
decrypted += decipher.final('utf8');
console.log('解密后的明文:', decrypted);
```

## RSA算法（非对称加密）

### RSA加解密

RSA是1977年由罗纳德·李维斯特（Ron Rivest）、阿迪·萨莫尔（Adi Shamir）和伦纳德·阿德曼（Leonard Adleman）一起提出的。当时他们三人都在麻省理工学院工作。RSA就是他们三人姓氏开头字母拼在一起组成的

RSA算法是一种非对称加密算法，与对称加密算法不同的是，RSA算法有两个不同的密钥，一个是公钥，一个是私钥。其中公钥用来加密，私钥用来解密

```javascript
const crypto = require('crypto');

// 生成PKCS#8格式的RSA密钥对
const { publicKey, privateKey } = crypto.generateKeyPairSync('rsa', {
  modulusLength: 2048, // 密钥长度
  publicKeyEncoding: {
    type: 'spki', // 公钥格式
    format: 'pem', // PEM格式
  },
  privateKeyEncoding: {
    type: 'pkcs8', // 私钥格式
    format: 'pem', // PEM格式
  },
});

console.log('Public Key (PKCS8):', publicKey);
console.log('Private Key (PKCS8):', privateKey);

// 使用公钥加密
const text = '我是待加密的信息';
console.log('加密前的原文:', text);

const encryptBuffer = crypto.publicEncrypt({
  key: publicKey,
  padding: crypto.constants.RSA_PKCS1_OAEP_PADDING, // 填充方式
  },
  Buffer.from(text)
);

console.log('加密后的密文:', encryptBuffer.toString('hex'));

// 使用私钥解密
const decryptBuffer = crypto.privateDecrypt({
  key: privateKey,
  padding: crypto.constants.RSA_PKCS1_OAEP_PADDING,
  },
  encryptBuffer
);

console.log('解密后的明文:', decryptBuffer.toString());