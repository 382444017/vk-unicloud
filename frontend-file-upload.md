---
type: "manual"
description: "前端文件上传API文档"
---

# 前端文件上传

VK框架提供了统一的文件上传API `vk.uploadFile`，支持多种存储方式：unicloud空间内置存储、扩展存储、阿里云OSS。

## 🎯 接口名：vk.uploadFile

### 请求参数

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| title | 上传时的loading提示语 | String | - | - |
| file | 要上传的文件对象，file与filePath二选一 | File | - | - |
| filePath | 要上传的文件路径，file与filePath二选一 | String | - | - |
| suffix | 指定上传后的文件后缀 | String | - | - |
| provider | 云存储供应商 | String | - | unicloud, extStorage, aliyun |
| cloudPath | 指定上传后的云端文件路径 | String | - | - |
| cloudDirectory | 指定上传后的云端目录 | String | - | - |
| needSave | 是否需要将图片信息保存到admin素材库 | Boolean | false | true |
| category_id | 素材库分类id，当needSave为true时生效 | String | - | - |
| onUploadProgress | 上传进度回调 | Function | - | - |
| success | 上传成功回调 | Function | - | - |
| fail | 上传失败回调 | Function | - | - |
| complete | 完成回调 | Function | - | - |

### 返回值 (vk-unicloud核心库版本 ≥ 2.17.0)

| 参数名 | 类型 | 说明 |
|--------|------|------|
| provider | string | 本次上传的存储供应商 |
| filePath | string | 本地文件路径 |
| cloudPath | string | 云端文件路径 |
| fileID | string | 云端文件ID |
| fileURL | string | 云端文件URL |
| url | string | 云端文件URL，与fileURL一致 |
| extendInfo | object | 扩展存储额外返回的信息 |

### extendInfo 扩展信息

| 参数名 | 类型 | 说明 |
|--------|------|------|
| key | string | 等于cloudPath |
| hash | string | 文件的hash值 |
| name | string | 上传前的文件名 |
| size | number | 文件大小，单位B |
| width | number | 图片或视频的宽度 |
| height | number | 图片或视频的高度 |
| format | string | 文件格式 |

## 🚀 基础文件上传示例

### 选择并上传图片

```javascript
// 选择图片
uni.chooseImage({
  count: 1,
  sizeType: ['compressed'],
  success: (res) => {
    // 文件上传
    vk.uploadFile({
      title: "上传中...",
      file: res.tempFiles[0],
      success: (res) => {
        // 上传成功
        let url = res.url;
        console.log('文件URL:', url);
        this.imageUrl = url;
      },
      fail: (err) => {
        // 上传失败
        console.error('上传失败:', err);
        vk.toast('上传失败，请重试');
      }
    });
  }
});
```

### 使用文件路径上传

```javascript
// 使用文件路径上传
vk.uploadFile({
  title: "上传中...",
  filePath: "/path/to/file.jpg",
  success: (res) => {
    console.log('上传成功:', res);
  }
});
```

## ⚙️ 高级功能配置

### 上传图片并保存到admin素材库

```javascript
// 选择图片
uni.chooseImage({
  count: 1,
  success: (res) => {
    vk.uploadFile({
      title: "上传中...",
      file: res.tempFiles[0],
      needSave: true, // 保存到素材库
      category_id: "001", // 指定分类ID
      success: (res) => {
        console.log('上传成功并保存到素材库:', res);
      }
    });
  }
});
```

### 自定义云端文件路径

```javascript
// 自定义云端路径
vk.uploadFile({
  title: "上传中...",
  file: res.tempFiles[0],
  cloudPath: "user/avatar/user_123.jpg", // 完整路径
  success: (res) => {
    console.log('文件保存路径:', res.cloudPath);
  }
});

// 或者指定目录
vk.uploadFile({
  title: "上传中...",
  file: res.tempFiles[0],
  cloudDirectory: "user/avatars", // 指定目录
  success: (res) => {
    console.log('文件保存路径:', res.cloudPath);
  }
});
```

### 监听上传进度

