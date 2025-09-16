# 弹窗组件示例

本文档展示了 `pages_template/components/dialog/` 目录下的弹窗组件示例代码。

## dialog-basic.vue - 基础弹窗

```vue
<template>
  <view class="page">
    <vk-data-page-header title="Dialog 弹窗" subTitle="基础弹窗"></vk-data-page-header>
    <view class="page-body">
      <el-button @click="dialogVisible = true">打开弹窗</el-button>
      
      <vk-data-dialog
        v-model="dialogVisible"
        title="基础弹窗标题"
        width="500px"
        @confirm="handleConfirm"
        @cancel="handleCancel"
      >
        <p>这是弹窗内容区域</p>
        <p>可以放置任何自定义内容</p>
      </vk-data-dialog>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      dialogVisible: false
    }
  },
  methods: {
    handleConfirm() {
      console.log('确认按钮点击')
      this.dialogVisible = false
    },
    handleCancel() {
      console.log('取消按钮点击')
      this.dialogVisible = false
    }
  }
}
</script>
```

## dialog-form.vue - 表单弹窗

```vue
<template>
  <view class="page">
    <vk-data-page-header title="Form 表单" subTitle="弹窗表单"></vk-data-page-header>
    <view class="page-body">
      <el-button @click="form1.props.show = true">弹出表单</el-button>

      <vk-data-dialog
        v-model="form1.props.show"
        title="表单标题"
        width="600px"
        mode="form"
      >
        <vk-data-form
          ref="form1"
          v-model="form1.data"
          :action="form1.props.action"
          :columns="form1.props.columns"
          :rules="form1.props.rules"
          :form-type="form1.props.formType"
          :loading.sync="form1.props.loading"
          :auto-close="true"
          label-width="140px"
          @success="onFormSuccess"
        ></vk-data-form>
      </vk-data-dialog>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      form1: {
        data: {
          text: "",
          number: null,
          radio: 1,
          checkbox: [1,2]
        },
        props: {
          action: "template/test/sys/test",
          columns: [
            { key: "text", title: "text类型字段", type: "text" },
            { key: "number", title: "number类型字段", type: "number" },
            { key: "radio", title: "radio类型字段", type: "radio",
              data: [
                { value: 1, label: "选项1" },
                { value: 2, label: "选项2" }
              ]
            },
            { key: "checkbox", title: "checkbox类型字段", type: "checkbox",
              data: [
                { value: 1, label: "选项1" },
                { value: 2, label: "选项2" }
              ]
            }
          ],
          rules: {
            text: [
              { required: true, message: "text不能为空", trigger: "change" }
            ]
          },
          formType: "",
          loading: false,
          show: false
        }
      }
    }
  },
  methods: {
    onFormSuccess() {
      console.log("表单提交成功")
    }
  }
}
</script>
```

## dialog-table.vue - 表格弹窗

```vue
<template>
  <view class="page">
    <vk-data-page-header title="Dialog 弹窗" subTitle="表格弹窗"></vk-data-page-header>
    <view class="page-body">
      <el-button @click="dialogVisible = true">打开表格弹窗</el-button>
      
      <vk-data-dialog
        v-model="dialogVisible"
        title="表格弹窗"
        width="800px"
        height="600px"
      >
        <vk-data-table
          :data="tableData"
          :columns="tableColumns"
          :pagination="true"
        ></vk-data-table>
      </vk-data-dialog>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      dialogVisible: false,
      tableData: [
        { id: 1, name: "张三", age: 25, city: "北京" },
        { id: 2, name: "李四", age: 30, city: "上海" },
        { id: 3, name: "王五", age: 28, city: "广州" }
      ],
      tableColumns: [
        { key: "id", title: "ID", width: 100 },
        { key: "name", title: "姓名", width: 150 },
        { key: "age", title: "年龄", width: 100 },
        { key: "city", title: "城市", width: 150 }
      ]
    }
  }
}
</script>
```

## 弹窗配置参数

### vk-data-dialog 属性

| 参数 | 说明 | 类型 | 默认值 |
|------|------|------|--------|
| v-model | 控制弹窗显示/隐藏 | Boolean | false |
| title | 弹窗标题 | String | '' |
| width | 弹窗宽度 | String | '500px' |
| height | 弹窗高度 | String | 'auto' |
| mode | 弹窗模式（form/table/normal） | String | 'normal' |
| show-close | 是否显示关闭按钮 | Boolean | true |
| close-on-click-modal | 是否可以通过点击 modal 关闭弹窗 | Boolean | true |
| close-on-press-escape | 是否可以通过按下 ESC 关闭弹窗 | Boolean | true |

### 事件

| 事件名 | 说明 | 参数 |
|--------|------|------|
| confirm | 点击确认按钮时触发 | - |
| cancel | 点击取消按钮时触发 | - |
| close | 弹窗关闭时触发 | - |
| open | 弹窗打开时触发 | - |

## 使用技巧

### 1. 表单弹窗自动关闭

```vue
<vk-data-dialog v-model="dialogVisible" mode="form">
  <vk-data-form
    @success="dialogVisible = false"
    @cancel="dialogVisible = false"
  ></vk-data-form>
</vk-data-dialog>
```

### 2. 自定义弹窗底部按钮

```vue
<vk-data-dialog v-model="dialogVisible">
  <template #footer>
    <el-button @click="dialogVisible = false">取消</el-button>
    <el-button type="primary" @click="handleSubmit">提交</el-button>
  </template>
</vk-data-dialog>
```

### 3. 全屏弹窗

```vue
<vk-data-dialog
  v-model="dialogVisible"
  width="90%"
  height="80%"
  :fullscreen="true"
>
  <!-- 弹窗内容 -->
</vk-data-dialog>
```

### 4. 嵌套弹窗

```vue
<vk-data-dialog v-model="outerDialog">
  <el-button @click="innerDialog = true">打开内层弹窗</el-button>
  
  <vk-data-dialog v-model="innerDialog" append-to-body>
    <p>内层弹窗内容</p>
  </vk-data-dialog>
</vk-data-dialog>
```

## 最佳实践

1. **合理使用弹窗模式**
   - 使用 `mode="form"` 用于表单操作
   - 使用 `mode="table"` 用于数据展示
   - 使用默认模式用于自定义内容

2. **控制弹窗大小**
   - 表单弹窗建议宽度 500-600px
   - 表格弹窗建议宽度 800-1000px
   - 详情弹窗根据内容自适应

3. **处理弹窗状态**
   - 及时清理弹窗内的表单数据
   - 处理弹窗关闭时的回调逻辑
   - 考虑弹窗的嵌套关系

4. **用户体验优化**
   - 提供明确的关闭和确认按钮
   - 支持键盘操作（ESC关闭）
   - 添加加载状态提示