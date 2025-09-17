# 25、file-select 素材库选择

## 效果图

## 万能表单使用方式

```js
{ key: "image1", title: "多选图片", type: "file-select", placeholder: "请选择图片", fileType: "image", multiple: true, multipleLimit: 6, imageFit:"cover" },
```

## API

### 公共属性

[点击查看『公共属性』](opens new window)

### 组件属性

| 参数 | 说明 | 类型 | 默认值 | 可选值 |
|------|------|------|--------|--------|
| fileType | 文件类型 | String | image | image、video、other |
| multiple | 是否可多选 | Number | - | - |
| multipleLimit | 最大多选数量 | Number | 9 | - |
| defaultCategory | 默认显示哪个分类下的素材（分类ID） | String | - | - |
| upload | 是否允许上传 | Boolean | true | false |
| updateCategory | 是否允许编辑分类 | Boolean | true | false |
| imageFit | 图片显示模式 详细介绍(opens new window) | String | cover | fill、contain、cover、none、scale-dow |
| cloudDirectory | 上传至指定的云端目录（默认会以年月日为目录） | String | - | - |
| cloudPathRemoveChinese | 删除文件名中的中文（默认true） | Boolean | true | false |
| provider | 云存储供应商，可选： unicloud 上传至空间内置存储 extStorage 上传至扩展存储 aliyun 上传至阿里云oss | String | - | - |
| fileSize | 限制文件大小 | Number | - | - |
| sizeUnit | 文件的单位 | String | MB | KB、MB、GB |
| returnType（1.21.0） | 返回值类型 | String | url | url、id |

### 设置双向绑定的值为file_id

默认双向绑定的值为 url，若要设置为 file_id，则指定属性 returnType 的值 为 id 即可

```js
{ key: "image1", title: "多选图片", type: "file-select", placeholder: "请选择图片", fileType: "image", multiple: true, multipleLimit: 6, imageFit:"cover", returnType: "id" },
```

## 万能表格使用方式

```js
 { key: "image", title: "图片", type: "image", width: 120 },
```

## template 使用方式

```html
<vk-data-input-file-select v-model="image1" placeholder="请选择图片" multiple :multiple-limit="9" file-type="image" image-fit="cover"></vk-data-input-file-select>
```

## 注意：

需确保有以下云函数（如没有，则从新版本中拷贝）

- admin/system_uni/uni-id-files/files/kh/delete
- admin/system_uni/uni-id-files/files/kh/getList
- admin/system_uni/uni-id-files/files/kh/getTempFileURL
- admin/system_uni/uni-id-files/files/kh/update
- admin/system_uni/uni-id-files/categories/kh/getList
- admin/system_uni/uni-id-files/categories/sys/add
- admin/system_uni/uni-id-files/categories/sys/delete
- admin/system_uni/uni-id-files/categories/sys/update
- user/kh/addUploadRecord

**注意**：前端 vk.uploadFile 带参数 needSave: true 时，上传的图片记录会保存到admin后台，可在 素材管理 中查看