```javascript
vk.uploadFile({
  title: "上传中...",
  file: res.tempFiles[0],
  onUploadProgress: (res) => {
    let { progress } = res;
    console.log(`当前进度：${progress}%`);
    this.uploadProgress = progress;
  },
  success: (res) => {
    console.log('上传完成:', res);
  }
});
```

## 🔄 多文件上传

### 顺序上传

```javascript
// 选择多个文件
uni.chooseImage({
  count: 5,
  success: async (res) => {
    const results = [];
    
    for (let i = 0; i < res.tempFiles.length; i++) {
      try {
        const result = await vk.uploadFile({
          title: `上传第${i + 1}个文件`,
          file: res.tempFiles[i],
          cloudDirectory: "gallery"
        });
        results.push(result);
      } catch (error) {
        console.error(`第${i + 1}个文件上传失败:`, error);
      }
    }
    
    console.log('所有文件上传完成:', results);
    this.imageUrls = results.map(item => item.url);
  }
});
```

### 并发上传

```javascript
// 选择多个文件
uni.chooseImage({
  count: 5,
  success: (res) => {
    const uploadPromises = res.tempFiles.map((file, index) => {
      return vk.uploadFile({
        title: `上传第${index + 1}个文件`,
        file: file,
        cloudDirectory: "gallery"
      });
    });

    // 并发上传
    Promise.all(uploadPromises)
      .then(results => {
        console.log('所有文件上传完成:', results);
        this.imageUrls = results.map(item => item.url);
      })
      .catch(err => {
        console.error('部分文件上传失败:', err);
      });
  }
});
```

## 🏗️ 存储配置

### 完整配置示例 (app.config.js)

```javascript
export default {
  service: {
    // 云储存相关配置
    cloudStorage: {
      /**
       * vk.uploadFile 接口默认使用哪个存储
       * unicloud 空间内置存储（默认）
       * extStorage 扩展存储
       * aliyun 阿里云oss
       */
      defaultProvider: "unicloud",
      
      // 空间内置存储配置
      unicloud: {
        // 暂无配置项
      },
      
      // 扩展存储配置
      extStorage: {
        provider: "qiniu", // qiniu: 扩展存储-七牛云
        // 根目录名称
        dirname: "public",
        // 用于鉴权的云函数地址
        authAction: "user/pub/getUploadFileOptionsForExtStorage",
        // 自定义域名，如：cdn.example.com
        domain: "cdn.example.com",
        // 上传时，是否按用户id进行分组储存
        groupUserId: false,
      },
      
      // 阿里云OSS配置
      aliyun: {
        // 密钥和签名信息
        uploadData: {
          OSSAccessKeyId: "",
          policy: "",
          signature: "",
        },
        // oss上传地址
        action: "https://xxxxxxxx.oss-cn-hangzhou.aliyuncs.com",
        // 根目录名称
        dirname: "public",
        // oss外网访问地址
        host: "https://xxx.xxx.com",
        // 上传时，是否按用户id进行分组储存
        groupUserId: false,
      }
    }
  }
}
```

## 🌐 小程序域名白名单

### 内置存储域名
需要将unicloud服务空间的域名添加到小程序域名白名单中。

### 扩展存储域名
- **上传域名**: `https://upload.qiniup.com`
- **下载域名**: 你绑定的自定义域名

### 阿里云OSS域名
需要将OSS的域名添加到小程序域名白名单中。

## ❓ 常见问题

### Q: 小程序本地可以上传，体验版无法上传
A: 检查域名白名单是否已正确配置。

### Q: 上传扩展存储报错，云函数不存在
A: 需要复制最新框架项目中的 `user/pub/getUploadFileOptionsForExtStorage` 云函数。

### Q: 如何切换默认存储方式？
A: 只需修改 `app.config.js` 中的 `defaultProvider` 配置即可，无需修改上传代码。

## 🔗 相关文档

- [云端文件上传API](cloud-upload-file.md)
- [扩展存储图片处理](cloud-ext-storage-images.md)
- [扩展存储视频处理](cloud-ext-storage-videos.md)