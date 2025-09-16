# 详情组件示例

本文档展示了 `pages_template/components/detail/` 目录下的详情组件示例代码。

## detail-basic.vue - 基础详情

```vue
<template>
  <view class="page">
    <vk-data-page-header
      title="Form 表单详情"
      subTitle="展示表单详情"
    ></vk-data-page-header>
    
    <view class="page-body" style="max-width: 800px;margin: 0 auto;">
      <vk-data-detail
        :data="data"
        :columns="columns"
        size="medium"
        label-width="150px"
      ></vk-data-detail>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      data: {
        text: "这是文本",
        money: 10000,
        number: 10000,
        radio: 1,
        checkbox: [1,2],
        select: 1,
        image: [
          "https://example.com/image1.png",
          "https://example.com/image2.png"
        ],
        province: {
          code: "11",
          name: "北京市"
        },
        address: {
          codes: ["33", "3301", "330110"],
          names: ["浙江省", "杭州市", "余杭区"],
          text: "浙江省杭州市余杭区"
        },
        textarea: "这是多行文本内容",
        json: {
          a: 1,
          b: "aaa"
        },
        switch: true,
        object: {
          a: 1,
          b: "aaa"
        }
      },
      columns: [
        { key: "text", title: "text类型字段", type: "text" },
        { key: "money", title: "money类型字段", type: "money" },
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
        },
        { key: "select", title: "select类型字段", type: "select",
          data: [
            { value: 1, label: "选项1" },
            { value: 2, label: "选项2" }
          ]
        },
        { key: "image", title: "image类型字段", type: "image", limit: 6 },
        { key: "province", title: "province类型字段", type: "province" },
        { key: "address", title: "address类型字段", type: "address" },
        { key: "textarea", title: "textarea类型字段", type: "textarea",
          autosize: { minRows: 6, maxRows: 10 }
        },
        { key: "json", title: "json类型字段", type: "json" },
        { key: "switch", title: "switch类型字段", type: "switch" },
        { key: "object", title: "object类型字段", type: "object",
          columns: [
            { key: "text", title: "text类型字段", type: "text" },
            { key: "switch", title: "switch类型字段", type: "switch" }
          ]
        }
      ]
    }
  }
}
</script>
```

## detail-table.vue - 表格详情

```vue
<template>
  <view class="page">
    <vk-data-page-header
      title="表格详情"
      subTitle="包含表格的详情页面"
    ></vk-data-page-header>
    
    <view class="page-body">
      <!-- 基础信息 -->
      <vk-data-detail
        :data="basicData"
        :columns="basicColumns"
        label-width="120px"
        class="mb-20"
      ></vk-data-detail>
      
      <!-- 表格信息 -->
      <el-card header="相关数据列表">
        <vk-data-table
          :data="tableData"
          :columns="tableColumns"
          :pagination="true"
        ></vk-data-table>
      </el-card>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      basicData: {
        name: "张三",
        age: 28,
        gender: 1,
        department: "技术部",
        position: "前端工程师"
      },
      basicColumns: [
        { key: "name", title: "姓名", type: "text" },
        { key: "age", title: "年龄", type: "number" },
        { key: "gender", title: "性别", type: "radio",
          data: [
            { value: 1, label: "男" },
            { value: 2, label: "女" }
          ]
        },
        { key: "department", title: "部门", type: "text" },
        { key: "position", title: "职位", type: "text" }
      ],
      tableData: [
        { id: 1, project: "项目A", progress: 80, status: 1 },
        { id: 2, project: "项目B", progress: 50, status: 2 },
        { id: 3, project: "项目C", progress: 100, status: 3 }
      ],
      tableColumns: [
        { key: "id", title: "ID", width: 80 },
        { key: "project", title: "项目名称", width: 200 },
        { key: "progress", title: "进度", type: "progress", width: 150 },
        { key: "status", title: "状态", type: "tag", width: 100,
          options: [
            { value: 1, text: "进行中", color: "primary" },
            { value: 2, text: "待审核", color: "warning" },
            { value: 3, text: "已完成", color: "success" }
          ]
        }
      ]
    }
  }
}
</script>

<style scoped>
.mb-20 {
  margin-bottom: 20px;
}
</style>
```

## 详情字段类型

### 基础字段类型

