# 7、radio 单选

## 万能表单使用方式

### 静态数据方式1

**应用场景**：选项数据为静态数据的情况。

```js
{
  key: "radio1", title: "radio类型", type: "radio", itemWidth: 80,
  data: [
    { value: 1, label: "选项一" },
    { value: 2, label: "选项二" }
  ]
},
```

### 按钮形式

```js
{
  key: "radio1", title: "radio类型", type: "radio", optionType: "button",
  data: [
    { value: 1, label: "选项一" },
    { value: 2, label: "选项二" }
  ]
},
```

### 静态数据方式2

**应用场景**：选项数据需要通过函数计算

```js
{
  key: "radio1", title: "radio类型", type: "radio", itemWidth: 80,
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
  key: "radio4", title: "远程radio", type: "radio",
  border: true,
  itemWidth: 80,
  action: "admin/system/user/sys/getList",
  props: { list: "rows", value: "_id", label: "nickname" }
},
```

### 远程数据带参数方式1

```js
{
  key: "radio4", title: "远程radio", type: "radio",
  border: true,
  itemWidth: 80,
  action: "admin/system/user/sys/getList",
  actionData: {
    a: 1
  },
  props: { list: "rows", value: "_id", label: "nickname" }
},
```

### 远程数据带参数方式2

```js
{
  key: "radio4", title: "远程radio", type: "radio",
  border: true,
  itemWidth: 80,
  action: "admin/system/user/sys/getList",
  actionData: () => {
    return {
      a: that.form1.data.a
    }
  },
  props: { list: "rows", value: "_id", label: "nickname" },
},
```

### 数据预处理

```js
{
  key: "radio4", title: "远程radio", type: "radio",
  border: true,
  itemWidth: 80,
  action: "admin/system/user/sys/getList",
  props: { list: "rows", value: "_id", label: "nickname" },
  dataPreprocess: (list) => {
    list.map((item, index) => {
      item.name = `${item.nickname}(${item._id})`
    });
    return list;
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
| actionData | 动态模式 - 远程请求的云函数时的额外参数 | Object、Function | - | - |
| props | 数据源的属性匹配规则 | Object | { list:'list', value:'value', label:'label' } | - |
| dataPreprocess | 动态模式 - 云函数返回的数据进行预处理 | function(list) | - | - |
| textColor | 按钮形式的 Radio 激活时的文本颜色 | String | #ffffff | - |
| fill | 按钮形式的 Radio 激活时的填充色和边框色 | String | #409EFF | - |
| optionType | 选项形状类型 | String | default | button |
| border | 是否显示边框 | Boolean | false | true |
| itemWidth | 选项的统一宽度（用于排版） | Number | - | - |
| isRequest | 是否是http请求模式 | Boolean | false | true |
| requestHeader | http请求头 | Object | - | - |
| requestMethod | http请求method | String | - | - |
| onChange | function(val, formData, column, index, option) | Function | - | - |
| direction | 元素排列方向 row 横向 column 竖向 | String | row | row、column |
| columnMargin | direction=column时元素的上下间隔 | Number | 20 | - |
| defaultIndex | 默认选择第几个，仅在万能表单生效 | Number | - | - |

### onChange 使用示例

```js
{
  key: "radio4", title: "远程radio", type: "radio",
  border: true,
  itemWidth: 80,
  action: "admin/system/user/sys/getList",
  props: { list: "rows", value: "_id", label: "nickname" },
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
  key: "role", title: "选择角色", type: "radio",
  border: true,
  itemWidth: 80,
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
          let list = res.rows.map((item, index) => {
            return {
              value: item.role_id,
              label: item.role_name
            }
          });

          success({
            list: list
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
  }
},
```

#### 自定义function-云函数请求模式示例

```js
{
  key: "role", title: "选择角色", type: "radio",
  border: true,
  itemWidth: 80,
  action: (obj={})=>{
    let {
      data, // 请求数据
      success, // 成功回调
      fail, // 失败回调
      complete, // 成功回调
    } = obj;
    // 发起http请求
    vk.callFunction({
      url: `admin/system/role/sys/getList`,
      data: data,
      success: (res) => {
        if (typeof success === "function") {
          let list = res.rows.map((item, index) => {
            return {
              value: item.role_id,
              label: item.role_name
            }
          });

          success({
            list: list
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
  }
},
```

## 万能表格使用方式

```js
{ 
  key: "gender", title: "性别", type: "radio", width: 120, defaultValue: 0,
  data: [
    { value: 1, label: "男" },
    { value: 2, label: "女" },
    { value: 0, label: "保密" }
  ]
},
```

## template 使用方式

### 静态数据方式

**应用场景**：选项数据为静态数据的情况。

```html
<vk-data-input-radio
  v-model="form1.value"
  :localdata="[
    { value:1, label:'男' },
    { value:2, label:'女' },
    { value:0, label:'保密' }
  ]"
></vk-data-input-radio>
```

### 远程数据方式

**应用场景**：需要从数据库中获取选项的情况。

```html
<vk-data-input-radio
  v-model="form1.value"
  :item-width="80"
  action="admin/system/user/sys/getList"
  :props="{ list:'rows', value:'_id', label:'nickname' }"
></vk-data-input-radio>