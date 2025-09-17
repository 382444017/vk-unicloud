# 27、tree-select 树形选择

## 万能表单使用方式

### 静态数据方式1

**应用场景**：选项数据为静态数据的情况。

```js
{
  key: "tree1", title: "本地数据", type: "tree-select",
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
    },
  ]
},
```

### 静态数据方式2

**应用场景**：选项数据需要通过函数计算

```js
{
  key: "tree1", title: "本地数据", type: "tree-select",
  data: () => {
    let list = that.list;
    return list;
  }
},
```

### 远程数据方式

**应用场景**：需要从数据库中获取选项的情况。

```js
{
  key: "parent_id", title: "父级菜单", type: "tree-select", tips: "父级的menu_id",
  action: "admin/system/menu/sys/getAll",
  props: { list: "rows", value: "menu_id", label: "label", children: "children" },
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
| actionData | 动态模式 - 远程请求的云函数时的额外参数 | Object、Function | - | - |
| props | 数据源的属性匹配规则 | Object | 查看props | - |
| multiple | 是否可多选 | Boolean | false | true |
| showCheckbox | 节点是否可被选择 | Boolean | false | true |
| checkStrictly | 在显示复选框的情况下，是否严格的遵循父子不互相关联的做法 | Boolean | false | true |
| defaultExpandAll | 是否默认展开所有节点 | Boolean | true | false |
| expandOnClickNode | 是否在点击节点的时候展开或者收缩节点， 默认值为 true，如果为 false，则只有点箭头图标的时候才会展开或者收缩节点。 | Boolean | false | true |
| checkOnClickNode | 是是否在点击节点的时候选中节点，默认值为 false，即只有在点击复选框时才会选中节点。 | Boolean | true | false |
| defaultExpandedKeys | 默认展开的节点的 key 的数组 | Array | - | - |
| defaultCheckedKeys | 默认勾选的节点的 key 的数组 | Array | - | - |
| currentNodeKey | 当前选中的节点 | String、Number | - | - |
| accordion | 是否每次只打开一个同级树节点展开 | Boolean | false | true |
| indent | 相邻级节点间的水平缩进，单位为像素 | indent | 16 | - |
| iconClass | 自定义树节点的图标 | string | - | - |
| isRequest | 是否是http请求模式 | Boolean | false | true |
| requestHeader | http请求头 | Object | - | - |
| requestMethod | http请求method | String | - | - |

### props

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| list | 数据源的键名，一般为rows | String | list | - |
| value | 指定选项的值为选项对象的某个属性值 | String | value | - |
| label | 指定选项标签为选项对象的某个属性值 | String | label | - |
| children | 指定选项的子选项为选项对象的某个属性值 | String | children | - |
| disabled | 指定选项的禁用为选项对象的某个属性值 | String | disabled | - |
| isLeaf | 指定节点是否为叶子节点，仅在指定了 lazy 属性的情况下生效 | String | disabled | - |

### onChange 使用示例

```js
{
  key: "tree1", title: "本地数据", type: "tree-select",
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
  onChange: (val, formData, column, index, option, val2, option2) => {
    /**
     * val 双向绑定的表单值
     * formData 双向绑定的整个表单数据源
     * option val对应的对象
     * val2 多选模式下包含了父级的值
     * option2 val2对应的对象
     */
    console.log(1, val, formData, column, index, option, val2, option2);

  }
}
```

**推荐使用 watch 代替 onChange** [传送门 - watch](opens new window)

### 多选模式下，获取选中的树状结构的数据

```js
{
  key: "tree1", title: "本地数据", type: "tree-select",
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
  onChange: (val, formData, column, index, option, val2, option2) => {
    /**
     * val 双向绑定的表单值
     * formData 双向绑定的整个表单数据源
     * option val对应的对象
     * val2 多选模式下包含了父级的值
     * option2 val2对应的对象
     */
    // 在多选模式下想获取树状结构的数据，可以参考下面写法
    let newObj = vk.pubfn.copyObject(option2); //拷贝
    let arr = []
    for (let i = 0; i < newObj.length; i++) {
      delete newObj[i].children //去除children
      arr.push(newObj[i])
    }
    // 通过数组转对象的方法转成需要的树状结构并赋给formData
    formData.objArr = vk.pubfn.arrayToTree(arr, {
      id: "_id",
      parent_id: "parent_id",
      children: "children"
    });
  }
}
```

### 自定义function请求模式

vk-unicloud-admin-ui 的npm依赖版本需 >= 1.17.17

此方式同样支持http，且更自由化，比如可以在发起请求前处理请求参数，在请求成功后，处理返回参数等等。

**优势**：更自由化、基本可以满足所有需求场景

**劣势**：代码量较多

#### 自定义function-http请求模式示例

```js
{
  key: "parent_id", title: "父级菜单", type: "tree-select", tips: "父级的menu_id",
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
  props: { list: "rows", value: "menu_id", label: "label", children: "children" },
},
```

#### 自定义function-云函数请求模式示例

```js
{
  key: "parent_id", title: "父级菜单", type: "tree-select", tips: "父级的menu_id",
  action: (obj={})=>{
    let {
      data, // 请求数据
      success, // 成功回调
      fail, // 失败回调
      complete, // 成功回调
    } = obj;
    // 发起http请求
    vk.callFunction({
      url: `admin/system/menu/sys/getAll`,
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
  props: { list: "rows", value: "menu_id", label: "label", children: "children" },
},
```

## 万能表格使用方式

无

## template 使用方式

### 静态数据方式

**应用场景**：选项数据为静态数据的情况。

```html
<vk-data-input-tree-select
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
></vk-data-input-tree-select>
```

### 远程数据方式

**应用场景**：需要从数据库中获取选项的情况。

```html
<vk-data-input-tree-select
  v-model="form1.value"
  action="admin/system/menu/sys/getAll"
  :props="{ list:'rows', value:'menu_id', label:'label', children:'children' }"
  placeholder="请选择"
></vk-data-input-tree-select>