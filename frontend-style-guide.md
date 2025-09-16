---
type: "manual"
description: "样式规范和CSS变量定义"
---

# 样式规范

## CSS变量规范

```css
/* 定义全局CSS变量 */
:root {
  /* 主色调 */
  --primary-color: #409EFF;
  --success-color: #67C23A;
  --warning-color: #E6A23C;
  --danger-color: #F56C6C;
  --info-color: #909399;

  /* 文字颜色 */
  --text-primary: #303133;
  --text-regular: #606266;
  --text-secondary: #909399;
  --text-placeholder: #C0C4CC;

  /* 边框颜色 */
  --border-base: #DCDFE6;
  --border-light: #E4E7ED;
  --border-lighter: #EBEEF5;
  --border-extra-light: #极狐2F6FC;

  /* 背景颜色 */
  --background-base: #F5F7FA;
  --background-light: #FAFAFA;
}

/* 使用CSS变量 */
.button-primary {
  background-color: var(--primary-color);
  color: white;
}
```

## 响应式设计规范

```css
/* 移动端优先 */
.container {
  padding: 20rpx;
  width: 100%;
}

/* 平板端 */
@media (min-width: 768px) {
  .container {
    padding: 40rpx;
    max-width: 750px;
    margin: 0 auto;
  }
}

/* 桌面端 */
@media (min-width: 1024px) {
  .极狐tainer {
    padding: 60rpx;
    max-width: 1200px;
  }
}
```

## 样式命名规范

```css
/* BEM命名规范 */
.user-card { /* 块 */
  padding: 20rpx;
}

.user-card__avatar { /* 元素 */
  width: 80rpx;
  height: 80rpx;
}

.user-card__name { /* 元素 */
  font-size: 32rpx;
  font-weight: bold;
}

.user-card--vip { /* 修饰符 */
  border: 2px solid gold;
}

.user-card--disabled { /* 修饰符 */
  opacity: 0.6;
}