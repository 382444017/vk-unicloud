# 上传组件示例

本文档展示了 `pages_template/components/upload/` 目录下的上传组件示例代码。

## upload-basic.vue - 基础上传

```vue
<template>
  <view class="page">
    <vk-data-page-header
      title="Upload 上传"
      subTitle="基础用法"
    ></vk-data-page-header>
    <view class="page-body">
      <vk-data-upload
        v-model="form1.images"
        :limit="6"
      ></vk-data-upload>
    </view>

    <el-button @click="clearImages">清空值</el-button>
    <el-button @click="setImages">设置值</el-button>

    <view class="tips mt15">
      <view class="mt15 json-view" v-if="form1">
        <view style="font-size: 14px;">表单数据</view>
        <pre>
          {{ form1 }}
        </pre>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      form1: {
        images: []
      }
    }
  },
  methods: {
    clearImages() {
      this.form1.images = []
    },
    setImages() {
      this.form1.images = [
        'https://example.com/image1.jpg',
        'https://example.com/image2.jpg'
      ]
    }
  }
}
</script>
```

## upload-drag.vue - 拖拽上传

```vue
<template>
  <view class="page">
    <vk-data-page-header
      title="Upload 上传"
      subTitle="拖拽上传"
    ></vk-data-page-header>
    <view class="page-body">
      <vk-data-upload
        v-model="form1.files"
        :limit="10"
        drag
        multiple
        accept=".jpg,.jpeg,.png,.gif,.pdf,.doc,.docx"
        :auto-upload="true"
        :show-file-list="true"
      >
        <div class="upload-drag-area">
          <i class="el-icon-upload"></i>
          <div class="upload-text">将文件拖到此处，或<em>点击上传</em></div>
          <div class="upload-tip">支持jpg、png、pdf等格式，单个文件不超过10MB</div>
        </div>
      </vk-data-upload>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      form1: {
        files: []
      }
    }
  }
}
</script>

<style scoped>
.upload-drag-area {
  padding: 40px;
  text-align: center;
  border: 2px dashed #dcdfe6;
  border-radius: 6px;
  background-color: #f5f7fa;
}

.upload-drag-area .el-icon-upload {
  font-size: 48px;
  color: #c0c4cc;
  margin-bottom: 16px;
}

.upload-text {
  font-size: 14px;
  color: #606266;
  margin-bottom: 8px;
}

.upload-text em {
  color: #409eff;
  font-style: normal;
}

.upload-tip {
  font-size: 12px;
  color: #909399;
}
</style>
```

## 上传组件配置

### vk-data-upload 属性

| 参数 | 说明 | 类型 | 默认值 |
|------|------|------|--------|
| v-model | 绑定的文件列表 | Array | [] |
| action | 上传的URL地址 | String | '' |
| limit | 最大允许上传个数 | Number | 9 |
| multiple | 是否支持多选文件 | Boolean | false |
| drag | 是否启用拖拽上传 | Boolean | false |
| accept | 接受上传的文件类型 | String | '' |
| auto-upload | 是否自动上传 | Boolean | true |
| show-file-list | 是否显示已上传文件列表 | Boolean | true |
| list-type | 文件列表展示类型（text/picture/picture-card） | String | 'text' |
| headers | 设置上传的请求头部 | Object | {} |
| data | 上传时附带的额外参数 | Object | {} |
| with-credentials | 支持发送 cookie 凭证信息 | Boolean | false |

### 事件

| 事件名 | 说明 | 参数 |
|--------|------|------|
| change | 文件状态改变时的钩子 | file, fileList |
| progress | 文件上传时的进度钩子 | event, file, fileList |
| success | 文件上传成功时的钩子 | response, file, fileList |
| error | 文件上传失败时的钩子 | error, file, fileList |
| remove | 文件列表移除文件时的钩子 | file, fileList |

## 上传类型示例

### 1. 图片上传

```vue
<vk-data-upload
  v-model="form.images"
  action="/api/upload/image"
  :limit="6"
  accept="image/*"
  list-type="picture-card"
  :multiple="true"
>
  <i class="el-icon-plus"></i>
</vk-data-upload>
```

### 2. 文件上传

```vue
<vk-data-upload
  v-model="form.documents"
  action="/api/upload/file"
  :limit="5"
  accept=".doc,.docx,.pdf,.xls,.xlsx"
  :show-file-list="true"
  :multiple="true"
>
  <el-button size="small" type="primary">点击上传</el-button>
  <div slot="tip" class="upload-tip">支持doc、docx、pdf、xls、xlsx格式</div>
</vk-data-upload>
```

### 3. 多类型文件上传

