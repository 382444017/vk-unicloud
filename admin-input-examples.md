# VK框架输入框组件示例

本文档展示了 `pages_template/components/input/` 目录下的VK框架输入框组件示例代码。

## 基础输入框组件

### input.vue - 聚合输入框

```vue
<template>
  <view class="page">
    <vk-data-page-header title="Input 表单输入" subTitle="聚合输入框"></vk-data-page-header>
    <view class="page-body">
      <view class="tips">根据type自动渲染的不同的输入框</view>
      <view class="mt15">
        <el-radio-group v-model="type">
          <el-radio label="number">数字输入框</el-radio>
          <el-radio label="money">金额输入框</el-radio>
          <el-radio label="percentage">百分比输入框</el-radio>
          <el-radio label="discount">折扣输入框</el-radio>
          <el-radio label="number-box">步进器</el-radio>
          <el-radio label="textarea">多行文本输入框</el-radio>
        </el-radio-group>
      </view>
      <view class="mt15">
        <vk-data-input :type="type" v-model="form1.value" :precision="0" width="300px" placeholder="请输入"></vk-data-input>
      </view>
    </view>
  </view>
</template>
```

**功能特点：**
- 通过 `type` 属性自动渲染不同类型的输入框
- 支持数字、金额、百分比、折扣、步进器、多行文本等多种类型
- 可设置精度、宽度等参数

## 选择器组件

### input-select.vue - 下拉选择器

```vue
<template>
  <view class="page">
    <vk-data-page-header title="Input 表单输入" subTitle="select 下拉选择"></vk-data-page-header>
    <view class="page-body">
      <!-- 基础下拉选择 -->
      <vk-data-input-select
        v-model="form1.value1"
        :localdata='[{ value:1, label:"选项1" }, { value:2, label:"选项2" }]'
        size="small" clearable placeholder="请选择"
      ></vk-data-input-select>

      <!-- 分组下拉选择 -->
      <vk-data-input-select
        v-model="form1.value2" :group="true"
        :localdata='[
          { label: "分组1", children: [{ value:1, label:"选项1" }, { value:2, label:"选项2" }] },
          { label: "分组2", children: [{ value:3, label:"选项3" }, { value:4, label:"选项4" }] }
        ]' size="small" clearable placeholder="请选择"
      ></vk-data-input-select>
    </view>
  </view>
</template>
```

**功能特点：**
- 支持基础下拉选择和分组下拉选择
- 可设置大小、清空按钮、占位符等
- 支持本地数据源配置

### input-remote-select.vue - 远程搜索选择器

```vue
<template>
  <view class="page">
    <vk-data-page-header title="Input 表单输入" subTitle="remote-select 远程搜索选择器"></vk-data-page-header>
    <view class="page-body">
      <view class="tips">主要用于选择 user_id 等外键字段</view>
      <vk-data-input-remote-select
        v-model="form1.user_id" placeholder="请输入用户名"
        action="admin/select/kh/user" width="300px"
      ></vk-data-input-remote-select>
    </view>
  </view>
</template>
```

**功能特点：**
- 支持远程搜索和选择
- 主要用于外键字段选择（如user_id）
- 通过 `action` 属性配置后端API接口

## 数字输入组件

### input-number.vue - 数字输入框

```vue
<template>
  <view class="page">
    <vk-data-page-header title="Input 表单输入" subTitle="number 数字"></vk-data-page-header>
    <view class="page-body">
      <!-- 正整数 -->
      <vk-data-input-number v-model="form1.value1" :precision="0" width="300px" placeholder="请输入数字"></vk-data-input-number>

      <!-- 2位小数 -->
      <vk-data-input-number v-model="form1.value2" :precision="2" width="300px" placeholder="请输入数字"></vk-data-input-number>

      <!-- 最大值限制 -->
      <vk-data-input-number ref="input-number-3" v-model="form1.value3" :precision="0" width="300px"
        placeholder="请输入数字" :max="100"></vk-data-input-number>

      <!-- 方法调用示例 -->
      <el-button @click="focus">获得焦点</el-button>
      <el-button @click="select">选中文字</el-button>
    </view>
  </view>
</template>

<script>
methods: {
  focus() { this.$refs["input-number-3"].focus(); },
  select() { this.$refs["input-number-3"].select(); }
}
</script>
```

**功能特点：**
- 支持整数和小数输入
- 可设置精度、最大值、最小值等限制
- 支持编程式方法调用（focus、select等）

### input-number-box.vue - 步进器

```vue
<template>
  <view class="page">
    <vk-data-page-header title="Input 表单输入" subTitle="number-box 步进器"></vk-data-page-header>
    <view class="page-body">
      <!-- 整数步进器 -->
      <vk-data-input-number-box v-model="form1.value1" :precision="0" width="200px" placeholder="请输入数字"></vk-data-input-number-box>

      <!-- 小数步进器 -->
      <vk-data-input-number-box v-model="form1.value2" :precision="2" :step="0.01" width="200px" placeholder="请输入数字"></vk-data-input-number-box>

      <!-- 范围限制 -->
      <vk-data-input-number-box v-model="form1.value3" :precision="0" :max="10" :min="1" width="200px" placeholder="请输入数字"></vk-data-input-number-box>

      <!-- 隐藏按钮 -->
      <vk-data-input-number-box v-model="form1.value4" :precision="0" :controls="false" width="200px" placeholder="请输入数字"></vk-data-input-number-box>
    </view>
  </view>
</template>
```

