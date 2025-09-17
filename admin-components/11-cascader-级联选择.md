# 11、cascader 级联选择

## 万能表单使用方式

### 静态数据方式

**应用场景**：选项数据为静态数据的情况。

**示例**：

```js
{
  key: "cascader1", title: "本地数据级联", type: "cascader",
  data: [
    {
      value: 1,
      label: "数学",
      children: [
        { value: 11, label: "奥数" },
        { value: 12, label: "微积分" }
      ]
    },
    {
      value: 2,
      label: "语文",
      children: [
        { value: 21, label: "文言文" },
        { value: 22, label: "古诗" }
      ]
    }
  ]
},
```

### 静态数据方式2

**应用场景**：选项数据需要通过函数计算

**示例**：

```js
{
  key: "cascader1", title: "本地数据级联", type: "cascader",
  data: () => {
    let list = that.list;
    return list;
  }
},
```

### 远程数据方式

**应用场景**：需要从数据库中获取选项的情况。

**示例**：

```js
{
  key: "cascader2", title: "云端数据级联", type: "cascader",
  action: "admin/system/permission/sys/getAll",
  props: {
    list: "rows",
    value: "permission_id",
    label: "label",
    children: "children",
    // multiple:true
  }
},
```

### 远程搜索带参数方式1

**示例**：

```js
{
  key: "cascader2", title: "云端数据级联", type: "cascader",
  action: "admin/system/permission/sys/getAll",
  actionData: {
    a: 1
  },
  props: {
    list: "rows",
    value: "permission_id",
    label: "label",
    children: "children",
    // multiple:true
  }
},
```

### 远程搜索带参数方式2

**示例**：

```js
{
  key: "cascader2", title: "云端数据级联", type: "cascader",
  action: "admin/system/permission/sys/getAll",
  actionData: () => {
    return {
      a: that.form1.data.a
    }
  },
  props: {
    list: "rows",
    value: "permission_id",
    label: "label",
    children: "children",
    // multiple:true
  }
},
```

### 远程懒加载方式

**应用场景**：数据量很大，一次性获取数据库容易超时时。

**示例**

云函数代码见本页面最下方。

```js
{
  key: "cascader2", title: "云端数据级联", type: "cascader",
  action: "admin/system/menu/sys/getCascader",
  props: {
    list: "rows",
    value: "menu_id",
    label: "label",
    children: "children",
    lazy: true
  }
},
```

### 遵守父子节点不互相关联

**应用场景**：选商品分类的时候，可以选择子分类，也可以直接选父分类

**参数**：

- `checkStrictly:true`（设置为true让所有节点都可以被直接选择）
- `emitPath:false`（设置为false只返回被选中节点的值）

**示例**

云函数代码见本页面最下方。

```js
{
  key: "cascader2", title: "云端数据级联", type: "cascader",
  action: "admin/system/menu/sys/getCascader",
  props: {
    list: "rows",
    value: "menu_id",
    label: "label",
    children: "children",
    checkStrictly: true,
    emitPath: false
  }
},
```

## API

### 公共属性

[点击查看『公共属性』](opens new window)

### 组件属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| data | 静态模式数据源 | Array、Function | - | - |
| action | 支持： 1、vk框架下的云函数地址 2、http请求地址 3、自定义function请求模式 | String、Function | 无 | - |
| props | 数据源的属性匹配规则 | Object | 查看props | - |
| showAllLevels | 输入框中是否显示选中值的完整路径 | Boolean | true | false |
| collapseTags | 多选模式下是否折叠Tag | Boolean | false | true |
| separator | 选项分隔符 | String | 斜杠' / ' | - |
| clearable | 是否可以清空选项 | Boolean | true | false |
| filterable | 是否可搜索选项 | Boolean | - | - |
| filterMethod | 自定义搜索逻辑，filterable:true时生效 | function(node, keyword) | - | - |
| debounce | 搜索关键词输入的去抖延迟，毫秒，filterable:true时生效 | Number | 300 | - |
| beforeFilter | 筛选之前的钩子，若返回 false 或者返回 Promise 且被 reject，则停止筛选，filterable:true时生效 | function(value) | - | - |
| isRequest | 是否是http请求模式 | Boolean | false | true |
| requestHeader | http请求头 | Object | - | - |
| requestMethod | http请求method | String | - | - |

### props

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| expandTrigger | 次级菜单的展开方式 | String | click | hover |
| multiple | 是否多选 | Boolean | false | true |
| checkStrictly | 是否严格的遵守父子节点不互相关联 | Boolean | false | true |
| emitPath | 在选中节点改变时，是否返回由该节点所在的各级菜单的值所组成的数组，若设置 false，则只返回该节点的值 | Boolean | true | false |
| lazy | 是否动态加载子节点，需与 lazyLoad 方法结合使用 | Boolean | false | true |
| lazyLoad | 加载动态数据的方法，仅在 lazy 为 true 时有效 function(node, resolve)，node为当前点击的节点，resolve为数据加载完成的回调(必须调用) | function(node, resolve) | - | - |
| value | 指定选项的值为选项对象的某个属性值 | String | value | - |
| label | 指定选项标签为选项对象的某个属性值 | String | label | - |
| children | 指定选项的子选项为选项对象的某个属性值 | String | children | - |
| disabled | 指定选项的禁用为选项对象的某个属性值 | String | disabled | - |
| leaf | 指定选项的叶子节点的标志位为选项对象的某个属性值，当 lazy = true 时生效 | String | leaf | - |

