---
type: "manual"
---

# uniCloud 扩展云存储 - CDN管理和高级功能

## 📄 文件处理API

### 资源下载

```javascript
// 强制下载
let downloadUrl = "https://example.com/file.pdf?attname=document.pdf";
```

### 二维码生成

```javascript
// 生成二维码
let qrUrl = "https://example.com/qrcode?mode=0&text=Hello%20World";

// 自定义二维码
let qrUrl = "https://example.com/qrcode?mode=0&text=Hello%20World&level=L&size=200";
```

## 🔄 CDN管理API

### 刷新CDN缓存

```javascript
let res = await extStorageManager.refreshCdnCache({
  fileList: ["qiniu://test.jpg"] // 待刷新的文件地址列表
});
```

### 获取CDN流量数据

```javascript
let res = await extStorageManager.getCdnFlow({
  domains: ["cdn.example.com"],
  granularity: 'day', // 粒度：5min、hour、day
  startDate: "2024-04-01",
  endDate: "2024-04-30"
});
```

### 获取TOP100统计数据

```javascript
let res = await extStorageManager.getCdnTop({
  type: 2, // 1 topURL 2 topIP
  domains: ["cdn.example.com"],
  startDate: "2024-05-12",
  endDate: "2024-05-12"
});
```

## 🔐 域名安全配置

### 修改防盗链

```javascript
let res = await extStorageManager.updateDomainReferer({
  domain: "cdn.example.com",
  refererType: "white", // white白名单 black黑名单
  refererValues: ["www.example.com"],
  nullReferer: true // 是否允许空referer
});
```

### 修改IP黑白名单

```javascript
let res = await extStorageManager.updateDomainIpacl({
  domain: "cdn.example.com",
  ipACLType: "black", // white白名单 black黑名单
  ipACLValues: ["192.168.1.1"]
});
```

## 📱 小程序域名配置

### 上传域名白名单
```
https://upload.qiniup.com
```

### 下载域名白名单
```
你的自定义域名，如：https://cdn.example.com
```

## 🔧 高级功能

### 安全防护配置

```javascript
// 配置域名安全策略
module.exports = {
  async setupDomainSecurity(domain) {
    const extStorageManager = uniCloud.getExtStorageManager({
      provider: "qiniu",
      domain: "cdn.example.com"
    });

    // 设置防盗链
    await extStorageManager.updateDomainReferer({
      domain,
      refererType: "white",
      refererValues: [
        "*.example.com",
        "localhost",
        "127.0.0.1"
      ],
      nullReferer: false
    });

    // 设置IP黑名单（示例）
    await extStorageManager.updateDomainIpacl({
      domain,
      ipACLType: "black",
      ipACLValues: [
        "192.168.1.100", // 示例恶意IP
      ]
    });

    return { success: true };
  }
}
```

## 📊 监控与统计

### CDN流量监控

```javascript
// 获取CDN使用统计
module.exports = {
  async getCdnStats(startDate, endDate) {
    const extStorageManager = uniCloud.getExtStorageManager({
      provider: "qiniu",
      domain: "cdn.example.com"
    });

    // 获取域名列表
    const { domains } = await extStorageManager.getDomains();

    // 获取流量数据
    const flowData = await extStorageManager.getCdnFlow({
      domains,
      granularity: 'day',
      startDate,
      endDate
    });

    // 获取TOP访问IP
    const topIPs = await extStorageManager.getCdnTop({
      type: 2, // topIP
      domains,
      startDate,
      endDate
    });

    return {
      flowData,
      topIPs,
      totalDomains: domains.length
    };
  }
}
```

## 🚨 错误处理

### 常见错误及解决方案

```javascript
// 错误处理示例
module.exports = {
  async safeUpload(data) {
    try {
      const extStorageManager = uniCloud.getExtStorageManager({
        provider: "qiniu",
        domain: "cdn.example.com"
      });

      const result = await extStorageManager.uploadFile(data);
      return { success: true, data: result };

    } catch (error) {
      console.error('上传失败:', error);

      // 根据错误类型返回不同信息
      if (error.code === 'NETWORK_ERROR') {
        return { success: false, message: '网络连接失败，请重试' };
      } else if (error.code === 'FILE_TOO_LARGE') {
        return { success: false, message: '文件过大，请选择小于10MB的文件' };
      } else if (error.code === 'INVALID_FORMAT') {
        return { success: false, message: '文件格式不支持' };
      else {
        return { success: false, message: '上传失败，请稍后重试' };
      }
    }
  }
}
```

## 🔗 相关文档

- [基础配置和文件管理](cloud-ext-storage-basic.md)
- [图片处理API](cloud-ext-storage-images.md)
- [视频处理API](cloud-ext-storage-videos.md)