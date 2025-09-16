# 使用crypto进行加密解密

## 介绍

crypto 是 Nodejs 的内置模块，提供了加密功能，包括对 OpenSSL 的哈希、HMAC、加密、解密、签名、以及验证功能的一整套封装。

## Hash（哈希函数）

又称为不可逆加密函数，主要用于生成数据的一个固定长度的"指纹"，无法通过加密内容解密成原始内容。

安全性排序：SHA3 ＞ SHA256 ＞ SHA1 ＞ MD5

性能排序：MD5 ＞ SHA1 ＞ SHA256 ＞ SHA3

### MD5算法

MD5生成一个128位的哈希值，通常表示为32个十六进制数，在其早期广泛用于各种验证和安全性较低的场合，如检查文件完整性。

```javascript
const crypto = require('crypto');
let text = 'Hello, world!';
let hash = crypto.createHash('md5').update(text).digest('hex');
console.log('md5 Hash: ', hash);
```

### SHA1算法

SHA-1比MD5安全的哈希算法，生成一个160位的哈希值，通常表示为40个十六进制数。

```javascript
const crypto = require('crypto');
let text = 'Hello, world!';
let hash = crypto.createHash('sha1').update(text).digest('hex');
console.log('sha1 Hash: ', hash);
```

### SHA256算法

比SHA1更安全的哈希算法，SHA-256生成的哈希值长度为256位，通常以64个十六进制数字表示。这种哈希算法被广泛认为是安全的，适用于多种需要数据完整性和安全性的场合。

```javascript
const crypto = require('crypto');
// 待哈希的数据
let text = 'Hello, world!';
let hash = crypto.createHash('sha256').update(text).digest('hex');
console.log('SHA-256 Hash: ', hash);
```

### SHA3算法

SHA-3（Secure Hash Algorithm 3）是NIST（美国国家标准与技术研究院）在2015年公布的一种加密哈希函数。它是继SHA-1和SHA-2之后，作为新的安全哈希标准而设计的。SHA-3的设计目的是为了提高安全性，尤其是在面对量子计算威胁的背景下。

```javascript
const crypto = require('crypto');
// 待哈希的数据
let text = 'Hello, world!';
let hash = crypto.createHash('sha3-512').update(text).digest('hex');
console.log('sha3-512 Hash: ', hash);
```

## Hmac（加密哈希函数）

Hmac算法也是一种哈希算法，它也可以指定MD5、SHA1、SHA256、SHA3等哈希算法，但需要配置密钥，它主要用于消息认证，即验证一条消息是否未被篡改，并且确实是由持有共享密钥的发送者发送的。

```javascript
const crypto = require('crypto');
let text = 'Hello, world!';
let hmac = crypto.createHmac('sha256', 'secret-key').update(text).digest('hex');
console.log('SHA-256 Hmac: ', hmac);