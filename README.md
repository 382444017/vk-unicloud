# VK Unicloud 开发文档

本项目包含 VK Unicloud 平台的完整开发文档，涵盖前端开发、云函数开发、管理后台开发等多个方面。

## 📚 文档分类

### 前端开发
- [状态管理](frontend-state-management.md)
- [前端文件上传](frontend-file-upload.md)
- [列表渲染](frontend-list-rendering.md)
- [JavaScript API](frontend-javascript-api.md)
- [用户中心API](frontend-user-center-api.md)
- [工具函数](frontend-utility-functions.md)
- [应用配置](frontend-app-config.md)
- [拦截器配置](frontend-interceptor-config.md)
- [全局参数](frontend-global-params.md)
- [本地持久化缓存](frontend-local-storage.md)
- [本地临时会话缓存](frontend-session-storage.md) 
- [扩展图标库](frontend-icon.md)
- [清明节灰色页面实现方案](frontend-is-qingming.md)

### 管理后台示例代码
- [表单组件详细示例](admin-form-examples.md) - VK框架表单组件完整示例和用法
- [弹窗组件示例](admin-dialog-examples.md) - 模态框、抽屉、消息提示示例
- [详情组件示例](admin-detail-examples.md) - 详情展示、卡片布局示例
- [上传组件示例](admin-upload-examples.md) - 文件上传、图片上传、拖拽上传示例
- [Element UI 组件示例](admin-element-examples.md) - Element UI 表单、表格、弹窗组件示例
- [图标组件示例](admin-icons-examples.md) - Element UI 图标展示和使用示例
- [输入框组件示例](admin-input-examples.md) - 各种输入框类型和高级功能示例

### 云开发
- [云函数](cloud-function-development.md)
- [数据库 api](cloud-database-development.md)
- [云对象](cloud-object-development.md)
- [云端文件上传](cloud-upload-file.md)
- [JavaScript API](cloud-javascript-api.md)
- [权限系统](cloud-permission-system.md)
- [响应格式](cloud-response-format.md)
- [缓存系统](cloud-cache-system.md)
- [框架概述](cloud-framework-overview.md)
- [定时器（定时任务）](cloud-timer.md)
- [固定出口IP](cloud-eip.md)
- [使用axios等工具请求云函数或云对象](cloud-http-request.md)
- [URL化之URL重写](cloud-url-rewrite.md)
- [使用crypto进行加密解密](cloud-crypto-part1.md)
- [AES加解密和RSA算法](cloud-crypto-part2.md)
- [RSA签名和密钥生成](cloud-crypto-part3.md)
- [表单验证规则](cloud-form-rules.md)
- [SSE通道（Server-Sent Events）](cloud-sse-channel.md)
- [可重入锁](cloud-reentrant-lock.md)
- [速率限制](cloud-rate-limit.md)
- [WebSocket支持](cloud-websocket-part1.md)
- [WebSocket高级用法](cloud-websocket-part2.md)
- [常见问题解答](cloud-question.md)

### 云函数示例代码
- [云函数示例模板](template/云函数示例模板.md) - 云函数开发模板和规范
- [数据库API示例文档](cloud-function-db-api.md) - 数据库增删改查操作示例
- [开放平台API示例文档](cloud-function-openapi.md) - 微信等第三方平台集成示例
- [基础测试函数文档](cloud-function-test-basic.md) - 单元测试和功能测试示例
- [高级测试函数文档](cloud-function-test-advanced.md) - 集成测试和性能测试示例

### 扩展存储上的文件处理api
- [图片处理](cloud-ext-storage-images.md)
- [视频处理](cloud-ext-storage-videos.md)

### 管理后台
- [框架概述](admin-framework-overview.md)
- [表格组件](admin-table-component.md) - 表格组件示例
- [表单组件](admin-form-component.md) - 表单组件示例
- [页面模板](admin-page-templates.md)
- [菜单配置](admin-menu-config.md)
- [权限系统](admin-permission-system.md)

### VK Pay支付
- [介绍](vk-pay-introduction.md)
- [配置](vk-pay-configuration.md)
- [API使用](vk-pay-api-usage.md)
- [前端回调](vk-pay-callback-frontend.md)
- [错误处理](vk-pay-error-handling.md)
- [高级功能](vk-pay-advanced.md)

## 🚀 快速开始

1. 克隆本项目
2. 使用 Context7 工具进行文档检索
3. 根据需求查阅相关文档

## 🔍 文档检索

本项目已配置 Context7 支持，可以使用以下命令进行文档检索：

```bash
# 使用 Context7 检索文档
context7 search "你的搜索关键词"
```

## 📝 贡献指南

欢迎提交 Pull Request 来完善文档内容。

## 📄 许可证

MIT License