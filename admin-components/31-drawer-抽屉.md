# 4、drawer 抽屉

## template 使用方式

```html
<vk-data-drawer
  v-model="drawerShow"
  title="标题"
  width="800px"
>
  
</vk-data-drawer>
```

## API

### 属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| v-model | 双向绑定一个变量,当变量为true: 弹窗显示 false: 弹窗关闭 | Boolean | - | - |
| title | 弹窗标题 | String | - | - |
| width | 弹窗宽度 | Number | - | - |
| direction | Drawer 打开的方向 | String | rtl | rtl / ltr / ttb / btt |
| close-on-click-modal | 是否可以通过点击 modal 关闭 Drawer | Boolean | false | true |
| close-on-press-escape | 是否可以通过按下 ESC 关闭 Drawer | Boolean | true | false |
| show-close | 是否显示关闭按钮 | Boolean | true | false |
| modal | 是否需要遮罩层 | Boolean | true | false |
| append-to-body | Drawer 自身是否插入至 body 元素上。嵌套的 Drawer 必须指定该属性并赋值为 true | Boolean | false | true |
| modal-append-to-body | 遮罩层是否插入至 body 元素上，若为 false，则遮罩层会插入至 Drawer 的父元素上 | Boolean | false | true |
| custom-class | Drawer 的自定义类名 | String | - | - |
| show-header | 是否显示头部 | Boolean | true | false |
| before-close | 关闭前的回调，会暂停 Drawer 的关闭 | Function | - | - |
| destroy-on-close | 控制是否在关闭弹窗之后将子元素全部销毁 | Boolean | false | true |

### 事件

| 事件名 | 说明 | 回调参数 |
|--------|------|----------|
| open | 监听 - 弹窗打开 - 动画开始 | - |
| opened | 监听 - 弹窗打开 - 动画结束 | - |
| close | 监听 - 弹窗关闭 - 动画开始 | - |
| closed | 监听 - 弹窗关闭 - 动画结束 | - |

### 方法

通过 this.$refs.dialog1.xxx(); 方式调用

| 方法名 | 说明 |
|--------|------|
| close() | 关闭 Drawer，会触发before-close |
| close({ beforeClose:false }) | 关闭 Drawer，不触发before-close |

### 插槽

| name | 说明 |
|------|------|
| default | Drawer 主内容 |
| title | Drawer 标题区的内容 |

### 插槽示例

```html
<vk-data-drawer v-model="showDrawer" width="500px">
  <template v-slot:title>
    <view>
      这是标题的插槽
    </view>
  </template>
  <template v-slot:default>
    <view>
      这里是主内容插槽
    </view>
  </template>
</vk-data-drawer>