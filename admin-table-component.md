# 万能表格组件

组件名：`vk-data-table`

核心思想：通过 JSON 配置渲染规则

## 基础用法

### Vue 模板代码
```html
<vk-data-table :data="table1.data" :columns="table1.columns"></vk-data-table>
```

### JavaScript 代码
```javascript
export default {
  data() {
    return {
      table1: {
        data: [],
        columns: [
          { key: "_id", title: "用户ID", type: "text", width: 200 },
          { key: "username", title: "用户名", type: "text", width: 200 },
          { key: "nickname", title: "用户昵称", type: "text", width: 200 },
          { key: "mobile", title: "手机号", type: "text", width: 200 },
          { key: "comment", title: "备注", type: "text", minWidth: 200 },
        ]
      }
    };
  }
};
```

## 进阶用法

请直接看示例文件 `/pages_template/components/table/table-easy`

## API

### 属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| action | 动态模式 - 支持云函数地址、http地址、自定义function | String、Function | 无 | - |
| auto-action | 动态模式 - 是否组件加载完毕后自动运行action | Boolean | 无 | - |
| query-form-param | 动态模式 - 请求参数（表格查询参数） | Object | {} | - |
| data-preprocess | 动态模式 - 云函数返回的数据进行预处理 | function(list) | - | - |
| is-request | 动态模式 - 是否是http请求模式 | Boolean | false | true |
| request-header | 动态模式 - http请求头 | Object | - | - |
| props | 动态模式 - 渲染数据的配置选项 | Object | - | - |
| retry-count | 动态模式 - 请求最大重试次数 | Number | 0 | - |
| retry-interval | 动态模式 - 每次重试间隔，单位毫秒 | Number | 0 | - |
| data | 静态模式 - 列表数据 | Array | 无 | - |
| total | 静态模式 - 总记录数 | Number | 无 | - |
| columns | 通用 - 字段显示规则 | Array | [] | - |
| height | 通用 - table的高度 | Number | 无 | - |
| max-height | 通用 - table的最大高度 | Number | 无 | - |
| row-height | 通用 - 行高 | Number | 无 | - |
| row-key | 通用 - 行数据的 Key | String | "_id" | - |
| top | 通用 - margin-top的高度 | Number | 10 | - |
| selection | 通用 - 显示多选框 | Boolean | false | true |
| selectable | 通用 - 多选框禁用规则 | Function(row,index) | - | - |
| rowNo | 通用 - 显示序号 | Boolean | false | true |
| pagination | 通用 - 显示分页器 | Boolean | false | true |
| page-size | 通用 - 每页显示数量 | Number | 10 | - |
| page-sizes | 通用 - 每页显示数量选择列表 | Array | [1, 5, 10, 20, 50, 100, 1000] | - |
| get-count | 执行count请求的模式 | String、Boolean | auto | auto、true、false |
| max-page-count | 最大可显示的页数 | Number | - | - |
| right-btns | 右侧显示的按钮列表 | Array | [] | - |
| right-btns-type | 右侧显示的按钮类型 | String | button | text |
| right-btns-align | 右侧显示的按钮对齐方式 | String | center | left right |
| right-btns-more | 右侧更多按钮 | Array | [] | - |
| right-btns-width | 右侧按钮宽度，单位px | Number | - | - |
| custom-right-btns | 自定义右侧按钮 | Array | [] | - |
| empty-text | 空数据时显示的文本内容 | String | "暂无数据" | - |
| default-expand-all | 是否默认展开所有行 | Boolean | false | true |
| tree-props | 渲染嵌套数据的配置选项 | Object | {children: 'children', hasChildren: 'hasChildren'} | - |
| border | 是否带有纵向边框 | Boolean | false | true |
| stripe | 是否为斑马纹 | Boolean | false | true |
| size | Table 的尺寸 | String | 无 | medium / small / mini |
| show-header | 是否显示表头 | Boolean | true | false |
| highlight-current-row | 是否要高亮当前行 | Boolean | true | false |
| detail-dialog-width | 详情弹窗的宽度 | Number,String | "830px" | - |
| multiple | 可多选 | Boolean | true | false |
| default-sort | 默认排序规则 | Object | - | - |
| show-summary | 是否需要显示合计行 | Boolean | false | true |
| summary-method | 自定义合计的计算函数 | Function | - | - |
| total-option | 需要自动统计的行 | Array | - | - |
| expand | 是否开启点击可以展开行 | Boolean | false | true |
| left-fixed | 序号、多选框是否固定在左侧 | Boolean | true | false |
| right-fixed | 操作按钮是否固定在右侧 | Boolean | true | false |
| searched-clean-selection | 表格搜索后是否清空多选框选中的值 | Boolean | true | false |
| need-alert | 表格请求失败后是否自动alert弹窗 | Boolean | true | false |

