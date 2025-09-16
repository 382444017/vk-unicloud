---
type: "manual"
---

# uniCloud 扩展云存储 - 视频处理API

## 🎬 视频处理API

### 音视频转码

```javascript
// 视频转码
let videoUrl = "https://example.com/video.mp4?avthumb/mp4/s/640x480/vb/1.25m";

// 音频转码
let audioUrl = "https://example.com/audio.mp3?avthumb/mp3/ab/320k";
```

### 视频单帧缩略图

```javascript
// 获取视频第1秒的缩略图
let thumbnailUrl = "https://example.com/video.mp4?vframe/jpg/offset/1";

// 指定缩略图尺寸
let thumbnailUrl = "https://example.com/video.mp4?vframe/jpg/offset/1/w/300/h/200";
```

### 音视频元信息

```javascript
// 获取音视频元信息
let infoUrl = "https://example.com/video.mp4?avinfo";
```

## 🛡️ 机器智能审核

### 视频审核

```javascript
// 视频内容审核
let auditUrl = "https://example.com/video.mp4?videoAudit";
```

## 🎯 实际应用场景

### 视频处理应用

```javascript
// 视频上传后处理
module.exports = {
  async processVideo(data = {}) {
    let { fileID } = data;

    // 使用 vk.uploadFile 获取上传参数，限制为视频格式
    const uploadOptions = vk.uploadFile.getOptions({
      title: "上传视频",
      fileType: "video",
      fileMaxSize: 100, // 限制100MB
      mimeTypes: ["video/mp4", "video/avi", "video/mov", "video/mkv"]
    });

    // 获取视频信息
    let videoInfoUrl = `${fileID}?avinfo`;

    // 生成视频缩略图
    let thumbnailUrl = `${fileID}?vframe/jpg/offset/1/w/300/h/200`;

    // 转码为不同清晰度
    let videoFormats = {
      sd: `${fileID}?avthumb/mp4/s/640x480/vb/800k`,
      hd: `${fileID}?avthumb/mp4/s/1280x720/vb/1500k`,
      fhd: `${fileID}?avthumb/mp4/s/1920x1080/vb/3000k`
    };

    return {
      thumbnail: thumbnailUrl,
      formats: videoFormats
    };
  }
}
```

## 🔗 相关文档

- [基础配置和文件管理](cloud-ext-storage-basic.md)
- [图片处理API](cloud-ext-storage-images.md)
- [CDN和高级功能](cloud-ext-storage-advanced.md)