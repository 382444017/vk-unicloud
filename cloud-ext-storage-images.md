---
type: "manual"
---

# uniCloud 扩展云存储 - 图片处理API

## 🖼️ 图片处理API

### 图片瘦身

```javascript
// 基本瘦身
let imageUrl = "https://example.com/image.jpg?imageslim";

// 控制质量损失
let imageUrl = "https://example.com/image.jpg?imageslim/zlevel/2";
```

### 图片基本处理

```javascript
// 等比缩放
let imageUrl = "https://example.com/image.jpg?imageView2/1/w/200/h/200";

// 格式转换
let imageUrl = "https://example.com/image.jpg?imageView2/1/w/200/h/200/format/png";

// 质量调整
let imageUrl = "https://example.com/image.jpg?imageView2/1/w/200/h/200/q/80";
```

### 图片高级处理

```javascript
// 缩放
let imageUrl = "https://example.com/image.jpg?imageMogr2/thumbnail/!50p"; // 缩放到50%
let imageUrl = "https://example.com/image.jpg?imageMogr2/thumbnail/200x"; // 宽度200px

// 裁剪
let imageUrl = "https://example.com/image.jpg?imageMogr2/crop/300x300"; // 裁剪300x300

// 旋转
let imageUrl = "https://example.com/image.jpg?imageMogr2/rotate/45"; // 旋转45度

// 高斯模糊
let imageUrl = "https://example.com/image.jpg?imageMogr2/blur/3x5"; // 半径3，sigma值5
```

### 图片圆角处理

```javascript
// 圆角处理
let imageUrl = "https://example.com/image.jpg?roundPic/radius/50"; // 50像素圆角
let imageUrl = "https://example.com/image.jpg?roundPic/radius/!25p"; // 25%圆角
let imageUrl = "https://example.com/image.jpg?roundPic/radius/!50p"; // 变成圆形
```

### 图片水印

```javascript
// 图片水印
let watermarkUrl = "https://example.com/watermark.png";
let encodedWatermark = Buffer.from(watermarkUrl).toString('base64');
let imageUrl = `https://example.com/image.jpg?watermark/1/image/${encodedWatermark}/dissolve/50/gravity/SouthEast/dx/20/dy/20`;
```

### 图片盲水印

```javascript
// 添加盲水印
let watermarkImage = "watermark.png";
let encodedWatermark = Buffer.from(watermarkImage).toString('base64');
let imageUrl = `https://example.com/image.jpg?watermark/5/version/3/method/encode/imageKey/${encodedWatermark}`;

// 提取盲水印
let imageUrl = `https://example.com/image.jpg?watermark/5/version/3/method/decode`;
```

## 🛡️ 机器智能审核

### 图片审核

```javascript
// 图片内容审核
let auditUrl = "https://example.com/image.jpg?imageAudit";
```

## 🎯 实际应用场景

### 头像上传与处理

```javascript
// 云对象：处理头像上传
module.exports = {
  async uploadAvatar(data = {}) {
    let { cloudPath } = data;

    const extStorageManager = uniCloud.getExtStorageManager({
      provider: "qiniu",
      domain: "cdn.example.com"
    });

    // 获取上传参数，限制为图片格式
    return extStorageManager.getUploadFileOptions({
      cloudPath: `avatars/${cloudPath}`,
      allowUpdate: false,
      fsizeLimit: 1024 * 1024 * 2, // 限制2MB
      mimeLimit: "image/jpeg;image/png;image/gif"
    });
  },

  // 生成不同尺寸的头像
  generateAvatarSizes(originalUrl) {
    return {
      small: `${originalUrl}?imageView2/1/w/50/h/50/q/80`,
      medium: `${originalUrl}?imageView2/1/w/100/h/100/q/80`,
      large: `${originalUrl}?imageView2/1/w/200/h/200/q/80`,
      thumbnail: `${originalUrl}?roundPic/radius/!50p` // 圆形头像
    };
  }
}
```

### 商品图片处理

```javascript
// 生成商品图片的多种规格
function generateProductImages(originalUrl) {
    return {
        // 列表缩略图
        thumbnail: `${originalUrl}?imageView2/1/w/200/h/200/q/75`,
        // 详情页主图
        detail: `${originalUrl}?imageView2/2/w/750/q/85`,
        // 放大镜图片
        zoom: `${originalUrl}?imageView2/2/w/1200/q/90`,
        // 带水印的图片
        watermarked: `${originalUrl}?watermark/1/image/${encodedWatermark}/dissolve/30/gravity/SouthEast/dx/10/dy/10`
    };
}
```

## 🔧 高级功能

### 文件批量处理

```javascript
// 批量处理图片
async function batchProcessImages(fileList) {
    const extStorageManager = uniCloud.getExtStorageManager({
        provider: "qiniu",
        domain: "cdn.example.com"
    });

    const results = [];

    for (let fileID of fileList) {
        // 生成多种尺寸
        const processed = {
            original: fileID,
            small: `${fileID}?imageView2/1/w/200/h/200/q/75`,
            medium: `${fileID}?imageView2/1/w/400/h/400/q/80`,
            large: `${fileID}?imageView2/1/w/800/h/800/q/85`,
            webp: `${fileID}?imageView2/1/w/400/h/400/format/webp/q/80`
        };

        results.push(processed);
    }

    return results;
}
```

### 智能图片压缩

```javascript
// 根据图片大小智能选择压缩策略
function smartImageCompress(imageUrl, fileSize) {
    let quality = 85;
    let format = '';

    // 根据文件大小调整质量
    if (fileSize > 1024 * 1024) { // 大于1MB
        quality = 70;
        format = '/format/webp';
    } else if (fileSize > 500 * 1024) { // 大于500KB
        quality = 75;
    }

    // 应用图片瘦身
    return `${imageUrl}?imageslim/zlevel/3${format}/q/${quality}`;
}
```

## 🔗 相关文档

- [基础配置和文件管理](cloud-ext-storage-basic.md)
- [视频处理API](cloud-ext-storage-videos.md)
- [CDN和高级功能](cloud-ext-storage-advanced.md)