## columns（字段列表）

columns 是一个数组，数组内每个元素有以下属性：

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| key | 键名 | String | 无 | - |
| title | 标题 | String | 无 | - |
| type | 类型 | String | 无 | 查看type |
| width | 宽度 | Number | 无 | - |
| minWidth | 最小宽度 | Number | 无 | - |
| align | 对其方式 | String | center | left 、right |
| headerAlign | 表头对其方式 | String | center | left 、right |
| sortable | 是否是排序字段 | String | custom | true 、false |
| fixed | 列是否固定在左侧或者右侧 | string, boolean | 无 | true、left、right |
| show | 显示规则 | string 、 array | ["detail","row","expand"] | "detail"、"row"、"expand"、 "none" |
| defaultValue | 默认值 | String | 无 | - |
| formatter | 自定义格式化函数 | function(val, row, column, index) | - | - |
| buttons | 扩展按钮列表 | Array | - | - |

## default-sort（默认排序）

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| name | 需要排序的字段名 | String | - | - |
| type | 排序类型 | String | asc（升序） | desc（降序） |

如默认按排序值从小到大排序：
```html
<vk-data-table
  :default-sort="{ name:'sort', type:'asc' }"
></vk-data-table>
```

推荐写法：
```html
<vk-data-table
  :default-sort="{ name:'_add_time', type:'asc' }"
></vk-data-table>
```

```javascript
columns: [
  { key: "_add_time", title: "添加时间", type: "time", width: 160, sortable: "custom" },
]
```

## right-btns（右侧固定按钮列表）

### 高效用法
```html
<vk-data-table
  :right-btns="['detail_auto','update','delete','more']"
></vk-data-table>
```

### 自定义显示规则用法
```html
<vk-data-table
  :right-btns="table1.rightBtns"
  right-btns-align="right"
></vk-data-table>
```

```javascript
data() {
  return {
    table1: {
      rightBtns: [
        'detail_auto',
        {
          mode: 'update',
          title: '编辑',
          plain: true,
          round: true,
          disabled: (item) => {
            return item._id == '002'
          }
        },
        {
          mode: 'delete',
          title: '删除',
          show: (item) => {
            return item._id != '002'
          }
        },
        'more'
      ]
    }
  }
}
```

### 根据用户角色或权限控制
```javascript
data() {
  return {
    table1: {
      rightBtns: [
        'detail_auto',
        {
          mode: 'update',
          title: '编辑',
          disabled: (item) => {
            return !this.$hasRole("admin") && !this.$hasPermission("user-delete");
          }
        },
        {
          mode: 'delete',
          title: '删除',
          show: (item) => {
            return this.$hasRole("admin") || this.$hasPermission("user-delete");
          }
        },
        'more'
      ]
    }
  }
}
```

### right-btns数组内的可选值
| 可选值 | 说明 |
|--------|------|
| detail | 点击后触发detail事件 |
| detail_auto | 点击后自动弹出详情页 |
| update | 点击后触发update事件 |
| delete | 点击后触发delete事件 |
| more | 与 rightBtnsMore 搭配使用 |

## right-btns-more（更多按钮列表）

```html
<vk-data-table
  :right-btns="['detail_auto','update','delete','more']"
  :right-btns-more="table1.rightBtnsMore"
></vk-data-table>
```

```javascript
data() {
  return {
    table1: {
      rightBtnsMore: [
        {
          title: '按钮1',
          disabled: (item) => {
            return item._id == '002'
          },
          onClick: (item) => {
            vk.toast(`${item._id}-按钮1`);
          }
        }
      ]
    }
  }
}
```

