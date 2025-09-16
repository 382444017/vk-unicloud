---
type: "manual"
description: "VK-UniCloud 云开发规范索引文件"
---

# VK-UniCloud 云开发规范

## 📚 规则文件索引

本文件是VK-UniCloud云开发规范的索引文件，详细内容请参考以下专门的规则文件：

1. **[框架概述](./cloud-framework-overview.md)** - VK-UniCloud框架概述和项目结构规范
2. **[云函数开发](./cloud-function-development.md)** - 云函数开发规范和模板
3. **[云对象开发](./cloud-object-development.md)** - 云对象开发规范和模板
4. **[数据库开发](./cloud-database-development.md)** - 数据库Schema定义和开发规范
5. **[缓存系统](./极速开发-cache-system.md)** - 缓存系统使用规范和最佳实践
6. **[权限系统](./cloud-permission-system.md)** - 权限系统架构和使用规范
7. **[响应体规范](./cloud-response-format.md)** - 响应体格式规范和标准
8. **[JavaScript API](./cloud-javascript-api.md)** - JavaScript API (vk.pubfn) 使用规范
9. **[最佳实践](./cloud-best-practices.md)** - 云开发最佳实践指南

## 快速开始

### 云函数开发
```javascript
// 参考：cloud-function-development.md
module.exports = {
  main: async (event) => {
    let { data, userInfo, util } = event;
    let { vk } = util;
    let res = { code: 0, msg: '' };
    
    // 业务逻辑
    return res;
  }
}
```

### 云对象开发  
```javascript
// 参考：cloud-object-development.md
var cloudObject = {
  isCloudObject: true,
  _before: async function() {
    vk = this.vk;
  },
  getInfo: async function(data) {
    // 业务逻辑
  }
}
```

### 数据库操作
```javascript
// 参考：cloud-database-development.md
// 新增数据
let id = await vk.baseDao.add({
  dbName: "table-name",
  dataJson: data
});

// 查询数据  
let result = await vk.baseDao.select({
  dbName: "table-name",
  pageIndex: 1,
  pageSize: 20
});
```

## 重要提醒

1. **云函数只有 `main` 方法**，不支持 `before`、`after` 等方法（这些是云对象的特性）
2. **API返回值格式**：大部分数据库API直接返回数据，不是 {code, data} 格式
3. **权限验证**：在云函数中需要手动进行权限验证
4. **错误处理**：使用 try-catch 包裹业务逻辑，返回适当的错误码

## 相关链接

- [VK-UniCloud 官方文档](https://vk-unicloud-doc.vkcloud.cn/)
- [uniCloud 官方文档](https://uniapp.dcloud.net.cn/uniCloud/)
- [uni-id 用户身份验证](https://uniapp.dcloud.net.cn/uniCloud/uni-id.html)

**注意：详细的使用规范和示例请参考各个专门的规则文件。**