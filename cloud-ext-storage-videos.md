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

## 🔗 相关文档

- [基础配置和文件管理](cloud-ext-storage-basic.md)
- [图片处理API](cloud-ext-storage-images.md)