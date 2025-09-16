---
type: "manual"
description: "云端文件上传API文档"
---

# 云端文件上传

> vk-unicloud 版本需 ≥ 2.18.6

VK框架提供了统一的云端文件上传API `vk.uploadFile`，支持多种存储方式。

## 🎯 接口名：vk.uploadFile

### 请求参数

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| provider | 云存储供应商 | string | - | unicloud, extStorage |
| cloudPath | 云端文件路径 | string | - | - |
| fileContent | buffer 或要上传的文件可读流 | - | - | - |
| isPrivate | 是否是私有文件，仅扩展存储有效 | boolean | - | - |

### 返回值

| 参数名 | 类型 | 说明 |
|--------|------|------|
| provider | string | 本次上传的存储供应商 |
| cloudPath | string | 云端文件路径 |
| fileID | string | 云端文件ID |
| fileURL | string | 云端文件URL |
| url | string | 云端文件URL，与fileURL一致 |
| isPrivate | boolean | 是否是私有文件，仅扩展存储会返回此字段 |

## 🚀 基础示例

### 上传文本文件

```javascript
// 模拟一个1KB的文件
const buffer = Buffer.alloc(1024); // 创建一个1KB的Buffer
let uploadFileRes = await vk.uploadFile({
  cloudPath: "public/test.txt",
  fileContent: buffer
});
console.log('uploadFileRes: ', uploadFileRes);
```

### 上传图片文件

```javascript
// 读取本地图片文件
const fs = require('fs');
const imageBuffer = fs.readFileSync('/path/to/image.jpg');

// 上传图片
let uploadFileRes = await vk.uploadFile({
  cloudPath: "public/images/avatar.jpg",
  fileContent: imageBuffer,
  provider: "extStorage" // 指定使用扩展存储
});
console.log('图片上传成功:', uploadFileRes.url);
```

## ⚙️ 存储配置

### provider 配置

`provider` 可指定云存储供应商，若不传则自动从云端配置中获取默认值。

| 可选项 | 说明 |
|--------|------|
| unicloud | 空间内置存储 |
| extStorage | 扩展存储 |

### 默认上传至unicloud空间内置存储

在 `uni-config-center/vk-unicloud/index.js` 中配置：

```javascript
"service": {
  // 云储存相关配置
  "cloudStorage": {
    /**
     * vk.uploadFile 接口默认使用哪个存储
     * unicloud 空间内置存储（默认）
     * extStorage 扩展存储
     */
    "defaultProvider": "unicloud",
  }
},
```

### 默认上传至扩展存储

在 `uni-config-center/vk-unicloud/index.js` 中配置：

```javascript
"service": {
  // 云储存相关配置
  "cloudStorage": {
    "defaultProvider": "extStorage",
    // 扩展存储配置
    "extStorage": {
      "provider": "qiniu", // qiniu: 扩展存储-七牛云
      "domain": "cdn.example.com", // 自定义域名
      "bucketName": "", // 存储空间名称
      "bucketSecret": "", // 存储空间密钥
      "endpoint": {
        "upload": "", // 上传接口的代理地址
      }
    }
  }
},
```

## 🛠️ 公共API

### vk.getTempFileURL（获取临时下载链接）

```javascript
// 获取私有文件的临时下载链接
let tempUrlRes = await vk.getTempFileURL({
  fileList: ["https://cdn.example.com/private/file.jpg"],
});
console.log('临时链接:', tempUrlRes.fileList[0]);
```

**请求参数**
| 参数 | 说明 | 类型 |
|------|------|------|
| provider | 云存储供应商 | string |
| fileList | 云端文件列表 | Array |

### vk.downloadFile（下载文件）

```javascript
// 下载文件
let fileContent = await vk.downloadFile({
  fileID: "https://cdn.example.com/file.txt",
});
console.log('文件内容:', fileContent.toString());
```

### vk.deleteFile（删除文件）

```javascript
// 删除文件
let deleteRes = await vk.deleteFile({
  fileList: ["https://cdn.example.com/file.txt"],
});
console.log('删除结果:', deleteRes.fileList);
```

## 🔧 扩展存储专属API

### 获取扩展存储管理对象

```javascript
// 获取扩展存储管理对象
const extStorageManager = vk.getExtStorageManager();

// 或者指定参数
const extStorageManager = vk.getExtStorageManager({
  provider: "qiniu",
  domain: "cdn.example.com"
});
```

### 修改文件状态

```javascript
const extStorageManager = vk.getExtStorageManager();

// 将文件设置为私有权限
let updateRes = await extStorageManager.updateFileStatus({
  fileID: "qiniu://test.jpg",
  isPrivate: true, // true 私有 false 公共
});
console.log('修改状态结果:', updateRes);
```

### 获取域名列表

```javascript
const extStorageManager = vk.getExtStorageManager();
let { domains = [] } = await extStorageManager.getDomains();
console.log('域名列表:', domains);
```

### 获取TOP100统计数据

```javascript
const extStorageManager = vk.getExtStorageManager();
let { domains = [] } = await extStorageManager.getDomains();

// 查询统计数据
let statsRes = await extStorageManager.getCdnTop({
  type: 2, // 1 topURL 2 topIP
  domains,
  startDate: "2024-05-12",
  endDate: "2024-05-12"
});
console.log('TOP100统计数据:', statsRes.data);
```

## 🎯 实际应用场景

### 用户头像上传

```javascript
// 云函数：处理用户头像上传
module.exports = {
  async uploadUserAvatar(data = {}) {
    const { user_id, avatarBuffer } = data;
    
    // 上传到用户专属目录
    const uploadRes = await vk.uploadFile({
      cloudPath: `users/${user_id}/avatar.jpg`,
      fileContent: avatarBuffer,
      provider: "extStorage"
    });
    
    // 更新用户头像URL
    await vk.baseDao.update({
      dbName: "uni-id-users",
      where: { _id: user_id },
      data: { avatar: uploadRes.url }
    });
    
    return uploadRes;
  }
}
```

### 文件批量处理

```javascript
// 批量处理上传的文件
async function processUploadedFiles(fileList) {
  const results = [];
  
  for (const fileInfo of fileList) {
    try {
      // 生成缩略图路径
      const cloudPath = `processed/${Date.now()}_${fileInfo.name}`;
      
      // 上传处理后的文件
      const uploadRes = await vk.uploadFile({
        cloudPath: cloudPath,
        fileContent: fileInfo.buffer,
        provider: "extStorage"
      });
      
      results.push(uploadRes);
    } catch (error) {
      console.error('文件处理失败:', error);
    }
  }
  
  return results;
}
```

## 🔗 相关文档

- [前端文件上传API](frontend-file-upload.md)
- [扩展存储图片处理](cloud-ext-storage-images.md)
- [扩展存储视频处理](cloud-ext-storage-videos.md)
- [云数据库开发规范](cloud-database-development.md)