## custom-right-btns（自定义右侧固定按钮）

```html
<vk-data-table
  :custom-right-btns="table1.customRightBtns"
  right-btns-align="right"
></vk-data-table>
```

```javascript
data() {
  return {
    table1: {
      customRightBtns: [
        {
          title: '按钮1', type: 'primary', icon: 'el-icon-edit',
          disabled: (item) => {
            return item._id == '002'
          },
          onClick: (item) => {
            vk.toast(`${item._id}-按钮1`);
          }
        }
      ]
    }
  }
}
```

## 行高对不齐处理

### 方案一：设置固定行高
```html
<vk-data-table
  :row-height="100"
></vk-data-table>
```

### 方案二：去掉左右两侧浮动
```html
<vk-data-table
  :left-fixed="false"
  :right-fixed="false"
></vk-data-table>
```

## http请求模式

```html
<vk-data-table
  ref="table1"
  action="https://www.xxx.com/xxx/xxx"
  :is-request="true"
  :props="{ rows: 'rows', total: 'total', pageIndex: 'pageIndex', pageSize: 'pageSize', formData: 'formData' }"
  :columns="table1.columns"
  :query-form-param="queryForm1"
></vk-data-table>
```

## 自定义function请求模式

### 自定义function-http请求模式示例
```javascript
export default {
  data() {
    return {
      table1: {
        action: (obj = {}) => {
          let { data, success, fail, complete } = obj;
          vk.request({
            url: `https://www.xxx.com/api/list`,
            method: "POST",
            header: { "content-type": "application/json; charset=utf-8" },
            data: data,
            success: (res) => {
              if (typeof success === "function") {
                success({
                  rows: res.rows,
                  total: res.total,
                  getCount: res.getCount,
                  hasMore: res.hasMore
                });
              }
            }
          });
        }
      }
    }
  }
}
```

## 数据预处理

```html
<vk-data-table
  :data-preprocess="table1.dataPreprocess"
></vk-data-table>
```

```javascript
export default {
  data() {
    return {
      table1: {
        dataPreprocess: (list) => {
          list.map((item, index) => {
            item.a = 1;
          });
          return list;
        }
      }
    }
  }
}
```

## 展开行

```html
<vk-data-table
  ref="table1"
  :action="table1.action"
  :columns="table1.columns"
  :query-form-param="queryForm1"
  :expand="true"
></vk-data-table>
```

## 表格自带的多选框禁用规则

```html
<vk-data-table
  :selection="true"
  :selectable="table1.selectable"
></vk-data-table>
```

```javascript
export default {
  data() {
    return {
      table1: {
        selectable: (row, index) => {
          if (index == 1) {
            return false;
          } else {
            return true;
          }
        }
      }
    }
  }
}
```

## 异常重试机制

```html
<vk-data-table
  ref="table1"
  :action="table1.action"
  :columns="table1.columns"
  :query-form-param="queryForm1"
  :retry-count="3"
></vk-data-table>
```

## 列支持拖动改变宽度

```html
<vk-data-table
  ref="table1"
  :border="true"
></vk-data-table>
```

## show（字段显示规则）

show是一个字符串数组，用于控制字段在不同场景下的显示：

- 如果不存在 show 参数，则代表全部显示
- 如果数组中有包含 "detail"，则会在详情弹窗时显示
- 如果数组中有包含 "row"，则会在表格行内显示
- 如果数组中有包含 "expand"，则会在表格行展开时显示
- 如果数组只有 ["none"]，则代表都不显示

### 示例
```javascript
// 只在详情弹窗时显示
{ key: "nickname", title: "昵称", type: "text", show: ["detail"] }

// 只在表格行内显示
{ key: "nickname", title: "昵称", type: "text", show: ["row"] }

