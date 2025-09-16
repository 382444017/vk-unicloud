---
type: "manual"
description: "文件上传规范"
---

# 文件上传

VK框架提供了统一的文件上传API，支持多种存储方式。

## 基础文件上传

```javascript
// 选择并上传图片
uni.chooseImage({
  count: 1,
  sizeType: ['compressed'],
  success: (res) => {
    vk.uploadFile({
      title: "上传中...",
      file: res.tempFiles[0],
      success: (uploadRes) => {
        console.log('上传成功:', uploadRes.url)
        // 使用上传后的URL
        this.imageUrl = uploadRes.url
      },
      fail: (err) => {
        console.error('上传失败:', err)
        vk.toast('上传失败，请重试')
      }
    })
  }
})
```

## 上传参数配置

```javascript
vk.uploadFile({
  title: "上传中...",
  file: fileObject, // 文件对象
  // filePath: "/path/to/file", // 或者使用文件路径

  // 存储配置
  provider: "unicloud", // unicloud | extStorage | aliyun
  cloudPath: "custom/path/image.jpg", // 自定义云端路径
  cloudDirectory: "uploads", // 云端目录

  // 扩展功能
  needSave: true, // 是否保存到admin素材库
  category_id: "001", // 素材库分类ID

  // 进度回调
  onUploadProgress: (res) => {
    console.log(`上传进度：${res.progress}%`)
    this.uploadProgress = res.progress
  },

  success: (res) => {
    console.log('上传成功:', res)
    // res.url - 文件访问URL
    // res.fileID - 云端文件ID
    // res.cloudPath - 云端文件路径
  }
})
```

## 多文件上传

```javascript
// 选择多个文件
uni.chooseImage({
  count: 9,
  success: (res) => {
    const uploadPromises = res.tempFiles.map(file => {
      return vk.uploadFile({
        file: file,
        cloudDirectory: "gallery"
      })
    })

    // 并发上传
    Promise.all(uploadPromises)
      .then(results => {
        console.log('所有文件上传完成:', results)
        this.imageList = results.map(item => item.url)
      })
      .catch(err => {
        console.error('上传失败:', err)
      })
  }
})
```

## 存储配置

在 `app.config.js` 中配置默认存储：

```javascript
export default {
  service: {
    cloudStorage: {
      // 默认存储提供商
      defaultProvider: "unicloud", // unicloud | extStorage | aliyun

      // 扩展存储配置
      extStorage: {
        provider: "qiniu",
        dirname: "public",
        domain: "cdn.example.com",
        groupUserId: false
      },

      // 阿里云OSS配置
      aliyun: {
        uploadData: {
          OSSAccessKeyId: "",
          policy: "",
          signature: ""
        },
        action: "https://bucket.oss-cn-hangzhou.aliyuncs.com",
        dirname: "public",
        host: "https://cdn.example.com"
      }
    }
  }
}