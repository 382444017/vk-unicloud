---
type: "manual"
---

# uniCloud 扩展云存储 - 基础配置和文件管理

## 📚 文档概述

这是uniCloud扩展云存储的基础配置和文件管理API文档。

## 🔧 基础配置

### 启用扩展库

在云函数或云对象中添加扩展库依赖：

1. 右键云函数或云对象
2. 点击"管理公共模块或扩展库依赖"
3. 勾选 `uni-cloud-ext-storage` 扩展库

### 获取扩展存储管理对象

```javascript
const extStorageManager = uniCloud.getExtStorageManager({
  provider: "qiniu", // 扩展存储供应商
  domain: "example.com", // 域名地址
  bucketName: "", // 可选，bucket名称
  bucketSecret: "" // 可选，bucket密钥
});
```

## 📁 文件管理API

### 获取前端上传参数

```javascript
// 云对象示例
module.exports = {
  getUploadFileOptions(data = {}) {
    let { cloudPath } = data;
    
    const extStorageManager = uniCloud.getExtStorageManager({
      provider: "qiniu",
      domain: "example.com"
    });
    
    return extStorageManager.getUploadFileOptions({
      cloudPath: cloudPath,
      allowUpdate: false, // 是否允许覆盖更新
      fsizeLimit: 1024 * 1024 * 10, // 文件大小限制（10MB）
      expiresIn: 3600, // token有效期（秒）
      mimeLimit: "image/*" // 文件类型限制
    });
  }
}
```

### 云端上传文件

```javascript
// 上传网络图片
let imageBuffer = await uniCloud.request({
  url: "https://www.example.com/image.jpg",
  method: "GET",
  responseType: "buffer"
});

let res = await extStorageManager.uploadFile({
  cloudPath: `${Date.now()}.png`,
  fileContent: imageBuffer.data,
  allowUpdate: false
});

// 上传base64图片
let base64 = "data:image/png;base64,iVBORw0KGgo...";
let base64Str = "base64,";
let base64Index = base64.indexOf(base64Str);
if (base64Index > -1) base64 = base64.substring(base64Index + base64Str.length);

let fileContent = new Buffer(base64, 'base64');
let res = await extStorageManager.uploadFile({
  cloudPath: `${Date.now()}.png`,
  fileContent,
  allowUpdate: false
});
```

### 获取临时下载链接

```javascript
let res = await extStorageManager.getTempFileURL({
  fileList: ["qiniu://test.jpg"], // 文件地址列表
  expiresIn: 3600 // 有效期（秒）
});
```

### 删除文件

```javascript
let res = await extStorageManager.deleteFile({
  fileList: ["qiniu://test.jpg"] // 待删除的文件地址列表
});
```

### 修改文件状态

```javascript
// 设置文件为私有权限
let res = await extStorageManager.updateFileStatus({
  fileID: "qiniu://test.jpg",
  isPrivate: true // true 私有 false 公共
});
```

## 🌐 前端上传示例

### uni-app 前端上传代码

```javascript
// 选择图片并上传
uni.chooseImage({
  count: 1,
  success: async (res) => {
    const filePath = res.tempFilePaths[0];
    uni.showLoading({ title: "上传中...", mask: true });

    // 调用云对象获取上传参数
    const extStorageCo = uniCloud.importObject("ext-storage-co");
    const uploadFileOptionsRes = await extStorageCo.getUploadFileOptions({
      cloudPath: `images/${Date.now()}.jpg`
    });

    // 执行上传
    const uploadTask = uni.uploadFile({
      ...uploadFileOptionsRes.uploadFileOptions,
      filePath: filePath,
      success: (uploadFileRes) => {
        if (uploadFileRes.statusCode === 200) {
          const result = {
            cloudPath: uploadFileOptionsRes.cloudPath,
            fileID: uploadFileOptionsRes.fileID,
            fileURL: uploadFileOptionsRes.fileURL
          };
          console.log("上传成功", result);
        }
      },
      fail: (err) => {
        console.log("上传失败", err);
      }
    });

    // 监听上传进度
    uploadTask.onProgressUpdate((res) => {
      console.log("上传进度", res.progress + "%");
    });

    uni.hideLoading();
  }
});
```

## ⚠️ 注意事项

1. **文件路径格式**：支持 fileID、cloudPath、fileURL 三种格式
2. **权限控制**：私有文件需要通过 getTempFileURL 获取临时访问链接
3. **处理限制**：
   - 同步处理：原图最大20MB
   - 持久化处理：无文件大小限制
4. **URL编码**：水印图片URL需要进行URL安全的Base64编码
5. **缓存刷新**：每日最多刷新100个文件，生效时间1-10分钟

## 🔗 相关文档

- [图片处理API](cloud-ext-storage-images.md)
- [视频处理API](cloud-ext-storage-videos.md)
- [CDN和高级功能](cloud-ext-storage-advanced.md)