```vue
<vk-data-upload
  v-model="form.attachments"
  action="/api/upload/any"
  :limit="10"
  accept="image/*,.pdf,.doc,.docx,.xls,.xlsx,.zip,.rar"
  :multiple="true"
  :show-file-list="true"
>
  <el-button size="small" type="primary">选择文件</el-button>
</vk-data-upload>
```

### 4. 自定义上传

```vue
<vk-data-upload
  v-model="form.files"
  :auto-upload="false"
  :multiple="true"
  :show-file-list="true"
  @change="handleFileChange"
>
  <el-button size="small" type="primary">选择文件</el-button>
</vk-data-upload>

<el-button 
  v-if="form.files.length > 0"
  @click="uploadFiles"
  type="success"
>
  开始上传
</el-button>
```

## 上传处理函数

### 1. 自定义上传逻辑

```javascript
methods: {
  async customUpload(file) {
    try {
      const formData = new FormData()
      formData.append('file', file.raw)
      formData.append('type', 'avatar')
      
      const response = await this.$http.post('/api/upload', formData, {
        headers: { 'Content-Type': 'multipart/form-data' },
        onUploadProgress: (progressEvent) => {
          const percent = Math.round(
            (progressEvent.loaded * 100) / progressEvent.total
          )
          file.percentage = percent
        }
      })
      
      return response.data
    } catch (error) {
      throw new Error('上传失败')
    }
  }
}
```

### 2. 文件验证

```javascript
methods: {
  beforeUpload(file) {
    const isImage = file.type.includes('image/')
    const isLt10M = file.size / 1024 / 1024 < 10
    
    if (!isImage) {
      this.$message.error('只能上传图片文件!')
      return false
    }
    
    if (!isLt10M) {
      this.$message.error('图片大小不能超过10MB!')
      return false
    }
    
    return true
  }
}
```

### 3. 多文件上传处理

```javascript
methods: {
  handleUploadMultiple(files) {
    const uploadPromises = files.map(file => {
      return new Promise((resolve, reject) => {
        const formData = new FormData()
        formData.append('file', file.raw)
        
        this.$http.post('/api/upload', formData)
          .then(response => resolve(response.data))
          .catch(error => reject(error))
      })
    })
    
    Promise.all(uploadPromises)
      .then(results => {
        console.log('所有文件上传成功', results)
      })
      .catch(error => {
        console.error('文件上传失败', error)
      })
  }
}
```

## 最佳实践

### 1. 文件类型验证

```javascript
// 图片文件验证
const imageValidator = (file) => {
  const allowedTypes = ['image/jpeg', 'image/png', 'image/gif']
  const isImage = allowedTypes.includes(file.type)
  const isLt5M = file.size / 1024 / 1024 < 5
  
  if (!isImage) {
    this.$message.error('只能上传JPG、PNG、GIF格式的图片!')
    return false
  }
  
  if (!isLt5M) {
    this.$message.error('图片大小不能超过5MB!')
    return false
  }
  
  return true
}
```

### 2. 上传进度显示

```vue
<vk-data-upload
  @progress="handleProgress"
>
  <template #default>
    <el-progress 
      v-if="uploading"
      :percentage="progressPercent"
      :status="progressStatus"
    ></el-progress>
  </template>
</vk-data-upload>
```

### 3. 错误处理

```javascript
methods: {
  handleUploadError(error, file) {
    console.error('上传错误:', error)
    
    if (error.response) {
      switch (error.response.status) {
        case 413:
          this.$message.error('文件太大，请压缩后重新上传')
          break
        case 415:
          this.$message.error('不支持的文件格式')
          break
        default:
          this.$message.error('上传失败，请重试')
      }
    } else {
      this.$message.error('网络错误，请检查网络连接')
    }
  }
}
```

### 4. 文件预览

```javascript
methods: {
  handlePreview(file) {
    if (file.type.includes('image/')) {
      // 图片预览
      this.previewImage = file.url
      this.previewVisible = true
    } else {
      // 文件下载
      window.open(file.url, '_blank')
    }
  }
}
```

## 服务器端响应格式

上传组件期望服务器返回以下格式的响应：

```javascript
{
  "code": 0,          // 0表示成功，非0表示失败
  "msg": "success",    // 提示信息
  "data": {           // 上传成功返回的数据
    "url": "https://example.com/uploads/image.jpg",
    "name": "image.jpg",
    "size": 102400,
    "type": "image/jpeg"
  }
}
```

## 安全考虑

1. **文件类型验证** - 在客户端和服务端都进行文件类型验证
2. **文件大小限制** - 设置合理的文件大小限制
3. **病毒扫描** - 对上传的文件进行病毒扫描
4. **访问控制** - 控制上传文件的访问权限
5. **文件名处理** - 对文件名进行安全处理，防止路径遍历攻击