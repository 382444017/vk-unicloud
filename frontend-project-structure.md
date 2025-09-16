---
type: "manual"
description: "项目结构和文件命名规范"
---

# 项目结构规范

## 通用目录结构

```
项目根目录/
├── src/                    # 源码目录
│   ├── pages/             # 页面文件
│   │   ├── index/         # 首页
│   │   ├── auth/          # 认证相关
│   │   └── profile/       # 个人中心
│   ├── components/        # 公共组件
│   │   ├── common/        # 通用组件
│   │   └── business/      # 业务组件
│   ├── utils/            # 工具函数
│   ├── static/           # 静态资源
│   └── uni_modules/      # uni模块
├── uniCloud-alipay/       # 云端代码
├── pages.json            # 页面配置
└── manifest.json         # 应用配置
```

## 文件命名规范

- **页面文件**: `kebab-case.vue` (如: `user-profile.vue`)
- **组件文件**: `PascalCase.vue` (如: `UserCard.vue`)
- **工具文件**: `camelCase.js` (如: `dateUtils.js`)
- **常量文件**: `UPPER_CASE.js` (如: `API_CONSTANTS.js`)