```javascript
// 文本显示
{ key: "name", title: "姓名", type: "text" }

// 数字显示
{ key: "age", title: "年龄", type: "number" }

// 金额显示
{ key: "salary", title: "薪资", type: "money" }

// 百分比显示
{ key: "progress", title: "进度", type: "percent" }
```

### 选择器字段类型

```javascript
// 单选显示
{ key: "gender", title: "性别", type: "radio",
  data: [
    { value: 1, label: "男" },
    { value: 2, label: "女" }
  ]
}

// 多选显示
{ key: "hobbies", title: "爱好", type: "checkbox",
  data: [
    { value: "reading", label: "阅读" },
    { value: "sports", label: "运动" }
  ]
}

// 下拉选择显示
{ key: "department", title: "部门", type: "select",
  data: [
    { value: "tech", label: "技术部" },
    { value: "sales", label: "销售部" }
  ]
}
```

### 特殊字段类型

```javascript
// 图片显示
{ key: "avatar", title: "头像", type: "image", limit: 6 }

// 文件显示
{ key: "attachments", title: "附件", type: "file" }

// JSON数据格式化显示
{ key: "config", title: "配置信息", type: "json" }

// 代码高亮显示
{ key: "code", title: "代码片段", type: "code", language: "javascript" }

// 地址信息显示
{ key: "address", title: "地址信息", type: "address" }

// 省份信息显示
{ key: "province", title: "省份", type: "province" }
```

### 状态字段类型

```javascript
// 标签显示
{ key: "status", title: "状态", type: "tag",
  options: [
    { value: 1, text: "启用", color: "success" },
    { value: 0, text: "禁用", color: "danger" }
  ]
}

// 开关状态显示
{ key: "enabled", title: "启用状态", type: "switch" }

// 进度条显示
{ key: "completion", title: "完成度", type: "progress" }

// 评分显示
{ key: "rating", title: "评分", type: "rate", max: 5 }
```

## 详情组件配置

### vk-data-detail 属性

| 参数 | 说明 | 类型 | 默认值 |
|------|------|------|--------|
| data | 详情数据对象 | Object | {} |
| columns | 字段配置数组 | Array | [] |
| label-width | 标签宽度 | String | '100px' |
| size | 组件大小（medium / small / mini） | String | 'medium' |
| border | 是否显示边框 | Boolean | false |
| column | 每行显示的列数 | Number | 1 |

### 字段配置参数

| 参数 | 说明 | 类型 | 默认值 |
|------|------|------|--------|
| key | 字段键名 | String | - |
| title | 字段标题 | String | - |
| type | 字段类型 | String | 'text' |
| width | 字段宽度 | String | - |
| data | 选项数据（radio/checkbox/select） | Array | [] |
| options | 配置选项（tag/rate等） | Object | {} |
| format | 格式化函数 | Function | - |
| render | 自定义渲染函数 | Function | - |

## 使用示例

### 1. 基础详情展示

```vue
<vk-data-detail
  :data="userInfo"
  :columns="userColumns"
  label-width="120px"
></vk-data-detail>
```

### 2. 多列布局

```vue
<vk-data-detail
  :data="productInfo"
  :columns="productColumns"
  :column="2"
  label-width="100px"
></vk-data-detail>
```

### 3. 自定义格式化

```vue
<vk-data-detail
  :data="orderInfo"
  :columns="[
    {
      key: 'create_time',
      title: '创建时间',
      type: 'text',
      format: (value) => dayjs(value).format('YYYY-MM-DD HH:mm:ss')
    }
  ]"
></vk-data-detail>
```

### 4. 自定义渲染

```vue
<vk-data-detail
  :data="customData"
  :columns="[
    {
      key: 'custom_field',
      title: '自定义字段',
      render: (value, row) => {
        return `<span style="color: red;">${value}</span>`
      }
    }
  ]"
></vk-data-detail>
```

## 最佳实践

1. **合理分组信息**
   - 将相关信息分组显示
   - 使用卡片组件分隔不同模块
   - 考虑信息的阅读顺序

2. **响应式布局**
   - 在小屏幕设备上使用单列布局
   - 在大屏幕设备上使用多列布局
   - 使用合适的标签宽度

3. **数据格式化**
   - 对日期时间进行友好格式化
   - 对金额数字进行千分位格式化
   - 对状态码进行语义化显示

4. **性能优化**
   - 避免在详情页加载过多数据
   - 使用分页加载表格数据
   - 考虑图片的懒加载