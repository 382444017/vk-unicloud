# VK Unicloud 开发文档

VK Unicloud 是一个全栈开发解决方案，提供从前端到云端的一站式开发体验。本项目包含 VK Unicloud 平台的完整开发文档，涵盖前端开发、云函数开发、管理后台开发等多个方面。

## 🌟 平台特色

- **全栈一体化**：统一的开发规范和 API 设计，前后端无缝协作
- **组件化开发**：丰富的内置组件库，提高开发效率
- **云原生架构**：基于云函数和云数据库，弹性扩展
- **开箱即用**：完善的权限系统、支付系统、文件处理等功能

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
- [弹窗组件示例](admin-dialog-examples.md) - 模态框、抽屉、消息提示示例
- [详情组件示例](admin-detail-examples.md) - 详情展示、卡片布局示例
- [上传组件示例](admin-upload-examples.md) - 文件上传、图片上传、拖拽上传示例
- [Element UI 组件示例](admin-element-examples.md) - Element UI 表单、表格、弹窗组件示例
- [图标组件示例](admin-icons-examples.md) - Element UI 图标展示和使用示例
- [输入框组件示例](admin-input-examples.md) - 各种输入框类型和高级功能示例

### 云开发
- [云函数](cloud-function-development.md)
- [数据库 API](cloud-database-docs) - 拆分为多个子文档
  - [1. 数据库基础操作](cloud-database-docs/1-database-basic-operations.md)
  - [2. 万能连表查询](cloud-database-docs/2-database-selects.md)
  - [3. 查询操作符详解](cloud-database-docs/3-database-query-operators.md)
  - [4. 数据库聚合查询](cloud-database-docs/4-database-aggregation.md)
  - [5. DAO层的作用及方法命名规范](cloud-database-docs/5-dao-layer.md)
  - [6. 事务使用](cloud-database-docs/6-database-transactions.md)
  - [7. 关于schema的说明](cloud-database-docs/7-database-schema.md)
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
- [表单验证](cloud-form-rules.md)
- [SSE通道（Server-Sent Events）](cloud-sse-channel.md)
- [可重入锁](cloud-reentrant-lock.md)
- [速率限制](cloud-rate-limit.md)
- [WebSocket支持](cloud-websocket-part1.md)
- [WebSocket高级用法](cloud-websocket-part2.md)
- [中间件过滤器](cloud-middleware-filter.md)
- [常见问题解答](cloud-question.md)
- [云函数故障排除](cloud-function-troubleshooting.md)

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
- [表格组件](admin-table-component.md) - 万能表格组件
- [表单组件](admin-form-component.md) - 万能表单组件
- [页面模板](admin-page-templates.md)
- [菜单配置](admin-menu-config.md)
- [权限系统](admin-permission-system.md)
- [内置组件详细](admin-components) - VK框架组件完整示例和用法
  - [0-公共属性](admin-components/0-公共属性.md)
  - [1-text-单行文本](admin-components/1-text-单行文本.md)
  - [2-textarea-多行文本](admin-components/2-textarea-多行文本.md)
  - [3-money-金额输入](admin-components/3-money-金额输入.md)
  - [4-number-数字输入](admin-components/4-number-数字输入.md)
  - [5-percentage-百分比输入](admin-components/5-percentage-百分比输入.md)
  - [6-discount-折扣输入](admin-components/6-discount-折扣输入.md)
  - [7-radio-单选](admin-components/7-radio-单选.md)
  - [8-checkbox-多选](admin-components/8-checkbox-多选.md)
  - [9-select-下拉选择](admin-components/9-select-下拉选择.md)
  - [10-remote-select-远程下拉](admin-components/10-remote-select-远程下拉.md)
  - [11-cascader-级联选择](admin-components/11-cascader-级联选择.md)
  - [12-table-select-通过表格选择](admin-components/12-table-select-通过表格选择.md)
  - [13-address-地址选择](admin-components/13-address-地址选择.md)
  - [14-switch-开关](admin-components/14-switch-开关.md)
  - [15-rate-评分](admin-components/15-rate-评分.md)
  - [16-slider-滑块](admin-components/16-slider-滑块.md)
  - [17-color-颜色选择](admin-components/17-color-颜色选择.md)
  - [18-image-图片上传](admin-components/18-image-图片上传.md)
  - [19-file-文件上传](admin-components/19-file-文件上传.md)
  - [20-date-日期选择](admin-components/20-date-日期选择.md)
  - [21-time-时间选择](admin-components/21-time-时间选择.md)
  - [22-editor-富文本编辑器](admin-components/22-editor-富文本编辑器.md)
  - [23-json-JSON编辑器](admin-components/23-json-JSON编辑器.md)
  - [24-file-select-素材库选择](admin-components/24-file-select-素材库选择.md)
  - [25-icon-图标选择](admin-components/25-icon-图标选择.md)
  - [26-tree-select-树形选择](admin-components/26-tree-select-树形选择.md)
  - [27-map-地图选址选择](admin-components/27-map-地图选址选择.md)
  - [28-tag-标签](admin-components/28-tag-标签.md)
  - [29-link-外部超链接](admin-components/29-link-外部超链接.md)
  - [30-dialog-弹窗](admin-components/30-dialog-弹窗.md)
  - [31-drawer-抽屉](admin-components/31-drawer-抽屉.md)
  - [32-vk-data-menu-nav-左侧菜单](admin-components/32-vk-data-menu-nav-左侧菜单.md)
  - [33-custom-editor-tinymce-多功能富文本编辑器](admin-components/33-custom-editor-tinymce-多功能富文本编辑器.md)

### VK Pay支付
- [介绍](vk-pay-introduction.md)
- [配置](vk-pay-configuration.md)
- [API使用](vk-pay-api-usage.md)
- [前端回调](vk-pay-callback-frontend.md)
- [错误处理](vk-pay-error-handling.md)
- [高级功能](vk-pay-advanced.md)

## 🚀 快速开始

1. 克隆本项目
   ```bash
   git clone <项目地址>
   ```

2. 安装 Context7 工具（如果需要文档检索功能）
   ```bash
   npm install -g context7
   ```

3. 浏览文档内容
   - 直接打开 Markdown 文件阅读
   - 或使用 Context7 进行文档检索

## 🔍 文档检索

本项目已配置 Context7 支持，可以使用以下命令进行文档检索：

```bash
# 使用 Context7 检索文档
context7 search "你的搜索关键词"
```

## 📖 阅读建议

1. 如果你是初学者，建议按以下顺序阅读：
   - [前端开发](#前端开发)
   - [云开发](#云开发)
   - [管理后台](#管理后台)

2. 如果你已有一定基础，可以直接查找需要的文档：
   - 使用文档检索功能快速定位
   - 浏览[文档分类](#文档分类)找到相关主题

## 🤝 贡献指南

我们欢迎社区贡献，帮助完善 VK Unicloud 文档：

1. Fork 本项目
2. 创建你的特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交你的更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启 Pull Request

### 文档规范

- 使用 Markdown 格式编写
- 遵循统一的文档结构
- 提供实际可运行的代码示例
- 保持语言简洁明了

## 📄 许可证

MIT License

## 📞 联系方式

如有问题或建议，请通过以下方式联系我们：
- 提交 Issue
- 发送邮件至 [维护者邮箱]