### onChange 使用示例

```js
{
  key: "cascader1", title: "cascader类型1", type: "cascader",
  data: [
    {
      value: 1,
      label: "数学",
      children: [
        { value: 11, label: "奥数" },
        { value: 12, label: "微积分" }
      ]
    },
    {
      value: 2,
      label: "语文",
      children: [
        { value: 21, label: "文言文" },
        { value: 22, label: "古诗" }
      ]
    }
  ],
  onChange: (val, formData, column, index, option) => {
    console.log(1, val, formData, column, index, option);
  }
},
```

**推荐使用 watch 代替 onChange** [传送门 - watch](opens new window)

### 自定义function请求模式

vk-unicloud-admin-ui 的npm依赖版本需 >= 1.17.17

此方式同样支持http，且更自由化，比如可以在发起请求前处理请求参数，在请求成功后，处理返回参数等等。

**优势**：更自由化、基本可以满足所有需求场景

**劣势**：代码量较多

#### 自定义function-http请求模式示例

```js
{
  key: "cascader2", title: "云端数据级联", type: "cascader",
  action: (obj={})=>{
    let {
      data, // 请求数据
      success, // 成功回调
      fail, // 失败回调
      complete, // 成功回调
    } = obj;
    // 发起http请求
    vk.request({
      url: `https://www.xxx.com/api/list`,
      method: "POST",
      header: {
        "content-type": "application/json; charset=utf-8",
      },
      data: data,
      success: (res) => {
        if (typeof success === "function") {
          success({
            rows: res.rows
          });
        }
      },
      fail: (res) => {
        if (typeof fail === "function") fail(res);
      },
      complete: (res) => {
        if (typeof complete === "function") complete(res);
      }
    });
  },
  props: {
    list: "rows",
    value: "permission_id",
    label: "label",
    children: "children",
    // multiple:true
  }
},
```

#### 自定义function-云函数请求模式示例

```js
{
  key: "cascader2", title: "云端数据级联", type: "cascader",
  action: (obj={})=>{
    let {
      data, // 请求数据
      success, // 成功回调
      fail, // 失败回调
      complete, // 成功回调
    } = obj;
    // 发起http请求
    vk.callFunction({
      url: `admin/system/permission/sys/getAll`,
      data: data,
      success: (res) => {
        if (typeof success === "function") {
          success({
            rows: res.rows
          });
        }
      },
      fail: (res) => {
        if (typeof fail === "function") fail(res);
      },
      complete: (res) => {
        if (typeof complete === "function") complete(res);
      }
    });
  },
  props: {
    list: "rows",
    value: "permission_id",
    label: "label",
    children: "children",
    // multiple:true
  }
},
```

## 万能表格使用方式

无

## template 使用方式

### 静态数据方式

**应用场景**：选项数据为静态数据的情况。

**示例**：

```html
<vk-data-input-cascader
  v-model="form1.value"
  :localdata="[
    {
      value:1,
      label:'数学',
      children:[
        { value:11,label:'奥数' },
        { value:12,label:'微积分' }
      ]
    },
    {
      value:2,
      label:'语文',
      children:[
        { value:21, label:'文言文' },
        { value:22, label:'古诗' }
      ]
    },
  ]"
  placeholder="请选择"
></vk-data-input-cascader>
```

### 远程数据方式

**应用场景**：需要从数据库中获取选项的情况。

**示例**：

```html
<vk-data-input-cascader
  v-model="form1.value"
  action="admin/system/permission/sys/getAll"
  :props="{ list:'rows', value:'permission_id', label:'label', children:'children' }"
  placeholder="请选择"
></vk-data-input-cascader>
```

### 远程懒加载方式云函数代码示例

**示例**：

```js
'use strict';
module.exports = {
  /**
   * 获取菜单级联数据(懒加载形式)
   * @url admin/system/menu/sys/getCascader 前端调用的url参数地址
   * data 请求参数 说明
   * @param {Object} node 			当前点击的节点
   * res 返回参数说明
   * @param {Number} code 错误码，0表示成功
   * @param {String} msg 详细信息
   */
  main: async (event) => {
    let { data = {}, userInfo, util, filterResponse, originalParam } = event;
    let { customUtil, uniID, config, pubFun, vk, db, _ } = util;
    let { uid } = data;
    let res = { code: 0, msg: "" };
    // 业务逻辑开始-----------------------------------------------------------
    let { node = {} } = data;
    let whereJson = {
      enable: true
    };
    if (node.root) {
      whereJson["parent_id"] = null;
    } else {
      whereJson["parent_id"] = node.value;
    }
    res = await vk.baseDao.selects({
      dbName: "opendb-admin-menus",
      pageIndex: 1,
      pageSize: 500,
      whereJson,
      sortArr: [{ name: "sort", type: "asc" }],
      // 副表列表
      foreignDB: [{
        dbName: "opendb-admin-menus",
        localKey: "menu_id",
        foreignKey: "parent_id",
        as: "hasChildren",
        limit: 1, // 避免浪费查询,这里设置limit:1
      }]
    });
    // 数据预处理
    let rows = res.rows;
    for (let i in rows) {
      let item = rows[i];
      rows[i].label = `${item.name}（${item.menu_id}）`;
      if (vk.pubfn.isNull(item.hasChildren)) {
        rows[i].leaf = true;
      }
    }
    res.rows = rows;
    // 业务逻辑结束-----------------------------------------------------------
    return res;
  }
}