**功能特点：**
- 支持步进器形式的数字输入
- 可设置步长、范围限制
- 支持隐藏控制按钮

## 特殊格式输入组件

### input-money.vue - 金额输入框

```vue
<template>
  <view class="page">
    <vk-data-page-header title="Input 表单输入" subTitle="money 金额"></vk-data-page-header>
    <view class="page-body">
      <view class="tips">金额单位是分，输入框显示1，实际值100</view>
      <vk-data-input-money v-model="form1.value1" placeholder="请输入金额" width="300px"></vk-data-input-money>
    </view>
  </view>
</template>
```

**功能特点：**
- 金额输入专用组件
- 显示值为元，实际存储值为分（1元 = 100分）
- 自动处理金额格式转换

### input-percentage.vue - 百分比输入框

```vue
<template>
  <view class="page">
    <vk-data-page-header title="Input 表单输入" subTitle="percentage 百分比"></vk-data-page-header>
    <view class="page-body">
      <view class="tips">输入框显示1，实际值0.01</view>
      <vk-data-input-percentage v-model="form1.value1" placeholder="请输入百分比" :precision="2" :max="100" width="300px"></vk-data-input-percentage>
    </view>
  </view>
</template>
```

**功能特点：**
- 百分比输入专用组件
- 显示值为百分比数值，实际存储值为小数（1% = 0.01）
- 支持精度控制和最大值限制

## 地址选择组件

### input-address.vue - 省市区选择器

```vue
<template>
  <view class="page">
    <vk-data-page-header title="Input 表单输入" subTitle="address 省市区选择"></vk-data-page-header>
    <view class="page-body">
      <view class="tips">同时支持下拉选择和输入搜索</view>
      
      <!-- 省市区三级选择 -->
      <vk-data-input-address v-model="form1.address1" placeholder="请选择省市区"></vk-data-input-address>

      <!-- 省市二级选择 -->
      <vk-data-input-address v-model="form1.address2" placeholder="请选择省市" :level="2"></vk-data-input-address>

      <!-- 省份一级选择 -->
      <vk-data-input-address v-model="form1.address3" placeholder="请选择省" :level="1"></vk-data-input-address>
    </view>
  </view>
</template>
```

**功能特点：**
- 支持省市区三级联动选择
- 可通过 `level` 属性控制选择层级（1-3级）
- 支持搜索功能

### input-province.vue - 省份选择器

```vue
<template>
  <view class="page">
    <vk-data-page-header title="Input 表单输入" subTitle="province 省份选择"></vk-data-page-header>
    <view class="page-body">
      <view class="tips">同时支持下拉选择和输入搜索</view>
      <vk-data-input-province v-model="form1.value"></vk-data-input-province>
    </view>
  </view>
</template>
```

**功能特点：**
- 专门的省份选择器
- 支持搜索和下拉选择

## 高级输入组件

### input-json.vue - JSON编辑器

```vue
<template>
  <view class="page">
    <vk-data-page-header title="Input JSON" subTitle="JSON编辑器"></vk-data-page-header>
    <view class="page-body">
      <vk-data-input-json v-model="form1.value1" mode="code" placeholder="请输入JSON"></vk-data-input-json>
    </view>
  </view>
</template>
```

**功能特点：**
- 专业的JSON编辑器
- 支持代码模式编辑
- 语法高亮和格式验证

## 使用说明

### 组件命名规范

所有VK输入框组件都以 `vk-data-input-` 为前缀：
- `vk-data-input` - 基础输入框
- `vk-data-input-select` - 下拉选择器
- `vk-data-input-remote-select` - 远程搜索选择器
- `vk-data-input-number` - 数字输入框
- `vk-data-input-number-box` - 步进器
- `vk-data-input-money` - 金额输入框
- `vk-data-input-percentage` - 百分比输入框
- `vk-data-input-address` - 地址选择器
- `vk-data-input-province` - 省份选择器
- `vk-data-input-json` - JSON编辑器

### 通用属性

| 属性 | 类型 | 说明 | 示例 |
|------|------|------|------|
| v-model | any | 双向绑定值 | `v-model="form.value"` |
| placeholder | string | 占位文本 | `placeholder="请输入"` |
| width | string | 宽度设置 | `width="300px"` |
| size | string | 组件大小 | `size="small"` |
| clearable | boolean | 是否可清空 | `:clearable="true"` |
| precision | number | 数值精度 | `:precision="2"` |
| min | number | 最小值 | `:min="0"` |
| max | number | 最大值 | `:max="100"` |

### 数据格式说明

1. **金额格式**: 显示为元，存储为分（1元 = 100分）
2. **百分比格式**: 显示为百分比数值，存储为小数（1% = 0.01）
3. **地址格式**: 返回完整的省市区信息对象

### 最佳实践

1. **表单绑定**: 使用 `v-model` 进行双向数据绑定
2. **验证处理**: 结合VK框架的表单验证规则使用
3. **事件处理**: 监听 `change`、`input` 等事件处理业务逻辑
4. **性能优化**: 对于远程选择器，合理使用防抖和缓存机制

通过以上组件，您可以快速构建各种复杂的表单输入界面，提高开发效率。