// 都不显示
{ key: "nickname", title: "昵称", type: "text", show: ["none"] }
```

## type（字段类型）

```javascript
table1: {
  columns: [
    { key: "nickname", title: "昵称", type: "text", width: 120, defaultValue: "未设置昵称" },
    { key: "avatar", title: "头像", type: "avatar", width: 80, imageWidth: 40, shape: "circle" },
    { key: "images", title: "图片", type: "image", width: 120, imageWidth: 60 },
    { key: "rate", title: "评分", type: "rate", width: 120 },
    { key: "switch", title: "开关", type: "switch", width: 120, activeValue: true, inactiveValue: false },
    { key: "icon1", title: "图标", type: "icon", width: 120 },
    {
      key: "icon2", title: "图标", type: "icon", width: 120,
      data: [
        { value: 1, icon: "vk-icon-activityfill" },
        { value: 2, icon: "vk-icon-crownfill" }
      ]
    }
  ]
}
```

## 事件

| 事件名 | 说明 | 回调参数 |
|--------|------|----------|
| table-mounted | 表格组件挂载完毕时 | - |
| success | 表格数据查询成功时 | { data, list, total } |
| fail | 表格数据查询失败时 | err |
| complete | 表格数据查询无论成功和失败都会触发 | res |
| detail | 点击详情按钮时 | { item, row, open } |
| update | 点击修改按钮时 | { item, row } |
| delete | 点击删除按钮时 | { item, deleteFn } |
| current-change | 点击(高亮)某一行 | row |
| row-click | 单击某一行 | row, column, event |

## 方法

通过 `this.$refs.table1.xxx();` 方式调用

| 方法名 | 说明 |
|--------|------|
| refresh | 刷新 |
| search | 查询（搜索） |
| getCurrentRow | 获取当前选中的行的数据 |
| getTableData | 获取整个表格数据 |
| getTableFormatterData | 获取整个表格数据（格式化后的数据） |
| getMultipleSelection | 获取多选框的数据 |
| showDetail | 显示详情页 |
| closeDetail | 关闭详情页 |
| exportExcel | 导出xls表格文件 |
| deleteRows | 删除指定的行 |
| updateRows | 更新指定的行数据 |

### 示例用法
```javascript
// 显示详情页
this.$refs.table1.showDetail(item);

// 删除指定的行
this.$refs.table1.deleteRows({
  ids: ["60acf6248a69dc00018d8520"],
  success: () => {}
});

// 导出表格数据
this.$refs.table1.exportExcel();
```

## 插槽

### 重写字段渲染示例
```html
<vk-data-table>
  <template v-slot:gender="{ row, column, index }">
    <view>我是插槽：{{ row.gender }}</view>
  </template>
</vk-data-table>
```

### 展开行插槽示例
```html
<vk-data-table>
  <template v-slot:tableExpand="{ row }">
    <view>我是插槽：{{ row._id }}</view>
  </template>
</vk-data-table>
```

## 万能表格搜索组件

```html
<!-- 表格搜索组件开始 -->
<vk-data-table-query
  v-model="queryForm1.formData"
  :columns="queryForm1.columns"
  @search="search"
></vk-data-table-query>
<!-- 表格搜索组件结束 -->

<!-- 表格组件开始 -->
<vk-data-table
  :query-form-param="queryForm1"
></vk-data-table>
<!-- 表格组件结束 -->
```

## vk.baseDao.getTableData

```javascript
let data = {
  pageIndex: 1,
  pageSize: 20,
  formData: { nickname: "张飞" },
  columns: [
    { key: "nickname", title: "昵称", type: "text", width: 160, mode: "%%" },
  ],
  sortRule: [{ name: "_add_time", type: "desc" }]
};

let dbName = "表名";
vk.baseDao.getTableData({
  dbName,
  data,
  whereJson: { user_id: uid }
});
```

## 万能表格合计列

### 简单模式
```html
<vk-data-table
  :show-summary="true"
  :total-option="[
    { key: 'balance', 'unit': '元', type:'money', precision:2 }
  ]"
></vk-data-table>
```

### 自定义模式
```html
<vk-data-table
  :show-summary="true"
  :summary-method="summaryMethod"
></vk-data-table>
```

```javascript
summaryMethod({ columns, data }) {
  const means = [''];
  let totalOption = [{ key: 'money', 'unit': '元', type: "money" }];
  
  // 自定义合计逻辑
  return [means];
}
```

## 进阶用法

更多高级用法和完整示例，请参考：
- `/pages_template/components/table/table-easy`
- `/pages_template/components/table/table-pro`
- `/pages_template/components/table/table-slot`