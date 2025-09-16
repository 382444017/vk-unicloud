---
type: "manual"
description: "VK-UniCloud 管理端框架概述和项目结构规范"
---

# 管理端概述

VK-UniCloud管理端是基于Vue + Element UI/Plus构建的后台管理系统，提供完整的CRUD页面模板和组件。

## 核心特性

- **万能表格**: 通过JSON配置快速生成CRUD页面
- **万能表单**: 支持60+字段类型的表单组件
- **权限控制**: 基于RBAC的权限管理系统
- **菜单管理**: 动态菜单配置系统
- **响应式设计**: 适配不同屏幕尺寸

## 技术栈

- **前端框架**: Vue 2/3 + Element UI/Plus
- **状态管理**: Vuex/Pinia (可选)
- **路由管理**: Vue Router
- **HTTP客户端**: VK-UniCloud内置请求方法
- **构建工具**: HBuilderX + uni-app

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

**重要说明**: 管理端不包含独立的云开发环境，通过配置关联到客户端的云开发环境，共享云函数和数据库。