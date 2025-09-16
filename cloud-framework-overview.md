---
type: "manual"
description: "VK-UniCloud 框架概述和项目结构规范"
---

# VK-UniCloud 框架概述

## 框架概述

VK-UniCloud 是一个基于 UniCloud 的快速开发框架，包含客户端框架和管理端框架两部分：

- **客户端框架**: vk-unicloud-router - 云函数路由模式开发框架
- **管理端框架**: vk-unicloud-admin - 基于 Element UI 的后台管理系统

### 核心特性

#### 1. 云函数路由模式
- 使用纯原生代码实现路由模式，兼容性强
- 减少云函数数量，一个云函数实现多个功能
- 支持云函数和云对象两种模式
- 美化云函数请求日志，便于调试

#### 2. 内置丰富API
- 集成 uni-id 用户系统
- 提供 vk.baseDao 数据库API
- 内置微信、支付宝、百度等平台服务端API
- 支持短信、邮件、支付等功能

#### 3. 权限管理
  - 全局过滤器，过滤非法请求
  - 完善的用户角色权限系统
  - 支持权限精确到每个云函数

#### 4. 开发工具
- 万能表格组件（管理端）
- 万能表单组件（管理端）
- 代码生成器
- 调试工具

## 项目结构

### 云端目录结构
```
uniCloud-alipay/
├── cloudfunctions/           # 云函数目录
│   ├── common/              # 公共模块
│   │   ├── uni-config-center/  # 配置中心
│   │   ├── uni-id/            # 用户身份管理
│   │   └── vk-unicloud/       # VK框架核心
│   └── router/              # 路由云函数
│       ├── service/         # 业务逻辑
│       │   ├── admin/       # 管理端业务
│       │   ├── client/      # 客户端业务
│       │   └── user/        # 用户中心
│       └── index.js         # 入口文件
├── database/                # 数据库Schema
│   ├── uni-id-users.schema.json
│   └── custom-table.schema.json
└── cloudobjects/           # 云对象目录（可选）
```

### 云函数目录权限规范
```
service/
├── admin/                   # 管理端业务逻辑
│   └── [模块]/
│       ├── sys/            # 需要特定权限验证
│       ├── kh/             # 需要登录 + allow_login_background
│       └── pub/            # 公开访问
├── client/                  # 客户端业务逻辑
│   └── [模块]/
│       ├── kh/             # 需要登录
│       └── pub/            # 公开访问
└── user/                    # 用户中心服务
    ├── sys/                # 管理员权限
    ├── kh/                 # 用户登录权限
    └── pub/                # 公开访问