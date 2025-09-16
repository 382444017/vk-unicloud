---
type: "manual"
description: "VK-UniCloud 管理端框架概述和项目结构规范"
---

# 管理端概述

VK-UniCloud管理端（vk-unicloud-admin）是基于 `uniapp` + `unicloud` + `uni-id` + `vk-unicloud-router` + `element` 的一套快速admin完整开发框架。

## 核心价值

**最大亮点**：使用 `vk-unicloud-admin` 后，即使你是一个纯后端，不会写任何css，照样可以写出功能强大且页面好看的admin管理系统。

## 与官方 unicloud-admin 的关系

没有太大关系，用户在此框架上的编码风格与 `unicloud-admin` 差别较大。但框架兼容 `unicloud-admin`（官方的admin插件可以直接用在 `vk-unicloud-admin` 中）。

## 核心特性

- **万能表格**: 通过JSON配置快速生成CRUD页面，支持分页、排序、多条件搜索、导出Excel等
- **万能表单**: 支持60+字段类型的表单组件，支持可视化拖拽生成
- **权限控制**: 基于RBAC的权限管理系统，权限可精确到每一个云函数
- **菜单管理**: 动态菜单配置系统，支持开发环境和生产环境分离
- **主题系统**: 内置3个主题（纯黑，纯白，黑白）且支持自定义主题
- **高自由度**: 支持插槽自定义，也支持直接使用element原生代码
- **异常重试**: 连接异常时自动重新查询，提升用户体验
- **pages-dev.json机制**: 开发调试页面不会被发布到正式版中

## 技术栈与兼容性

- **前端框架**: Vue 2/3 + Element UI/Plus
- **状态管理**: Vuex/Pinia (可选)
- **路由管理**: Vue Router
- **HTTP客户端**: VK-UniCloud内置请求方法
- **构建工具**: HBuilderX + uni-app

## 内置功能页面

框架内置了以下完整的系统管理页面：

- ✅ 用户管理 - 完整的用户CRUD和权限分配
- ✅ 角色管理 - 角色创建、编辑和权限配置
- ✅ 权限管理 - 细粒度权限控制和管理
- ✅ 菜单管理 - 动态菜单配置和管理
- ✅ 应用管理 - 多应用配置和管理
- ✅ 系统缓存管理 - Redis缓存管理和监控
- ✅ 动态组件数据 - 动态组件配置和管理
- ✅ 用户登录日志 - 用户登录记录和审计
- ✅ 系统操作日志 - 系统操作记录和追踪
- ✅ VK框架组件演示 - 所有组件的演示示例
- ✅ element静态功能演示 - Element UI功能演示
- ✅ App升级中心管理 - App版本管理和升级（1.14.0+）
- ✅ 持续完善中 - 不断添加新功能和优化

## 快速开始

- **下载地址**: [https://ext.dcloud.net.cn/plugin?id=5043](https://ext.dcloud.net.cn/plugin?id=5043)
- **文档地址**: [https://vkdoc.fsq.pub/admin/](https://vkdoc.fsq.pub/admin/)
- **社区支持**: Q群：22466457（众多开发者，有问必答）
- **表单可视化工具**: [https://vkunicloud.fsq.pub/vk-form-visualizer/](https://vkunicloud.fsq.pub/vk-form-visualizer/)

**注意**: 只要你开发过传统 `vue admin` 项目，那么上手此框架的学习成本几乎为0。

**兼容性说明**: 虽然是PC admin框架，但也是uniapp项目，支持uniapp所有H5的API和插件市场所有uni-admin插件。

## 项目结构

### 管理端特有目录结构

```
管理端/
├── src/
│   ├── pages/              # 页面文件
│   ├── components/         # 组件文件
│   ├── static/            # 静态资源
│   └── utils/             # 工具函数
├── pages_template/        # 页面模板 (管理端特有)
│   ├── components/        # 模板组件
│   ├── list/              # 列表页模板
│   ├── add/               # 新增页模板
│   └── edit/              # 编辑页模板
├── static_menu/           # 菜单配置 (管理端特有)
│   ├── menu.json          # 生产环境菜单
│   └── menu-dev.json      # 开发环境菜单
├── pages.json            # 页面配置
└── pages-dev.json        # 开发页面配置
```